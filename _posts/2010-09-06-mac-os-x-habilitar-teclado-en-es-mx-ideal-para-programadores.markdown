---
author: admin
comments: true
date: 2010-09-06 20:22:51+00:00
layout: post
slug: mac-os-x-habilitar-teclado-en-es-mx-ideal-para-programadores
title: '[Mac OS X] Habilitar teclado en ES-MX, ideal para programadores'
wordpress_id: 436
categories:
- Apple/Mac
- i18n/L10n
---

Una de las cosas que más me desconcertó cuando empecé a trabajar  con Mac, fue la ubicación de las teclas. Y es que, desde que entré a  estudiar informática, siempre había elegido la distribución mexicana del  teclado, por la simplicidad con la cual se pueden escribir caracteres  como `<` y `>`, y también las infaltables `{}` y `[]`, sin olvidar la ubicación del tilde, al lado de la P y no de la Ñ.
Más grande aún fue mi sorpresa al ver que sólo estaba el teclado Español de España (discúlpenme mis lectores españoles, pero su configuración de  teclado es lo más incómodo que hay para programar) y que simplemente no  podía elegir otra distribución.
Así que la hice corta: Googleé de inmediato y encontré la solución. Sin  embargo, esa misma solución ahora está offline, así que acá tienen un  respaldo, esta vez escrito por este humilde servidor.
<!-- more -->
Maneras de poder establecer la configuración propia del teclado existen varias, quizás la más conocida es [Ukelele](http://scripts.sil.org/cms/scripts/page.php?site_id=nrsi&id=ukelele) (a quién se le habrá ocurrido ese nombre?) pero después de unas cuantas  vueltas, no me terminó convenciendo mucho que digamos. Dijo el picado  en realidad porque nada de lo que hiciera funcionó.


## Paso 1: Bajar el layout


Lo más importante: el layout en sí. Lo pueden bajar desde aquí:
[Bajar layout](http://blog.unreal4u.com/wp-content/uploads/layout.tar.gz)

Lo descomprimen y eso.


## Paso 2: Copiar los archivos


En mi caso, preferí copiarlo a mi carpeta local, es decir, luego de  descompimir los archivos lo copié a la carpeta Library/Keyboard Layouts  de mi home.

Luego, hay que **reiniciar** (Cerrar sesión creo que  sirve, pero creo que me funcionó recién cuando reinicié) y a la vuelta,  podemos seleccionar en Preferencias del Sistema / Idioma y Texto /  Fuentes de Entrada el idioma Latinoamericano que aparece en el listado.

O sea, en breve:
[bash]$ cd /tmp/
$ wget http://blog.unreal4u.com/wp-content/uploads/layout.tar.gz
$ tar xvzf layout.tar.gz
$ mv Latinoamericano.* ~/Library/Keyboard\ Layouts/
$ rm layout.tar.gz
# reboot
[/bash]

Con esto, casi todas las teclas quedan iguales al teclado mexicano al  cual estamos acostumbrados, excepto por el signo mayor y menor, que  quedan arriba del tab. De todas formas, no es algo a lo que cueste  acostumbrarse como sí lo podría ser el caso de llaves, corchetes, signos  y otros símbolos que ocupamos frecuentemente.

Por si están medios perdidos, así es como quedan algunas teclas que sólo algunas veces ocupamos en la nueva distribución:
~ = alt + (signo más)
^ = alt + ñ
¬ = alt + l (L minúscula)
| = (entre shift y z)
º = shift + (entre shift y z)
@ = alt + q

Espero que les haya servido.
