---
author: admin
comments: true
date: 2011-04-09 21:33:27+00:00
layout: post
slug: instalando-postgresql-php-pgsql-y-php-pdo_pgsql-en-mac-os-x
title: Instalando PostGreSQL, php-pgsql y php-pdo_pgsql en Mac OS X
wordpress_id: 534
categories:
- Apple/Mac
- Bases de Datos
- Linux
- PHP
---

No me malentiendan: desde que ocupo Mac OS X que me encanta y no me cambiaría de vuelta a Linux ni menos a Windows, pero hay algunas cosas que son capaces de hacerte doler la cabeza. Una de ellas es justamente la instalación de PostGreSQL y sus conectores para PHP: php-pgsql y su driver unificado mejorado PDO. Entre medio, hay que borrar usuarios de sistema, compilar y parchear, así que preparaos porque esto se viene bueno.

Lo bueno de todo esto, es que la instalación de PostGreSQL está dentro de lo más fácil. Sólo basta con [bajar un paquete](http://www.enterprisedb.com/products/pgdownload.do#osx), borrar un usuario de sistema e instalar para que todo resulte bien, lo cual se hace de la siguiente manera: (Todos los comandos se ejecutan como root o sudo, lo que prefieran)
[bash]
# en 10.6:
dscl . delete /users/postgres
# en 10.7:
dscl . delete /users/_postgres
cd /Volumes/PostgreSQL\ 9.0.3-1/
./postgresql-9.0.3-1-osx.app/Contents/MacOS/installbuilder.sh
# seguir los pasos en pantalla.
# el siguiente comando crea el superuser de postgres:
# el nombre de la cuenta (segundo parámetro) puede ser cualquier cosa.
createuser pgsql --pwprompt --username=postgres
[/bash]
<!-- more -->

Con eso debería quedar instalado PostGreSQL. Primera parte lista.



## Instalando php-pgsql


Suponiendo que PHP ya está instalado (tb suponiendo que el que está instalado es la versión 5.3.3 que se distribuye con Snow Leopard 10.6) ahora comienza el dolor de cabeza, ya que la versión de PHP de Mac OS X no viene compilado para funcionar con pgsql. Súmenle a eso que tenía varias copias de PHP dando vueltas por lo que en un principio no sabía cuáles archivos eran los que tenía que tocar. Al final resultó ser `/etc/php.ini` la configuración de PHP aplicada y `/etc/apache2/httpd.conf` la configuración de Apache. Ojo que /etc/ es un symlink, la ruta absoluta es /private/etc/. 

Lo primero que debemos hacer si no lo hemos hecho es instalar xCode que trae GCC y todo el material necesario para poder compilar nuestras aplicaciones. Si no lo has hecho, bájalo desde apple.com y prepárate por que son algunos gigas. (casi 4 si mal no me acuerdo). Una vez instalado xCode podemos proseguir a lo demás.
Después de mamarnos la instalación de xCode, podemos proseguir. Lo primero que hay que hacer es ir a [las fuentes](http://www.postgresql.org/ftp/source/) y bajar el source del mismo paquete que instalamos, que en mi caso es la versión más actual: 9.0.3. Es probable que esta guía sirva también para las versiones posteriores.
Una vez bajado y descomprimido, nos vamos nuevamente a la terminal y ejecutamos como uds quieran: 
[bash]
cd postgresql-[version]
CFLAGS="-arch i386 -arch x86_64" ./configure --prefix=/usr/local/mypostgres
make
[/bash]
Después de un rato, make **FALLARÁ** pero eso será justo después del punto que nos interesa :)
[bash]
make -C src/interfaces
sudo make -C src/interfaces install
make -C src/include
sudo make -C src/include install
make -C src/bin
# ejecutamos el siguiente comando como root:
make -C src/bin install
[/bash] 

Con esto, debería quedar instalado libpq que es el paquete que nos interesa. Podemos proceder ahora a bajar el source la versión de PHP que tengamos instalado y lo descomprimimos. Una vez hecho eso, ejecutamos: 
[bash]
cd php-[version]/ext/pgsql
phpize
./configure --with-pgsql=/usr/local/mypostgres
make -j8
#ejecutamos como root
sudo make install
[/bash]
Ahora editamos el archivo `/etc/php.ini` y agregamos `extension=pgsql.so` como una extensión. Lo último que falta es reiniciar Apache para que los cambios surtan efectos. ¡Felicitaciones! Terminamos de instalar el soporte de pgsql para PHP a Mac OS X y podemos proceder a borrar todas las carpetas temporales que creamos.



## Instalando PDO-pgsql


Afortunadamente, esto es bastante más fácil que instalar la antigua librería. Hay que hacer lo siguiente (como root): 
[bash]
pecl channel-update pecl.php.net
pecl download pdo_pgsql
tar xvf PDO_PGSQL-1.0.2.tgz
cd PDO_PGSQL-1.0.2
phpize
./configure --with-pdo-pgsql=/Library/PostgreSQL/9.0/
make && make install
[/bash]
Finalmente, retocamos nuestra configuración de php.ini agregando `extension=pdo_pgsql.so` y reiniciamos Apache (con `apachectl restart`).

Y eso sería todo por hoy. Si te sirvió, deja un comentario agradeciendo.

Fuentes: 
[Jeremiah Peschka](http://facility9.com/2010/02/15/installing-postgresql-on-mac-os-x)
[StackOverflow](http://stackoverflow.com/questions/61747/installing-pdo-drivers-for-postgresql-on-mac-using-zend-for-eclipse)
[GNEGG](http://www.gnegg.ch/2009/08/snow-leopard-and-php/)
