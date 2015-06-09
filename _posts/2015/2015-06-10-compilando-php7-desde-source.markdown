---
comments: true
date: 2015-06-05 00:00:00+00:00
layout: post
slug: compilando-php7-desde-source
title: Compilando PHP7 desde source
categories:
- PHP
---

Que quede una cosa clara desde el principio! *Yo no soy un fan de compilar software*! Encuentro que hoy en día, compilar ya es cosa del pasado, a menos que quieras probar software que todavía no está listo para funcionar en producción. Como tal, quise probar qué tal andaba PHP7 con el código de la empresa, a raíz de las tantas maravillas que se ha dejado entrever de esta nueva versión.

Lo primero que necesitamos para empezar a trabajar es una instalación limpia y minimalista de CentOS 7. Enseguida, necesitaremos algunos paquetes para empezar:
<code>
yum install git autoconf gcc bison epel-release
</code>

En principio, no necesitaremos nada más para una instalación básica de PHP. Sin embargo, como también quiero un PHP que sea medianamente usable, necesitaremos de algunas dependencias. ¿Cuáles? Pues, después del salto.

<!-- more -->

## Las dependencias

Estas son las dependencias:

{% highlight bash %}
yum install gcc-c+ curl-devel
{% endhighlight %}


## Compilando PHP

El comando definitivo para compilar PHP7:

{% highlight bash %}
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
 --enable-pthreads \
 --enable-intl \
 --enable-opcache \
 --enable-fpm
{% endhighlight %}


