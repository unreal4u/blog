---
comments: true
date: 2011-02-15 16:00:49+00:00
layout: post
slug: el-problema-del-microsegundo-en-mysql
title: El problema del microsegundo en MySQL
wordpress_id: 505
categories:
- Bases de Datos
- Pensamientos Personales
---

Es increíble cómo hoy se celebran 6 años [desde que fue ingresado un feature request a MySQL Bugs](http://bugs.mysql.com/bug.php?id=8523) pidiendo que se implemente una función que de seguro es importante para muchos: los microsegundos en TIMESTAMP y otros relacionados. 

Si bien es cierto que por lo general no se necesita tanta precisión, de repente es necesario, ya sea para propósitos de debug o actividad en el sistema, considerando que es un sistema que ocupan muchas personas.
Imaginemos por un momento que administramos Facebook y ocurre lo peor que te puede pasar: uno de los balanceadores de carga entiende mal un request y arma un castillo de naipes sobrecargando paulatinamente cada uno de los servidores. 
<!-- more -->
Enseguida vienen las preguntas: ¿Es un bug? ¿De dónde viene? ¿Fue provocado por algún usuario? ¿Por cuál? En estos casos críticos es necesario conocer en qué momento exacto las cosas comenzaron a fallar, ya que un segundo es un espacio de tiempo demasiado amplio: es posible que caigan 2 o más servidores en ese mismo segundo y hay que encontrar el origen, aislarlo, arreglarlo y asegurarse de que no vuelva a pasar, pero para eso, se necesita saber qué usuario hizo qué cosa como para que empezaran a caer los naipes.

Algo parecido me pasó ayer con la aplicación de la empresa: apareció un error en el log del sistema. Todos los errores de PHP los capturo y los guardo en la base de datos, aunque muchas veces no tengo idea de a quién le salió el error debido a que todavía no depuro mucho la parte que es la encargada de revisar la sesión anteriormente existente y rescatar la información desde ahí. Hasta el momento tampoco me había preocupado mucho, ya que se logea la hora en que se produjo el error y además se logea la actividad del usuario: si al usuario en cuestión le apareció un error, voy a poder filtrar por la hora en que se produjo el error si fue él o no.

Sucede que por esas cosas del destino, revisando a quién le podría haber salido el error, me di cuenta que al filtrar por la hora habían dos posibles candidatos, momento en el cual me di cuenta que se hizo posible mi worst-case scenario: dos usuarios distintos que visitaron la **misma página** en el **mismo segundo**. ¿Quién había provocado el error?
Para complicar las cosas aún más, esos dos clicks no eran los únicos: también había una redirección de parte de uno de los usuarios, lo cual puede significar varias cosas. En total, para ese segundo en particular, ahora tenía 5 registros: 2 actividades de usuarios distintos, una redirección y el landing page de esa redirección, además obviamente del error. Para agregarle más enredo, no se sabe bien en qué orden ocurrieron las cosas debido a que MySQL internamente ordena las cosas de acuerdo a su propio criterio y no de acuerdo al microsegundo en el cual ocurrieron las cosas. (Para optimizar esa tabla que presenta una altísima actividad [para hacerse una idea: son 100 usuarios activos + 50 vendedores de campo que continuamente están ingresando ventas, revisando flujo de caja, revisando las cuentas de la empresa, viendo logística y un montón de etcétera], no tiene un simple id_actividad AUTO_INCREMENT asociada, sino una clave primaria compuesta entre varios campos importantes, entre ellas la hora).



## Ok, mucho blabla, ¿qué puedo hacer para solucionar esto?


Lamentablemente, y como una solución "óptima" no mucho. Existen algunas soluciones pero ninguna es efectiva debido a que el origen de ellas no es en MySQL mismo, sino que el origen se genera afuera de la base de datos, lo cual supone unas cuantas complicaciones que ya veremos. Pero veamos primero cómo podemos almacenar microsegundos. 

Llegué a dos potenciales soluciones, de la menos a la más recomendada son:

- Lo primero es tener una tabla donde la fecha que guardaremos se almacene en formato UNIX_TIMESTAMP como un bigint o un numeric (decimal) con la cantidad de dígitos "microsegundianos" que queramos almacenar. Desventajas? Al guardar como un número se pierden todas las ventajas de consultas un poco más complejas, como aquellas que incluyen MONTH(), DAY() y demases. Tampoco se pueden calcular rangos de fechas y otro montón de funciones más. Al menos para mi, se pierde demasiado comparado a la poca ganancia de los microsegundos.

- La segunda solución consiste en guardar la fecha en dos campos por separado: la primera siendo un campo de tipo TIMESTAMP y la segunda siendo un INT del largo que deseemos. Supongamos que queremos guardar 6 dígitos significativos, por lo tanto nuestro rango iría de 0 a 999.999, un número que alcanza a ser guardado en un MEDIUMINT sea UNSIGNED o no. (Por favor tened [esta página como referencia](http://dev.mysql.com/doc/refman/5.0/en/numeric-types.html)). Ahora, si queremos ordenar por microsegundo, en nuestro ORDER tendríamos que colocar: `ORDER BY UNIX_TIMESTAMP(campo_timestamp),campo_microsegundo`. ¿Fácil, cierto?



## Y vienen los problemas...


La segunda solución sería ideal si no fuera por un pequeño detalle: ¿de dónde podemos sacar el dato del microsegundo? Una cosa es segura: NO a través de MySQL. Prueben con las siguientes variantes: 

{% highlight sql %}
SELECT MICROSECOND(NOW());
SELECT MICROSECOND(CURRENT_TIMESTAMP);
SELECT EXTRACT(MICROSECOND FROM NOW()); 
SELECT DATE_FORMAT(NOW(),'%f'); 
{% endhighlight %}

¿Qué devuelve? Les simplifico la respuesta: 0 (Cero). En todos los casos. Punto. NO HAY forma de sacar el valor del microsegundo exacto en MySQL. Es un dato que siempre provendrá del exterior. 
Ahora bien, en sí eso no es mucho problema porque siempre nos queda la opción de generarlo con PHP. Con un poco de suerte PHP y MySQL estarán en el mismo servidor así que la hora y el microsegundo siempre estarán coordinados... ¿pero qué pasaría si están en máquinas separadas? O peor aún, ¿si entremedio le metemos un balanceador de carga para el webserver? 
Respuesta corta: termino con una ensalada de microsegundos que no corresponde a la realidad, ya que una máquina tendrá una hora distinta de otra y además esa hora difiere de la hora del servidor de base de datos. Además hay que tomar en cuenta que en promedio un servidor tiene un margen de error de 3 segundos por año (en el mejor de los casos, tuve que arreglar hace poco un server al cual no se le sincronizaba la hora hacía 9 meses y tenía un desfase de 574.473159 segundos, seguramente porque entremedio de esos 9 meses hubo una hibernación que Linux no considera como tiempo que pasa) es casi imposible que a nivel de microsegundos una fecha pueda ser certera.

En el escenario más probable, un sistema grande, con muchos usuarios y carga, teniendo un balanceador de carga y 2 personas visitando una misma página al mismo tiempo, es que se los mande a dos webservers distintos, dando como resultado, 2 horas (microsegundianamente visto) totalmente distintas, ya que una diferencia de hora de 0.05 segundos ya da resultados muy distintos.

Como conclusión... es que espero que MySQL, después de 6 años incorpore de una buena vez el microsegundo como parte de un campo TIMESTAMP, TIME o DATETIME. (En lo personal, que lo dejen dentro de TIME que sería la aproximación más exacta a lo que se quiere lograr). Y por mientras los usuarios "comunes" de MySQL simplemente tendremos que conformarnos con soluciones parches como el expresado arriba o bien cambiarnos a PostGreSQL que sí tiene soporte para este y muchos otros tipos de datos más, donde en temas de tipos de datos para trabajar con monedas o direcciones ip's están a años luz de MySQL. (No saben los dolores de cabeza que he tenido porque MySQL no me permite guardar 192.168.1.0/28 y tengo que generar y guardar el intervalo de forma manual).
