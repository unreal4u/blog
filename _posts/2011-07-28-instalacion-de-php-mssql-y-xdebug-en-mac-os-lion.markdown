---
comments: true
date: 2011-07-28 05:01:22+00:00
layout: post
slug: instalacion-de-php-mssql-y-xdebug-en-mac-os-lion
title: Instalación de php-mssql y xDebug en Mac OS Lion
wordpress_id: 571
categories:
- Apple/Mac
- Bases de Datos
- PHP
---

Ayer les contaba acerca de que Mac OS en su nueva versión del sistema operativo había actualizado la versión de PHP (sin preguntar) y que tenía que instalar las extensiones de nuevo. Hoy toca el día en que el artículo que explica cómo instalar dos de las extensiones más populares bajo este sistema operativo y con la nueva versión de PHP incluida.

Lo primero que hay que hacer es bajar las dependencias:  
[Freedts](ftp://ftp.ibiblio.org/pub/Linux/ALPHA/freetds/stable/freetds-stable.tgz)  
[PHP5.3.6](http://php.net/downloads.php#v5)  
[xDebug](http://xdebug.org/download.php)  
Descomprimen todo en un directorio y hacen click en Continuar Leyendo.
<!-- more -->

Lo primero que tenemos que hacer antes de continuar es instalar Xcode que ahora está cómodamente disponible en la App Store. Ojo que una vez instalado (según la App Store) necesitaremos ir a Aplicaciones y apretar el ícono que dice "Instalar Xcode" para que todas las herramientas de desarrollo estén disponibles.

Ejecutamos los siguientes comandos. Ejecutar todo lo que esté con asterisco como root o con sudo, como quieran:

{% highlight bash %}
cd freetds-XXXX/
./configure --prefix=/etc/freetds --with-tdsver=8.0 --sysconfdir=/etc/freetds/conf/
make
# make install
# cp modules/mssql.so /usr/lib/php/extensions/no-debug-non-zts-20090626/
cd ../xdebug-XXXX/
phpize
./configure --enable-xdebug
make
# cp modules/xdebug.so /usr/lib/php/extensions/no-debug-non-zts-20090626/
# cp /etc/php.ini.default /etc/php.ini
# vi /etc/php.ini
// Hacemos los ajustes necesarios a nuestra configuración, 
// importante es que las siguientes tres líneas queden dentro
// de la configuración:
extension=mssql.so
[xdebug]
zend_extension="/usr/lib/php/extensions/no-debug-non-zts-20090626/xdebug.so"
{% endhighlight %}

Con estos pasos, ya pueden disfrutar de su instalación de PHP con xDebug y el conector de PHP para Microsoft SQL Server.
