---
comments: true
date: 2010-01-02 06:41:17+00:00
layout: post
slug: remover-quitar-contenido-desde-google
title: Remover (quitar) contenido desde Google
wordpress_id: 311
categories:
- Mundo Web
---

Existe una infinidad de información acerca de cómo mejorar el ranking en Google (u otros buscadores), acerca de cómo mejorar el SEO, miles de consejos -algunas bastante místicas por decir lo menos-, PERO casi no existe información sobre cómo darse de alta o quitar el contenido de los buscadores. 

Les cuento el pequeño problema: Hace ya bastante tiempo, tengo un servidor de desarrollo. Este servidor de desarrollo afortunadamente es bastante estable: funciona bien hace más de 1 año y sólo lo he reiniciado 2 veces a lo largo del año que recién pasó (Feliz año nuevo a todo esto): la primera fue debido a una costumbre windowsera y la segunda fue por una actualización importante del kernel. Sin embargo, lo realmente importante es que el downtime de este servidor es de menos de un 1% durante este año y eso fue sólo porque en el datacenter hubo una serie de malos entendidos que llevaron a que me desconectaron el servidor durante todo un fin de semana. Sino hubiese sido por este pequeño incidente, el downtime hubiese sido de menos de un 0.01% (Considerando ese 0.01% como el tiempo que se demora la máquina en reiniciar 2 veces durante el transcurso de un año). 
<!-- more -->
Lo único malo de esto, es que Google piensa que este servidor tan estable debería aparecer en su listado, mientras que yo pienso todo lo contrario: es un servidor **de desarrollo**, y por lo general el contenido que se genera en este servidor, finalmente termina copiándose al server final. 

A continuación las típicas preguntas: 
**¿Cómo es que llegaron a indexarme?** 
La verdad no tengo idea, lo más probable es que en alguna página tenía un link hacia el servidor de desarrollo lo cual llevó a Google a indexarla. Supongo que en el transcurso de 1 año eso es perfectamente factible: durante ese año lo más probable es que alguna vez haya puesto un link en alguna parte. 

**¿Cuál es el problema?**
Que Google termina castigando al usuario final por contenido duplicado en una máquina de desarrollo. (medio WTF pero es como Google funciona). Es así como me encontré con la desagradable sorpresa de que buscando por un término fácilmente relacionable con una empresa para la cual estaba desarrollando una página, el servidor oficial aparecía en primer lugar y el servidor de desarrollo aparecía en cuarto lugar. Incluso habían usuarios que se habían creado una cuenta en el servidor de desarrollo a pesar de los múltiples avisos ¬¬. 

**¿Cómo quitarlo?** 
Me puse a investigar cómo dar de alta este servidor el cual nunca había querido indexar siquiera. Fue así como me inscribí en Google Webmasters donde, [según una guía "oficial" de Google](http://googlewebmastercentral.blogspot.com/2007/04/requesting-removal-of-content-from-our.html) podría sacar la página de Google. Sin embargo, el link para sacarme que aparecía muy claro en el SS de la guía, ni siquiera me aparecía ¬¬. 
Quedándome claro que ese no era el método, seguí leyendo el mismo artículo, que finalmente explicaba otros métodos para que la gran G no me siguiera indexando, los cuales eran bastante más atinados y fáciles de seguir que hacer clicks en un mar de links. 



## Primer método: retornar un 404


Google, tal como otros buscadores, desindexa un sitio que aunque previamente funcionaba bien, ahora retorna un error 404. Lo único "malo" de esto es que se tiene que eliminar todo el sitio, lo cual obviamente no era la idea: mal que mal, se necesita seguir desarrollando en la página y eliminarlo definitivamente no es la mejor opción. 



## Segundo método: Bloquear mediante robots.txt


Si bien es cierto nunca he hablado del robots.txt supongo que los que visitan este blog algo sabrán del mismo, y si no saben, una muy breve explicación: robots.txt es uno de los primeros archivos a visitarse por los buscadores, y en ella se indican los directorios o archivos que están prohibidos o permitidos para la indexación.
Sin embargo, no hay que olvidar que puede llegar a ser un arma de doble filo: este archivo es público, por lo tanto, es mejor no colocar rutas confidenciales, ya que como investigación previa a cómo hackear una página, este es sin duda uno de los primeros archivos que visitaré, justamente para ver si acaso existe alguna ruta confidencial. Facebook de hecho, tiene cosas bien interesantes en su robots.txt. Además, aunque la gran mayoría de los buscadores respetan este archivo, no todos lo hacen (Aunque son los menos). 
En primera instancia, elegí este método ya que es el más rápido para bloquear sólo una sección. Se hace de la siguiente manera: 

<pre>User-agent: *
Disallow: /</pre>
Esto le dice a todos los buscadores (un asterisco en User-agent indica todos los buscadores) que no indexen nada que se encuentre desde el home en adelante. Es decir, si pusiera esto en mi blog, estaría denegando expresamente a Google, Bing, Yahoo y otros que me indexen el sitio completo, subcarpetas y archivos incluidos. 

Sin embargo, por esas cosas de la vida, uno quizás no querría denegar el acceso a todo el sitio, sino que sólo a una subcarpeta en específico a sólo Google. 
<pre>User-agent: GoogleBot
Disallow: /carpeta/
Disallow: /login.php
Disallow: /otra-carpeta/archivo.jpg
Disallow: /*.gif$</pre>

Con esa orden, estamos denegando el acceso al GoogleBot a la carpeta llamada "carpeta" y mediante la segunda línea, al archivo llamado login.php que se encuentra en la raíz. Con la tercera línea, estamos denegando el acceso a archivo.jpg dentro de la carpeta "otra-carpeta" y finalmente, mediante la cuarta línea, estamos denegando el acceso a todos los archivos cuyo nombre termina en .gif. 
Más información acerca de las expresiones regulares permitidos en robots.txt se puede encontrar [en este enlace](http://www.google.com/support/webmasters/bin/answer.py?hl=es&answer=156449). (Hacer click en "Crear de forma manual un archivo robots.txt" para ver esa información). 



## Tercer método: Prohibir mediante meta-tags


Este método es bastante bueno, pero tiene algunas desventajas bastante grandes también. Básicamente, se trata de incluir un metatag en cada HTML que le indica a los buscadores que no se indexe tal página, las combinaciones útiles son: 

{% highlight html %}<meta name="robots" content="noindex, follow">
<meta name="robots" content="index, nofollow">
<meta name="robots" content="noindex, nofollow">{% endhighlight %}

Esto es bastante fácil de entender: el name es siempre "robots" si se quiere tomar en cuenta todos los buscadores. ("googlebot" en minúsculas si se quiere especificar solamente Google). 
Content se refiere básicamente qué tipo de acciones son los que están prohibidos. Los posibles valores son siempre "index", "noindex", "follow" y "nofollow", pero obviamente no todas las combinaciones son posibles. 


  * _index_ o _noindex_ se refieren a si acaso se desea indexar el sitio: index para sí indexar, noindex para no indexar.


  * _follow_ o _nofollow_ se refieren a seguir los enlaces de esa página; de esa forma; si pongo un nofollow en hola.php y hola.php contiene links hacia otras páginas o secciones de mi página, mediante esa directiva no se seguirán.



**¿Las desventajas?**


  1. Si una página ya se encuentra indexada, lo más probable es que las imágenes también lo estén. Por lo tanto, si nuestra imagen se puede linkear directamente, seguirá indexada.


  2. ¿Ya les dije que tenía un servidor de desarrollo cierto? Imagínense qué pasaría si se me olvida sacar esta regla de `/includes/header.php` y queden todas las páginas sin indexarse. Para los lentos: se borrarían todas las páginas desde los buscadores del dominio final lo cual obviamente **no** es la idea. 





## [Opcional] Google Hell



Si desde hace tiempo conocen de manejo SEO y otros relacionados, seguramente conocerán el término Google Hell. Este término se inauguró hace ya bastante tiempo atrás cuando la página cae en una cierta categoría de sitio spam y Google decide (de un día a otro) no mostrar más tu página en el buscador, a no ser que busques por el dominio mismo de la página. Aunque es altamente recomendado para quitar contenido de Google, también puede haber el riesgo de que la misma decida también tirar al infierno la página que está en producción, con lo cual el cliente no estará muy contento.
Sin embargo, el apellido "Hell" viene porque una vez que un sitio cae en esta categoría, es casi imposible salir. En la gran mayoría de los casos, se opta por simplemente comprar otro dominio y empezar de nuevo: las respuestas por parte de Google siempre son automatizados y son del tipo: "Lo sentimos, pero el proceso de indexación es automatizado y no podemos hacer nada". Sin duda alguna, el cliente cuya empresa se llama "Lavaseco S.A." y tiene el dominio lavaseco.cl no estará muy contento de tener que cambiar su super hiper genial dominio por alguno que sea mucho más difícil de recordar. 
Obviamente es una excelente forma para que Google nunca más nos indexe, pero por cierto es la que NO debería aplicarse. 




## Conclusiones



Quitar una página desde un buscador puede ser un poco más difícil que tratar de incluirlo en alguno de ellos. Tampoco existe un método 100% fiable, a no ser que se caiga en el Google Hell. Sin embargo, no todo es imposible y aquí se expresaron al menos 3 métodos bastante fiables explicando sus ventajas y desventajas. 
Sin duda, es que creo que el segundo método es el mejor de todos: nos permite tener un control bastante detallado de qué contenido está prohibido de indexar y además también bloquea la indexación de imágenes y otro tipo de documentos que no tengan un HTML de por medio, tales como documentos PDF, .doc y otros, los que no son posibles de bloquear con el tercer método. 

Bueno, eso sería todo por hoy. Espero que haya sido claro en todo, y si no quedó muy claro, dejen su comentario más abajo :D
