---
comments: true
date: 2009-12-20 03:01:26+00:00
layout: post
slug: micro-optimizaciones-en-php-desmitificadas
title: Micro-optimizaciones en PHP... desmitificadas!
wordpress_id: 175
categories:
- PHP
---

Cualquier blog que tenga que ver con PHP, HTML, CSS y tecnologías afines, **tiene** necesariamente un post que habla sobre las micro-optimizaciones en PHP. El único pero es que muchas veces estas micro-optimizaciones son tan ridículas que ni siquiera valen la pena mencionarlos y es ahí donde entra en juego las peleas hacia un lado u otro. Sin embargo, y como para destacar por sobre el resto, voy a hacer algo: cada optimización será probada y comprobada y ustedes; queridos lectores; podrán probarlos también.

Si además encuentran que una micro-optimización está mal hecha o que podría incluir alguno que sea nuevo, pueden proponerlo y se incluye, tan fácil como eso. 
¿Te interesó? Pues sigue leyendo :)

<!-- more -->

Lo cierto es que teniendo en cuenta solo algunas optimizaciones, podemos crear en su totalidad un sistema más rápido y mejor optimizado, sin sacrificar memoria, tiempo o código muy enredado para que otra persona o nosotros mismos en 6 meses más lo entienda.



## ¿Qué viene primero... El huevo o la gallina?



En realidad, el huevo o la gallina poco tienen que ver con este apartado, pero me pareció una bonita frase que quise incluir. Lo que sí viene primero son las prioridades: ¿queremos que nuestro sistema sea ultra-rápido, que sea totalmente entendible para <del>b</del>n00bs o bien que sea un sistema que no consuma recurso alguno? 
Personalmente, siempre he preferido irme por el lado de que el sistema primero que nada sea a prueba de errores, que además sea conservador en el uso de la memoria (reservar y llenar un pedazo de 250MB de RAM **toma tiempo**), que sea legible en su lectura (código fuente) y por último que sea lo más rápido posible teniendo en cuenta estas matices. 

Pero... vamos por el grueso del artículo. Por el momento, existen 2 pruebas realizadas: 


  1. (Página 2) - Revisión de echo, comillas simples y comillas dobles.


  2. (Página 3) - Comparación entre sizeof(), count() y su implicancia en los arreglos.


A medida que tenga tiempo voy a ir colocando más pruebas. Por supuesto, están invitados a postear las pruebas que quisieran que realizara. 





## Primera optimización: echo, comillas simples, comillas dobles



Nuestra primera prueba será una test sencillo, crearemos distintos números de elementos, los cuales tendrán: 


  * Primera: un simple echo a cada elemento con comillas dobles, aprovechando la interpretación de PHP


  * Segunda: un echo a cada elemento con comillas simples concatenando el contador


  * Tercera: una concatenación a una variable, la cual se imprimirá al finalizar el ciclo. Será con comillas dobles, aprovechando la interpretación de PHP


  * Cuarta: una concatenación a una variable, la cual se imprimiará al finalizar el ciclo. Será con comillas simples, concatenando


¿El código?
{% highlight php %}
<!--<?php
define('NMAX',200);

$t = time() + microtime();
for($i = 0; $i < NMAX; $i++) {
  echo "a$i";
}
$r[] = number_format((time()+microtime()) - $t,5,'.',',');

$t = time() + microtime();
for ($i = 0; $i < NMAX; $i++) {
  echo 'b'.$i;
}
$r[] = number_format((time()+microtime()) - $t,5,'.',',');

$t = time() + microtime();
$pantalla = '';
for ($i = 0; $i < NMAX; $i++) {
  $pantalla .= "c$i";
}
echo $pantalla;
$r[] = number_format((time()+microtime()) - $t,5,'.',',');

$t = time() + microtime();
$pantalla = '';
for ($i = 0; $i < NMAX; $i++) {
  $pantalla .= 'd'.$i;
}
echo $pantalla;
$r[] = number_format((time()+microtime()) - $t,5,'.',',');
?>-->;
Tiempos:
<ul><?php
unset($pantalla);
$i = 1;
foreach($r AS $a){
  echo '<li>El tiempo para caso '.$i.' = '.$a.' segundos</li>';
  $i++;
}
?></ul>
Máximo de memoria ocupada: <strong><?php echo round(memory_get_peak_usage() / 1024); ?></strong> KiB.<br>
N&uacute;mero de elementos: <strong><?php echo NMAX; ?></strong>.<br>
Revise el código fuente para la salida.

{% endhighlight %}

Tienen dos opciones: copy/paste o verlo en [vivo y en directo](http://blog.unreal4u.com/bench/comillas.php) (en este momento tiene 10 elementos, algo más terrenal que un millón de elementos xD)



### Resultados



Ok. Los resultados la verdad es que me sorprendieron bastante, aunque en general se asemejó bastante a lo que tenía en mente. Para los más estadísticos, aquí están los datos: 

![2009-12-19-u4u-0006](http://blog.unreal4u.com/wp-content/gallery/optimizacion/2009-12-19-u4u-0006.png)

De arriba podemos concluir: 


  1. Siempre y cuando los ítems a concatenar sean menos que 1.000, conviene mucho más el caso 4.


  2. Cuando los datos a concatenarse estén alrededor de 25.000, conviene mucho más ocupar el caso 1. 


  3. Casi siempre, conviene más el caso 4, a excepción de cuando los datos estén alrededor de los 25.000, donde es mejor el caso 1.


  4. Hasta que hayan alrededor de 50.000 concatenaciones, en cuanto a tiempo da exactamente lo mismo. Sin embargo, en y por arriba de esta cifra, los tiempos entre uno u otro se reducen drásticamente hasta llegar a arriba de un cuarto de segundo (que ya es apreciable).


  5. Cuando los datos a concatenarse se encuentren en el rango de los 1.000 a 24.999, se puede ocupar cualquiera de los 4 casos. Sin embargo, el caso 4 resulta casi siempre más rápido que el caso 1.

Por lo tanto, **en general resulta más rápido concatenar con comilla simple a una variable e imprimir esa variable al final**. 





## Contar elementos de un arreglo y el for



Esta prueba es bastante interesante, debido a que siempre se lee en distintos foros sobre la diferencia entre el count() y el sizeof() y su implicancia en velocidad. Veamos el código aplicado: 

{% highlight php %}
<?php
define('NMAX',10);
for($i = 0; $i < NMAX; $i++) $arr[] = 'asdfqwerty'.$i;

$t = time() + microtime();
for ($i = 0; $i < count($arr); $i++) $tmp = $arr[$i];
$r[] = number_format((time()+microtime()) - $t,7,'.',',');

$t = time() + microtime();
$numero = count($arr);
for ($i = 0; $i < $numero; $i++) $tmp = $arr[$i];
$r[] = number_format((time()+microtime()) - $t,7,'.',',');

$t = time() + microtime();
for ($i = 0; $i < sizeof($arr); $i++) $tmp = $arr[$i];
$r[] = number_format((time()+microtime()) - $t,7,'.',',');

$t = time() + microtime();
$numero = sizeof($arr);
for ($i = 0; $i < $numero; $i++) $tmp = $arr[$i];
$r[] = number_format((time()+microtime()) - $t,7,'.',',');

unset($arr,$numero,$t,$tmp);
?>Tiempos:<ul><?php
foreach($r AS $a) echo '<li>Tiempo caso 1: '.$a.'</li>';
?></ul>;
Máximo de memoria ocupada: <strong><?php echo round(memory_get_peak_usage() / 1024); ?></strong> KiB<br>
N&uacute;mero de elementos: <strong><?php echo NMAX; ?></strong>.
{% endhighlight %}
Nuevamente, existen dos opciones: copian y pegan el código para probarlo en sus equipos o bien ven [una prueba en vivo y en directo](http://blog.unreal4u.com/bench/sizeof.php) con 10 elementos.



### Resultados



Esta vez, y a diferencia de la prueba anterior, no hubieron muchas sorpresas, más que los que esperaba encontrar:
![2009-12-19-u4u-0008](http://blog.unreal4u.com/wp-content/gallery/optimizacion/2009-12-19-u4u-0008.png)

Las pruebas son reveladoras: por lo general, **es mucho mejor ocupar count() _antes_ del for**. La función sizeof si bien es cierto cumple exactamente el mismo cometido, es más lento que count() a medida que el tamaño del arreglo crece. Para arreglos enanos es mejor ocupar sizeof(), pero la diferencia es tan poca que es realmente despreciable. Mi consejo es que **siempre se ocupe count() para revisar tamaños de arreglos**. 





## Otras pruebas



A medida que pase el tiempo, iré colocando más tests, así que estén atentos. Por el momento, lo siento por lo poco, pero entre programar, verificar, anotar los resultados y sacar conclusiones se va harto tiempo, cosa que en estos momentos me está haciendo mucha pero mucha falta. 

Saludos !!
