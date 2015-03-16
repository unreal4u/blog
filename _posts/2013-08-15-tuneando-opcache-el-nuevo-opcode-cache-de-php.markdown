---
comments: true
date: 2013-08-15 22:04:30+00:00
layout: post
slug: tuneando-opcache-el-nuevo-opcode-cache-de-php
title: Tuneando opcache, el nuevo opcode cache de PHP
wordpress_id: 818
categories:
- Mundo Web
- Pensamientos Personales
- PHP
---

Hace poco se decidió, con gran ayuda de Zend, incluir Zend Optimizer+ en el código fuente de PHP como la opción predeterminada para que hiciera de opcode cache en favor de APC u otro. Las principales razones tenían que ver con que con Zend Optimizer+ estaban trabajando más personas que con APC (sólo Rasmus Lerdorf estaba trabajando con APC, mientras que de Zend Optimizer+ más personas conocían su funcionamiento [Fuente: lista de correos PHP-DEV]), [también es más rápido que APC](https://docs.google.com/spreadsheet/ccc?key=0Agw0hgqCxf0cdEZsdm1yNjd3amJReG05MzJYSF9USGc#gid=0) y como última gran razón (hay varias más), la manera de trabajar [es más consistente que APC](https://wiki.php.net/rfc/optimizerplus#alternatives). Por lo tanto, la opción más lógica era por supuesto realizar el cambio.

A partir de PHP 5.5 ya viene incluida, aunque existen packages para PHP 5.3 y 5.4, tan fácil de instalar como invocar un simple `yum install php-opcache`. El nuevo nombre pasó de ser Zend Optimizer+ a opcache, así que de ahora en adelante, me referiré sólo a opcache.
<!-- more -->



## Barniz teórico


En aquellos lejanos tiempos de APC, configurarlo bien era difícil. Ciertos parámetros parecían trabajar bien, hasta que un par de días después uno revisaba y resultaba que la supuesta configuración que trabajaba bien en realidad no trabajaba tan bien después de todo. Vuelta a revisar, a configurar, jugar y experimentar hasta que de alguna forma, casi por arte de magia todo trabajaba medianamente bien y de forma consistente. Por si fuera poco, algunas cosas simplemente no trabajaban bien con APC (wp-super-cache alguien?) y daban errores extrañísimos que eran incomprensibles. 
Es por eso que cuando leí las ventajas y desventajas de opcache en cierto sentido me enamoré del mismo ya que prometía una mejor experiencia, contando como única desventaja que no liberaba memoria que ya no estaba en uso, haciendo que cuando se alcanzara cierto porcentaje de memoria inútil, se re-iniciara todo el motor. La desventaja de esto es que todos los scripts se tienen que compilar nuevamente, pero esto mismo hace que se eviten muchos problemas de sincronización, que eran típicos de APC. Por lo demás, con un buen tuneo, esto no debería pasar jamás. 



## Tuneo propiamente tal


Y es ahí donde entramos a un terreno totalmente distinto: cómo tunear un motor del cual sabemos poco y nada? Investigando a través de Internet di con un script bastante útil que me ayudó bastante a entender cómo funciona y de qué forma tunear opcache, así que apunten sus mouses a [este link para bajar dicho script](https://gist.github.com/ck-on/4959032/77fb6cfa1adc45358746ba072d1edd8ca7402347).

Muy básicamente, tendrán una pantalla que tendrá más o menos lo siguiente:
[singlepic id=23 w=320 h=240 float=center]

Sin embargo, la información más interesante para el tuneo, viene un poco más abajo (no se alcanza a ver en la foto), y son tres directivas claves para un tuneo efectivo. Estos son: 


  * Oom Restarts


  * Hash Restarts


  * Manual Restarts


Lo que representan cada uno de estos números, lo podemos encontrar en el commit [1fe43d07 de Dmitry Stogov](https://github.com/php/php-src/commit/1fe43d07989316d308e4dfd6989038fa09a51ff1), específicamente en el archivo `ext/opcache/ZendAccelerator.h`:
{% highlight c %}
unsigned long   oom_restarts;     /* number of restarts because of out of memory */
unsigned long   wasted_restarts;  /* number of restarts because of wasted memory */
unsigned long   hash_restarts;    /* number of restarts because of hash overflow */
unsigned long   manual_restarts;  /* number of restarts sheduled by opcache_reset() */ 
{% endhighlight %}

`oom_restart` apunta directamente a la directiva `opcache.memory_consumption`. Si este es muy bajo, tendremos reinicios frecuentes. 
`wasted_restarts` no aparece (al menos directamente) en el listado del script, pero se tendrán que fijar con un poco más de atención a "Current Wasted Percentage" y otros parecidos: el treshold se define bajo la directiva `opcache.max_wasted_percentage`. Predeterminadamente, se fija en un 5%. Una vez que este límite se alcanza, se producirá un restart por memoria "obsoleta". Si cambian frecuentemente archivos, es muy probable que este valor se incremente de forma regular. De lo contrario, tal como demuestra la imagen, casi nunca será necesario un restart.
`hash_restarts` tiene que ver directamente con la directiva `opcache.max_accelerated_files` o; tal como se ve en la imagen; en el segundo gráfico de barra vertical. Si este límite se alcanza se producirá un restart. Tiene que ver un poco con la cantidad de archivos que tendrán que dejar bajo cache. Con poco me refiero a que tendrán que revisar cuál es el mejor valor, en mi caso tengo los siguientes valores:  
Num Cached Scripts: 488  
Num Cached Keys: 625  
Max Cached Keys: 1979

Si revisamos nuevamente el gráfico, podemos ver que cierta parte de los keys ha sido marcada como obsoleta: a qué se debe no tengo idea pero siempre ocurre. Sacando cuentas podemos darnos cuenta que la diferencia del número de scripts obsoletos junto con la cantidad de scripts efectivamente guardados equivalen al número total de keys. Jugando con el valor ya mencionado deberían poder solucionar el problema si es que radica en que el número de archivos resulta demasiado alto. (Como podría ocurrir con por ejemplo Magento).

Finalmente, tenemos `manual_restarts`, que vendría siendo ni más ni menos las veces que nosotros mismos en nuestro propio código hemos llamado a la función opcache_reset() para reiniciar el motor en runtime. Nada que hacerle excepto quizás deshabilitar esa función en nuestro php.ini bajo la directiva `disable_functions`. 



## Otras directivas interesantes


En una máquina en producción, queremos la experiencia más rápida posible. Por lo tanto, se puede ahondar un poco más descartando ciertas partes que pueden resultar necesarias para cuando estamos desarrollando pero que no tiene relevancia alguna en el servidor de producción. Tales directivas son:  
`opcache.enable_cli`: Si queremos habilitar opcache para uso en línea de comando debemos habilitar esta opción.  
`opcache.save_comments`: Salva los PHPDoc en la cache. Total y absolutamente innecesario en un servidor en producción. Además por lo general tienden a ser bastante grandes.  
`opcache.load_comments`: Carga los PHPDoc desde la cache. Ver la explicación de arriba.  
`opcache.enable_file_override`: Si ocupan y/o abusan de funciones como is_file(), file_exists() o is_readable(), esta directiva les ayudará bastante ya que en vez de ir a pegar el pinchazo al disco duro, revisará en la misma memoria de opcache si el archivo existe. Sin embargo, hay que tener cuidado con la siguiente opción.  
`opcache.validate_timestamps`: Podemos señalarle a opcache si no queremos que verifique si el archivo ha sido actualizado. En sí, es una excelente optimización, sobretodo si el código casi no cambia, pero además puede tener otras consecuencias bastante difíciles de detectar y diagnosticar, por lo que mi recomendación es que si se está 100% seguro de que esta directiva no tiene consecuencias en su código, la habiliten. De otra forma, mejor dejarla desactivada.  



## Resumiendo


Las directivas más importantes a configurar son: 


  * opcache.enable


  * opcache.enable_cli


  * opcache.memory_consumption


  * opcache.max_wasted_percentage


  * opcache.max_accelerated_files


  * opcache.enable_file_override


  * opcache.validate_timestamps


Con la única que debemos tener real cuidado es con esta última, ya que puede devolvernos archivos antiguos u obsoletos si no renovamos nosotros mismos la cache con algún cambio y además si borramos o actualizamos algún archivo que comprobamos con is_readable(), is_file() o file_exists() más adelante, podemos encontrarnos con una respuesta (de parte de opcache) obsoleta ya que responderá desde su propia cache.
La lista completa de opciones para opcache las podemos encontrar en [el siguiente link](http://www.php.net/manual/en/opcache.configuration.php).

Espero les haya servido. Como opcache es bastante nuevo, cuesta encontrar información al respecto.
