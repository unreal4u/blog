---
comments: true
date: 2010-09-24 17:59:22+00:00
layout: post
slug: solucion-a-problemas-comunes-en-tchile
title: Solución a problemas comunes en tchile
wordpress_id: 448
categories:
- Mundo Web
- PHP
---

Muchas veces, cuando algún cliente me contacta, ya se ha comprado  el dominio y el hosting (Lo cual es una excusa más para no seguir  perdiendo plata y que esté la página arriba rápido). Y dentro del universo de hostings, [tchile](http://www.tchile.com/) es tal vez el hosting más popular en Chile, casi todos mis clientes tienen su página hosteada ahí.
Sin embargo, no todo es color de rosas. He tenido bastantes problemas en  el pasado por páginas subidas ahí y también -como no- hasta Wordpress  con el theme predeterminado simplemente me mostraba una página en blanco  cuando intentaba editar o crear un nuevo post.

Por lo tanto, opté por lo sano y me puse a investigar todas aquellas  cosas que es bueno desactivar cuando tenemos que montar alguna página en  este hosting.
<!-- more -->
Primero debo partir subrayando el hecho de que tchile no es un hosting malo, por el precio es bastante bueno y el soporte responde, aunque se demora su buen resto. En general tienen buen uptime y en el transcurso de los últimos 6 meses he podido contabilizar unas 3 caídas graves. Algo rescatable es que, si bien es cierto el servicio estuvo caído mayormente el 19 de febrero, el 27 de febrero (y posterior) estuvo siempre arriba.
Sin embargo, tchile -como cualquier otro hosting en Chile- comparte una misma regla: atrévanse a crecer mucho en visitas y cualquier hosting chileno dará jugo. (Donde muchas visitas está determinado por 25.000+ visitas únicas diarias). Ante esa eventualidad, es mil veces mejor hostearse en Estados Unidos, donde los precios, atención y uptime son muy superiores a lo que les puede ofrecer cualquier hosting en Chile.



## Primer gran obstáculo: caché


Me ocurrió una vez que quise instalar osCommerce que subía el  index... lo visitaba una vez, actualizaba y veía el index de hace 1 mes,  lo mismo con las demás páginas estáticas. Bueno, básicamente lo que  pasaba era que Apache al ver que el documento no se había modificado de  la última vez que se visitaba la página, resolvía entregar la que tenía  en caché... el único problema es que no había actualizado la caché con  lo cual entregaba la antigua. Grave problema.

Para resolver esto, apliqué un pequeño y sucio hack, que es mandar la  cabecera con modificación en el pasado. De esta forma, le estoy  diciendo a Apache que cualquier request me la tiene que procesar y no  devolver desde la caché. Cómo se hace esto? De la siguiente forma:
{% highlight php %}
<?php
header('Expires: Tue, 03 Jul 2001 06:00:00 GMT');
header('Last-Modified: '.gmdate('D, d M Y H:i:s').' GMT');
header('Cache-Control: no-store, no-cache, must-revalidate');
header('Cache-Control: post-check=0, pre-check=0');
header('Pragma: no-cache');
{% endhighlight %}

Queda bastante claro no?


## Segundo obstáculo: filtro anti-spam


Esta me costó bastante ya que fue difícil de detectar y sobretodo de  reproducir. Sucede que tchile carga predeterminadamente un archivo .php  que no sé qué hace pero tengo entendido que filtra un poco el spam. Este  archivo se carga mediante la directiva `auto_prepend_file` de PHP y afortunadamente se puede sobre-escribir mediante .htaccess (Por  lo menos en los servidores que me han tocado se puede).
Para sobre-escribir este filtro de spam, basta colocar en su `.htaccess`:
{% highlight bash %}
php_value auto_prepend_file none
{% endhighlight %}

Con esas dos cosas (por mientras) estarán más que bien en tchile. Si tienen otros tips, no olviden compartirlas en los comentarios!
