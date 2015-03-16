---
comments: true
date: 2011-07-17 06:26:47+00:00
layout: post
slug: manito-de-gato-al-blog
title: Manito de gato al blog
wordpress_id: 555
categories:
- Javascript/jQuery
- Pensamientos Personales
---

Hace mucho tiempo que quería hacer algunas cosas nuevas en el blog, entre las cuales estaban:


  * Una actualización general: Cuando instalé el blog WordPress iba en la versión 2.7.9 y nunca más la actualicé.


  * Actualización de Plugins: Aunque no me gusta ocupar plugins, sí tenía algunos que estaban sumamente desactualizados.


  * Incorporar botones sociales: Hoy en día, casi infaltables si uno quiere hacer las cosas de forma rápida.


  * Cambiar el blog de máquina: Antes estaba en una máquina casi exclusiva, ahora, estoy en el servidor web que sirve todas las páginas, pero podré administrar mejor.


Así que buenas noticias: actualicé WordPress a la última versión (3.2.1), actualicé los plugins a la última versión y también incorporé los botones [+1 de Google](http://www.google.com/webmasters/+1/button/), [Like de Facebook](http://developers.facebook.com/docs/reference/plugins/like/) y el [Tweet Button de Twitter](http://twitter.com/about/resources/tweetbutton#url-fields). Y para dejar algo de aporte, revise el código después del salto.
<!-- more -->

La verdad, pensé que incorporar estos botones iba a ser un poco más complicado, pero resultó bastante más fácil de lo que pensé. Lo primero que tenemos que hacer es visitar cada una de las páginas arriba mencionadas y seleccionar el estilo adecuado a nuestro gusto. Una vez hecho eso, procedemos a su instalación.



## Botón +1 de Google


En header.php de nuestro theme, justo antes del cierre de la cabecera, agregamos el siguiente código:

{% highlight html %}
<script type="text/javascript" src="https://apis.google.com/js/plusone.js"></script>
{% endhighlight %}

Luego, para cuando queramos agregar el botón en algún lado, digamos single.php o index.php, sólo basta con: 

{% highlight html %}
<g:plusone size="medium" href="<?php the_permalink(); ?>"></g:plusone>
{% endhighlight %}

size y href son obviamente configurables. Revisen también la página de Google, porque al incluir el primer javascript, es posible setear algunas configuraciones de inmediato, como por ejemplo el idioma.



## Like de Facebook


Este botón es quizás el más difícil de estilar debido a que trabaja directamente con HTML en vez de CSS para eso. De todas formas, es bastante fácil, basta con agregar este código:

{% highlight html %}
<div id="fb-root"></div><script src="http://connect.facebook.net/en_US/all.js#appId=148806621860058&amp;xfbml=1"></script><fb:like href="<?php the_permalink(); ?>" send="false" layout="button_count" width="80" show_faces="false" font=""></fb:like>
{% endhighlight %}

Pueden revisar las opciones que ofrece el botón en el mismo link que cité arriba, de las 3 opciones, es la mejor documentada.



## Tweet Button de Twitter


De las tres, es la que peor documentación tiene, sin embargo, su inclusión sigue siendo, tal como los demás botones, casi un chiste de lo fácil que es.
Lo primero que tenemos que hacer es incluir el siguiente código en nuestra cabecera:

{% highlight html %}
<script src="http://platform.twitter.com/widgets.js" type="text/javascript"></script>
{% endhighlight %}

Y después, cuando queramos imprimir el botón:

{% highlight html %}
<a href="http://twitter.com/share" class="twitter-share-button" count="horizontal" lang="es" text="<?php the_title(); ?>" url="<?php the_permalink(); ?>">Tweet</a>
{% endhighlight %}

Y con eso, quedará inmediatamente insertado en nuestro blog.

Espero que les haya servido de ayuda para además, revitalizar un poco el blog.
