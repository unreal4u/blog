---
comments: true
date: 2010-01-24 05:05:53+00:00
layout: post
slug: nueva-class-publicada-csstacker
title: 'Nueva class publicada: CSStacker'
wordpress_id: 351
categories:
- Classes
- CSS
- Mundo Web
- PHP
---

A partir de hoy se lanza mi segunda class al público en general: es una class que es capaz de tomar varios CSS, comprimirlo, optimizarlo y enviarlo hacia el cliente (con gzip). 

De esta forma, el [CSS principal de CHW](http://www.chw.net/wp-content/themes/chw-5.0/s/base.css) por ejemplo, pasa de pesar 43876 bytes a 5802 bytes, una compresión de un 87%! 

Cómo se hace esto? Muy simple: se toma el o los CSS a procesarse, se eliminan los comentarios, los retornos de carros, los espacios que sobran, los tabs que sobran, se cambian todos los 0px a 0, los colores se convierten a su respectivo código corto y por último los colores se tratan de acortar de #EEAA11 a #EA1. 
Por último, a ese archivo resultante se le aplica gzip y se manda al cliente. Por último, si el cliente ya tiene en su caché el CSS, simplemente se manda un código HTTP de que el archivo no ha sido modificado, reduciendo de esta manera ancho de banda.
<!-- more -->
Mi class trabaja con dos formas de caché, ambos configurables: 
El primer caché se genera en el servidor. De esta manera, se pueden dar distintas combinaciones para distintos perfiles. 
Si en una página cargo hola.css y chao.css y en otra página cargo hola.css y mundo.css, ambos tendrán una caché distinta. 
Esta caché se puede activar o desactivar, aunque desactivado no tiene mucha gracia ya que cada vez que hay un request, se debe realizar todo el proceso de arriba nuevamente. 

La segunda caché es la del cliente: si el CSS no ha cambiado entonces no descarga nada. Si en cambio, el archivo fuente ha cambiado o el cliente no tiene en caché el CSS se descarga nuevamente. 
Esta caché se puede desactivar como también regular el tiempo que dura. 

¿Qué ventajas tiene esto? 



	
  1. Los CSS quedan originales, no teniendo que renombrarse. Es tan fácil como llegar y editarlos en caso que sea necesario. Cuando se editan, se actualiza automáticamente la caché interna y se manda la nueva versión al cliente. 

	
  2. No más problemas extraños con distintos navegadores: la class se encarga de hablar con el navegador. 

	
  3. Menos HTTP requests por cliente. 

	
  4. Menos peso que transferir hacia el cliente. 

	
  5. De fácil integración: sólo son 2 líneas de código :D 



Por último, también es posible enviar el CSS como un archivo (recomendado) o bien imprimirlo inline como parte del estilo. 

Y de dónde se puede descargar esta maravilla? De acá: 


#### [http://unreal4u.users.phpclasses.org/browse/package/5950.html](http://unreal4u.users.phpclasses.org/browse/package/5950.html)



En ese link se encuentra la class, documentación completa (en inglés, sorry es requisito del sitio), el archivo de configuración necesaria y por último un par de ejemplos. 

Cualquier duda, puede ser mediante phpclasses.org o bien [mediante CHW](http://www.chw.net/foro/webmasters-f91/300941-nueva-class-publicada-csstacker.html) o por último, como comentario en este mismo blog :)

[UPDATE]: Saqué la versión 1.3 hace algunos días que incorpora algo nuevo: uno puede forzar la creación de un archivo caché (en el servidor) y ahora también existe la opción de sólo imprimir el nombre del archivo caché que genera. De esa forma, podemos incluirlo en nuestro código fuente sin tener otro archivo dando vueltas y que el Webserver se encargue de lidiar con la caché del navegador. (Aunque esto hay que configurarlo, mi class la hace solo :D )
