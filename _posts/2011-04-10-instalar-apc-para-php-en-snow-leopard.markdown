---
comments: true
date: 2011-04-10 09:02:45+00:00
layout: post
slug: instalar-apc-para-php-en-snow-leopard
title: Instalar APC para PHP en Snow Leopard
wordpress_id: 541
categories:
- Apple/Mac
- Linux
- PHP
---

Siguiendo con la tanda de instalar extensiones infaltables para PHP, tenemos el caso de APC. Como todo el mundo ya debería saber, APC acelera la ejecución del código en forma enorme, gracias a que almacena en caché una copia de nuestro script ya compilado y ejecuta directamente ese en vez de parsear y posteriormente compilar cada vez nuestro script.
Por si fuera poco, APC permite servirnos como un buen módulo de caché, y aunque me gustaría escribir al tiro sobre eso, el objetivo de este post no es para eso. Sepan que de todas formas estoy preparando otro post con justamente esa información.

<!-- more -->


## Los prerequisitos


Aparte de xCode, esta extensión en particular necesita de pcre. Afortunadamente, la instalación es bastante sencilla y es como sigue: 

{% highlight bash %}
tar xzf pcre-8.11.tar.gz
cd pcre-8.11
./configure --prefix=/usr/local
make
# y ahora como root:
make install
{% endhighlight %}

Listo, con eso debería estar solucionado la primera parte. 



## Instalando APC


La última versión que estaba disponible a la fecha de escribir esta entrada, era la versión 3.1.7, aunque esta guía también debería servir las versiones hacia arriba sin problemas.
Lo primero que tenemos que hacer es [bajarnos la última versión de APC](http://pecl.php.net/package/APC) y descomprimirla. Luego, en la terminal, ejecutamos:

{% highlight bash %}
cd APC-[version]/
phpize
MACOSX_DEPLOYMENT_TARGET=10.6 CFLAGS="-arch x86_64 -g -Os -pipe -no-cpp-precomp" CCFLAGS="-arch x86_64 -g -Os -pipe" CXXFLAGS="-arch x86_64 -g -Os -pipe" LDFLAGS="-arch x86_64 -bind_at_load" ./configure
make
# y como root:
make install
{% endhighlight %}

Por último, agregamos a nuestro `/etc/php.ini`:

{% highlight bash %}
# en la parte de extensiones:
extension=apc.so

# al último abajo:
[apc]
apc.enabled = 1
apc.shm_segments = 1
apc.shm_size = 128
apc.ttl = 7200
apc.user_ttl = 7200
apc.num_files_hint = 1024
apc.mmap_file_mask = /tmp/apc.XXXXXX
apc.enable_cli = 1
{% endhighlight %}

Reiniciamos apache con `apachectl restart` y podemos proceder a verificar con `phpinfo()` que APC se encuentra instalado.

Fuentes:
[Lakedata](http://www.lakedata.net/index.php/eng/News/Install-APC-Cache-on-OS-X-Snow-Leopard)
