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
<code>
yum install git autoconf gcc bison epel-release
</code>

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

Esta es una extensión que se puede bajar desde PECL que sirve para obtener soporte para threading desde PHP mismo en código, de una manera fácil.  
Para instalarla, debemos bajar el paquete desde PECL y dejarla en la carpeta <code>ext</code> de PHP. Para eso, ejecuten los siguientes comandos:

{% highlight bash %}
cd ext/
curl -O https://pecl.php.net/get/pthreads-2.0.10.tgz
tar xvzf pthreads-2.0.10.tgz
mv pthreads-2.0.10 pthreads
rm pthreads-2.0.10.tgz
cd ..
{% endhighlight %}

Sin embargo, no haremos nada todavía con este módulo ya que al parecer presenta problemas de incompatibilidad, entendible debido a la gran cantidad
de cosas distintas que hay en PHP7.

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
 --with-mysqli=/usr/bin/mysql\_config \
 --enable-maintainer-zts \
 --enable-pthreads \
 --enable-intl \
 --enable-opcache \
 --enable-fpm \
 --with-readline
{% endhighlight %}

Una vez compilado, podemos proceder a crear el build y la instalación propiamente tal, a tomar café!

{% highlight bash %}
make
make install
{% endhighlight %}



