---
comments: true
date: 2009-11-26 14:00:20+00:00
layout: post
slug: mi-primera-clase-publicada
title: Mi primera clase publicada
wordpress_id: 10
categories:
- Classes
- PHP
---

Hace ya bastante tiempo (cerca de 6 ó 7 meses) me hice casi adicto a revisar diariamente [phpclasses.org](http://www.phpclasses.org/): un sitio dedicado principalmente a la publicación de código fuente PHP con un requerimiento: todas deben ser classes, es decir, programado pensando en OOP. 

La verdad es que esto era todo un mundo nuevo para mí: si bien es cierto conocía el concepto detrás de, nunca me había aventurado a hacer algo yo mismo y para ser más sinceros: nunca me había dedicado al tema. 

<!-- more -->
Esto siguió así hasta que, ya cansado de la ignorancia, me puse a investigar un poco del tema, y para eso, nada mejor que hacer algo que me solucionara la vida de aquí a la eternidad: quería hacer una class que fuera fácil de ocupar (AKA que con una sola línea hiciera la consulta y retornara los resultados en un arreglo), y que aprovechara las herramientas que entregaba el nuevo conector de mysql para PHP: MySQLi (MySQL Improved). Necesitaba algo que funcionara de forma fácil, rápida y en todos lados. 
Este nuevo conector, según algunos bench que nunca llegué realmente a comprobar, es más rápida que el antiguo conector, y además está basado en OOP, lo cual facilita su trabajo y manipulación. Además incorpora un montón de cosas nuevas, como el soporte nativo a procedimientos almacenados, consultas parametrizadas y multiqueries. La verdad era que todo eso se veía muy bonito en la pantalla, pero de ahí a que realmente fuera así en la práctica ya era otro cuento totalmente distinto. 

Bueno, como dije, sonaba todo muy bonito, así que decidí ponerle de una buena vez manos a la obra y crearme mi propia class que pudiera conectarse a una DB, realizara una conexión singleton y ejecutara las consultas parametrizadas para olvidarme por siempre de mysql_real_escape_string y otros más. El único problema de esto era que no conocía nada de esto. 
Al final, entre intento e intento inútil, decidí mejor hacer una búsqueda en phpclasses.org para ver qué había cuando buscaba mysqli. Grande fue mi sorpresa cuando me encontré con una class que hacía justamente lo que estaba pidiendo: hacía una conexión singleton, era parametrizado y entregaba el resultado de la consulta en un arreglo, listo para trabajar con ella. Mertol Kasanan y su class llamada [Reduced MySQLi](http://www.phpclasses.org/browse/package/5191.html) hacía justamente lo que estaba pidiendo. Lo bajé y después de corregir algunas pifias chicas empecé a testearlo más en profundidad. 

Fue ahí donde me encontré con la sorpresa de que en realidad no se ajustaba 100% a lo que andaba buscando, pero por lo menos ya tenía una base con la cual trabajar. Cuento corto: empecé a trabajar en esa class y terminé poniendo hartas cosas más: 



	
  1. En vez de usar variables para conectarse a la DB, ahora lo hice con constantes: más fáciles de ocupar y no se pueden cambiar una vez que se declaran. (A menos que se un-declaren)

	
  2. Agregué estadísticas de uso en vivo y en directo, convenientemente llamadas LiveStats®, para así poder diagnosticar rápidamente cuánto tiempo se demoró cada consulta, cuántas se habían hecho y su uso en RAM.

	
  3. Validé el XML que se genera como log, y ocupé SimpleXML para eso.

	
  4. Implementé un módulo de caché el cual va generando archivos XML dependiendo de la consulta. Este archivo XML no se guarda ni se lee con SimpleXML debido a que tiene que ser rápido y para lograr esa rapidez, tuve que crearme esas funciones a mano.



Lo mejor de todo esto es que la licencia es BSD, así que pueden hacer lo que ustedes quieran con ella. Lo único que pido es que, en la medida de lo posible, se mencione el autor original y además se me comunique dónde o en qué proyectos se encuentra implementado, más que nada para conocer. 

Y lo más importante después de tanto bla bla: el link de descarga: 


# [Descarga](http://www.phpclasses.org/browse/package/5812.html)






No es por hacerme barra, pero [al más puro estilo Steve Jobs](http://www.chw.net/2009/11/steve-jobs-nos-ensena-a-ahorrarnos-millones-en-abogados/), le voy a decir a los creadores de Wordpress: 



<blockquote>Actualizen el conector de mysql por mi class. No es para tanto.

    unreal4u

    Enviado desde mi Blog</blockquote>
