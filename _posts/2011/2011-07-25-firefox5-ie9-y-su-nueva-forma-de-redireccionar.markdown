---
comments: true
date: 2011-07-25 03:45:26+00:00
layout: post
slug: firefox5-ie9-y-su-nueva-forma-de-redireccionar
title: Firefox5 + IE9 y su nueva forma de redireccionar
wordpress_id: 557
categories:
- Mundo Web
- PHP
---

Yo siempre he opinado, como desarrollador, que todos los nuevos cambios en un programa son bienvenidos, sobretodo si son buenos. Pero ocurre también que a veces estos nuevos cambios terminan siendo contra-productivos, ya que requiere una gran inversión de tiempo en acomodar lo que antes funcionaba bien y que ahora funciona mal o derechamente no funciona. A veces sólo es necesario una línea de código, pero llegar a esa conclusión puede ser una gran pérdida de tiempo.

En ese sentido, Firefox en su última versión del popular navegador, cambió la forma de tratar las redirecciones 302 y que pueden producir más de algún dolor de cabeza teniendo en cuenta lo poco difundido de esta nueva característica.
<!-- more -->

El problema surgió cuando uno de los vendedores me dijo: "No funciona la página". Debo reconocer que al principio lo ignoré debido a que los vendedores siempre dicen que no funciona la página cuando el mensaje de error desplegado al mismo dice muy claramente: "RUT inválido, por favor intente nuevamente".
Lo único malo es que esta vez, más y más vendedores me empezaron a decir lo mismo y eventualmente le empezó a pasar a los gerentes (A los dos días de hecho). Ya a estas alturas, debía haber un denominador común que hiciera que este error se produjera, así que me puse a investigar.

Una de las primeras cosas que me llamó la atención, es que en los logs empezó a aparecer el siguiente denominador común: Firefox/5.0.1. Me encantó el hecho que Mozilla actualice automáticamente sin preguntarle al usuario si desea hacerlo, pero en estos momentos estaba odiando tal característica xD.

Con eso en mente, tuve que bajar el mentado navegador (yo todavía estoy en 3.6.19 y por el momento no pienso actualizar) y empezar a probar. Finalmente logré aislar el "bug" que estaba relacionado a "algo" que tenía que ver con las sesiones y las redirecciones 302. Cuento corto: hice cientos de tests durante 1 semana ([Live HTTP Headers](https://addons.mozilla.org/en-US/firefox/addon/live-http-headers/) jamás había sido tan útil), buscaba en Google por todos los términos que podrían llegar a ser y no logré encontrar la pifia. Seguía pasando en el sistema en producción pero no podía reproducir el error en menos de 10 líneas, necesario para poder reportar el problema en bugzilla.

Eso hasta que un buen día, [POR FIN APARECIÓ UN POST](http://www.inventpartners.com/content/firefox-5-redirect-bug-pragma-no-cache-bug) que contenía exactamente el mismo problema y lo mejor de todo: su solución, y aquí viene lo mejor: Internet Explorer 9 tiene exactamente el mismo comportamiento!

El problema está en que la [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616.html) #sección 10 (la que regula los códigos de estatus HTTP) dice que bajo la redirección 302 el resultado se puede o no dejar en caché. Bueno, Firefox ANTES de la versión 5, NO cacheaba la redirección 302 y ahora SÍ lo hace (tratándolo como si fuera una redirección 301), por lo tanto; y después de esta magna explicación; tenemos que la solución a este problema es simplemente enviar **Pragma:no-cache** y **Cache-Control: no-cache** antes de enviar las cabeceras de Location para que estos dos navegadores NO almacenen la redirección: 

{% highlight php %}
<?php 

$donde = 'http://blog.unreal4u.com/';
$tipo = 302;
header('Pragma: no-cache');
header('Cache-Control: no-cache');
header('Location: '.$donde,TRUE,$tipo);
{% endhighlight %}

Más información en la fuente: 
Fuente: [InventPartners](http://www.inventpartners.com/content/firefox-5-redirect-bug-pragma-no-cache-bug)
