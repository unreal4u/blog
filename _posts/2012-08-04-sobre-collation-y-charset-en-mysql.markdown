---
author: admin
comments: true
date: 2012-08-04 23:59:30+00:00
layout: post
slug: sobre-collation-y-charset-en-mysql
title: Sobre COLLATION y CHARSET en MySQL
wordpress_id: 627
categories:
- Bases de Datos
- i18n/L10n
- Mundo Web
---

Hace bastante tiempo que tenía ganas de abordar varias cosas de MySQL, pero una de las más importantes (a mi juicio) es lo relativo a los COLLATIONs y los CHARSETs que tanto dolor de cabeza le pueden dar a aquellos de habla hispana. ¿Quién no ha tenido problemas con los tildes ([no confundir con los acentos](http://lenguayliteratura.org/hot/109/page_02.htm)) en alguna página? 

Y aunque este tema [ya fue tocado en un post previo](http://blog.unreal4u.com/2010/09/problemas-en-los-tildes-o-acentos/), éste será un poco más específico y aclaratorio sólo sobre estas dos propiedades de cada tabla y/o campo.
<!-- more -->



## Introducción


Desde prácticamente los inicios de MySQL es que siempre he tenido la curiosidad con respecto a qué significan estas cosas raras: al principio todo funcionaba mágicamente usando latin1, el cual también era el predeterminado de MySQL (así como también Apache y PHP). Sin embargo, los problemas empezaron cuando el mundo decidió migrar hacia UTF-8, y más específicamente cuando Apache o PHP (una de las dos, no me acuerdo bien cuál) decidió que de ahora en adelante el charset predeterminado sería este mismo. Aunque siempre existía la opción de aplicar [iconv()](http://www.php.net/manual/es/function.iconv.php), estaba más que claro que era un poco contra-productivo tener que aplicar esta función a cualquier salida hecha por la base de datos, así que en algún momento de la vida empecé a ocupar UTF-8 también en la base de datos.



## Partiendo por lo básico


Una de las primeras cosas que se deben saber es el cómo un CHARSET u otro guarda la información. Para ser muy breves y concisos, utf8 ocupa hasta 3 bytes en cada caracter, mientras que latin1 siempre ocupará 1 solo byte.

Aunque ninguno de los dos campos es obligatorio al crear una tabla, se recomienda que sí se haga no por ustedes, sino que si algún día cambian la configuración, tendrán una mezcla de charsets en sus bases de datos, el cual puede ser un parto detectar y cambiar.
Con respecto a esto mismo, si colocan un CHARSET, MySQL le asignará de forma automática un COLLATION que está asociado al CHARSET. La lista de estos dos la pueden averiguar con el siguiente SQL: 
[sql]
-- Mostrar los CHARSETs instalados:
SHOW CHARACTER SET;
-- Mostrar COLLATIONS instalados:
SHOW COLLATION;
[/sql]

El primero debería entregarles un listado de todos los CHARSET instalados y también debería mostrarles el COLLATION asociado. También les muestra el número de bytes máximo que ocupa cada CHARSET.
El segundo comando muestra todos los COLLATIONs instalados.

Por último, cabe destacar que podemos asignar COLLATIONs y CHARSETs; en orden de prioridad; a nivel de:




  * MySQL completo


  * Bases de datos


  * Tablas


  * Campos



Eso significa que si se asigna un CHARSET y COLLATION a nivel de MySQL, todas las bases de datos heredarán esta propiedad. A su vez, todas las tablas heredarán las propiedades de la base de datos y asimismo, todos los campos heredarán las propiedades predeterminadas de las tablas. En cualquiera de estos pasos podemos sobre-escribir el predeterminado con nuestro propio CHARSET y COLLATION.



## ¿Y qué significan entonces?


En palabras muy breves, una de las principales diferencias es que CHARSET hace referencia a cómo MySQL guarda internamente el dato y COLLATION es una manera de decirle cómo debe comparar el texto y/o ordenarlo. Para explicar bien este punto apliquemos algunos ejemplos: 
[sql]
-- Creamos la tabla:
CREATE TABLE IF NOT EXISTS collationTests (
    name01 CHAR(5) CHARSET utf8 COLLATE utf8_unicode_ci,
    name02 CHAR(5) CHARSET latin1 COLLATE latin1_general_cs,
    name03 CHAR(5) CHARSET ASCII COLLATE ascii_general_ci,
    name04 CHAR(5) CHARSET utf8 COLLATE utf8_bin,
    name05 CHAR(5) CHARSET latin1 COLLATE latin1_bin,
    name06 CHAR(5) CHARSET ASCII COLLATE ascii_bin,
) ENGINE=MyISAM;

-- Insertamos algunos datos:
INSERT INTO collationTests VALUES ('Ñandú','Ñandú','Nandu','Ñandú','Ñandú','Nandu');
[/sql]

Lo que hicimos arriba fue crear una tabla con diversos CHARSETs y luego insertamos una columna con el mismo dato, a método de comparación. Enseguida, aplicamos algunos SELECTs que es donde empieza la diversión total. 

Lo primero que cabe destacar es que el CHARSET ASCII no permite ingresar otra cosa que no esté en la tabla ASCII predeterminada, así que la letra "Ñ" y aquellas letras con tilde quedan absolutamente descartadas.

[sql]
SELECT LENGTH(name01) AS bL01, CHAR_LENGTH(name01) AS cL01 FROM collationTests;
SELECT LENGTH(name02) AS bL02, CHAR_LENGTH(name02) AS cL02 FROM collationTests;
SELECT LENGTH(name03) AS bL03, CHAR_LENGTH(name03) AS cL03 FROM collationTests;
[/sql]

La primera serie de consultas que vamos a realizar tiene que ver con el tamaño (en bytes) de cada campo relevante y su tamaño en número de caracteres. Hago esta aclaración ya que ambas funciones tienen esa distinción.

El resultado de esto es: 
[code]
bL01	cL01
7	    5
bL02	cL02
5	    5
bL03	cL03
5	    5
[/code]

Como vemos, en UTF-8 se guardaron 7 bytes de información (un byte extra en la letra "Ñ", otro byte extra en la letra "ú") pero el largo de cada cadena en cada caso es de 5 caracteres.

Aplicamos algunas consultas para ver las diferencias entre COLLATIONs:
[sql]
SELECT * FROM collationTests WHERE name01 LIKE 'N%';
SELECT * FROM collationTests WHERE name01 LIKE 'ñ%';
SELECT * FROM collationTests WHERE name01 LIKE 'Ñ%';
SELECT * FROM collationTests WHERE name04 LIKE 'N%';
SELECT * FROM collationTests WHERE name04 LIKE 'ñ%';
SELECT * FROM collationTests WHERE name04 LIKE 'Ñ%';
[/sql]

Las primeras 3 (`CHARSET utf8 COLLATE utf8_general_ci`) devolverán un registro cada uno, mientras que de las últimas 3 (`CHARSET utf8 COLLATE utf8_bin`) sólo el último devolverá un resultado positivo. 
Esto se debe a que la columna "name01" tiene COLLATION utf8-general-ci, que, entre otras cosas, considera como sinónimo la letra "N" y "Ñ", y además es case-insensitive (`utf8-general**-ci**`). Esto también se aplica a los tildes, de forma que si buscamos por ñandu (sin tilde) el resultado entregado será el mismo que si buscamos por "Ñandú". De igual forma, "ÑaÑdU" igual entregará un resultado positivo para "Ñandú". 
En las últimas 3 sin embargo, si no buscamos **exactamente** por lo ingresado en la base de datos no se devolverá ningún registro, así que cualquier cosa que no sea un match exacto de "Ñandú" simplemente se descartará.



## Ordenando resultados


Al principio del punto anterior dije que los COLLATION también servían para cambiar la manera en que ordenamos la información. Si nos fijamos en los COLLATION presentes para UTF-8, podemos apreciar de que existen 2 que llaman la atención: utf8_spanish_ci y utf8_spanish2_ci. 

La diferencia entre ambos es que la primera se ocupa para español moderno mientras que la segunda se aplica a español tradicional.
De esta forma, podemos ver que ambos incorporan el uso de la "Ñ" como una letra entre la "N" y la "O", de forma que si tenemos los siguientes registros:
[code]
Nicolás
Ñandú
Operación
[/code]
Ambos COLLATION ordenarán esos 2 registros de esa forma. Sin embargo, si tenemos los siguientes registros: 
[code]
Cruzada
Baño
Carlos
Dedo
Chile
[/code]

Una COLLATION utf8_spanish_ci ordenará los registros de una forma mientras que utf8_spanish2_ci la ordenará de otra, debido a que el español tradicional considera "ch" como una letra entre la "C" y la "D". Asimismo, considera el uso de la letra "LL" como una letra entre la "L" y "M". 

Si quieren una prueba de concepto, ejecuten lo siguiente: 
[sql]
CREATE TABLE IF NOT EXISTS spanishCollation (
    name01 VARCHAR(15) CHARSET utf8 COLLATE utf8_spanish_ci,
    name02 VARCHAR(15) CHARSET utf8 COLLATE utf8_spanish2_ci
) ENGINE=MyISAM;

INSERT INTO spanishCollation VALUES ('Baño', 'Baño'),('Carlos', 'Carlos'),('Cruzada', 'Cruzada'),('Chile', 'Chile'),('Llorar', 'Llorar'),('Lámina', 'Lámina'),('Loreto', 'Loreto'),('Dedo', 'Dedo');

SELECT * FROM spanishCollation ORDER BY name01;
SELECT * FROM spanishCollation ORDER BY name02;
[/sql]

Al ejecutar las consultas se podrá ver claramente la diferencia entre ambas formas de ordenar los resultados.



## Conclusiones


Aunque parece ser un tema menor, la verdad es que hay harto que pensar y decidir detrás de los CHARSETs y COLLATIONs. Por el lado del CHARSET, aunque parezca como mejor opción utilizar siempre UTF8, hay casos en que resulta totalmente inútil: si queremos guardar una cadena cuyo valor sabemos que no contendrá valores distintos del alfabeto, es mejor ocupar ASCII y de esa forma estar seguros que no se pueden ingresar caracteres inválidos. Por otro lado, si estamos seguros de que nuestra aplicación nunca jamás tendrá otro idioma que no sea español, también podemos utilizar latin1, aunque hay que tener cuidado en hacer los ajustes necesarios en todos aquellos lados donde podría haber una influencia de otro set de caracteres.

El tema del COLLATION sí es un poco más extenso ya que dependerá mucho del cómo se efectúen las búsquedas y también el idioma en que estemos trabajando. MySQL asigna utf8_general_ci como COLLATION predeterminado de UTF-8, pero éste presenta algunos problemas en hebreo y en algunas localizaciones de idiomas cirílicos; principalmente bielorruso, macedonio, serbio y ucraniano; así que para estos casos resulta mejor ocupar utf8_unicode_ci, la que tampoco está exenta de polémica ya que es más lento que utf8_general_ci. 

Como gran conclusión: traten de usar UTF-8 como CHARSET donde sea posible, y si quieren tener velocidad vayan por utf8_general_ci como COLLATION pero si quieren tener certeza de que todo está bien ordenado y que sea compatible con (casi) todos los idiomas del mundo ocupen utf8_unicode_ci.

Fuentes:
[code.openark.org](http://code.openark.org/blog/mysql/mysqls-character-sets-and-collations-demystified)
[dev MySQL](http://dev.mysql.com/doc/refman/5.0/en/charset-unicode-sets.html)
