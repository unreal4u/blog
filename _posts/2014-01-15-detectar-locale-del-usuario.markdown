---
comments: true
date: 2014-01-15 12:57:14+00:00
layout: post
slug: detectar-locale-del-usuario
title: Detectar locale del usuario
wordpress_id: 858
categories:
- i18n/L10n
- PHP
---

Tal como prometí [en uno de los primeros posts del año](http://blog.unreal4u.com/2014/01/i18n-cosas-que-tomar-en-cuenta/), ha llegado la hora de cumplir con mi mandamiento y este es el segundo post en la serie de posts que seguirán al respecto de la internacionalización y localización de su sitio.

En esta entrega, explicaré a grandes rasgos cómo capturar la locale de un usuario, con algunos métodos de fallback si es necesario. 

Lo primero que les contaré, es que gran parte de la información de un usuario ya la manda el navegador: un usuario que habla español es muy probable que ocupe un navegador en español, como también un usuario de habla holandesa ocupará un navegador en holandés. 
<!-- more -->

Gracias a esta información ya podremos detectar con cierta fiabilidad cuál es la locale que queremos mostrar.
Sin embargo, puede darse el caso también de que esta información no sea certera o que el usuario está ocupando un computador que no sea de él. En este caso, podemos hacer un fallback a detectar el país a través de la ip. Bases de datos hay bastantes, pero personalmente recomiendo el de [Maxmind](http://dev.maxmind.com/geoip/geoip2/geolite2/) que es gratuito y bastante certero. Sin embargo, puede darse el caso de que no estén muy actualizados.

Todos estos puntos nos llevan al hecho de que tendremos que dejar otra via alternativa para poder elegir qué locale queremos desplegar, siempre de forma manual y ojalá en forma clara.

Tendremos por lo tanto, que implementar 3 métodos: la primera es a través de las preferencias del navegador, la segunda es a través de la detección de la IP y su asociación a algún país, y la tercera es manualmente. 



## Primer método


El primer método es increíblemente sencillo y paradójicamente, también el menos usado, aún cuando debería ser el más certero. Esto es debido a que ocupa las cabeceras que manda el navegador, característica que en lo personal opino es una de las más valiosas pero también muy mal documentadas. Para ver esto, echemos un vistazo a un request típico del navegador: 

[singlepic id=24 w=320 h=240 float=center]

Una de las líneas más interesantes es la que empieza por Accept-Language. Es en esta cabecera donde el navegador le informa a la página web que queremos visitar que su lenguaje preferido (en este caso) es inglés de Estados Unidos. Sin embargo, este valor puede ir cambiando siempre, y puede ponerse también más complicado, como por ejemplo el siguiente request:

`Accept-Language: es-US;q=0.5,es-CL;q=0.8,ru-RU;q=0.1`

En este caso, deberemos leer el request como: Quiero que me entregues la página en español de Chile con máxima prioridad (0.8), pero si ese no está disponible, en inglés de Estados Unidos con una prioridad más baja (0.5), y si cualquiera de ellas no está disponible, en ruso de Rusia con prioridad mínima (0.1).
La parte que realza la importancia es el parámetro "q" detrás de cada lenguaje. Un ejemplo bastante completo podremos encontrar en la cabecera Accept (refiérase a la imagen de arriba nuevamente), que le indica al navegador que desea recibir como primera preferencia un documento en HTML, en segunda preferencia una página XHTML+XML, en tercera preferencia sólo XML, y como último caso, cualquier otra cosa que tenga (Piense en JSON u otros formatos menos conocidos). 

Esta manera de asignar cosas da para un mundo totalmente nuevo, pero para su conveniencia, existen funciones que parsean estos parámetros y que pueden rescatar de ahí de manera rápida y eficiente cualquier cosa que necesiten.

En el caso de la locale, PHP ya lo tiene cubierto con la función `\Locale::acceptFromHttp()`, cuya forma de usarlo es la siguiente:

{% highlight php %}
$locale = Locale::acceptFromHttp($_SERVER['HTTP_ACCEPT_LANGUAGE']);
echo $locale;
{% endhighlight %}

Hasta acá todo bonito, pero hay que tener ojo con esa función, ya que devolverá NULL en el caso que dicha cadena no se haya podido parsear. Es en este punto cuando deberemos cambiar a algo un poco más generalizado, que es detectando la IP del cual el usuario proviene. De lo contrario, lo único que falta es escribir en la sesión del usuario cuál ha sido la locale elegida.



## Detectando la IP



Tal como mencioné, desde Maxmind pueden bajar una base de datos gratuita. Una vez que obtengan esta base de datos, seguirán algunos pasos para obtener el país y finalmente la locale. Sin embargo, el gran truco está en saber desde cuál cabecera rescatar la ip. Cuál ocupar dependerá mucho de la situación: si el webserver está detrás de un proxy, el proxy cambiará los valores de las cabeceras, así que a grandes rasgos, estos pasos son:




  * Revisar PHP_SAPI


  * Consultar `$_SERVER['HTTP_X_FORWARDED_FOR']`


  * Consultar `$_SERVER['HTTP_CLIENT_IP']`


  * Consultar `$_SERVER['REMOTE_ADDR']`



El primer paso es relevante debido a que PHP_SAPI nos indicará si el request viene desde CLI (línea de comandos) o desde la Web: si viene desde la línea de comandos $_SERVER no estará seteado y la ip deberemos setearla manualmente (típicamente a localhost). 

Nuestro código entonces será:

{% highlight php %}
function getIpFromClient() {
    $ip = '127.0.0.1';
    if (PHP_SAPI != 'cli') {
        if (!empty($_SERVER['HTTP_X_FORWARDED_FOR'])) {
            $ip = $_SERVER['HTTP_X_FORWARDED_FOR'];
        } elseif (!empty($_SERVER['HTTP_CLIENT_IP'])) {
            $ip = $_SERVER['HTTP_CLIENT_IP'];
        } elseif (!empty($_SERVER['REMOTE_ADDR'])) {
            $ip = $_SERVER['REMOTE_ADDR'];
        }
    }
    return $ip;
}
{% endhighlight %}

El segundo paso consistirá en consultar a la base de datos si la IP corresponde a algún país conocido. Como existen muchos segmentos que no están en la base de datos (piensen en subredes tipo A, B y C o la misma máquina local), tendrán que tener en mente que siempre deberá existir un valor predeterminado. La consulta es bastante simple y fácil de hacer.

Ahora lo único que falta es asociar el país a algún locale... proceso que es increíblemente simple dado que tienen PHP 5.3 como mínimo y donde resulta un mero llamado a `\Locale::getPrimaryLanguage()` para terminar de obtener todos los datos que se estén buscando, en este caso, una locale.



## Manualmente



La forma manual es una de las más simples: basta con un selectbox con locales y algún método en su código que en todas las páginas verifique que venga algún $_POST o $_GET en específico y sería. Personalmente, prefiero que ese $_GET venga con nombre "locale", de esa forma se sabe a ciencia cierta qué representa.



## Resumiendo



Para esta entrada, escribí un pequeño demo que hace justamente todo el trabajo junto. No es lo más elegante ni tampoco es lo que mejor se ajusta a sus necesidades, pero debería ayudarlos a entender mejor el tema y avanzar bastante. Lo único que faltaría después de esto, es escribir la preferencia del usuario en una variable de la sesión para no tener que recalcularlo en cada request que éste haga.

El script [está disponible en github](https://github.com/unreal4u/detecting-locale-blog). ¡Revísenlo y hasta la próxima!
