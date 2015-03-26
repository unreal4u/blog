---
comments: true
date: 2013-02-03 15:26:52+00:00
layout: post
slug: por-que-jquery-le-devolvio-la-diversion-a-javascript
title: Por qué jQuery le devolvió la diversión a JavaScript
wordpress_id: 727
categories:
- Javascript/jQuery
- Pensamientos Personales
---

Este post es una historia llena de sentimientos encontrados, escalofriantes partidos de futbol, encontrarle el sentido a las cosas y volver a vivir como un adolescente enamorado. Ah, y también tiene relación con jQuery. Seguramente a estas alturas ya tengo toda su atención del mundo, así que ahora procederé a explicar el sentido de todo esto.

Corría el año 2001 cuando aprendí de forma bastante incipiente que podía escribir "Hola mundo" en la Web, mediante código escrito por mi en notepad! La primera vez que pude hacer eso, fue en un computador Compaq que tenía un procesador Cyrix que... bueno, francamente era una mierda de equipo. Corría Windows 95 porque no se podía Windows 98, tenía la friolera cantidad de 28MB en RAM y se quedaba pegado sólo porque uno lo miraba (o dejaba de, cualquiera de las dos condiciones servía al menos una vez al día). La cosa es que por aquellos años, mediante tarea en la Universidad, tuve que hacer una página que validara algunos formularios mediante el temido JavaScript: ese lenguaje críptico donde cualquier equivocación significaba que el navegador simplemente dejaba de responder con un mensaje de error tipo "Error en la línea 0, columna 0". Gran aporte. Debo confesar que jamás me llamó la atención: encontraba JavaScript engorroso, complicado y en general poco divertido de programar (no se podía hacer casi nada que no fuera validar formularios). Si quieres leer cómo sigue esta historia, haz click en "continuar leyendo".<!-- more -->

En fin, pasaron los años y ya habiendo terminado la Universidad (y con ello los estúpidos ensayos de validar formularios en JavaScript), trataba por todos los medios posibles de eludir el uso del mismo, lo que me había resultado bastante bien: ya corría el año 2010 y las conexiones de Internet andaban mucho más rápidos que hacía 10 años atrás, así que no le veía la necesidad de perder tiempo en tonterías cuando de todas formas había que validar los datos en el servidor. La única vez que ocupé JavaScript fue para hacer un pequeño script que activara todos los checkboxes en una lista. Éramos felices yo y el desgraciado de JavaScript cada uno por su lado. 

Sin embargo, por aquellos años se estaba haciendo famoso algo que -lo que pensaba en ese tiempo- era un nuevo lenguaje de programación llamado "jQuery". Asimismo, también estaba muy de moda esa otra técnica nueva llamada "Ajax" que permitía hacer consultas al servidor y mostrar ese resultado en la página sin tener que recargar (con F5) la página!

De a poco comencé a acercarme un poco a jQuery, porque mal que mal era la gran novedad, pero a decir verdad, en cuanto miraba el código, entre todos los paréntesis, corchetes y llaves varias, veía una gran mezcolanza de chino mandarín... antiguo. Sin embargo, con un poco de suerte me habían funcionado algunos pedazos de código bajados de Internet así que seguía siendo feliz en mi ignorancia. 

Ese mismo año, fue el mundial de futbol en Sudáfrica. Como buen holandés que soy, apoyaba fervientemente a mi equipo y estaba segurísimo desde el segundo partido ganado (después del primero estaba seguro que no pasábamos a la segunda ronda) que éste iba a ser **EL** mundial en que Holanda levantaría esa preciada copa por primera vez en su historia... Pero no fue así. Me van a perdonar mis lectores españoles, pero ganaron ustedes en dudosas condiciones, con un gol de oro que salió solamente gracias a la suerte un minuto antes de que hubieran perdido por penales frente a un Holanda que lo había dado todo ya en el partido con Brasil donde (jeje) salimos victoriosos y donde Uruguay había dado una sorpresa tremenda también. España por otro lado, estaba en perfectas condiciones debido a que pudieron jugar de forma relajada con Paraguay y ganándole de la misma manera a Alemania.

Sin embargo, lo peor pasó: España había metido el gol, dejándonos con un sabor amargo a segundo lugar. Depresión, ganas de matarme y todas esas cosas se me pasaron por la cabeza en ese momento. La vida ya no tenía sentido alguno y los días pasaban lentos y agonizantes etc etc etc. Ya lo saben: todas esas cosas malas y negativas.

Fue en ese momento donde una agradable sorpresa me volvió a subir el ánimo un poco: de alguna forma me enteré que [SitePoint](http://products.sitepoint.com/) iba a lanzar (temporalmente) uno de sus libros de forma gratuita dependiendo de quién ganara en el mundial. Dicho y hecho: había ganado España, así que el libro a lanzarse gratuitamente por algunos días resultó ser [jQuery: Novice to Ninja](http://www.sitepoint.com/books/jquery2/). Desanimado por un lado pero curioso por el otro, decidí bajarlo y echarle una ojeada.

Los primeros 3 capítulos del libro lo fueron todo: aprendí que jQuery es en realidad JavaScript, que es sólo un framework hecho en base al mismo y en una noche, sólo mediante algunas simples líneas, entendí absolutamente todo el objetivo de jQuery y la verdadera utilidad y alcance del mismo. Hasta me adelanté un par de capítulos y aprendí que el tan famoso Ajax no es otra cosa que una llamada hecha en JavaScript a un script externo! 
Si pueden, compren el libro porque ha sido una de mis mejores inversiones (gratuitas xD) que he hecho. Ese libro me liberó del sentimiento de humillación y ayudó a enfocarme en otra cosa, volví a ver el sentido de la vida y lo mejor de todo: me motivó a aprender JavaScript, que me ha sido infinitamente útil en la vida laboral.



## Lo importante: ¿cómo funciona jQuery?


Si entienden la siguiente frase, entenderán todo lo que conlleva jQuery. **Uno hace una selección mediante selectores muy parecidos a los encontrados en CSS, y en base a esa selección uno ejecuta acciones**. Esa es toda la gracia de jQuery y eso es suficiente. Para explicar mejor el ejemplo, veamos el siguiente ejemplo:

`$('#holaClick').click(function(){ alert('hola!'); } );`

"$" es en definitiva una llamada a jQuery, representa el objeto jQuery y con ella se puede acceder a todas sus funciones. Se recomienda que cuando exista la posibilidad de que tengamos más de un framework, cambiemos el signo $ por la palabra "jQuery" ya que otras librerías también ocupan el signo peso para auto-referenciarse.

Luego viene '#holaClick'. Lo que estamos haciendo ahí es, mediante sintaxis de CSS, seleccionar el elemento con id "holaClick". De esta forma, cualquier acción que elijamos se va a ejecutar sobre ese elemento y no otro. También [podemos elegir otros selectores](http://api.jquery.com/category/selectors/), pero eso ya es tarea de ustedes. La gran gracia de este tipo de selectores es que podemos seleccionar más de un elemento, por ejemplo si tenemos varios párrafos de class hola, simplemente podemos que poner ".hola" como selector y de esa forma, cualquier acción que ejecutemos se aplicará sobre todos los elementos que tengan como class ".hola". ¿Qué más simple?

Acto seguido viene el nombre de nuestra acción. En este punto, [recomiendo visitar esta página](http://api.jquery.com/) para poder ver un listado de todas las acciones que son posibles de ejecutar. Todas las que empiezan con un punto son acciones. 

De ahí, viene la siguiente palabra clave: function(){}. Esta declaración es necesaria debido a que jQuery funciona mucho con lo que se llaman "funciones anónimas", que si bien es cierto suena aterrador, no es otra cosa que el declarar una función dentro de otra. Lo importante del asunto es que es dentro de esta función donde (finalmente) ejecutaremos nuestras acciones.

Finalmente, vienen nuestras acciones, que en este caso será imprimir un alert() con el texto hola. Entonces, la manera de leer el pedazo de código de arriba en humano es el siguiente: "cuando hagamos _[accion]_ click en _[seleccion]_ el elemento con id "holaClick", debemos ejecutar una función que _[funcion]_ imprima un alert() con el texto hola". ¿Fácil cierto?



## Código interactivo


Ahora que ya sabemos lo básico, vámonos a un ejemplo con algo más de código.

{% highlight javascript %}
<!DOCTYPE html>
<html>
    <head>
        <title>Hello world!</title>
        <script type="text/javascript" src="../jquery-1.8.2.min.js"></script>
    </head>
    <body>
        <p class="hola">Hola mundo!</p>
        <p class="chao">Chao mundo!</p>
        <p class="hola">Y nuevamente un hola mundo!</p>

        <div id="myDiv" style="display:none">Este es el div interno</div>

        <input type="button" id="paragraph-change" value="Cambiar fondo de P" /><br />
        <input type="button" id="toggle-div" value="Toggle div" /><br />
        <input type="button" id="hola-change" value="Cambiar color hola" /><br />

        <script type="text/javascript">

$(document).ready(function(){
	$('#paragraph-change').click(function(){
	    $('p').css('background','#EEE');
	});

	$('#toggle-div').click(function(){
	    $('#myDiv').toggle();
	});

        $('#hola-change').click(function(){
            $('.hola').css('color','#999');
        });
});

        </script>
    </body>
</html>
{% endhighlight %}

En un principio, parece muchísimo código, pero la verdad es que es todo bastante simple.  
Lo primero que hacemos es incluir la librería de jQuery que [pueden bajar desde esta página](http://jquery.com/), la última versión cuando escribí esta entrada era la 1.8.2 así que esa usé.
De ahí continúo con un poco de código para rellenar y creo tres párrafos de texto. Dos con el class "hola" y uno con el tag "chao". También creo un div oculto con el contenido "Este es el div interno".

Por el momento y a muy grandes rasgos sin entrar en tecnicismos, lo único que hay que saber es que el caso ideal es que jQuery empiece a ejecutar acciones recién cuando el documento está cargado y el motor de JavaScript se haya inicializado. A partir de este momento, podemos "amarrar" ciertos eventos sin entrar en conflicto con la forma nativa en que los browsers manejan el stack de llamadas al motor de JavaScript cuando se carga Window por ejemplo. De todas formas, es un tema bastante más complejo, pero quédense con la idea de que todas las acciones en jQuery tienen que ir dentro de un bloque `$(document).ready()`, para que nunca tengan problemas de que al inicio de carga de una página ciertas acciones se ejecuten mientras que otras no.

De ahí en adelante, podemos definir acciones. En el caso de arriba, tenemos definidos 3 acciones. La primera cuando hagamos click en el id "paragraph-change", la segunda cuando hagamos click en el id "toggle-div" y la tercera cuando hagamos click en el id "hola-change". Coincidentemente, creamos 3 botones con esos 3 ids. 
Pude haber ocupado el método onclick de HTML, pero la verdad prefiero esta forma ya que es mucho más ordenado, queda todo el código JavaScript en un solo lado y podemos incluir archivos externos junto con toda nuestra definiciones convenientemente en un solo lado. Facilita bastante de esta forma si estamos trabajando con módulos y controladores, ya que de la misma forma en que organizamos nuestro código PHP podemos organizar el código JavaScript.

Sin embargo, ahora viene lo entretenido: las acciones dentro de nuestras funciones son otras llamadas a jQuery. Y aquí viene el gran poder de jQuery: las funciones de jQuery funcionarán en casi cualquier navegador, aunque esto cambiará a partir de la(s) próxima(s) versión(es) de jQuery, que se separará en dos corrientes: jQuery 1.9 para seguir soportando navegadores antiguos, y otro jQuery 2.0 que desecha mucho código para estos navegadores antiguos, para que de esa forma pueda seguir creciendo en otra corriente separada y mantener un codebase/footprint más chico.

En fin, las funciones que llamamos son dos:  
`$('p').css('background','#EEE');`: Este cambiará el CSS de todos los elementos de tipo "p" (párrafos) y les cambiará el fondo al color #EEE.  
`$('#myDiv').toggle();`: Esta es sin duda una de las funciones más interesantes de jQuery, ya que toggle() es una función que esconderá o mostrará lo que seleccionemos. La gracia radica en que lo hace automático, de forma que si el elemento está escondido, lo mostrará, y si el elemento es visible, lo ocultará.   
`$('.hola').css('color','#999');`: Tal como el primero, esta función cambiará el CSS de todos aquellos elementos que tienen como class ".hola", y cambiará el color de la letra a #999.



## Conclusiones


Cuando corría el año 2001, Javascript era simplemente un lenguaje con el que no se podían hacer muchas cosas y durante mucho tiempo fue totalmente ignorado: prueba de esto es que recién desde hace un par de años los navegadores le han puesto la suficiente atención y gracias a ello es que se ha optimizado bastante los motores de Javascript para que de esa forma, puedan realizar cada vez más rápido códigos ya más complejos. Hoy en día, resulta difícil pensar en algo que Javascript NO pueda hacer: desde simples cambios de estilo hasta recargar la página y detectar un montón de acciones por parte del usuario es un repertorio habitual para cualquier desarrollador activo en este lenguaje. Por lo mismo han nacido herramientas que intentan facilitarle el trabajo al desarrollador y una de ellas es sin duda jQuery.

Y aunque hace algunos años atrás trataba de ignorar por completo cualquier cosa que tuviera que ver con Javascript, ahora soy un adepto fan a incorporar esta herramienta a cualquier página que ayude al usuario a hacer las cosas de forma más rápido o amigable.
