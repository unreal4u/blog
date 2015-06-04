---
comments: true
date: 2015-06-05 00:00:00+00:00
layout: post
slug: entendiendo-e-implementando-composer-en-tu-codigo-php
title: Entendiendo e implementando Composer en tu código PHP
categories:
- Pensamientos Personales
---

Este post fue originalmente publicado en [capa9.net](http://www.capa9.net/portada/entendiendo-e-implementando-composer-en-tu-codigo-php/). Este post sirve como una copia de respaldo para futura referencia personal.

Hola estimada comunidad! 

Tanto tiempo que no escribía para un sitio que no fuera mi cof cof [blog.unreal4u.com](http://blog.unreal4u.com) cof **blog** cof ! Pero como todo en la vida, ya era hora que me dedicara a escribir un par de líneas, contribuyendo de esa forma con mi granito de arena a la comunidad Open-Source, y por supuesto celebrando el nuevo [capa9.net](http://www.capa9.net/) (junto con todos los viejos estandartes)!

El tema de hoy será composer, y cómo esta aplicación ha cambiado por siempre el cómo se escribe código PHP, dando una madurez increíble al eco-sistema donde por primera vez ya nos podemos dedicar a escribir código profesional, modular y sobretodo, simple. Sí, lo leyeron bien: **SIMPLE**.

<!-- more -->

#### Disclaimer

Para efectos prácticos, recomiendo que si van a seguir los ejemplos de esta guía/tutorial, instalen una versión reciente de PHP, aunque todo el código funcionará con PHP 5.4, recomiendo que instalen PHP 5.6. Si son atrevidos, pueden instalar PHP 7. También (y sólo por esta guía) deberán tener instalada la extensión <code>mbstring</code>. En CentOS, es simplemente: 

{% highlight bash %}
yum install php-mbstring
{% endhighlight %}

En Ubuntu/Debian, será:
{% highlight bash %}
apt-get install php5-mbstring
{% endhighlight %}

No se olviden de reiniciar apache.

#### Barniz teórico

Antes que todo, me gustaría empezar aclarando un poco la historia detrás de y de cómo surgió composer como aplicación. 

Corría el año 1997 y PHP estaba recién despegando como aplicación. No existían muchos frameworks, la web estaba recién despegando y muchos amateurs encontraban chorra la idea de poder tener algo de dinamicidad en sus páginas. Esto siguió así por hartos años, hasta el año 2004 aproximadamente.   
En el año 2004, fue el primer remezón fuerte de PHP. Nació PHP 5 y con ello se abrió la posibilidad de poder programar orientado a objetos de forma completa. Aunque existía un soporte básico para OOP en PHP 4, la verdad es que era bastante primitivo y no permitía mucha jugabilidad.  
Esto fue el gran comienzo para que nacieran portales como PECL (2003), se empezaron a crear los primeros frameworks y CMS's. Entre otros, nació osCommerce (2003), WordPress (2003), Symphony y CakePHP (2005) y finalmente, el 2008 nació Zend Framework. Entre medio de todo este tumulto de frameworks y CMS's, nació también PHPUnit, que años más tarde cambiaría la forma de programar. (Más sobre eso más adelante).

El segundo gran cambio en la comunidad se dio en 2009, año en que nació PHP-FIG (PHP Framework Interoperability Group) que tiene como objetivo principal crear un solo estándar universal de programación y desarrollo en PHP. Algunos de estos estándares [PSR-1](http://www.php-fig.org/psr/psr-1/) y [PSR-2](http://www.php-fig.org/psr/psr-2/), ambos complementando el uno al otro) tienen que ver con el formato con el que se escribe cierto código (tabs o espacios, tags de apertura de PHP, camelCasing, nombres de archivos, etc) y otros tienen que ver con un tema importante y conflictivo dentro de la comunidad que es el autoloading. ([PSR-0](http://www.php-fig.org/psr/psr-0/) que luego fue deprecado en favor de [PSR-4](http://www.php-fig.org/psr/psr-4/)).  
Durante este año también se liberó al público PHP 5.3.0, que fue el primer release en incluir namespaces, que fue la base fundamental para el autoloading.

#### Autoloading: qué es y para qué sirve

Antes que naciera PHP-FIG, cada framework tenía su propio estándar. Los archivos estaban formateados de una forma, tenían su propia forma de implementar e inicializar estos archivos y en general era todo un gran desorden. En desmedro de por ejemplo Java, donde mediante su sistema de beans había resuelto todo de forma increíblemente limpio y fácil. 

El autoloading implementado por composer (y escrito por PHP-FIG) es entonces un sistema universal de carga de módulos que le permite a uno como programador no calentarse más la cabeza por tonteras como qué archivos incluir. Olvídense de <code>include()</code>, <code>require()</code>, <code>include_once()</code> o <code>require_once()</code>, eso pasó al pasado y hoy en día, si una aplicación está bien escrita (AKA ocupa PSR-0 ó PSR-4), sólo basta incluir el autoloader de composer y olvidarse del resto. Varios frameworks ya ocupan esto en la actualidad.

#### Qué es composer

Hasta el momento he mencionado 5 veces (6 si se cuenta el título del post) la palabra composer sin siquiera describir qué es. En términos simples, es un dependency manager escrito para PHP. Si han trabajado con CentOS, sin duda alguna conocerán yum. Si han trabajado con Debian/Ubuntu, conocerán apt-get. De la misma forma, composer vendría siendo el yum o apt-get de PHP. Éste les organizará los paquetes necesarios que requieran, incluyendo las dependencias que éste tenga. De esta forma, si alguno de los paquetes que uds bajen tiene otra dependencia, composer la bajará también. Además, como un pequeño bonus, pueden revisar por ciertos módulos adicionales antes de proseguir con la instalación. 

Adicionalmente, composer viene de la mano con una página web, que sirve como información del repositorio completo. Esta página es: [packagist.org](https://packagist.org/). En esta página, pueden consultar releases, revisar información general y muchos otros. Un ejemplo? Revisen mi class: [https://packagist.org/packages/unreal4u/string-operations](https://packagist.org/packages/unreal4u/string-operations).

#### Instalando composer

Instalar composer es increíblemente fácil. Sólo tienen que seguir las instrucciones en [getcomposer.org](https://getcomposer.org/download/), que les copy-pastearé a continuación: 

{% highlight bash %}
curl -sS https://getcomposer.org/installer | php
{% endhighlight %}

Con eso les quedará composer instalado en la carpeta donde estén. Si quieren que quede global, sólo tendrán que mover composer.phar a <code>/usr/bin/</code> u otra ubicación que ustedes estimen convenientes. Para efectos prácticos, supondré que eso hicieron. 

#### Aprendiendo lo básico de composer

Composer se guía por estándares. De esta forma, se supondrá que corren un VCS (Version Control System) y que al menos trabajen con dos sistemas: uno en producción y otro para testear los cambios antes de subirlos a producción. 

Uno de los primeros comandos es:

{% highlight bash %}
composer-phar self-update
{% endhighlight %}

Que lo que hará es actualizar composer mismo. Luego de esto, viene la parte divertida. 

Para trabajar con composer, se debe hacer mediante un archivo json que estará ubicada en la raíz del sistema que estamos desarrollando. Veamos el contenido de este archivo: 

{% highlight json %}
{
  "require" : {
    "php" : ">=5.4.0",
    "unreal4u/string-operations" : "@stable",
    "zendframework/zend-validator" : "@stable"
  }
}
{% endhighlight %}

Esto lo deben guardar como composer.json. Luego, en la misma carpeta donde dejaron este archivo, ejecutan lo siguiente: 

{% highlight bash %}
composer.phar install
{% endhighlight %}

De forma casi mágica, se empezarán a bajar paquetes. El primero será <code>unreal4u/string-operations</code>, que es una librería que escribí yo que sirve para trabajar con strings en unicode, y el segundo es <code>zendframework/zend-validator</code>, que sirve entre otros para validar que un número de tarjeta de crédito efectivamente sea válido. Este a su vez tiene un requerimiento a otro paquete llamado <code>zendframework/zend-stdlib</code>. 

Por último, el primer requerimiento en nuestro json dice que necesitamos por lo menos la versión 5.4.0 de PHP. 

#### Ocupando el código

Postearé un ejemplo de cómo sería ocupar zendValidator antes de la introducción de composer y cómo es ahora. Primero el antes: 

{% highlight php %}
<?php

include('../vendor/zendframework/zend-validator/ValidatorInterface.php');
include('../vendor/zendframework/zend-validator/Translator/TranslatorAwareInterface.php');
include('../vendor/zendframework/zend-validator/AbstractValidator.php');
include('../vendor/zendframework/zend-validator/CreditCard.php');

$ccValidator = new Zend\Validator\CreditCard();
if (!$ccValidator instanceof Zend\Validator\CreditCard) {
    echo 'Houston! We have a problem';
} else {
    echo 'ccValidator is a valid object';
}
echo PHP_EOL;
?>
{% endhighlight %}

Y el ahora: 

{% highlight php %}
<?php

include('../vendor/autoload.php');

$ccValidator = new Zend\Validator\CreditCard();
if (!$ccValidator instanceof Zend\Validator\CreditCard) {
    echo 'Houston! We have a problem';
} else {
    echo 'ccValidator is a valid object';
}
echo PHP_EOL;
?>
{% endhighlight %}

Como pueden ver, el ahora es mucho más simple. Obtenemos exactamente la misma funcionalidad, pero sin tener que preocuparnos manualmente de los requerimientos de por medio. 

#### Sistemas en producción y composer

Composer tiene algunos argumentos que uno puede pasar al comando principal para sistemas en producción. Esto hará que el autoloader sea aproximadamente un 55% más rápido que en su sistema de pruebas y además, mediante otro argumento, uno puede elegir no instalar las dependencias que son netamente para asistir en el desarrollo. Estos dos modificadores se pueden invocar de la siguiente manera: 

{% highlight bash %}
composer.phar install --no-dev --optimize-autoloader
{% endhighlight %}

Un ejemplo de composer.json diferenciado sería el siguiente:

{% highlight json %}
{
  "require" : {
    "php" : ">=5.3.0",
    "unreal4u/string-operations" : "@stable",
    "zendframework/zend-validator": "@stable"
  },
  "require-dev" : {
    "phpunit/phpunit" : "@stable"
  }
}
{% endhighlight %}

Con <code>--no-dev</code>, no se instalará el paquete PHPUnit y sus dependencias, esto no debería ser necesario en un servidor en producción.  
De la misma forma, lo que hago yo es siempre generar una salida optimizada aún en mi sistema de desarrollo, más aún cuando ya he terminado de trabajar con composer y ya necesito trabajar en código propio, ya que esto se nota en el rendimiento, sobretodo si se ocupan muchas classes.

#### Por qué utilizar composer?

Si bien es cierto pueden bajar cada paquete por separado e instalarlos en su máquina, hay un par de problemas que vendrán a futuro con el cual tendrán que lidiar: 

* Updates: Un update será cosa de correr un <code>composer.phar update</code>. Con esto, se actualizarán todos los paquetes a la última versión. No más buscar, no más copiar y pegar archivos, no más nada. 

* Consistencia: Cómo aseguran que todo el mundo ocupe exactamente la misma versión? Con composer, pueden bloquear cierta versión para asegurar que todo el mundo ocupa la misma versión hasta que uds estimen convenientes el upgrade.

* Rollback: Qué pasa si acaban de actualizar y resulta que tiene un bug que no permite que la página cargue adecuadamente? La forma más rápida de volver al pasado es editar su composer.json y correr un update o install. No más buscar y copiar o pegar. Cualquier versión que haya salido al público será siempre accesible y siempre se podrá instalar.

* Cantidad de código: La gran mayoría de los paquetes en packagist son releases oficiales, esto quiere decir que una gran parte de los archivos necesarios en desarrollo no están incluidos, lo cual ahorra tiempo y sobretodo recursos. Nadie más que el/los autores del paquete saben bien qué cosas dejar afuera. Para qué hacer el trabajo uno si otros ya lo han hecho por ti?

* Calidad: La gran mayoría de los paquetes en packagist son probados y testeados por miles o millones de personas. No te gusta un paquete? Puedes buscar otro que sea más/menos popular y ocupar ese. En vez de [PHPMailer](https://packagist.org/packages/phpmailer/phpmailer) puedes ocupar [SwiftMailer](https://packagist.org/packages/swiftmailer/swiftmailer) [Zend Mail](https://packagist.org/packages/zendframework/zend-mail).

#### Qué **NO** hacer?

El principal y más importante de los NO-DO, es **NO modificar los paquetes** que bajen. Si requieren funcionalidad extra que no está presente en el paquete que utilizan, extiendan la class y la implementan. Si se animan, creen un pull request y capaz que sea incorporado en la próxima versión del software que ocupen. En la empresa que trabajo lo hemos hecho así con un par de paquetes y hemos tenido éxito.

Otras cosas que no es recomendable hacer, es poner los mismos archivos que uno tiene en su propio PC en el servidor de producción. Composer tiene la gran ventaja de tener algunos parámetros especiales para máquinas en producción, de forma que lo que incluyan en su sección de "require-dev" no se instalará en el servidor de producción. Además, y como ya les comentabe, existe otro argumento que producirá un auto-loader optimizado, especial para sistemas en producción que acelerará el autoloading en aproximadamente un 55%.

#### Palabras al cierre

PHP es un lenguaje de programación que ha crecido harto con los años. Originalmente pensado como un lenguaje para poder llevar la cuenta del número de visitantes en tu homepage, ha crecido para servir como base a la segunda página web del mundo más grande: facebook. Si bien es cierto facebook hoy en día ocupa hack, el 99% del código PHP debería funcionar en hack. Además, con la liberación del futuro PHP7, se prometen bastantes mejoras en rendimiento, que dependiendo del caso llegarían a ser del 96%. Teniendo en consideración que PHP 5.4 es un 75% más rápido que PHP 5.3 y que PHP 5.5 es un 20% más rápido que PHP 5.4, un incremento de un 96% es casi una locura. Según las pruebas preliminares, PHP7 llegaría a ser tan rápido y en algunos casos más rápido que hack. (Como ejemplo: una página en WordPress es un 0.79% más rápido en hack, mientras que Drupal 8 llegaría a ser un 25% más rápido en PHP7 que en hack).

Combinando esto con la gran cantidad de tests hechos y escritos para PHP, es hoy en día increíblemente simple actualizar de versión. Eso sí, las versiones mayores siempre tendrán BC (backwards compatibility breakage) así que antes de actualizar sus sistemas en producción, se recomienda testear su software en una máquina de tests!

Si quieren bajar el código ocupado en este post, [lo pueden hacer desde Github](https://github.com/unreal4u/composer-basic-usage).

#### Fuentes

[Coding quality and coding standards](https://speakerdeck.com/unreal4u/coding-quality-and-coding-standards)  
[El código fuente de esta entrada](https://github.com/unreal4u/composer-basic-usage)

