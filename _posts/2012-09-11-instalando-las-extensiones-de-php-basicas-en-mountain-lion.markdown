---
author: admin
comments: true
date: 2012-09-11 21:17:51+00:00
layout: post
slug: instalando-las-extensiones-de-php-basicas-en-mountain-lion
title: Instalando las extensiones de PHP básicas en Mountain Lion
wordpress_id: 662
categories:
- Apple/Mac
- Bases de Datos
- PHP
---

Hasta el momento no había tenido la oportunidad de actualizar mi blog con las nuevas instrucciones para este nuevo sistema operativo, sin embargo difieren bastante poco, así que no esperen mucho cambio. De todas formas, las instrucciones ya están listas y es cosa de copiar y pegar. En esta ocasión, las extensiones que serán instaladas serán xDebug, APC, intl y el conector mssql para Microsoft SQL Server.<!-- more -->



## Instalando lo básico


Lo primero que necesitamos es la última versión estable de:
xDebug (más rato explicaré cómo bajarlo)
[PHP](http://www.php.net/get/php-5.3.13.tar.gz/from/a/mirror) (Ya que Mountain Lion viene con PHP 5.3.13, recomiendo bajar esa misma versión)
[Freetds](ftp://mirrors.ibiblio.org/freetds/stable/)
[pcre](ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre)

Ya con todo eso listo y descomprimido en una carpeta, procedemos a la instalación como tal. Para simplificarlo, ejecuten todos los comandos como root. Si quieren leer de qué se trata todo esto y por qué son necesarias las dependencias, siéntanse libres de consultar [este post](http://blog.unreal4u.com/2011/07/instalacion-de-php-mssql-y-xdebug-en-mac-os-lion/) y por supuesto también [este otro](http://blog.unreal4u.com/2011/04/instalar-apc-para-php-en-snow-leopard/) donde explico para qué sirve cada dependencia.
[bash]
git clone git://github.com/derickr/xdebug.git

cd freetds-0.91/
./configure --prefix=/etc/freetds --with-tdsver=8.0 --sysconfdir=/etc/freetds/conf/
make
make install

cd ../xdebug/
phpize
./configure --enable-xdebug
make
cp modules/xdebug.so /usr/lib/php/extensions/no-debug-non-zts-20090626/

cd ../php-5.3.13/
./configure

cd ext/mssql/
phpize
./configure --with-mssql=/etc/freetds
make
cp modules/mssql.so /usr/lib/php/extensions/no-debug-non-zts-20090626/

cd ../intl/
phpize
./configure --enable-intl
make
cp modules/intl.so /usr/lib/php/extensions/no-debug-non-zts-20090626/

cd ../../../pcre-8.31/
./configure --prefix=/usr/local
make
make install

cd ../APC-3.1.10/
phpize
MACOSX_DEPLOYMENT_TARGET=10.6 CFLAGS="-arch x86_64 -g -Os -pipe -no-cpp-precomp" CCFLAGS="-arch x86_64 -g -Os -pipe" CXXFLAGS="-arch x86_64 -g -Os -pipe" LDFLAGS="-arch x86_64 -bind_at_load" ./configure
make
make install
[/bash]

De ahí, revisamos nuestro php.ini y agregamos las siguientes líneas: 

[bash]
[xdebug]
zend_extension="/usr/lib/php/extensions/no-debug-non-zts-20090626/xdebug.so"
xdebug.idekey="macgdbp"
xdebug.remote_enable=1
xdebug.var_display_max_data = 512
xdebug.collect_vars = 1
xdebug.collect_params = 4
xdebug.show_local_vars = 1
xdebug.max_nesting_level = 250
;xdebug.profiler_enable = On
;xdebug.profiler_output_dir = "/tmp/cachegrind"
;xdebug.profiler_output_name = callgrind.out.%p
;xdebug.profiler_enable_trigger = 0
xdebug.show_mem_delta = 1
; Habilitar las 4 líneas de abajo sólo si queremos direccionar xDebug hacia algún IDE
;xdebug.remote_enable = On
;xdebug.remote_host = "localhost"
;xdebug.remote_port = 9000
;xdebug.remote_handler = "dbgp"

[apc]
extension = apc.so
apc.enabled= 1
apc.shm_segments = 1
apc.shm_size = 128M
apc.ttl = 7200
apc.user_ttl = 7200
apc.num_files_hint = 1024
apc.mmap_file_mask = /tmp/apc.XXXXXX
apc.enable_cli = 1

[intl]
extension = intl.so
[/bash]

Luego simplemente invocamos un "`apachectl restart`" (con root) y si todo está bien nuestro pc no debería explotar y ahora deberíamos tener disponibles todas las extensiones que nos hacen la vida más fácil. Agregué intl al listado debido a que las funciones multibyte que tiene son básicamente las mismas a las que ya estamos acostumbrados a las de PHP "normal" pero sin la problemática de que "سجل الخروج" lo considere más largo o corto que "Deslogearse". Si no saben de qué les estoy hablando pueden consultar [esta página](http://php.net/manual/en/book.mbstring.php) que habla de forma bastante extendida sobre funciones multibyte-safe. Adicionalmente, también pueden darse una pequeña vuelta por [este post](http://blog.unreal4u.com/2012/08/sobre-collation-y-charset-en-mysql/) y cómo no, también [este otro post](http://blog.unreal4u.com/2010/09/problemas-en-los-tildes-o-acentos/). 

