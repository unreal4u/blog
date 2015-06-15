---
comments: true
date: 2015-06-10 00:00:00+00:00
layout: post
slug: compilando-php7-desde-source
title: Compilando PHP7 desde source
categories:
- PHP
---

Que quede una cosa clara desde el principio! *Yo no soy un fan de compilar software*! Encuentro que hoy en día, compilar ya es cosa del pasado, a menos que quieras probar software que todavía no está listo para funcionar en producción. Como tal, quise probar qué tal andaba PHP7 con el código de la empresa, a raíz de las tantas maravillas que se ha dejado entrever de esta nueva versión.

Algunas de estas maravillas son que incluyeron PHPNG (PHP Next Generation), que mejora el uso de recursos (ocuparía MUCHO menos RAM), y ahora también se activó un JIT Engine (que aceleraría las llamadas recurrentes a funciones y métodos). Entre esas dos mejoras, deberían ver un incremento de velocidad del orden de 25% a 150% comparado con PHP 5.6, dependiendo del caso obviamente.

Esto quiere decir básicamente que en una instalación default de WordPress, la cantidad de instrucciones de CPU se redujeron de 9.413.106.833 a 2.483.106.468 y que el tiempo que ocupa generar 1000 veces el homepage se redujo de 26.756 segundos a 11.081 segundos (Como pueden [verificar acá](https://docs.google.com/spreadsheets/d/1qW0avj2eRvPVxj_5V4BBNrOP1ULK7AaXTFsxcffFxT8/edit?pli=1#gid=1334306309)). En ambos casos, es un incremento de velocidad importante y está casi a la par (y actualmente en muchos casos aún más rápido) que HHVM de Facebook.

Lo primero que necesitamos para empezar a trabajar es una instalación limpia y minimalista de CentOS 7. Por supuesto, necesitaremos algunos paquetes para empezar:
{% highlight bash %}
yum install git autoconf gcc bison epel-release
{% endhighlight %}

En principio, no necesitaremos nada más para una instalación *básica* de PHP. Sin embargo, como también quiero un PHP que sea medianamente usable, necesitaremos de algunas dependencias. ¿Cuáles? Pues, después del salto.

<!-- more -->

## Las dependencias

Estas son las dependencias para esta instalación de PHP7:

{% highlight bash %}
yum install gcc-c+ mariadb-devel aspell-devel recode-devel readline-devel \
 libxml2-devel openssl-devel bzip2-devel curl-devel libjpeg-devel libpng-devel \
 libXpm-devel freetype-devel gmp-devel libicu-devel libmcrypt-devel
{% endhighlight %}

Una cosa curiosa es que libmcrypt ya no está en el repositorio central de CentOS 7, así que por eso instalé epel para poder instalar este paquete.

Ahora tenemos que instalar PHP mismo, así que simplemente bajamos la última versión y compilamos desde ahí:  
{% highlight bash %}
git clone git.php.net/blabla
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
 --prefix=/usr \ # Path estándar para CentOS
 --with-config-file-path=/etc \ # Estándar para CentOS
 --enable-mbstring \ # Queremos las funciones mb
 --enable-zip \ # Queremos funcionalidad de Zip
 --enable-bcmath \ # Librería útil para cálculos matemáticos
 --enable-pcntl \ # Útil para CLI
 --enable-ftp \
 --enable-exif \ # Trabajar con imágenes? Claro!
 --enable-calendar \
 --enable-sysvmsg \
 --enable-sysvsem \
 --enable-sysvshm \
 --enable-wddx \
 --with-curl \ # curl es un must
 --with-mcrypt \ # mcrypt es casi obligación hoy en día
 --with-iconv \ # i18n
 --with-gmp \
 --with-pspell \
 --with-gd \ # GD es la más popular, también podría haber sido Imagick
 --with-jpeg-dir=/usr \
 --with-png-dir=/usr \
 --with-zlib-dir=/usr \
 --with-xpm-dir=/usr \
 --with-freetype-dir=/usr \
 --enable-gd-native-ttf \
 --enable-gd-jis-conv \
 --with-openssl \
 --with-pdo-mysql=/usr \ # PDO? Por supuesto!
 --with-gettext=/usr \ i18n
 --with-zlib=/usr \
 --with-bz2=/usr \
 --with-recode=/usr \
 --with-mysqli=/usr/bin/mysql\_config \
 --enable-maintainer-zts \ # Para pthreads
 --enable-intl \ # i18n
 --enable-opcache \ # Powaaa
 --enable-opcache-file \ # Experimental!
 --enable-fpm \ # Soporte fast-cgi
 --with-readline # Para phpdbg
{% endhighlight %}

Una vez compilado, podemos proceder a crear el build y la instalación propiamente tal, a tomar café!

{% highlight bash %}
make
make install
{% endhighlight %}

Si desean experimentar con el setting <code>--enable-opcache-file</code>, deberán incluir algunas opciones en su <code>php.ini</code>: 

{% highlight ini %}
opcache.enable_cli=1
opcache.file_cache=/tmp
opcache.file_cache_only=1
{% endhighlight %}

Esto debería hacer opcache persistente, ideal para varias sesiones de CLI.

Fuentes:  
[SupportLobby](http://supportlobby.com/library-errors-when-compile-php-5-on-centos/)  
[CryBit](http://crybit.com/20-common-php-compilation-errors-and-fix-unix/)  
[coderwall](https://coderwall.com/p/ggmpfa/php-configuration-error-and-solutions-in-rpm)

