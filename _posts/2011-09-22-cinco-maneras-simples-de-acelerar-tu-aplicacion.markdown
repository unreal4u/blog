---
author: admin
comments: true
date: 2011-09-22 20:39:22+00:00
layout: post
slug: cinco-maneras-simples-de-acelerar-tu-aplicacion
title: Cinco maneras simples de acelerar tu aplicación
wordpress_id: 494
categories:
- PHP
---

Estaba yo revisando mi twitter el otro día cuando me encontré con un artículo bien interesante acerca de cómo acelerar tu aplicación en PHP. De hecho, lo encontré tan bueno que decidí hacer el mismo listado, pero en español y poniéndole también de mi cosecha.



## Instalación de un Opcode Cache


En general, se puede acelerar mucho una aplicación hecha en PHP con un simple programita, el cual es tan bueno que será incluida dentro del core de PHP en su próxima release principal (5.4). El funcionamiento básico de este programa sigue un esquema bastante básico, aunque para entender eso primero deberemos entender el cómo funciona el ciclo de entrega de una página Web. Básicamente, el ciclo completo es el siguiente: 
<!-- more -->
![Figura 1](http://blog.unreal4u.com/wp-content/uploads/2011/04/fig01.gif)
Primero se pide la página, y esa petición le llega al WebServer. El mismo webserver, al detectar código PHP, se lo deriva a PHP, que revisa el código, lo parsea, lo compila y lo ejecuta, para entregar una salida. Por razones que desconozco, este ciclo se repite siempre, aunque el archivo no se haya modificado entremedio; o al menos; lo hará hasta la versión 5.4 de PHP, cuando se incluirá de forma predeterminada APC (Alternative PHP Cache). APC es además mantenida por varios desarrolladores de PHP, Facebook y Yahoo, así que comparado con las demás opciones es poco lo malo que se puede decir del mismo.

Lo que hace esta extensión es guardar una copia del script ya compilado, de forma que se evita algunos pasos innecesarios. Si el script fue modificado, entonces se compila nuevamente, pero hay que destacar que una vez que un sistema está estable, eso ocurrirá tarde mal y nunca: 
![Figura 2](http://blog.unreal4u.com/wp-content/uploads/2011/04/fig02.gif)

Instalar un Opcode Cache es definitivamente una de las mejores soluciones que se pueden implementar: por lo menos un incremento de velocidad de un 80% en cada request.



## Optimización de configuración de PHP


Hay diversas optimizaciones que se pueden hacer en la configuración de PHP, partiendo por las más básicas y también de forma más avanzada.
De las más básicas, son diversas las que no sólo incrementan la velocidad, pero también aumentan la seguridad: 
`register_globals`: Lo mejor es dejar desactivada esta opción, ya que aparte de tener que parsear todo el script para buscar las variables, es una falla de seguridad enorme.
`register_long_arrays`: Habilita o deshabilita las variables tipo $HTTP_*_VARS que ya no son válidas hace mucho tiempo. Se recomienda dejar desactivado. (Off)
`register_argc_argv`: También se recomienda dejarla en off. Crea los arreglos argc y argv cada vez que un script se ejecuta, algo totalmente innecesario en el 99% de los casos, y en aquellos casos donde sí es necesario (CLI) se crean automáticamente debido a que son obligatorios.
`auto_globals_jit`: Se recomienda dejar en "On". Inicializa las variables superglobales ENV y SERVER sólo cuando se necesitan en vez de siempre. Las tres funciones previas deberán estar todas desactivadas para que este parámetro tome efecto.
`magic_quotes_*`: Esta opción originalmente existía para limpiar las entradas a las bases de datos, pero la gran mayoría empezó a implementar sistemas propios de limpieza de datos que eran mucho más efectivas y que protegían incluso de las inyecciones más comunes de las cuales los magic_quotes no protegían y al final cayó en desuso. Es también importante para la velocidad debido a que no se tienen que parsear las cadenas antes de enviarlas a la base de datos.
`always_populate_raw_post_data`: Nunca ocupé esta opción y no tengo idea para qué sirve. Sólo sé que deja una copia de todo el POST en otro arreglo, un duplicado. Predeterminadamente, esta opción viene desactivada desde PHP4.2.3.
`output_buffering`: Esta opción debería dejarse siempre activado, ya que evita que con cada llamada a echo o print se devuelva algo al navegador. Con esta opción en cambio, se llena un buffer y recién cuando ese buffer se llena o el motor de PHP termina de procesar la página, se manda toda la información en el buffer.
`variables_order`: Esta opción es poco documentada lo cual es una pena, ya que permite desactivar cierto tipo de variables superglobales. Predeterminadamente, está ajustada en EGPCS (Environment, Get, Post, Cookie y Server) y perfectamente podemos desactivar una o más de una. La que siempre he visto que es seguro quitar, es la E de environment. Casi nunca he visto que ocupen esa variable superglobal. El incremento en velocidad se obtiene al no tener que parsear en cada request esta variable superglobal.
`date.timezone`: Esta directiva fue introducida a partir de PHP5.1 y es bastante útil colocarle un valor predeterminado ya que sino se producirá un error de tipo warning en cada request. Este error se escribe en el disco, haciendo más lento el acceso general sobretodo en un sitio con alta demanda, y además PHP hará una serie de requests al sistema operativo para averiguar el valor.
`short_open_tag` / `asp_tags`: Recomiendo encarecidamente dejarlas ambas en off. ¿Lo malo? No podrán iniciar un documento php con "implicit_flush`: Se recomienda dejar esta opción siempre en "off", ya que de lo contrario se ejecutará una llamada a "flush()" cada vez que se imprima un echo, un print o se haya enviado un bloque de información, algo totalmente innecesario en el 99% de los casos. (Y en aquellos casos donde sí es necesario, simplemente se hace una llamada manual a esta función).



## Controlar las llamadas a require e include


Por muy increíble que parezca, la siguiente regla es definitivamente una de las más importantes. Es increíble cómo una aplicación anda mucho más rápido al controlar bien el flujo de nuestra aplicación y no abusar de include_once() y require_once(), que lo único que hacen es revisar en la pila si el archivo ha sido incluido, si las funciones que aparecen en ese archivo ya fueron inicializados, si las classes que declaramos ya fueron declarados previamente y finalmente levantar un error (en el caso de un include o require simple) u omitir el error si hacemos la llamada con *_once(). 
Así que ya saben: ¡a revisar bien su flujo y que no haya ninguna llamada con *_once() que hacen nuestra aplicación mucho más lenta!
Además, algunas micro-optimizaciones incluyen el usar rutas absolutas para no buscar en todo nuestro `include_path` y tratar de mantener llamadas estáticas en vez de dinámicas para poder permitir al motor de PHP tener eso en cuenta antes de ejecutar y poder hacer una suerte de pre-fetching. (Ok, no ocurre exactamente así, pero mantengámoslo simple).



## Optimiza tu base de datos


Y llegamos al punto más crítico del artículo, el cual es tan extenso que perfectamente podría entrar en un post aparte.
Sólo mencionaré muy por encima las principales demoras en una base de datos:



	
  * Mal diseño de la base de datos. Definitivamente con esto podemos sepultar una aplicación al no tener un buen diseño pensando en el futuro en mente. Quizás hoy tenemos 100 registros, pero una vez que nuestra aplicación se lance, en pocos días podemos tener 250.000 registros en un día. Si un mes tiene 30 días, ¡tenemos que en dos meses podemos acumular 15.000.000 de registros! Índices y claves primarias cobrarán vital importancia.

	
  * Mal diseño de nuestras consultas. Muy similar a lo anterior, pero esta vez enfocada a la optimización de las consultas que le hacemos a la base de datos. Si hacemos un UNION de 15 tablas distintas, entonces anda preparanda esa tasa de café, porque la necesitarás. Lo mejor para solucionar este tipo de problemas es tomar un buen conector de base de datos y poner un EXPLAIN antes de nuestra consulta. (Ejemplo: si la consulta fuera `SELECT * FROM t1;` tenemos que escribir `EXPLAIN SELECT * FROM t1;`). Enseguida, MySQL, PostGreSQL u otro motor compatible con el estándar SQL99 nos explicará qué fue lo que hizo y en base a eso puedes reformular tu consulta.

	
  * Una conexión por consulta: He visto que hay gente que se conecta a la base de datos, hace una consulta y luego se desconecta. Luego, en el mismo request, se conecta a la base de datos y luego se desconecta. Así 50 veces más. En mi experiencia a lo largo de los años es que si hay algo costoso en términos de base de datos es logearse y obtener una conexión. ¡**EVITA múltiples conexiones** y trata de establecer sólo una por request!





## Cachear datos


Otra de las más grandes optimizaciones que uno puede hacer, es ahorrarse consultas a la base de datos y jugar con la caché, ya sea generándola uno mismo u ocupando activamente APC u otro motor de caché (memcache, Wincache, etc). Eso si hay que tener en cuenta que a partir de la próxima versión mayor de PHP se incluirá predeterminadamente APC. 
Volviendo a nuestro tema, la gran diferencia entre estos dos métodos (generando uno mismo la caché vs ocupar APC) es que mientras que en el primero por lo general guardamos código HTML ya listo y generado, en el segundo simplemente guardamos datos de PHP en memoria compartida. Esto último tiene algunas ventajas ya que primero que nada no tenemos que leer un archivo ya que ya se encuentra en memoria. Lo otro es que perfectamente podemos guardar un arreglo y por supuesto podemos cambiar la apariencia de ese arreglo sin tener que necesariamente ejecutar la misma rutina para generar ese arreglo. En este sentido, supongamos un menú lateral cuya estructura base es la misma pero cuyo HTML va cambiando ya que va resaltando el menú actual donde estamos, que en el fondo es otro HTML. 
Claro, podríamos guardar estos datos por ejemplo en una sesión, pero eso de todas formas implica un llamado a archivo que es lo que queremos evitar.

Espero que les haya gustado esta entrega de optimización y micro-optimizaciones, sobre el cual [ya tenía un artículo previo](http://blog.unreal4u.com/2009/12/micro-optimizaciones-en-php-desmitificadas/) que hace mucho tiempo está en el blog y nadie pescó.

Fuente: [developerWorks](http://www.ibm.com/developerworks/opensource/library/os-5waystunelamp/index.html?cmp=dw&cpb=dwlin&ct=dwgra&cr=twitter&ccy=zz&csr=lambwam)
