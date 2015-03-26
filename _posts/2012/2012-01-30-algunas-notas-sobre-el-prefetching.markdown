---
comments: true
date: 2012-01-30 21:29:17+00:00
layout: post
slug: algunas-notas-sobre-el-prefetching
title: Algunas notas sobre el prefetching
wordpress_id: 584
categories:
- Apple/Mac
- Javascript/jQuery
- Mundo Web
- Pensamientos Personales
- PHP
---

Desde hace tiempo que sigo bien de cerca algo que me tenía bien intrigado: el prefetch de HTML5, que me parece demasiado buena idea y espero que todos los navegadores lo implementen ipso-facto. Sin embargo, quise echar una pequeña mirada a cómo funcionaba (si es que funcionaba) y específicamente a cuál era la sintaxis necesaria y cuáles son los pequeños trucos de la vida que se pueden aplicar para lograr el efecto deseado. Haz click en leer más para conocer los detalles de esta investigación que algunas sorpresas dio y también muchos dolores de cabeza.

<!-- more -->



## ¿Qué es el prefetching?


Desde la entrada de HTML5, aparte de los tags de audio y video, hubo algo que me llamó bastante la atención: el prefetch. Esta técnica consiste en adivinar lo que el usuario bajará en su siguiente click en nuestra página por lo que la idea detrás de esta técnica es bajar el mentado archivo o página desde ya para que de esta forma cuando el usuario haga el click no tenga que bajar el archivo nuevamente. 
En teoría, suena bastante bien, pero quise llevarlo a la práctica para saber si acaso era factible o no poder implementar esta tecnología hoy en día. De acuerdo a casi todas las páginas web que consulté, simplemente había que incluir algunas cabeceras en nuestra página web para que milagrosamente funcionara el prefetching, lo cual me parecía demasiado bueno para ser verdad y así fue: resulta que el prefetching sólo ocurre bajo algunas condiciones y no siempre. 

Como primer punto de aquiles, el prefetching sólo funciona en Firefox y en Chrome, desde la versión 3.6 y 13 respectivamente, pero sinceramente es un punto que poco importa, ya que estos dos navegadores juntos ya obtienen más de un 50% de participación en el mercado de los navegadores ([fuente](http://gs.statcounter.com/#browser-ww-monthly-201107-201112)), por lo que nuestro grupo objetivo desde ya es bastante amplio. 

Sin embargo, de aquí en adelante todo empieza a irse cuesta abajo. Resulta que el prefetching no funciona en muchos casos, y como podrán leer más adelante, el prefetching de Firefox simplemente no cumple su cometido, lo que baja nuestra cuota de mercado a sólo un 25%. La otra característica sobresaliente es que el uso más intenso que pretendía darle no funciona: sólo funciona si el recurso prefetcheado es un link (elemento <a>), y no una llamada ajax. 



## Preparación


Para poder saber de forma rápida si acaso el prefetching funcionaba, escribí 2 archivos: uno como índice principal y el otro de prefetching, con el siguiente código, primero vemos nuestro índice:

{% highlight html %}
<!DOCTYPE html>
<html><head>
	<title>Testing prefetch with HTML5</title>
	<script type="text/javascript" src="../jquery.min.js"></script>
	<link rel="prefetch"  href="prefetch.php">
	<link rel="prerender" href="prefetch.php">
</head><body>
<a href="prefetch.php" class="prefetch">Please click here to display the (prefetched) page</a><br />

<input type="button" id="buttonContents" value="Get contents" />
<div id="contents"></div>
<script type="text/javascript">
$('#buttonContents').click(function(){
	$.ajax({
		url:'prefetch.php',
		success:function(data){
			$('#contents').html(data);
		}
	});
});
</script>
</body></html>
{% endhighlight %}

Es un documento HTML5 estándar, con dos elementos link: uno para firefox (que ocupa rel=prefetch) y la otra para Chrome, que ocupa rel=prerender. Dato rosa: la diferencia entre ambas es que el prerender también renderea la gráfica de la página, mientras que el prefetch sólo realiza este paso cuando la página se hace visible. En un documento complejo podría quizás llegar a notarse este punto, pero para el 99,6% de las páginas restantes no habría ninguna diferencia. De todas formas, ambas directivas son necesarias, ya que uno funciona para Firefox y la otra para Chrome.

Sólo para realizar un test, tenemos dos "links": uno que carga el archivo mediante ajax, y la otra es simplemente un link. Hice esta separación ya que uno es dinámico y la otra es estática, y era probable que los navegadores hicieran una diferenciación entre ambos tipos.

Este será nuestro archivo que vamos a "prefetchear":

{% highlight php %}
<?php
$fp = fopen('log.txt', 'a');
fwrite($fp,'['.strftime('%d-%m-%Y %T').'] - '.substr($_SERVER['HTTP_USER_AGENT'],strrpos($_SERVER['HTTP_USER_AGENT'], ' '),strlen($_SERVER['HTTP_USER_AGENT']))."\n");
fclose($fp);
sleep(3);
echo 'hello';
{% endhighlight %}

Por supuesto que tenemos un archivo llamado log.txt que tiene permisos 777. La principal razón del sleep era para simular la vida real y darle un tiempo de espera de carga de 3 segundos.



## Empiezan las sorpresas


Con estos dos archivos empecé a hacer las pruebas, y grandes fueron mis sorpresas, ya que lo escrito en el papel es muy distinto a la práctica. 

Primero dos links que me fueron de mucha ayuda:
http://browserspy.dk/prefetch.php
http://prerender-test.appspot.com/

El primero sirve para saber si el prefetching está funcionando para Firefox, mientras que el segundo sirve para verificar si el prefetching está funcionando para Chrome. 
También resultó bastante útil el task manager de Chrome (Window|Task Manager), y este link, también para Chrome: 
`chrome://net-internals/#prerender`

Ya con todas estas herramientas pude empezar a probar distintas configuraciones y estos fueron los resultados:




  * Firefox en Mac NO soporta prefetching. Así es, NO. Bajo ninguna circunstancia.


  * Firefox en Windows no tuvo mayores inconvenientes en hacer el prefetch, pero en cuanto se hace click en el documento "prefetcheado", lo empieza a descargar de nuevo. Más sobre esto después.


  * Chrome a partir de la versión 13 funciona bien el prerendering. ANTES de esta versión, no hubo caso en que funcionara.


  * Sólo se descargan documentos estáticos. Olvídense de pre-cargar ajax, no funcionará.





## ¿Qué pasa con Firefox?


Hasta que me di cuenta de lo que pasaba en Firefox, estaba feliz de que funcionara, pero me llevé una gran desilusión. Resulta que Firefox después de haber hecho el prefetching y cuando uno ya hace click en el link... empieza a descargar el documento de nuevo! El gran problema de esto, es que se pierde todo el objetivo de hacer un prefetching y más encima realiza un request adicional que nunca ocupará, gastando recursos en nuestro servidor.
Hay sin embargo un test que no he hecho y que tampoco creo que valga la pena: establecer cabeceras explícitamente en el futuro para que Firefox piense que el recurso que acaba de rescatar también es válido para el siguiente request. 
El único reparo que tengo con esto es que si tenemos una cabecera de prefetching es por que sabemos que el próximo recurso a rescatarse será el que le estamos indicando... ¿para qué necesito decirle explícitamente que quiero que conserve ese recurso?



## Conclusiones


Aunque Firefox soporta desde la versión 3.6 el concepto de prefetch, lo hace bastante mal. Uno pensaría que ya con tanto tiempo "soportando" prefetching, funcionaría bien pero como queda demostrado, lo soporta de forma paupérrima. 
Chrome en cambio me alegró el día ya que funciona y bastante bien. Incluso funciona si tenemos un javascript como el que sigue:

{% highlight javascript %}
var app = {
    prefetchLinks: function(){
        var hrefs = $("a.prefetch").map(function(index, domElement){
            return $(this).attr("href");
        });
        return $.unique(hrefs);
    },

    addPrefetchTags: function(){
        this.prefetchLinks().each(function(index,Element){
            $("<link />", {
                rel: "prefetch", href: Element
            }).appendTo('head');
            $('<link />', {
                rel:'prerender', href: Element
            }).appendTo('head');
        });
    },
};
$(document).ready(function(){
    app.addPrefetchTags();
});
{% endhighlight %}

Sacado (y modificado) [desde esta página](http://www.catswhocode.com/blog/mastering-html5-prefetching). Las modificaciones que tiene son en la llamada final de la función y en agregar el soporte para Chrome.

La gran sorpresa de esta función es que aunque construye bien el documento en todos los navegadores, el objetivo final sólo se logra en Chrome, ya que todos los demás navegadores no saben qué hacer con las etiquetas recién construidas, lo cual incluye -para mi sorpresa- a Firefox, que ni siquiera comienza a bajar los documentos que queramos rescatar.

Con esta pequeña investigación al respecto, creo que dejaré descansar el tema por uno o dos años para luego ver si se ha avanzado algo y si en ese futuro sirve o no.
También quiero dejar una pequeña enseñanza: aunque muchas páginas juran que el prefetching funciona con sólo agregar algunas líneas a tu código fuente, siempre hay que experimentar y tratar de hacer funcionar las cosas por uno mismo. En este caso, quedó demostradísimo que no es llegar y agregar links, ya que la funcionalidad final sólo se logra en un navegador mientras que para el otro es sólo una gastadera de recursos. Por si fuera poco, casi todas las páginas que promocionan el uso del prefetching sólo incluyen el código necesario para Firefox haciendo su solución totalmente inútil para la vida real, así que: cuidado con copy-paste!
