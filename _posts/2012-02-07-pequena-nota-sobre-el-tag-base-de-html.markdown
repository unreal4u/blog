---
comments: true
date: 2012-02-07 21:37:19+00:00
layout: post
slug: pequena-nota-sobre-el-tag-base-de-html
title: Pequeña nota sobre el tag base de HTML
wordpress_id: 591
categories:
- Javascript/jQuery
- Mundo Web
---

Cuando hoy ya estábamos a punto de lanzar un nuevo sitio web, empezamos a hacer los ajustes finales de ésta. Sin embargo, cuando estábamos probando los últimos ajustes, una gran sorpresa apareció: mientras que el sitio funcionaba bien en Safari, Firefox y demases, en Internet Explorer no había ningún estilo, ningún Javascript asociado y ninguna imagen. Investigando, nos dimos cuenta de que el problema estaba en la declaración `<base>`, ya que sólo al poner la URI completa (con http y todo incluido), Internet Explorer la tomaba en cuenta. Irónicamente, esta vez es Internet Explorer el que cumple con la norma, ya que el estándar de HTML 4.01 dicta que [el valor a ponerse en esta directiva debe corresponder a una URI completa y absoluta](http://december.com/html/4/element/base.html) tal como se denota en la [RFC2396](http://www.ietf.org/rfc/rfc2396.txt).

Así que ya saben, si no funciona:  
`<base href="/hola/chao/" />`  
Deben poner:  
`<base href="http://unreal4u.com/hola/chao/" />`

Para que Internet Explorer la tome en cuenta.
