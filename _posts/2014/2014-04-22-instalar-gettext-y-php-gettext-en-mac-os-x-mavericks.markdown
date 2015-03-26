---
date: 2014-04-22 13:18:39+00:00
layout: post
slug: instalar-gettext-y-php-gettext-en-mac-os-x-mavericks
title: Instalar gettext y php-gettext en Mac OS X Mavericks
wordpress_id: 882
categories:
- Apple/Mac
- i18n/L10n
- PHP
---

Desde hace algún tiempo que me vengo corriendo de no tener que instalar gettext en Mac OS, ya que implica hacer algunos cambios en código fuente, pero hoy ya no lo pude postergar más: composer se quejaba de que necesitaba una dependencia, de lo contrario no instalaba el software que necesitaba. Manos a la obra entonces, aquí una guía rápida de cómo instalar gettext para PHP en Mac OS X Mavericks. 

<!-- more -->

Todos los comandos se ejecutan como root:

{% highlight bash %}
# Primero tenemos que instalar gettext
cd
wget http://ftp.gnu.org/gnu/gettext/gettext-0.18.tar.gz
tar xvzf gettext-0.18.tar.gz
cd gettext-0.18
vi gettext-tools/gnulib-lib/stpncpy.c
## En regla ~34
## Cambiar: 
## __stpncpy (char *dest, const char *src, size_t n)
## por:
## (__stpncpy) (char *dest, const char *src, size_t n)
# Se va a demorar... agarren café
./configure
make
make install
cd
rm -rf gettext-0.18*
# Una vez instalado gettext, ahora tenemos que instalar la extensión
git clone https://github.com/php/php-src.git
cd php-src
# Vamos a la misma versión que tenemos instalado
git checkout `php -r 'echo "php-".PHP_VERSION;'`
cd ext/gettext
# Y configuramos el módulo
phpize
./configure
make
# Instalamos el módulo
cp modules/gettext.so /usr/lib/php/extensions/no-debug-non-zts-20100525/
vi /etc/php.ini
## Al final:
## [gettext]
## extension = gettext.so
# Reiniciamos apache
apachectl restart
{% endhighlight %}

La gran particularidad es el parche que tenemos que aplicarle a gettext-tools. Este parche tiene que ver con la manera en que Mac OS X implementa dicha función, el cual es sobreescrita por GNU, produciendo conflictos en cuanto a nombres. Basta encerrar con paréntesis el nombre de la función y podremos compilar sin problemas. 

Espero les haya servido el tip.
