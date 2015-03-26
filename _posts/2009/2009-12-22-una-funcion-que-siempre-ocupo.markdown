---
comments: true
date: 2009-12-22 03:01:41+00:00
layout: post
slug: una-funcion-que-siempre-ocupo
title: Una función que siempre ocupo
wordpress_id: 238
categories:
- Mundo Web
- PHP
---

Para todo CMS existen ciertas cosas que uno siempre utiliza. De esta forma, es posible construir una cosa una sola vez con la intención de reutilizarlo después. 

La reutilización de código fuente es lo mejor que se pudo haber inventado en la vida: no sólo nos permite utilizar ese viejo código nuevamente, pero también nos permite no equivocarnos, mejorar siempre el mismo código y de esta forma, facilitar la actualización y asegurar de mejor manera nuestras páginas. ¿Lo único malo? Siempre hay que saber ocuparlo. 

Esta función en específico hace una sola cosa: uno le pasa una imagen (guardada localmente), y construye con eso un XHTML válido para la W3C. Por supuesto que esto no es la única cosa que hace, pero para mejor entendimiento, mejor vean el código después del salto. 
<!-- more -->

Lo prometido es deuda: 

{% highlight php %}/*
  string c_img(string,[string],[string],[int,int]); // Retorna el código HTML de la imagen.
  in:  $ruta   = string      = Ruta de la imagen.
       $alt    = [OP] string = Texto alternativo
       $class  = [OP] string = Clase alternativa
       $width  = [OP] int    = ancho de la imagen. Opcional, calcula solo si no se entrega esto.
       $height = [OP] int    = alto de la imagen. Opcional, calcula solo si no se entrega esto.
  out: string                = texto según parámetros establecidos y validado en W3C.

*/
function c_img($ruta='',$alt='',$class='',$width=0,$height=0) {
  $salida = '<img src="'.$ruta.'" ';
  if (empty($alt)) $alt = ' ';
  if(file_exists($ruta) AND $width==0 AND $height==0) {
    $info_img = getimagesize($ruta);
    $salida .= $info_img[3];
  }
  else $salida .= 'width="'.$width.'" height="'.$height.'"';
  if ($class != '') $salida .= ' class="'.$class.'"';
  return $salida.' alt= "'.$alt.' " title="'.$alt.'" />';
}
{% endhighlight %}

¿Y cómo cresta ocupo esta cosa? Fácil mi estimado: 
{% highlight php %}
// Forma 1: Indicando sólo una ruta
echo c_img('i/imagen_top.jpg');
// <img src="i/imagen_top.jpg" width="985" height="120" alt= " " title=" " />

// Forma 2: Indicando ruta y texto alternativo
echo c_img('i/imagen_top.jpg','Cabecera');
// <img src="i/imagen_top.jpg" width="985" height="120" alt= "Cabecera" title="Cabecera" />

// Forma 3: Indicando ruta, texto alternativo y class
echo c_img('i/imagen_top.jpg','Cabecera','asdf');
// <img src="i/imagen_top.jpg" width="985" height="120" class="asdf" alt= "Cabecera" title="Cabecera" />

// Forma 4: Indicando ruta, texto alternativo, class y dimensiones
echo c_img('i/imagen_top.jpg','Cabecera','asdf',900,800);
// <img src="i/imagen_top.jpg" width="900" height="800" class="asdf" alt= "Cabecera" title="Cabecera" />
{% endhighlight %}

Sólo tres consideraciones: 


  1. Este código no realiza muchas validaciones


  2. **No se pueden incluir imágenes externas** (A menos que se de el ancho y alto explícitamente)


  3. Este código fue hecho hace por lo menos 2 años atrás


A pesar de eso, este código me ha acompañado por ya dos años y pronto será su jubilación. Había pensado en hacer una class que mandara un mail con todas las cabeceras anti-spam de hoy en día y también había pensado en hacer una class que hiciera las operaciones más simples del planeta sobre imágenes (Ej.: redimensionar una imagen y ponerle un borde, quizás hasta un watermark), pero en vez de eso, prefiero hacer algo que siempre ocuparé: una class que me permita insertar elementos XHTML válidos. Tan simple como eso pero a la vez muy útil. 

Pequeña nota antes del cierre: este código tiene **licencia BSD**, así que pueden hacer lo que les plazca: si les parece, pueden agregar fácilmente un onFocus o un onClick, para que así también esté la posibilidad de incluir código JavaScript en las imágenes. 

Saludos !!
