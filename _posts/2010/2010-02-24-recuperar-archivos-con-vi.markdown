---
comments: true
date: 2010-02-24 02:07:41+00:00
layout: post
slug: recuperar-archivos-con-vi
title: Recuperar archivos con vi
wordpress_id: 357
categories:
- Linux
---

Hace tiempo que no escribo pero pretendo mejorar eso y nada mejor que un tip cortito: debido a la pésima condición de la conexión que ofrece VTR, se me desconectó el PuTTY desde el servidor mientras estaba editando un archivo. El único problema es que el famoso archivo **no** lo había guardado así que era inevitable que los últimos cambios se perdieran. 

Sin embargo, es en este punto donde vi ofrece recuperar el borrador, pero como siempre había leído muy a la rápida, simplemente optaba por borrar este mismo. Para más mala suerte mía, pasé a borrar el archivo original en vez del archivo de recuperación ¬¬ 
<!-- more -->
Ahora bien, no es lo mismo perder 2 a 3 líneas que uno acaba de escribir a perder un archivo que contiene un poco más de 1100 líneas, que si bien es cierto fue sacado de un CMS, a estas alturas del partido está más que modificado. Tenía, por lo tanto, dos opciones: 


  * Recuperar ese archivo de recuperación


  * Descomprimir y luego buscar entre 4GB de datos que pesa el último respaldo y que está en un PC a 700km de mi ubicación actual... sin internet ¬¬


Intenté copiar el archivo con el viejo nombre del archivo, pero para mi gran sorpresa, el archivo se escribe de forma binaria. Fue ahí donde consulté la ayuda de vim y resultó que es sumamente fácil recuperar un archivo, basta ejecutar el siguiente comando: 

`vi -r nombre-del-archivo-original`

Y listo: tenemos el archivo con las últimas modificaciones listo para seguir trabajando. 

Eso fue mi pequeño tip del día, adelante estudios...
