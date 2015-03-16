---
comments: true
date: 2011-01-26 01:32:54+00:00
layout: post
slug: phpdocumentor-haciendo-una-buena-documentacion-excelente
title: 'phpDocumentor: Haciendo una buena documentación excelente'
wordpress_id: 484
categories:
- Pensamientos Personales
- PHP
---

No fue sino hasta el otro día que le encontré un propósito y una utilidad a la documentación bien hecha. 

Todo surgió debido a que andaba buscando un método determinado que había escrito una vez hace 5 meses atrás y que podría haber estado dentro de un par de classes o bien dentro de un par de archivos de distintas funciones: en resumen, no tenía idea de cómo se llamaba ni menos cómo se ocupaba. Incluso ni siquiera estaba seguro de si efectivamente podría hacer lo que tenía pensado ya que la función que había escrito hacía algo muy parecido, pero podría ser que no hiciera lo mismo. Traté de ubicarla mediante diversos términos [con este script](http://blog.unreal4u.com/2011/01/buscando-en-linux-hecho-facil/) pero no hubo caso. Ya casi dándome por vencido se me ocurrió la idea de que en realidad la documentación del programa estaba mal hecha y con múltiples formatos distintos: había que unificar todo.
<!-- more -->
Fue entonces que me puse a buscar estándares de documentación donde, al parecer, Java es definitivamente la número 1 con su JavaDoc, esquema que fue copiado por PHPDocumentor (y que convenientemente pasó a llamarse PHPDoc). Básicamente hay que seguir algunas reglas, las cuales son: 

{% highlight php %}
<?php 

/**
 * Un comentario PHPDoc siempre empieza con 2 asteriscos. Comentarios adicionales tendrán 1 asterisco
 * 
 * La primera línea será siempre una descripción corta, es como el título
 * Si se desea agregar o extender esa descripción, es necesario un espacio en blanco
 * y luego una descripción un poco más larga que dura hasta el siguiente
 * espacio en blanco.
 * 
 * @author PHPDoc es capaz de recibir varios parámetros, entre los más importantes, el autor...
 * @param string $asdf En caso de funciones, los parámetros que recibe esa función
 * @return bool O lo que retorna esa función
 * @version 0.1 También podemos dejar expresado la versión
 * @package Y una de las más importantes: agrupar contenido por packages
 * @subpackage Y por supuesto por sub-packages.
 */
{% endhighlight %}

Existen bastantes variantes más de etiquetas que podemos poner, [que están listados aquí](http://en.wikipedia.org/wiki/PHPDoc#Tags), página en la que además se explica mucho mejor el uso básico de PHPDoc.

En fin, una vez que unifiqué todos los comentarios, había que generar la documentación. Para esto, [bajé PHPDocumentor](http://sourceforge.net/projects/phpdocu/files/), configuré la configuración predeterminada ubicada en `user/default.ini` a mi gusto con las rutas y visité la página predeterminada que crea PHPDocumentor. Pasó un buen rato y terminé con mi documentación en un [esquema muy parecido a esto](http://manual.phpdoc.org/) (elegir alguna opción de las disponibles).

PHPDocumentor genera entonces toda la documentación técnica en un formato fácil de leer y fácil de seguir para el programador, desde donde es perfectamente posible combinarlo por ejemplo con las keywords propias que genera svn. 

De esta forma, es posible generar un completo manual para el desarrollador el cual podrá ser estudiado y eventualmente hasta auditado (y por supuesto aprobado) por alguna empresa externa.

Así que ya saben: no existe excusa alguna para generar una buena documentación... y hacer de esta buena documentación una excelente documentación.
