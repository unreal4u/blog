---
author: admin
comments: true
date: 2012-06-10 22:44:06+00:00
layout: post
slug: la-odisea-de-instalar-los-developer-tools-en-lion
title: La odisea de instalar los developer tools en Lion
wordpress_id: 616
categories:
- Apple/Mac
- Pensamientos Personales
---

Hasta Snow Leopard, era bastante simple instalar las herramientas de desarrollo en Mac. Bastaba con bajar Xcode, instalarlo y listo. Sin embargo, con Mac OS X Lion esto cambió radicalmente. Algo que se supone iba a ser más fácil (ubicar Xcode en la App Store e instalarlo) terminó siendo mucho más complicado, teniendo incluso que compilar a mano para instalar todas las herramientas. A continuación, un pequeño tutorial para solucionar el típico problema de (sonido de tambores): 
[code]Cannot find autoconf. Please check your autoconf installation and the
$PHP_AUTOCONF environment variable. Then, rerun this script.[/code]
<!-- more -->

El otro día decidí formatear el Mac más que nada porque nunca lo había hecho y quería tener la experiencia de. Cuando por primera vez me pasaron el equipo, venía con Snow Leopard y cuando salió Lion, simplemente lo instalé encima y seguí usando el equipo como todos los días, excepto por algunos pequeños detalles que rápidamente fueron solucionados.

Sin embargo, en cuanto empecé con la tarea me di cuenta que en realidad el paso de Snow Leopard a Lion cambió bastantes cosas más de las que no me había dado cuenta ya que había hecho todo previamente en SL.

Así es como por ejemplo cambiaron por completo la manera de instalación de Xcode: antes era instalarlo y uno tenía todas las herramientas de developer listas. Ahora en cambio, hay que instalar Xcode desde la App Store, iniciarlo y luego ir a las preferencias. De ahí hay que ir hacia la pestaña "Downloads" e instalar los Command Line Tools, o Herramientas de comando de línea en español.

![xcode4-3](http://blog.unreal4u.com/wp-content/gallery/miscelaneo/xcode4-3.png)

Una vez hecho eso... estamos recién a mitad de camino. Ahora viene la parte más complicada, que es tener que compilar algunas herramientas adicionales más que NO vienen en el paquete de herramientas ¬¬ 

Estas herramientas adicionales son m4, autoconf, automake y libtool, herramientas que cualquier linuxero debería conocer.

La instalación más simple es escribiendo un pequeño script para hacer todo de forma automática, y como hoy es su día de suerte, acá está:
[bash]
#!/bin/sh

WORK=$HOME/Builds/build-essential
if [ ! -d $WORK ] ; then
mkdir -p $WORK
fi
PREFIX=/usr/local
export PATH="$PREFIX/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin"

if [ ! -d $WORK/src ] ; then
mkdir -p $WORK/src
fi
cd $WORK/src
curl -O ftp://ftp.gnu.org/gnu/m4/m4-1.4.16.tar.bz2
curl -O ftp://ftp.gnu.org/gnu/autoconf/autoconf-2.68.tar.bz2
curl -O ftp://ftp.gnu.org/gnu/automake/automake-1.11.1.tar.bz2
curl -O ftp://ftp.gnu.org/gnu/libtool/libtool-2.4.tar.gz

cd $WORK

# m4
tar xjf $WORK/src/m4-*
cd $WORK/m4-*
echo in $WORK/m4-*
./configure\
    --prefix=$PREFIX
make clean && make -j4 && make install-strip
cd $WORK

# autoconf
tar xjf $WORK/src/autoconf-2*
cd $WORK/autoconf-*
./configure\
    --prefix=$PREFIX\
    --disable-debug
make clean && make -j4 && make install-strip
cd $WORK

# automake
tar xjf $WORK/src/automake-*
cd $WORK/automake-*
./configure\
    --prefix=$PREFIX
    --disable-debug
make clean && make -j4 && make install-strip
cd $WORK

# libtool
tar xjf $WORK/src/libtool-*
cd $WORK/libtool-*
./configure\
    --prefix=$PREFIX
make clean && make -j4 && make install-strip
cd $WORK
[/bash]

Esto lo guardan como install-tools.sh, le dan permisos de ejecución (chmod +x install-tools.sh) y lo **ejecutan como root**.

El script original [lo saqué desde acá](https://gist.github.com/1501637), pero dado que tenía tantas pifias de ruta, lo arreglé y resultó el script de arriba.
