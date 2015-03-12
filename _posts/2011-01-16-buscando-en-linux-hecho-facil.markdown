---
author: admin
comments: true
date: 2011-01-16 01:37:57+00:00
layout: post
slug: buscando-en-linux-hecho-facil
title: Buscando en Linux hecho fácil
wordpress_id: 486
categories:
- Apple/Mac
- Linux
---

Cuántas veces no les ha pasado que saben que una cierta cadena empieza con "hola_mun" pero no se acuerdan en qué termina ni menos en qué archivo? También: han tenido alguna vez que hacer cambios en varios archivos del tipo "$hola" a "$chao['hola']" y que sucede que esa variable está repartida en 200 archivos por distintos directorios?

Bueno, si eso les ha pasado y ocupan Linux, ha llegado su día de suerte: creé un script (para la terminal) que busca en diversos lados por una cadena determinada e imprime la salida. Pero qué sucede en aquellos casos donde tenemos que omitir ciertas carpetas temporales como por ejemplo las que crea subversion? Bueno, este script también considera eso. Haz click en leer más para conocer el script.
<!-- more -->
Lo prometido es deuda, a continuación, el script: 
[bash]#!/bin/bash
if test -z "$1"; then
  echo "Uso: ./b termino [carpeta]"
else
  if test -z "$2"; then
    CARPETA="."
  else
    echo "el argumento es: $2"
    CARPETA=$2
  fi

  echo "Busqueda por \"$1\" en la carpeta \"$CARPETA\":"
  echo ""
  find $CARPETA \! -regex ".*/\.svn/.*" \! -regex ".*respaldo.*" \! -regex ".*/cache/.*" | xargs grep "$1"
fi
[/bash]

Si van a buscar por palabras con espacios o símbolos extraños, el uso es el siguiente: 
`./b "el término"`

Este script también permite buscar en otra carpeta distinta a la actual pasándole como segundo parámetro la ruta de la ubicación donde buscar.
Si se desea omitir algún directorio adicional, se debe agregar como: 
`\! ".*NOMBRE.*"`
Donde NOMBRE es el nombre de la carpeta que desean omitir. Por supuesto que se aceptan expresiones regulares más complicadas, pero este script lo ocupo hace tiempo para efectos personales y hasta el día de hoy no se me había ocurrido publicarlo. Como me ha funcionado bien estos meses, no he investigado más allá.

Espero que les haya servido el consejo. Sus comentarios son bienvenidos (Luego de la aprobación previa claro está).
