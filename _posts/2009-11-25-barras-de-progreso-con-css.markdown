---
author: admin
comments: true
date: 2009-11-25 13:18:11+00:00
layout: post
slug: barras-de-progreso-con-css
title: Barras de Progreso con CSS
wordpress_id: 96
categories:
- CSS
---

Algunos días atrás [publiqué un post en CHW](http://www.chw.net/foro/webmasters-f91/285635-barras-de-progreso-con-css.html) que nadie pescó. Nadie lo pescará aquí tampoco ( xD ) pero por lo menos lo posteo xD

Encontré en Internet una forma bastante sencilla de crear barras de progreso sólo con CSS: es un truco bastante viejo pero encontré genial la idea que había detrás. Es demasiado simple pero a la vez ingenioso.

El truco en sí es bastante fácil:
<!-- more -->
[xhtml]
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="es" lang="es">
<head>
  <meta http-equiv="content-type" content="text/html; charset=utf-8" /> 
  <title>Porcentajes en CSS</title>
  <style type="text/css">
body{font-family:Arial;font-size:12px}
div.box{width:250px;border:1px solid #000;height:17px;margin:2px 0;background:#CCC}
div.bar{height:14px;line-height:11px;color:#FFF;text-align:right;padding:3px 0 0 0}
.bg-one{background:#000}
.bg-ima{background:url(progreso.png)}
  </style>
</head>
<body>
<h2>Porcentajes en CSS (Sin imagen)</h2>
  <div class="box"><div class="bar bg-one" style="width:40%">40%</div></div>
  <div class="box"><div class="bar bg-one" style="width:25%">25%</div></div>
  <div class="box"><div class="bar bg-one" style="width:93%">93%</div></div>
<h2>Porcentajes en CSS (Con imagen)</h2>
  <div class="box"><div class="bar bg-ima" style="width:86%">86%</div></div>
  <div class="box"><div class="bar bg-ima" style="width:3%">3%</div></div>
  <div class="box"><div class="bar bg-ima" style="width:50%">50%</div></div>
</body>
</html>[/xhtml]
Lo cual produce esta salida:


![](http://img52.imageshack.us/img52/649/20091119u4u0002.png)



Básicamente lo que se hace es declarar un contenedor de un ancho específico. Dentro de este declaramos otro con un ancho en porcentaje, que puede ser establecido fácilmente a través de PHP u otro método. Por último, le doy dos clases (aprovechando herencia y selectores de CSS2.1): la primera establece parámetros básicos y comunes a ambos, la segunda clase es simplemente el color de fondo. Esto se puede obviar si siempre se ocupa el mismo.

De esta manera es posible crear una bonita barra de progreso solamente con CSS.

Demo en vivo? Por supuesto: [Demo](http://unreal4u.com/test/barra/).

Fue el tip webmasterístico de hoy.

Saludos !!
