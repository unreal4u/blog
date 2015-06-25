---
comments: true
date: 2015-07-01 00:00:00+00:00
layout: post
slug: centos7-minimal-install
title: "CentOS7: instalación mínima"
categories:
- Linux
---

¡Buenos días, buenas tardes! Hoy voy a crear una guía de cómo instalo yo mis máquinas virtuales. Estas máquinas por lo general están destinadas a ser servidores web de alto rendimiento 
pero pueden ser usadas para cualquier cosa, ya que es sólo un sistema minimalista. Como soy un fan del software open-source y como hoy en día instalar una máquina que no sea
virtualizada es prácticamente un crimen, utilizaré VirtualBox con esta guía para poder instalar todo.

Lo primero es lo primero: tenemos que asignar una máquina virtual, y dentro de las cosas importantes que tenemos que hacer, es asignar el disco de instalación como un medio extraíble: 

{% include img.html url="/assets/minimal-install-centos7/01-virtualbox.png" %}

El resto de los settings es cosa de ustedes.

<!-- more -->

Una vez que tengan todos los settings listos, llegarán a la pantalla de menú principal del instalador. No se olviden de subir el cursor ya que la opción predeterminada es revisar si
el medio que bajaron no está corrupto: 

{% include img.html url="/assets/minimal-install-centos7/02-install.png" %}


