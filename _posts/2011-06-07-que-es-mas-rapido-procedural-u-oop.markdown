---
comments: true
date: 2011-06-07 05:02:54+00:00
layout: post
slug: que-es-mas-rapido-procedural-u-oop
title: ¿Qué es más rápido? ¿Procedural u OOP?
wordpress_id: 548
categories:
- PHP
---

Una de las dudas más recurrentes y que tiene mucha antigüedad ya sea en foros o listas de correo es si es mejor ocupar procedural u OOP para programar en PHP, particularmente desde el punto de vista de uso de los recursos y la velocidad. Desde hace algunos días [que venimos discutiendo eso en CHW](http://www.chw.net/foro/webmasters-f91/896015-me-cago-en-php5-y-poo.html), y pensé que sería bueno expandirme un poco más en el blog.

Lo primero que se me pasó por la mente es que cómo era posible que procedural podía ser más rápido y ocupar menos recursos que la programación orientada a objetos, que la segunda era mejor en ambientes corporativos, que el puro hecho de ser encapsulado otorgaba mejor seguridad, portabilidad y mantenabilidad, pero luego me di cuenta que una cosa no tiene nada que ver con la otra: durante la investigación que hice del tema me di cuenta que en realidad estaba mezclando los conceptos. ¿Cuál es mejor? ¿Cuáles son las ventajas y desventajas de cada una? Averíguelo haciendo click en "leer más" (o algo así).

<!-- more -->


## Los datos duros:


En muy breves palabras, y casi como un resumen, les presento las ventajas y desventajas sobre cada estilo de programación:


### Ventajas de Procedural sobre OOP:


Es de más rápido desarrollo en proyectos chicos  
Consume menos memoria  
En general, mejor para proyectos chicos



### Ventajas de OOP sobre Procedural:


Es (mucho) más fácil de mantener  
Es más transportable  
Es encapsulado y por lo tanto, modular  
Genial para proyectos grandes o en aquellos casos donde se tenga que reocupar mucho el código



## Las explicaciones científicas


Durante mis investigaciones con respecto a este tema, hubo una cosa que me llamó bastante la atención: PHP con OOP **va a consumir más memoria**. Esto porque lo primero que se hace al hacer un include es leer todo el (o los) archivo(s), parsearlo(s) creando una copia del mismo, compilarlo(s) y finalmente ejecutarlo como un programa único. Hasta el momento ambas maneras de programación son iguales, pero la gran diferencia radica en el compilado: el motor de PHP carga muchos más archivos si el programa es OOP, ya que es prácticamente un mundo nuevo y como tal, tiene muchas más reglas y posibilidades que cubrir.

OOP de por sí implica más código (escrito) que unas funciones simples y por lo tanto, sólo por esto, el peso total se ve incrementado por este hecho. Supongamos que tenemos:


  * index.php -- 200bytes que realiza un include de:


  * hola.class.php -- 500bytes


  * chao.class.php -- 400bytes


En total, ese script ocupará: 1100bytes al leer las fuentes, más X-bytes adicionales al parsearlo, donde X es un número cercano a 1100. Además de esto, tiene que levantarse el server de PHP, que puede ir desde 30kbytes hasta el infinito y más allá dependiendo de qué funciones nativas estemos llamando, la complejidad de nuestro programa y otras variables desconocidas por este humilde servidor.

Explicando un poco las demás ventajas y desventajas -aunque se escapa un poco del alcance de este artículo- resulta natural que un programa programado con OOP sea más transportable que su simil en Procedural, debido a que una class es una colección de funciones, que por lo general van juntas en un solo archivo o bien son extendidas por otras. Las funciones en cambio, no se pueden llegar y sobreescribir en caso de ser necesario y tampoco ofrecen la seguridad de una class: cualquiera puede llegar y tomar una función y asignarle valores arbitrarios y de esta forma alterar el producto final. Sólo con estos dos puntos usted querido lector ya debería haberse dado cuenta de las demás diferencias, de lo contrario, [busque en Google por PHP Procedural vs OOP](http://www.google.com/#sclient=psy&hl=es&safe=off&site=&source=hp&q=php+procedural+vs+oop&aq=f&aqi=&aql=&oq=&pbx=1&bav=on.2,or.r_gc.r_pw.&fp=f588205d3dcb938b&biw=1195&bih=774). 



## ¿Y el rendimiento? ¿Es más lento OOP que Procedural?


No se puede hablar de que un script sea más rápido o más lento con OOP, y si es que hubiera alguna diferencia en tiempo, sería despreciable: eso depende netamente de la mano del programador y qué hace el script: una conexión a una base de datos va a demorar ya sea bajo OOP o procedural, lo mismo corre si estamos trabajando con archivos (disco es muuuuuuucho más lento que ram).

Para contrarrestar este punto, una mucha mejor solución sería instalar algo como APC que crea una versión compilada y la deja en caché, de forma que no se tenga que parsear y compilar en cada request, con eso aumentamos la eficiencia en cerca de un 80% y se disminuye drásticamente el uso de memoria al no tener que leer y parsear, ya sea para una programación basada en OOP o en Procedural. Ya si queremos ir más allá, habrá que instalar Hip-Hop for PHP, que crea una versión en C++ de nuestra aplicación y que acelera hasta en un 50% adicional a la instalación con APC, pero esto nuevamente depende de la mano del programador y qué hace el programa, aunque sí se podría hacer notar que OOP saca una leve ventaja comparada con Procedural, ya que C++ se beneficia más de este tipo de programación (Discutible, pero es lo que he leído).



## Conclusiones


Tanto uno como otro método pueden ser apropiados para nuestras necesidades, así que mi mejor consejo es: **mezclen**. Las cosas más generales y que más se repiten (conexión a base de datos, limpieza de consultas, etc) manéjenlas via classes y de esa forma, solucionar un bug en todas sus implementaciones se realiza de forma rápida e indolora. Además, se puede reutilizar de la forma más simple posible. 
Por el otro lado, métodos relativamente simples o únicos, ármenlas de forma procedural: son de rápido desarrollo, de fácil implementación y lo más probable es que las hagan una vez en la vida y de ahí no necesite mayor mantención. 
