---
comments: true
date: 2010-08-24 01:54:44+00:00
layout: post
slug: es-mejor-curl-o-file_get_contents
title: Es mejor cURL o file_get_contents?
wordpress_id: 401
categories:
- PHP
---

Históricamente, siempre he tenido problemas con estas dos funciones. De repente está desactivada la directiva de poder obtener contenido remoto y eso producirá un montón de problemas si dependemos de file_get_contents() para nuestro sitio. Por otro lado, también es bastante inseguro y por lo mismo se le desactiva.
Las directivas que controlan en php.ini la inclusión de archivos remotos son dos:  
allow_url_fopen = Off  
allow_url_include = Off

Que por lo general se encuentran predeterminadamente en Off, especialmente en las últimas versiones de PHP. La diferencia entre ambas es que la primera permite tratar archivos con un protocolo determinado (tal como http:// o ftp://) como un archivo, y la segunda directiva establece si acaso se puede ocupar la función include() o require() con este tipo de archivos.
<!-- more -->

Todo lo expuesto arriba es solucionable (fácilmente) con cURL, el programa bastante más seguro de Linux que permite realizar transferencias de archivos desde distintos protocolos. Para aquellos que han usado wget, cURL es bastante parecido (sí, tiene el mismo poder que wget) pero se puede hacer de forma automatizada, invocándolo desde PHP mismo, o Perl, o Python, o lo que sea.

Sin embargo, cURL sólo permite la transferencia via protocolos, de esta forma, cargar un archivo local no se puede hacer (a menos que se le invoque como http://localhost/archivo, lo cual no siempre es así). 
De esta forma, les presento una función que les podrá hacer la vida un poco más fácil: aunque le falte mucho desarrollo todavía, en un principio cumple con lo que promete: si el archivo viene desde afuera, ocupa cURL. De lo contrario, simplemente rescata el archivo mediante file_get_contents().

{% highlight php %}
<?php
function file_get_contents_curl($url) {
  if (strpos($url,'http://') !== FALSE) {
    $fc = curl_init();
    curl_setopt($fc, CURLOPT_URL,$url);
    curl_setopt($fc, CURLOPT_RETURNTRANSFER,1);
    curl_setopt($fc, CURLOPT_HEADER,0);
    curl_setopt($fc, CURLOPT_VERBOSE,0);
    curl_setopt($fc, CURLOPT_SSL_VERIFYPEER,FALSE);
    curl_setopt($fc, CURLOPT_TIMEOUT,30);
    $res = curl_exec($fc);
    curl_close($fc);
  }
  else $res = file_get_contents($url);
  return $res;
}
{% endhighlight %}

Muchas cosas se pueden hacer para mejorar esta función, como por ejemplo verificar que cURL esté instalado, y si no lo está, verificar que se pueda rescatar el archivo mediante file_get_contents(). Sin embargo, eso ya es tarea del estimado lector. 
