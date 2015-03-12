---
author: admin
comments: true
date: 2010-08-29 14:18:09+00:00
layout: post
slug: abrir-todos-los-links-externos-en-nueva-ventana
title: Abrir todos los links externos en nueva ventana
wordpress_id: 429
categories:
- Javascript/jQuery
---

Hace un tiempo atrás, necesitaba que todos los links externos fueran abiertos en una nueva ventana. Primero hice una función en PHP que cuando construía el link, detectaba si el mismo era interno o externo y si era externo, lo dejaba con el famoso target="_BLANK". Sin embargo, al poco tiempo después me di cuenta que ésta no era la solución ideal ya que fallaba en dos aspectos fundamentales: Si el link era construido mediante TinyMCE (o sea, por el usuario), no pasaba por el "constructor" de links. Por otro lado, el código generado no era compatible con la W3C en el modo estricto (En este modo, se elimina el atributo "target").
Así que me puse a investigar qué necesitaba para que cumpliera con los dos propósitos... ¿La respuesta? JavaScript, y si podía utilizar el poder de selección de la librería jQuery, aún mejor. El resultado fue que todos los links externos de la página, independiente de quién las haya creado, se abren en otra ventana y lo mejor de todo es que el código de la página es compatible con la W3C.

[javascript]$(document).ready(function(){$('a:not([href^="http://blog.unreal4u.com/"])').attr("target","_BLANK");});[/javascript]

Obviamente tiene que reemplazar _http://blog.unreal4u.com/_ con la ruta de su aplicación.
