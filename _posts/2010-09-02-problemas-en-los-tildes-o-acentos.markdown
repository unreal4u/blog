---
author: admin
comments: true
date: 2010-09-02 16:25:53+00:00
layout: post
slug: problemas-en-los-tildes-o-acentos
title: ¿Problemas en los tildes o acentos?
wordpress_id: 432
categories:
- Bases de Datos
- Linux
- PHP
---

Desde que empecé en esto de la Web, siempre tuve un problema: las  tildes. O me salía el típico signo de pregunta o bien me salían  caracteres extraños, que intentando por aquí y por allá siempre lograba  evitar, aunque a veces me superaba y optaba simplemente por terminar  escribiendo [su entidad](http://www.w3schools.com/tags/ref_entities.asp).

Aunque no pretendo arreglarle la vida a todo el mundo, en este post  sí les dejaré algunos tips para asegurarse de que todo funciona como  debería: se verán algunas funciones de PHP que sirven harto y se verá  superficialmente la interacción con bases de datos.
Dato rosa antes de continuar: la diferencia entre acento y tilde, es que **tilde** representa el signo " `´` " mientras que **acento** se refiere a la fuerza que se le impregna a la sílaba específica. De esta manera, en la  palabra "sílaba", el tilde está en la letra "i", mientras que el acento  está en la sílaba "sí".
<!-- more -->


## Barniz teórico


Los charset no son más que códigos mediante los cuales el computador guarda de forma binaria la representación de un caracter que nosotros  podamos entender y comprender.
De esta manera, el computador no _entiende_ lo que significa la letra "A", pero sí conoce cómo trabajar con el número hexadecimal 65, representación de "A".
Es en esta última etapa donde está el problema: las codificaciones  disponibles por razones históricas no son las mismas, por lo que en un  charset determinado, la letra "A" podría estar representada por el  número 190 y en otro por el 65.

Volviendo un poco a la realidad actual, hoy en día tenemos 2 codificaciones que son las más populares: la ISO-8859-1 y UTF-8. Aunque  en sus primeros 127 caracteres son iguales, a partir de ahí la cosa cambia, aunque no mucho.
Sin embargo, para la desgracia de los hispano-hablantes, cada letra que nos hace distinto al inglés, tiene una codificación distinta. Esto  quiere decir que justamente las letras con tildes son aquellas que  confundiéndolos de codificación, no vamos a poder entender y nos  aparecen todo tipo de caracteres extraños, un signo de pregunta en caso  de querer imprimir la letra "á" de ISO-8859-1 en una codificación UTF-8 y dos símbolos extraños (dos debido a que UTF-8 guarda la letra "á" en  dos bytes en vez de uno) en caso de querer ver "á" codificado en UTF-8  en una codificación ISO-8859-1. Suena un poco enredado, pero tiene su  lógica.


## Empezando a solucionar el problema


Desde que comprendí el esquema de trabajo, es que empecé a ser  constante, y es lo más importante sin duda. Sucede que las  codificaciones están en todos lados: desde el archivo mismo con el que  uno trabaja hasta la consulta que la DB devuelve.
Para esto, primero debemos aclarar con cuál charset nos quedamos. Por  opción personal, prefiero UTF-8 debido a que es más nuevo y más compatible a nivel global, no importando el sistema operativo. Un Linux  instalado en China con sólo lo mínimo, es probable que no entienda la  codificación ISO-8859-1 que es la latinoamericana, pero sí va a tener UTF-8 instalada.

Partiendo por esto, es que debemos asegurarnos en primer lugar que los archivos que creemos estén codificadas bajo UTF-8. En Linux, el  cambio es fácil de hacer gracias a iconv, basta hacer un
`iconv -f ISO-8859-1 -t UTF-8 -o salida.txt entrada.txt`
para convertir uno (o más) archivos desde ISO-8859-1 a UTF-8. En Windows el asunto se pone un poco más complejo: el único método que sé que  funciona es bajando [PSPad](http://www.pspad.com/es/) y haciendo la conversión desde dentro del programa. Puede que exista una  forma más fácil, pero no tengo ningún Windows a mano en este momento como para ponerme a averiguar.

Después de los archivos le toca a la base de datos. Ojo que en MySQL, la codificación UTF-8 no existe! Sólo existe utf8 (en minúsculas sin  raya) y por supuesto latin1, que sería el equivalente a ISO-8859-1. Obviamente existen muchas codificaciones más, las que [pueden ver aquí](http://dev.mysql.com/doc/refman/5.1/en/charset-charsets.html).
Por lo general a MySQL le importa poco y nada el charset con el cual guarda los datos, sólo le importa el charset con el que **entrega** los datos, así que con decirle que trate todos los datos salientes en  esa conexión como UTF-8 debería ser suficiente. ¿No sabe cómo? [Ocupe mi class](http://www.phpclasses.org/package/5812-PHP-MySQL-database-access-wrapper-using-MySQLi.html) ![:)](../wp-includes/images/smilies/icon_smile.gif)
Si estás ocupando otro motor, ya depende de cada uno. En PostGreSQL por  ejemplo, se define el charset cuando se crea la base de datos (échenle  un vistazo [a este thread en CHW](http://www.chw.net/foro/bases-de-datos-f222/567361-miniguia-pasar-una-base-psql-de-sql_ascii-a-utf8.html) para saber cómo cambiar de uno a otro), mientras que en otros motores  la cosa puede ser un poco más difícil. De todas maneras, siempre debería  ser posible poder cambiar el charset.

Finalmente, está la salida hacia el navegador.
Aunque por lo general el navegador podrá averiguar por si solo el  charset del documento (Apache tomará el control), hay veces que tanto  Apache o el navegador no sabrán qué codificación aplicar. Para evitar  estos errores, se debe usar PHP para señalar qué charset se ocupará en  el documento, lo cual es algo que veremos en el próximo capítulo.


## Funciones útiles de PHP


Algunas funciones muy útiles de PHP son sin duda el poderoso [iconv()](http://www.php.net/manual/en/function.iconv.php) y en menor medida [utf8_encode()](http://www.php.net/manual/en/function.utf8-encode.php) y [utf8_decode()](http://www.php.net/manual/en/function.utf8-decode.php). También sirven de utilidad [htmlentities()](http://www.php.net/manual/en/function.htmlentities.php) (que por supuesto tiene su inversa [html_entity_decode()](http://www.php.net/manual/en/function.html-entity-decode.php)) y su casi simil [htmlspecialchars()](http://www.php.net/manual/en/function.htmlspecialchars.php). Por supuesto que también es importante la función [header()](http://www.php.net/manual/en/function.header.php), ya que sin ella Apache asumirá el control, lo cual obviamente no es la idea.
Existen otras funciones que hacen cosas parecidas o que extienden las  funcionalidades, pero jamás las he ocupado y para casi cualquier  operación que se quiera hacer, con el uso de estas funciones uno está  más que bien.

Pero qué se puede hacer con estas funciones? Básicamente, convertir de un formato a otro.
Aunque claramente están explicadas en el manual, haré un pequeño resumen acerca de qué es lo que hacen estas funciones:

**iconv**: Convierte de un charset a otro arbitrariamente. Ejemplo de uso:
[php]$cadena = iconv('ISO-8859-1','UTF-8//TRANSLIT','cádéñá cón tíldés');[/php]
El primer parámetro es origen, segundo destino y le agregué TRANSLIT para que de esta manera, se "traduzca" la letra que no se conoce lo  mejor posible. Esto podría llegar a pasar con algunos caracteres extraños.

**utf8_encode** y **utf8_decode**: Respectivamente, son sinónimos para:
[php]iconv('ISO-8859-1','UTF-8//TRANSLIT','cádéñá cón tíldés');
iconv('UTF-8','ISO-8859-1//TRANSLIT','cádéñá cón tíldés');[/php]

**htmlentities**: Convierte todos los caracteres que  tienen alguna equivalencia en entidad a su respectiva entidad. Sugiero  leer la documentación oficial ya que es bien powa esta función.

**html_entity_decode**: Lo mismo que la anterior, pero al revés.

**htmlspecialchars**: Lo mismo que htmlentities pero sólo se aplica a los siguientes caracteres:
& => &amp;
" => &quot; (Sólo cuando ENT_NOQUOTES no se encuentra presente)
' => &#039; (Sólo cuando ENT_QUOTES está presente)
< => &lt;
> => &gt;

**header**: Permite mandar cabeceras al navegador. Lo  más genial de esta función es que nos permite enviar el charset de la  siguiente forma:
[php]define('CHARSET','UTF-8');
header('Content-type: text/html; charset='.CHARSET);[/php]



## Conclusiones


Para trabajar de una buena manera, recomiendo en muy resumidas cuentas:



Crea todos tus archivos bajo la codificación UTF-8
Mantén la base de datos (y por supuesto las tablas en el caso de MySQL) bajo la codificación UTF-8
Siempre señala en tu cabecera la codificación que irá a ocupar la página.

Espero que les haya servido el post. Intenté hacerlo lo más corto posible sin caer en hacerlo demasiado complicado.
