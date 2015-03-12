---
author: admin
comments: true
date: 2010-02-01 13:45:27+00:00
layout: post
slug: script-para-reducir-peso-de-los-css
title: Script para reducir peso de los CSS
wordpress_id: 335
categories:
- CSS
- Mundo Web
---

En la Web hay una cosa que yo diría es bastante fundamental y nadie le presta mucha atención: el peso de los archivos. Mantener un peso bajo puede ahorrar mucho ancho de banda, cosa necesaria en cualquier sitio de alto tráfico, ya que además de acelerar la descarga también hace que al cliente o usuario final la página le aparezca más rápido.

Aunque me gustaría ahondar mucho más en el tema, por el momento sólo presentaré una pequeña función que puede ser bastante útil a la hora de reducir el peso de CSS gigantes, sin embargo, la verdadera ganancia puede no estar aquí, sino que en la compresión que se debe activar en el servidor, o bien ocupando `ob_start()` de PHP, pero como ya dije: eso ya es harina de otro costal y al ser más amplio preferiría dejarlo para otro post.

Sin embargo, a continuación les presento el pequeño script. Vamos... haz click en leer más :D
<!-- more -->


## Entendiendo CSS


CSS es un invento genial. Mediante el mismo, y combinándolo inteligentemente con HTML, podemos controlar cualquier aspecto de nuestra página. Es, como casi todo en la Web, fácilmente interpretable y también pesa poco: aplicando sólo una hoja de estilos, podemos controlar todo el aspecto gráfico de la página. Sin embargo, y casi de forma tan variable como lo sea el gusto del programador, es también posible llenar un CSS demasiado con comentarios, espacios demás y hasta caracteres demás. De muestra, un botón:

[css]html {
  background-color:#113355;
}
/* Algunos estilos */
p.hola, a:link {
  margin-left: 2px;
  margin-right: 2px;
  margin-top: 43px;
  margin-bottom: 43px;
}
/* El contenedor General de la página */
#wrapper {
  padding: 0px 0px 0px 0px;
  color: #5533AA;
}[/css]

Esto puede reducirse bastante, para que no quede tan críptico, dejé cada estilo en una línea:

[css]html{background:#135}
p.hola,a:link{margin:43px 2px}
#wrapper{padding:0;color:#53A}[/css]

Explicaré algunas reglas básicas:



	
  1. Los comentarios no sirven: si quieren un peso chico, eliminen todo comentario.

	
  2. margin, padding y algunos otros tienen un formato reducido. Sin embargo, es algo que se explicará en otro artículo

	
  3. El punto y coma final de cada declaración no es necesario. Elimínenlo.

	
  4. Los colores se pueden reducir, siempre y cuando los tres pares RGB sean iguales. Es útil.

	
  5. A CSS no le gustan mucho los espacios. Se pueden eliminar muchos.


Ahora bien, en el pequeño ejemplo de arriba, que son exactamente iguales, el peso del archivo se redujo de 263 bytes a 84 bytes. Ahora se pueden imaginar la misma situación, pero en la vida real donde el CSS no pesa algunos bytes, sino que bastantes kibibytes. (Eso serían bytes multiplicados por 1024). De todas formas, eliminando los saltos de línea, se puede dejar en 81 bytes, pero queda de difícil lectura.

Sin embargo, el tema que nos convoca hoy no hace algo tan avanzado, sino que simplemente elimina los puntos 1, 3 y la gran mayoría del punto 5. Aunque todavía está lejos de ser perfecto aunque hasta el momento he detectado que hace bien su pega.

[php]<?php
  function compress($buffer) {
    $buffer = preg_replace('!/\*[^*]*\*+([^/][^*]*\*+)*/!', '', $buffer);
    $buffer = str_replace(array("\r\n", "\r", "\n", "\t", '  ', '    ', '    '), '', $buffer);
    $buffer = str_replace(array(';}','; }',': ',' {','; '),array("}\n","}\n",':','{',';'),$buffer);
    return $buffer;
  }

header('Content-type: text/css');
echo compress(file_get_contents('highslide.css'));
?>
[/php]

En el ejemplo de arriba tomé el CSS de highslide, un script hecho en JavaScript que realiza el típico efecto zoom de una foto. El CSS de este script pesa 21.274 bytes, un poco más de 21KiB. Aunque el CSS en sí está bastante bien hecho, no está optimizado para la transferencia Web, tiene muchos espacios vacíos inútiles y también algunos comentarios, todo separado por un enter que parece que se le hubiera quedado pegado al programador.
Después de aplicar el script, quedó pesando 15.695 bytes, lo cual significa una reducción de 6KiB. Puede no sonar tanto, pero en cuanto a porcentaje estamos hablando de una **reducción de peso de un poco más de un 26%**, ¡sólo eliminando espacios innecesarios! Además, hay que tener en cuenta que se está agregando un enter después de cada declaración: al eliminar estos enter que en estricto rigor también están demás, obtenemos un archivo de 15.511 bytes, lo cual en porcentaje significa un poco más del 27%.

Ahora bien, la desventaja de este esquema es que cada vez que queramos modificar el CSS, tendremos que realizar el paso de conversión y luego escribir el archivo, algo bastante impráctico sobretodo si se está desarrollando continuamente. Para solucionar esto, se puede ampliar un poco la función de arriba, combinando CSS y PHP en un solo archivo.

[php]
<?php
  header('Content-type: text/css');
  ob_start("comprimir");
  function comprimir($buffer) {
    $buffer = preg_replace('!/\*[^*]*\*+([^/][^*]*\*+)*/!', '', $buffer);
    $buffer = str_replace(array("\r\n", "\r", "\n", "\t", '  ', '    ', '    '), '', $buffer);
    $buffer = str_replace(array(';}','; }',': ',' {','; '),array("}","}",':','{',';'),$buffer);
    return $buffer;
  }
?>
body{background:#1C762C none repeat scroll 0 0;color:#000;font-family:Arial;margin:0;padding:0}
img{border:none}
marquee{background:#FFF;height:168px;padding:0;text-align:center;width:130px}
a{color:#111;text-decoration:none}
ul{text-align:left}
<?php
  ob_end_flush();
?>
[/php]

Pero, esto tiene un pequeño detalle: hay que guardarlo como un archivo .php, debido a que por lo general la configuración no permite establecer un .css como un script ejecutable de PHP. Lo que en el fondo se hace es generar un archivo PHP que genera CSS gracias a `ob_start()` que, con una explicación muy a la lijera, toma la salida y le aplica la función que definimos antes de enviarlo al explorador. Finalmente, debemos indicarle expresamente dónde termina el archivo.

De esta forma, se consigue comprimir todo el contenido. El único cambio que habría que hacer en HTML sería el siguiente:

[xhtml]
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" lang="es">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
    <base href="http://looneyfactory.com/"/>
    <link rel="stylesheet" type="text/css" href="http://yui.yahooapis.com/3.0.0/build/cssreset/reset-min.css"/>
    <link href="estilo_general.css.php?v=1.0" rel="stylesheet" type="text/css"/>
  </head>
  <body>
<!-- el contenido del cuerpo -->
  </body>
</html>
[/xhtml]

Aquí pasan varias cosas interesantes. Primero llamamos a una aplicación de Yahoo, que es la encargada de resetear todos los valores predeterminados que puedan tener los navegadores. (¿Se acuerdan del artículo sobre [reseteo de CSS](http://blog.unreal4u.com/2010/01/resetear-css/)?). Después de eso, llamamos a nuestro estilo, que en realidad es un script hecho en PHP. Además, le pasamos la cadena v=1.0 y esto tiene una razón bastante específica: Hoy en día, no es extraño encontrar que algunos sitios (como por ejemplo: Yahoo, Google y en general cualquier empresa grande que tenga empleados que algo cachan sobre la Web) tengan habilitada una caché que dura menos que 1 año. El CSS de Yahoo por ejemplo, expira en 10 años más. Sin embargo, no tiene ni una gracia que se tenga que cambiar el nombre del archivo cada vez que hay un pequeño cambio en el CSS. Es por eso, que se le agrega al más puro estilo $_GET de PHP un identificador único con lo cual el navegador sub-entiende que ese nombre de archivo (completo, incluido los parámetros) lo debe guardar en caché. 



## Ahora entendí un poco más... Y todo junto cómo sería?


Afortunadamente para ustedes, cuando empezé a escribir este post comencé también a trabajar en una class que me hiciera esto de forma automática. Sin embargo, y como siempre, aunque el requerimiento era bastante simple, terminé por meterle mucho más contenido que lo que originalmente iba a hacer. 
Mayor información, tal como link hacia la class y qué cosas hace, las pueden encontrar [en este post](http://blog.unreal4u.com/2010/01/nueva-class-publicada-csstacker/). 
