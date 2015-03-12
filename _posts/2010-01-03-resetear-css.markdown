---
author: admin
comments: true
date: 2010-01-03 07:11:49+00:00
layout: post
slug: resetear-css
title: Resetear CSS
wordpress_id: 318
categories:
- CSS
- Pensamientos Personales
---

Me decidí hoy a hacer algunas cosas pendientes. Una de ellas es terminar de una buena vez por todas esa fantástica idea que me viene dando vueltas hace cerca de 2 años y que no he podido ejecutar por falta de tiempo: una página que se integre con la montonera de servicios externos para que de esa manera, todo tu movimiento en Internet lo puedas hacer mayormente desde esa página. 

También me llevé una agradable sorpresa: el post que habla acerca de [cómo manejar errores en PHP](http://blog.unreal4u.com/2009/12/manejar-errores-en-php/) ya se ubica primero en Google al buscar por php trigger_error :D Aunque a través de Google sólo he recibido 2 visitas, el solo hecho de ubicarme en primer lugar ya me hace más que feliz. 
Pero, el tema que nos concierne hoy, es acerca del reseteo de CSS. "¿Qué es?" "¿Para qué sirve?" y "¿Cómo podemos solucionar la problemática?" serán algunas de las interrogantes que se plantearán y que verán la luz en este post.
<!-- more -->


## ¿Qué es?


Tal como su nombre lo indica, es un reseteador de CSS (doh!!). ¿Qué es lo que significa exactamente? Bueno, para todos aquellos que se han lanzado a la aventura de crear una página Web que se vea exactamente igual en cualquier navegador, se ha podido dar cuenta de que no siempre se renderean exactamente igual todos los elementos. Existe siempre alguna forma lijeramente distinta de mostrar una cosa en un navegador y en otro. 
Para solucionar eso, nació la necesidad de poder "resetear" los valores predeterminados que un navegador en particular pudiera tener para un elemento en particular. De esta manera, se obtiene un documento en blanco que sirve de base para que de esta manera, se pueda empezar a trabajar. 



## ¿Para qué sirve?


Aunque parcialmente respondida en la pregunta anterior, aquí voy a terminar de responderla al 100%. 
El problema se origina debido a que los navegadores pueden aplicar ciertos valores predeterminados para ciertos elementos lo cual hace que ese elemento no se vea siempre igual. El problema fundamental de esto es que por lo general el o los desarrolladores tienen un navegador principal con el que prueban todo exhaustivamente y ocupan otros sólo para simplemente verificar que todo esté marchando bien. 
Un ejemplo práctico lo tiene por ejemplo la diferencia en el rendereo del elemento `h1` y `p` en Chrome y Firefox vs Opera e Internet Explorer:

El código:
[xhtml]<html><head><title>Probando...</title>
<style>
html{background:#333}
h1{background:#555;color:#FFF;width:300px}
p{background:#777;color:#CCC;width:300px}
</style>
</head><body>
<h1>Hola mundo!!!</h1>
<p>Chao mundo!!</p>
</body></html>[/xhtml]

La vista previa: 
[![2010-01-03-u4u-023](http://blog.unreal4u.com/wp-content/gallery/reseteo-css/thumbs/thumbs_2010-01-03-u4u-023.png)](http://blog.unreal4u.com/wp-content/gallery/reseteo-css/2010-01-03-u4u-023.png)

Si se fijan bien en la imagen, tanto Chrome como Firefox mantienen un estilo bastante parecido, pero definitivamente Internet Explorer tiene otra manera totalmente distinta de renderear la página, lo cual redunda en mayores espacios y finalmente en pixeles corridos. También Opera corre el espaciado con un par de pixeles de diferencia, sólo apreciable para el que tiene muy buen ojo. 
Cabe destacar eso sí, que en estilos más complicados, incluso hay diferencias entre Chrome y Firefox. 



## ¿Cómo podemos solucionar la problemática?


Y ha llegado el momento de la salvación y la principal razón de existir de este post: podemos solucionar estas pequeñas problemáticas en un 99% simplemente borrando cualquier estilo predeterminado que tome el navegador y estableciendo expresamente la nuestra de forma posterior al reseteo. 
Aunque creo haberlo mencionado antes, nunca está demás: CSS funciona de una manera bastante simple: cuando existe algún conflicto entre dos clases, simplemente se aplicará el último estilo declarado. De esta forma, si tenemos: 

[xhtml]<html>
<style>
p{color:#454;background:#9AF}
</style>
<body>
<p style="color:#29E">hola mundo!</p>
</body></html>[/xhtml]

Existe un claro conflicto entre el estilo declarado en la cabecera y el estilo declarado de forma inline con la propiedad `style`. Mientras que el primero establece un color `#445544`, el segundo establece un color de fuente `#2299EE`. En este caso, se aplicará el último estilo declarado, es decir, el que se declara de forma inline. 
Exactamente lo mismo pasa con esta solución: mediante un pequeño cambio en nuestro código de arriba, podemos primero resetear todos los estilos y después llamar a nuestro CSS de forma segura. 

Para facilitar aún más las cosas, y de paso no demorar más la carga de nuestra página, podemos externalizar el servicio. Es por lo mismo, que Yahoo nos ofrece el reseteo del CSS de forma totalmente gratuita, contando con el respaldo de ellos. ¿Cómo se hace? 

[xhtml]<html><head><title>Probando...</title>
<link rel="stylesheet" type="text/css" href="http://yui.yahooapis.com/3.0.0/build/cssreset/reset-min.css"/>
<style>
html{background:#333}
h1{background:#555;color:#FFF;width:300px}
p{background:#777;color:#CCC;width:300px}
</style>
</head><body>
<h1>Hola mundo!!!</h1>
<p>Chao mundo!!</p>
</body></html>[/xhtml]

Con este pequeño cambio, nuestro script quedaría rendereado de la siguiente forma en los distintos navegadores seleccionados: 

[![2010-01-03-u4u-024](http://blog.unreal4u.com/wp-content/gallery/reseteo-css/thumbs/thumbs_2010-01-03-u4u-024.png)](http://blog.unreal4u.com/wp-content/gallery/reseteo-css/2010-01-03-u4u-024.png)

Aunque existen todavía algunos pixeles corridos, ya no son tantos como antes, lo cual alivia bastante la pega extra a realizarse. 

Eso si que ahora será responsabilidad nuestra establecer los tamaños de las fuentes, pero mediante esta técnica se permitirá facilitar bastante el desarrollo cross-browser. Algunos consejos: la etiqueta `<strong>` también se resetea, al igual que la etiqueta `<em>` y varios más. Sugiero [echar un vistazo en Yahoo Developer Network](http://developer.yahoo.com/yui/3/cssreset/) para informarse de más cambios y otras posibilidades más avanzadas que jamás he ocupado. 



## Versión un poco más recortada (343 bytes)


La versión de Yahoo pesa 501 bytes. Abajo adjunto una versión un poquito más chica que pesa 343 bytes. Se eliminó todo comentario previo y también el punto y coma final de cada declaración que no es necesario. 

`
html{color:#000;background:#FFF}body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,code,form,fieldset,legend,input,textarea,p,blockquote,th,td{margin:0;padding:0}table{border-collapse:collapse;border-spacing:0}fieldset,img{border:0}address,caption,cite,code,dfn,em,strong,th,var{font-style:normal;font-weight:normal}li{list-style:none}caption,th{text-align:left}h1,h2,h3,h4,h5,h6{font-size:100%;font-weight:normal}q:before,q:after{content:''}abbr,acronym{border:0;font-variant:normal}sup{vertical-align:text-top}sub{vertical-align:text-bottom}input,textarea,select{font-family:inherit;font-size:inherit;font-weight:inherit}input,textarea,select{*font-size:100%}legend{color:#000}
`
