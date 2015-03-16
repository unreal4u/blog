---
comments: true
date: 2012-09-30 23:17:23+00:00
layout: post
slug: links-interesantes-sep-2012
title: Links interesantes - Sep. 2012
wordpress_id: 686
categories:
- Mi mundo en noticias
- Mundo Web
- Pensamientos Personales
- PHP
---

Desde bastante tiempo que no hacía esta sección y lamentablemente mi navegador nuevamente se encuentra lleno de páginas donde hay cosas muy interesantes pero que no he tenido el tiempo de visitar o leer en su totalidad (o soy tan pajarón que si creo un bookmark lo olvidaré). La mejor forma que tengo de respaldar esta información es simplemente escribirlas en mi blog así que eso haré.
Los links mencionados no tienen ningún orden específico.

A continuación, los links más interesantes colección septiembre 2012: 
<!-- more -->
[¿Qué tan grandes son los arreglos en PHP realmente?](http://nikic.github.com/2011/12/12/How-big-are-PHP-arrays-really-Hint-BIG.html) Sin duda, el post más interesante de todos los links que postearé ya que se hizo realmente una muy buena investigación para aclarar esta duda y las conclusiones son objetivas. Lectura muy pero muy recomendada. De hecho, dejen de leer este post y vayan a leer ese.

[Cargar Javascript de forma asincrónica](http://friendlybit.com/js/lazy-loading-asyncronous-javascript/). Esta técnica es ideal para por ejemplo incluir una librería enana con el fin de poder dar soporte de javascript básico a la página, luego cargar la página en sí para finalmente cargar el resto del javascript con la página ya cargada de forma asincrónica y de esta forma, guardar ese javascript en la caché del navegador. Ideal para aplicaciones pesadas en javascript.

[Sorteando arreglos en PHP de la forma más rápida posible](http://phpeasy.co.uk/sorting-array-data-using-splmaxheap/). Si han trabajado (mucho) con arreglos entonces se habrán dado cuenta que una operación pesada como ordenar o sortear (son dos cosas distintas: ordenar es listar de una u otra forma y sortear es agrupar en base a algún valor) un arreglo gigante toma muchísimo tiempo y también mucha memoria. Lo que no todo el mundo sabe es que PHP desde hace algún tiempo que viene incluyendo librerías para hacer este tipo de operaciones de forma rápida y eficiente. 

[Trabajando con dateTime de PHP](http://philsturgeon.co.uk/blog/2012/08/why-php-datetime-rocks). Otra librería más que viene desde PHP 5.2.0 y que hasta el momento ha sido bien poco utilizada es aquella que tiene que ver con fechas y horas. Este post es una pequeña guía introductoria sobre cómo ocupar esta misma librería. Lectura totalmente recomendada.

[Cómo escribir tu propia extensión en C (Introducción)](http://devzone.zend.com/303/extension-writing-part-i-introduction-to-php-and-zend/). Esto es lo que me ha (literalmente) quitado el sueño estos días. Me estoy metiendo de nuevo a C pero esta vez ocupando PHP como "framework", para escribir de esa forma una extensión en C para PHP. Lectura también recomendada si deseas aprender desde lo más básico, todas las funciones están definidas en el código fuente y podrás ver con tus propios ojos qué tanto cuesta uno u otro función.

[Self vs this](http://stackoverflow.com/questions/151969/php-self-vs-this). Muy buena lectura en Stack Overflow con respecto a qué es mejor utilizar y en qué consiste cada uno. Viva Stack Overflow.

[Hacerse millonario con la iTunes Store es más difícil que ganar la lotería](http://penny-arcade.com/report/editorial-article/going-broke-with-success-how-an-app-with-200000-downloads-led-to-devel). Un artículo que aunque no tiene nada que ver con PHP, sí resulta bastante interesante para estudiar el comportamiento de este mercado.

[Top 6 de ataques de seguridad a PHP](http://www.ansoncheunghk.info/article/top-6-security-attacks-php). No se equivoquen, a mi me encanta PHP pero también me da mucha rabia que sea tan fácil de programar. Ese tipo de cosas permite que alguien que no sabe deje muchos hoyos de seguridad lo cual al final le hace un mal peor a PHP ya que es gracias a esto que otros puedan decir que PHP sufre de mucha inseguridad. Lean el artículo de arriba y protéganse.

[Cómo usar de mejor manera la captura de errores](http://www.developerdrive.com/2012/07/php-error-checking/). Aunque en este momento estoy escribiendo una entrada al respecto (lo más probable es que salga el otro mes), durante mi investigación di con esta página con información fácil de digerir, aunque algo incompleto. De todas formas, es una lectura recomendada.

[Cómo servir la cerveza correctamente](http://www.sabrosia.com/2012/08/como-servir-la-cerveza-correctamente/?utm_source=dlvr.it&utm_medium=twitter). Ok, no tiene nada que ver con PHP y en realidad tampoco con el mundo informático, pero todo ser humano que se crea del sexo masculino debería saber por obligación cómo servir una buena cerveza. Si dices ser hombre no dejes de visitar ese post. Si eres mujer, deja loco a tu macho con una cátedra en el tema más apasionante del mundo: servir cerveza.

[Cómo se podría ver PHP 5.5](http://nikic.github.com/2012/07/10/What-PHP-5-5-might-look-like.html). Hartas cosas interesantes, entre ellas la más útil diría que es que empty() ahora por fin aceptará funciones como argumento. (En este momento hacer `empty(hola_mundo());` dará error, eso supuestamente cambiará en PHP 5.5). De todas formas, hay como 20 cambios más que pueden valer la pena. Lectura recomendada también para saber de qué forma tenemos que programar para que en futuras actualizaciones nuestra aplicación no cause una pantalla blanca. (Aunque en ambiente de desarrollo sirve tener habilitado que muestre errores de tipo E_USER_DEPRECATED).

[Entendiendo el recolector de basura de PHP](http://phpmaster.com/better-understanding-phps-garbage-collection/). Tema personalmente apasionante y algo misterioso, este post aclara bastante el tema y por lo mismo es bastante bueno. 

[¿Sabían que PHP 5.4 es aproximadamente un 20% más rápido que sus antecesores?](http://www.phpclasses.org/blog/post/187-The-Secret-PHP-Optimization-of-version-54.html) Esto se debe a la introducción (bastante silenciosa por lo demás) de un compilador JIT que -entre otras cosas- revisa el tipo de datos que una función devuelve, ahorrándole al motor mismo de PHP mucho trabajo. 

[Extensiones de CSS en Mozilla](https://developer.mozilla.org/en-US/docs/CSS/CSS_Reference/Mozilla_Extensions). Con la introducción de HTML5 y más particularmente CSS3, cada browser tiene su propia forma de manejar contenido avanzado. Pero... ¿qué cosas soporta cada browser? Mozilla hizo este listado completo de propiedades que soporta, si usan muchas características avanzadas de CSS3, entonces vale la pena echarle un vistazo.

[Twitter de phpdeveloper](https://twitter.com/phpdeveloper). Estuve en la charla que hizo hace poco el creador de este sitio web, y aunque conocía el sitio hace tiempo, no había tenido la oportunidad de conocer al cerebro detrás de. La idea de phpdeveloper es postear noticias interesantes en el mundo de PHP, editadas siempre personalmente por su dueño. Material interesante diario garantizado. De hecho, el 40% de los links de este post provienen de ese sitio xD.

[Tour de las opciones más comunes/confusas de php.ini](http://phpmaster.com/a-tour-of-php-ini/). En este tour (bastante básico a mi parecer) se tocan varios puntos importantes acerca de la configuración de php.ini. El post es útil para saber un poco más acerca de varias opciones de PHP pero tampoco es una guía definitiva. Si les gustó el material del link, [quizás deban también darse una vuelta por este post](http://blog.unreal4u.com/2010/08/configuracion-de-php-testing-vs-produccion-vs-hosting/) donde hablo de la diferenciación en configuración de PHP testing vs PHP producción.

[PHP, sesiones, __sleep y excepciones](http://www.phpdeveloper.org/news/17738?utm_source=twitterfeed&utm_medium=twitter). Otro post digno de rescatar debido a la gran investigación que se tuvo que hacer para llegar a la raíz del problema. Aunque no exactamente el mismo sí tuve un problema parecido hace tiempo y este post me dio algunas pistas de dónde ir.

[Comandos útiles para ser ocupados con Mac OS X](https://github.com/mathiasbynens/dotfiles/blob/master/.osx). Un montón de comandos que hacen diversas cosas para Mac OS X. Sirve de vez en cuando.

[Traits en PHP 5.4](http://php.refulz.com/traits-in-php-5-4-why-we-need-traits/). Aunque al principio no entendía la funcionalidad detrás de, después de leer este post me quedó bastante más claro el tema y por lo menos puedo decir más o menos para qué sirve. Todavía no lo he ocupado, pero cuando lo haré, este post será un buen punto de partida.

[Explicando ataques XSS](http://phpmaster.com/php-security-cross-site-scripting-attacks-xss/). Excelente post acerca de qué es y cómo evitar ataques XSS. Lectura más que recomendada.

[phpMyAdmin vulnerado en uno de los servidores de sourceforge](http://arstechnica.com/security/2012/09/questions-abound-as-malicious-phpmyadmin-backdoor-found-on-sourceforge-site/). La versión de phpMyAdmin afectada fue sólo en un servidor koreano del servicio de hosting gratuito de proyectos y sólo alrededor de 400 downloads fueron hechos con esa versión infectada. Una muestra más de la eficiencia del mundo del open source.

[Comparando OpenDDR y WURFL](http://www.samaxes.com/2012/06/comparing-openddr-to-wurfl/). Hasta hace poco, WURFL era la mejor base de datos disponible que aparte de listar marcas y modelos de casi cualquier dispositivo móvil que existe en el mundo sólo a partir de su user-agent string, también tenía mucha más información adicional. Lamentablemente, este año se realizó un cambio en las políticas de la empresa que está detrás de esta base de datos y decidieron empezar a cobrar por el servicio. Aparecieron algunos forks del proyecto y OpenDDR hasta el momento es el más exitoso. Los pro y los contras de cada uno aparece en el link de arriba.

[Usando LESS](http://css-tricks.com/using-less-as-a-live-css-engine/). Esta librería hace CSS mucho más potente y mucho más amigable con nosotros, los programadores. Una buena apuesta que (debo decir) ya lleva bastante tiempo con nosotros pero no ha despegado. Así que hagamos un poco de difusión.

[Compilando LESS en PHP](http://leafo.net/lessphp/). Por supuesto que una tan buena idea como LESS tenía que ser implementado en PHP. Así nació este compilador y de esta forma, crear documentos en LESS debería ser menos complejo ya que con sólo algunas líneas, este compilador hará todo el trabajo pesado por nosotros.
