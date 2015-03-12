---
author: admin
comments: true
date: 2011-07-27 16:34:22+00:00
layout: post
slug: crear-una-firma-html-en-mail5
title: Crear una firma HTML en Mail5
wordpress_id: 566
categories:
- Apple/Mac
---

Pequeño tip: el proceso para la creación de una firma que admita HTML en Mail5.0 es lijeramente distinto que en las versiones anteriores. En la nueva versión es: 



	
  * Crear un archivo .html con lo que quisiéramos dejar de firma. Guardar en una ubicación temporal.

	
  * Abrir Preferencias en Mail y agregar una nueva firma.

	
  * Cerrar Mail

	
  * En Safari, abrir el archivo .html creado y guardarlo como un archivo tipo .webarchive

	
  * Copiar el archivo exportado por Safari a `~/Library/Mail/V2/MailData/Signatures/`, teniendo cuidado en cambiarle el nombre al archivo sobreescribiendo el que ya esté. Si tienen varias firmas, primero revisen cuál es cuál, Mail5 le asigna una ID única a cada uno pero abriendo el archivo en un editor de texto, aunque verán mucha basura entremedio, igual se puede ver de cuál firma se trata.



[ACTUALIZACIÓN]
Otro tip para volver a activar el apretar una tecla y que ésta se repita es poner en la terminal el siguiente comando: 
`defaults write -g ApplePressAndHoldEnabled -bool false`

Gracias a [Rincón Apple](http://rinconapple.com/1584/activar-repeticion-del-teclado-en-os-x-lion/) por este dato.

[ACTUALIZACIÓN 2]
Tuve un problema con la copia de archivos: sucede que copiaba o movía y éste movimiento quedaba sin efecto alguno. La solución a este problema proviene directamente de los foros oficiales de Apple y consiste en borrar un archivo ubicado en: 
`rm -rf ~/Library/Preferences/com.apple.finder.plist`

Solución encontrada gracias a los [foros de Apple](https://discussions.apple.com/thread/3204529?start=0&tstart=0).
