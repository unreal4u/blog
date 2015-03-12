---
author: admin
comments: true
date: 2011-03-15 04:25:32+00:00
layout: post
slug: sobre-convenciones-y-notaciones-hungara-camelcase-etc
title: Sobre convenciones y notaciones (húngara, CamelCase, etc)
wordpress_id: 503
categories:
- Pensamientos Personales
---

Cuando el otro día escribí [sobre PHPDocumentor](http://blog.unreal4u.com/2011/01/phpdocumentor-haciendo-una-buena-documentacion-excelente/), en los comentarios se mencionó que podría crear un post sobre las distintas notaciones que se utilizan comúnmente. 

Lo primero que debo mencionar es que existen pocas notaciones, la verdad es que yo sólo conocía dos, y grande fue mi sorpresa cuando me di cuenta que sólo existían cuatro. Sin embargo, no deja de llamarme la atención la gran cantidad de convenciones que existen alrededor de las notaciones, que por lo general es una convención en cada empresa, adaptado específicamente a sus necesidades y requerimientos.<!-- more -->

Lo primero por lo cual partir es establecer la definición de qué es una convención y qué es una notación. En muy pocas palabras, una convención es un conjunto de reglas que se aplican para lograr un consenso en la programación, de aplicar ciertas reglas de común entendimiento y de esta forma acelerar el desarrollo y lograr trabajar mejor en grupo. En ella se establecen por ejemplo qué reglas se aplican al nombramiento de las variables, de las constantes, de las funciones, de las classes, de los archivos, del árbol de directorios y un gran etc (que incluye también la notación a utilizar). En definitiva, **una convención engloba una notación**.

Una de las convenciones más comunes es aquella que dicta que las constantes deben ir completas en letras mayúsculas para diferenciarlas fácilmente de las variables. También es común encontrarse que los nombres de las tablas en la base de datos deben ir completamente en minúsculas y que la id principal en caso de tener debe incluir la palabra "id_" seguido del nombre de la tabla. (Y en bases de datos que lo permitan, el nombramiento de los índices debe ser "pk_*", "uk_*", "fk_*" para claves primarias, únicas y foráneas respectivamente). También he visto casos donde las tablas se ordenan con un sistema de 3 letras al inicio de cada una (prefix) de acuerdo a su función primordial. (Como por ejemplo: "sis_*" para todas las tablas que involucren directamente el sistema base, "bod_*" para todas las tablas que involucren movimiento de bodegas, "ven_*" para todas aquellas relacionadas con las ventas y así sucesivamente.
Volviendo al tema un poco más en general, en una convención también se establece si se ocupará algún sistema de control de versiones como GIT, SVN u otro, cada cuánto se subirá la información al servidor de producción y qué se hace en caso de desastre. Por lo general, para toda esta información se crea un documento o manual para consultarlo en caso de ser necesario, especialmente útil si hay 100 personas trabajando en un proyecto y de esa forma seguir un solo estilo único en toda la empresa.

Por otro lado, también se debe elegir el tipo de notación a ocuparse, entre las cuales existen: 



## Notación C


Es la menos conocida de los cuatro. Consiste en que básicamente los espacios se cambian por un guión bajo. Esto resultaba útil durante los años 60 donde podía pasar que escribir una mayúscula era más lento que escribir un guión bajo. Lo otro que llama la atención de este tipo de notación es que todo se escribe en letras minúsculas.
Se ocupó mucho en C y C++, aunque actualmente está en desuso. Personalmente, lo ocupo para elementos en HTML, ya sea para señalar ids de elementos, classes y otros. (Ejemplo: `<p id="parrafo_principal" class="adorno_adecuado">`).



## Notación posicional


Otra notación bastante poco conocida hoy en día (pero que en sus tiempos fue famosa) es ésta que se ocupaba primordialmente en ambientes limitado por el famoso 8.3 (el que sea old-school va a saber al tiro, el que no, que investigue. Pista: DOS) que también se aplicaba a algunos lenguajes de programación.
Esta restricción también se aplicaba a algunos lenguajes de programación. De esta forma, (y el único ejemplo que pude encontrar en la red), el nombre de una variable sería LCCIIL01, donde LC sería la aplicación, C para denostar Cobol, IIL para señalar el sub-proceso y 01 era una secuencia.



## Notación CamelCase


Es quizás la más famosa y la que, aparte de los lenguajes de programación, también se le he visto fuera de estas. Existen en principio dos variantes de esta notación, la UpperCamelCase y la lowerCamelCase. El que tiene un ojo agudo va a conocer la diferencia de ambas sólo con leer el nombre mediante la cual se diferencian, pero para el ojo menos experto le diré que la principal diferencia entre ambas es que en el caso del upper la primera letra siempre se escribirá en mayúscula mientras que en la segunda la primera palabra siempre será entera en minúsculas.



## Notación Húngara


El inventor de esta notación ([Charles Simonyi](http://es.wikipedia.org/wiki/Charles_Simonyi)) nació en Hungría, por eso el nombre. Aunque personalmente debo decir que deberían haberlo llamado notación Simonyi ya que el caballero mencionado es demasiado seco, vean el link de arriba si no me creen.

En fin, volviendo al tema, la notación húngara está basada en parte en CamelCase, específicamente la lowerCamelCase. En su versión más básica, tenemos varios tipos de datos los que reciben una letra. De esa forma, un string recibirá la letra s, un int la letra i, un decimal la letra d. Un arreglo recibirá por supuesto la letra a. 
Teniendo eso en cuenta, y combinándola con la CamelCase no resulta difícil pensar en qué sigue: Un nombre cualquiera para nuestra variable siguiendo las reglas del CamelCase.

De esta forma, podemos tener: 
aIndicadorResultados
iContador
sPrimerNombre

Y así se continúa. De esta forma, con sólo un miro podemos saber de qué tipo es nuestra variable, y mediante CamelCase (asignándole un nombre significativo) podremos saber cuál es su función, lo cual encuentro ideal en un lenguaje como PHP donde una misma variable puede ser un float o un bit al momento siguiente.

Para finalizar, cabe destacar que los 3 tipos mencionados no son los únicos, para ver los restantes, favor visitar la [nunca mal ponderada wikipedia](http://es.wikipedia.org/wiki/Notaci%C3%B3n_h%C3%BAngara#Situaci.C3.B3n_actual) donde se explica con muchos más ejemplos y más profundamente el uso de esta notación.

Espero que les haya servido el artículo.
