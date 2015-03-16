---
comments: true
date: 2010-09-29 03:07:09+00:00
layout: post
slug: aprendiendo-a-manejar-svn-de-manera-avanzada
title: Aprendiendo a manejar svn de manera avanzada
wordpress_id: 450
categories:
- Linux
- Pensamientos Personales
---

Antes que todo, quisiera pedirle disculpas a un cierto amigo que  posteó un comentario, akismet lo tomó como spam y sin querer queriendo,  me confundí de botón y lo borré de la base de datos en vez de aprobarlo.  A la rápida, alcancé a leer algo de https y redirección (también tenía  un link hacia un más información en X sitio), pero no me fijé a qué post  correspondía. Así que si no es mucha molestia, se podría postear de  nuevo? :D Prometo fijarme mejor esta vez.

En fin, el post de hoy se trata de nada más ni nada menos que svn. Odiado por muchos ([Basta ver este video de Linus Torvalds donde básicamente dice que svn está mal desde su base](http://www.youtube.com/watch?v=4XpnKHJAok8))  y amado por otro puñado de desarrolladores, es bastante fácil de ocupar  y tiene opciones bien poderosas una vez que se aprende a ocupar bien. Y  para eso es este post :)
<!-- more -->
Había ocupado Subversion hace mucho tiempo atrás y en ese tiempo me  pareció bastante adecuado para un proyecto más o menos grande que  teníamos que hacer. Hoy en día, es parte fundamental de mi "kit" de  desarrollo y sin ella la actualización de los distintos sitios,  componentes y otros sería un dolor de cabeza grandísimo.
Sin embargo, cuando lo ocupé en ese entonces, apenas hacíamos commit y  update. No tenía ni la menor idea de que se podían ignorar ciertos  archivos y que hasta se podía poner automáticamente la licencia en cada  uno de los archivos, o el número de la última revisión o hasta la fecha y  autor del último cambio a ese archivo, herramientas muy poderosas todas  para llevar un buen control sobre un proyecto grande.

Lo primero es lo primero, si no está instalado, en CentOS hacemos un simple `yum install subversion`, el cual instalará el **cliente**. En esta guía **NO** enseñaré cómo configurar un nuevo repositorio ni todas las demás opciones, eso ya está bastante cubierto por otras páginas.


## Ignorar archivos


Una de las funciones más útiles es sin duda el poder ignorar  archivos. Esto se puede hacer mediante 2 (ó 3, depende del punto de  vista) maneras.  
La primera de ellas, y la más estática por así decirlo, es el archivo de configuración que se puede encontrar en: `~/.subversion/config`  
¡Este archivo de configuración tiene una particularidad! No pueden  quedar espacios en blanco al principio de una línea. Estuve cerca de 10  minutos revisando el por qué me salía el error  
`svn: /.subversion/config:71: Option expected`  
y era porque en esa línea tenía un espacio sobrante al principio.  
Al grano: Existe en este archivo una directiva llamada `global-ignores` que nos permite dejar ciertos archivos ignorados. Sin embargo, la gran  desventaja de este método es que cada cliente tendrá que hacer lo mismo.  ¿Se imaginan el enredo si a uno se le olvida y agrega su configuración  personalizada sobre-escribiendo la de todos sus compañeros?  
Para evitar esto, es que existe un segundo método, que se subdivide en  dos: la primera es directo en la línea de comandos, la segunda es  mediante un archivo y que también se hace en la línea de comandos (y que  parece funcionar mucho mejor).  
Debido a que el segundo método me funcionó mejor, sólo veré ese.

Ejemplifiquemos:
<pre>
.htaccess -> deseamos ignorarlo
cache/*.log -> deseamos ignorarlo, la carpeta "cache" sí queremos incluirla
includes/config.php -> deseamos ignorarlo
test/* -> deseamos ignorarlo, y también queremos excluir la carpeta "test"
</pre>

Aquí es donde viene lo interesante: tendré que ir carpeta por carpeta  ingresando los distintos ignores de cada una, ya que no puedo  simplemente hacer una lista general. Es bastante incómodo y en el svn  book esto no se explica, pero para eso está este post.

Para esto, voy a crear un archivo llamado .ignore en la raíz del svn, el que también se ignorará, cuyo contenido será:

    
    .htaccess
    .ignore
    test
    


Luego, creo también un archivo .ignore en la carpeta cache/ y la carpeta includes/, que va a tener (respectivamente)

    
    .ignore
    *.log
    

En la carpeta includes/ : 

    
    .ignore
    config.php
    


Y luego ejecuto en cada carpeta (raíz, cache/ e includes/) lo siguiente:

    
    svn propset svn:ignore -F .ignore .


Listo! Ahora podemos hacer un `svn status` y no nos saldrán los archivos ignorados.


## Propiedades especiales


Sin embargo, esto no es todo lo que se puede hacer. Otra de las  propiedades más útiles que encuentro de svn, es la capacidad de poner  ciertos atributos o propiedades sobre los archivos. Y aunque escribir  sobre ellas requeriría una guía bastante extensa (más aún de la que  tengo), me limitaré sólo a los keywords.

Los keywords no son nada más que pedazos de información que va en  cada archivo. De esta manera, es posible por ejemplo saber cuándo fue la  última modificación de ese archivo, quién lo hizo y bajo qué revisión  se sometió ese cambio.

Básicamente, en cada archivo .php de nuestro sistema tendremos:

{% highlight php %}
<php
/*
$Rev$
$Date$
$Author$
*/

$mi_php = 'hola mundo';
echo $mi_php;
{% endhighlight %}

Luego, en la consola, escribimos:

    
    svn propset svn:keywords "Date Author Rev" *.php
    # creo que esto también debería ser válido para no ir carpeta por carpeta:
    svn --recursive propset svn:keywords "Date Author Rev" *.php
    


Ahora, cada vez que se modifica el archivo, el resultado del .php será el siguiente:

{% highlight php %}
<?php
/*
$Rev: 40 $
$Date: 2010-09-23 21:49:40 -0400 (Thu, 23 Sep 2010) $
$Author: unreal4u $
*/

$mi_php = 'hola mundo';
echo $mi_php;
{% endhighlight %}

Y lo mejor de todo es que se va a ir actualizando a medida que se modifique, todo de manera automática.
Por supuesto que estos no son los únicos keywords, pueden [consultar el libro de svn](http://svnbook.red-bean.com/en/1.1/ch07s02.html) para mayor información.

Eso sería todo por hoy, la verdad no quise que saliera un artículo  tan extenso, así que es probable que más adelante escriba nuevamente  otro artículo con mayor claridad y más ejemplos.
