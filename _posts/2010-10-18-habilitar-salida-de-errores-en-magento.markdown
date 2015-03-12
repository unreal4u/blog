---
author: admin
comments: true
date: 2010-10-18 03:51:19+00:00
layout: post
slug: habilitar-salida-de-errores-en-magento
title: Habilitar salida de errores en Magento
wordpress_id: 458
categories:
- Magento
- Mundo Web
- PHP
---

Si alguna vez les ha salido el mensaje "Exception printing is disabled by default for security reasons" entonces sabrán que algo se  hizo mal. Sin embargo, tampoco podremos saber qué salió mal, algo  relativamente importante en el caso de que estemos desarrollando algún  plugin o modificando algunos comportamientos del mismo Magento.

Para activar la salida de errores, basta modificar un archivo:
[bash]cd /magento/
cp errors/local.xml.sample errors/local.xml
[/bash]

Y listo! Con este tip ya pueden revisar el trace que genera Magento y poder solucionar sus errores rápidamente.
