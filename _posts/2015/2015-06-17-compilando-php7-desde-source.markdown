---
comments: true
date: 2015-06-17 00:00:00+00:00
layout: post
slug: compilando-php7-desde-source
title: Compilando PHP7 desde source
categories:
- PHP
---

Que quede una cosa clara desde el principio! **Yo no soy un fan de compilar software**! Encuentro que hoy en día, compilar ya es cosa del pasado,
a menos que quieras probar software que todavía no está listo para funcionar en producción. Como tal, quise probar qué tal andaba PHP7 con el
código de la empresa, a raíz de las tantas maravillas que se ha dejado entrever de esta nueva versión.

Algunas de estas maravillas son que incluyeron PHPNG (PHP Next Generation), que mejora el uso de recursos (su aplicación ocupará MUCHO menos RAM),
y ahora también se activó un JIT Engine (que aceleraría las llamadas recurrentes a funciones y métodos). Entre esas dos mejoras, deberían ver un
incremento de velocidad del orden de **25% a 150% comparado con PHP 5.6**, dependiendo del caso obviamente.

Esto quiere decir básicamente que en una instalación default de WordPress, la cantidad de instrucciones de CPU se redujeron de 9.413.106.833 a
2.483.106.468 y que el tiempo que ocupa generar 1000 veces el homepage se redujo de 26.756 segundos a 11.081 segundos (Como pueden
[verificar acá](https://docs.google.com/spreadsheets/d/1qW0avj2eRvPVxj_5V4BBNrOP1ULK7AaXTFsxcffFxT8/edit?pli=1#gid=1334306309)). En ambos casos,
es un incremento de velocidad importante y está casi a la par (y actualmente en muchos casos aún más rápido) que HHVM de Facebook.

<!-- more -->

Lo primero que necesitamos para empezar a trabajar es una instalación limpia y minimalista de CentOS 7. Activen ethernet (Predeterminadamente
viene desactivado, esto se hace en <code>/etc/sysconfig/network-scripts/ifcfg-enp0s3</code>, cambiando la opción <code>ONBOOT=no</code> por
<code>ONBOOT=yes</code> al final de este archivo), y una vez hecho eso, necesitaremos algunos paquetes para empezar:  
{% highlight bash %}
yum install deltarpm # Ayudará a bajarle el tamaño de los paquetes que tengamos que bajar
yum update # Siempre mantener el sistema lo más actualizado posible
reboot
## Después del reinicio ##
yum install git autoconf gcc bison epel-release
{% endhighlight %}

En principio, no necesitaremos nada más para una instalación **básica** de PHP. Sin embargo, como también quiero un PHP que sea medianamente
usable, necesitaremos de algunas dependencias.

## Las dependencias

Todos los comandos se deben ejecutar como root.  
Estas son las dependencias para esta instalación de PHP7:

{% highlight bash %}
yum install gcc-c++ mariadb-devel aspell-devel recode-devel readline-devel \
 libxml2-devel openssl-devel bzip2-devel curl-devel libjpeg-devel libpng-devel \
 libXpm-devel freetype-devel gmp-devel libicu-devel libmcrypt-devel
{% endhighlight %}

Una cosa curiosa es que libmcrypt ya no está en el repositorio central de CentOS 7, así que por eso instalé epel para poder instalar este paquete.

Ahora tenemos que instalar PHP mismo, así que simplemente bajamos la última versión y compilamos desde ahí:  
{% highlight bash %}
git clone https://git.php.net/repository/php-src.git
cd php-src/
{% endhighlight %}

## pthreads

*Nota*: Aunque la idea de poder tener soporte directo desde PHP para threading suena muy atractivo, la verdad es que este módulo externo todavía presenta
algunos problemas de compatibilidad, entendible debido a que PHP7 todavía no está terminado. Sin embargo, para cuando sí esté listo, este método
debería servir, así que por mientras voy a dejar los primeros pasos acá con la promesa de que revisitaré esta sección en el futuro.

Esta es una extensión que se puede bajar desde PECL que sirve para obtener soporte para threading desde PHP mismo en código, de una manera fácil.  
Para instalarla, debemos bajar el paquete desde PECL y dejarla en la carpeta <code>ext/</code> de PHP. Para eso, ejecuten los siguientes comandos:

{% highlight bash %}
cd ext/
curl -O https://pecl.php.net/get/pthreads-2.0.10.tgz
tar xvzf pthreads-2.0.10.tgz
mv pthreads-2.0.10 pthreads
rm pthreads-2.0.10.tgz
cd ..
{% endhighlight %}

## Compilando PHP

El comando definitivo para compilar PHP7:

{% highlight bash %}
./buildconf
./configure \
 --prefix=/usr \
 --with-config-file-path=/etc \
 --enable-mbstring \
 --enable-zip \
 --enable-bcmath \
 --enable-pcntl \
 --enable-ftp \
 --enable-exif \
 --enable-calendar \
 --enable-sysvmsg \
 --enable-sysvsem \
 --enable-sysvshm \
 --enable-wddx \
 --with-curl \
 --with-mcrypt \
 --with-iconv \
 --with-gmp \
 --with-pspell \
 --with-gd \
 --with-jpeg-dir=/usr \
 --with-png-dir=/usr \
 --with-zlib-dir=/usr \
 --with-xpm-dir=/usr \
 --with-freetype-dir=/usr \
 --enable-gd-native-ttf \
 --enable-gd-jis-conv \
 --with-openssl \
 --with-pdo-mysql=/usr \
 --with-gettext=/usr \
 --with-zlib=/usr \
 --with-bz2=/usr \
 --with-recode=/usr \
 --with-mysqli=/usr/bin/mysql_config \
 --enable-maintainer-zts \
 --enable-intl \
 --enable-opcache \
 --enable-opcache-file \
 --enable-fpm \
 --with-readline
{% endhighlight %}

Sé que cuando compilan, les gusta saber qué compilan, así que aquí un pequeño listado de las opciones más desconocidas y/o emblemáticas/nuevas:

* enable-bcmath: La librería de matemáticas extendida de PHP. [Más información](http://php.net/manual/en/book.bc.php).
* enable-pcntl / sysvmsg / sysvsem / sysvshm: Process Control y asociados. Fundamental para procesos CLI. [Más información](http://php.net/manual/en/book.pcntl.php).
* enable-calendar: Las funciones de calendario. [Más información](http://php.net/manual/en/book.calendar.php).
* with-gmp: GNU Multiple Precision: Otra librería de matemáticas, suelo ocupar algunas funciones de repente. [Más información](http://php.net/manual/en/book.gmp.php).
* with-pspell: Librería de ortografía! [Más información](http://php.net/manual/en/book.pspell.php).
* enable-maintainer-zts: Para soporte de pthreads (más adelante). [Más información](http://php.net/manual/en/book.pthreads.php).
* enable-intl: i18n, fundamental para el mundo globalizado de hoy!
* enable-opcache-file: Opción nueva y experimental, aceleraría procesos CLI. Sin link de más información ya que es una opción nueva con PHP 7 y no hay documentación todavía. Sé de esta opción por la lista de correos.
* enable-fpm: Para crear un módulo fast-cgi en vez de php_module (que es viejo y lento). [Más información](http://php.net/manual/en/book.fpm.php). **Prometo que haré una guía que integra PHP con nginx y fast_cgi más adelante**.
* with-readline: Para phpdbg (Debugger de PHP interactivo). [Más información](http://phpdbg.com/).

Una vez compilado, podemos proceder a crear el build y la instalación propiamente tal, a tomar café!

{% highlight bash %}
make
make install
{% endhighlight %}

Al finalizar, tendremos un binario que se habrá copiado a alguna ruta donde hayan binarios (/usr/bin/ en este caso), y que si lo ejecutamos nos dará la siguiente salida:

{% highlight bash %}
[root@localhost ~]# php -v
PHP 7.0.0-dev (cli) (built: Jun 15 2015 14:34:33) 
Copyright (c) 1997-2015 The PHP Group
Zend Engine v3.0.0-dev, Copyright (c) 1998-2015 Zend Technologies
{% endhighlight %}

Si desean experimentar con el setting <code>--enable-opcache-file</code>, deberán crear un nuevo <code>/etc/php.ini</code>, y en ella incluir las siguientes opciones: 

{% highlight ini %}
[opcache]
opcache.enable = 1
opcache.enable_cli=1
opcache.file_cache=/tmp
opcache.file_cache_only=1
{% endhighlight %}

Esto debería activar opcache y además hacerlo persistente para procesos CLI, ideal para varias sesiones.

## Verificaciones

Ahora nos toca verificar que todo haya ido bien y que todo funciona. 

Primero aprovechamos de instalar composer, [tal como es descrito en este post](/2015/06/entendiendo-e-implementando-composer-en-tu-codigo-php/).

Enseguida copiamos un repositorio y ejecutamos algunas pruebas: 

{% highlight bash %}
git clone https://github.com/unreal4u/fileOperations.git
cd fileOperations/
composer.phar install
vendor/bin/phpunit
{% endhighlight %}

En mi PC de desarrollo normal, donde tengo PHP 5.5.20, el test con 3 runs me da la siguiente salida:

<pre>
Time: 3.28 seconds, Memory: 8.00Mb
Time: 1.35 seconds, Memory: 8.00Mb
Time: 1.36 seconds, Memory: 8.00Mb
</pre>

Esta es la salida de 3 runs con PHP 7:

<pre>
Time: 183 ms, Memory: 4.00Mb
Time: 196 ms, Memory: 4.00Mb
Time: 232 ms, Memory: 4.00Mb
</pre>

Como podemos ver, en la primera test PHP 7 es 16 veces más rápido que PHP 5.5, mientras que en la segunda y tercera es aproximadamente 6 veces más
rápido. La memoria? Pues ocupa un 50% menos de memoria.

## Conclusiones

PHP 7 es rápido y esta prueba lo comprueba. Lamentablemente tendremos que esperar hasta noviembre para la salida definitiva de PHP 7 al
mundo, pero desde ya se puede comprobar que los esfuerzos realizados por el equipo que desarrolla PHP rindieron su fruto. Además del incremento
en velocidad en aplicaciones viejas, queda por ver qué tanto más rápido será ocupar el JIT Engine, cuando tenga un tiempo y ahora que tengo una
versión ocupable de PHP 7 haré las optimizaciones correspondientes y postearé las pruebas.

Fuentes:  
[SupportLobby](http://supportlobby.com/library-errors-when-compile-php-5-on-centos/)  
[CryBit](http://crybit.com/20-common-php-compilation-errors-and-fix-unix/)  
[coderwall](https://coderwall.com/p/ggmpfa/php-configuration-error-and-solutions-in-rpm)

