---
author: admin
comments: true
date: 2010-09-20 04:21:29+00:00
layout: post
slug: proteger-un-directorio-con-htaccess-htpasswd
title: Proteger un directorio con .htaccess + .htpasswd
wordpress_id: 443
categories:
- Linux
---

Este post será cortito y es sólo para que no tenga que andar  buscando dentro de logs algo que aplico sólo muy a veces: se trata de  querer proteger un directorio predeterminado mediante htpasswd para que  de esta manera, necesitemos de autentificación antes de poder continuar  escudriñando en la página.

El método es bastante simple: basta crear un .htaccess y un  .htpasswd, el segundo ojalá fuera del directorio público. De todas  formas, apache _predeterminadamente_ manda un 403 (Prohibido) a  todos los archivos que empiezan con .ht (dato rosa, con expresiones  regulares sería "^\.ht") pero nunca está demás poner una capita  adicional a la seguridad del server. Más después del salto.
<!-- more -->

Lo primero es lo primero, tenemos que indicar dentro de un .htaccess que  el directorio que se desea visitar es un directorio protegido.
[bash]AuthType Basic
AuthName "Entrar al area 51"
AuthUserFile /home/user/.htpasswd
require valid-user
[/bash]
Es muy importante que AuthUserFile sea **una ruta absoluta** de lo contrario **no** funcionará! Obviamente tienen que reemplazar la ruta absoluta por la suya propia.

Finalmente, creamos nuestro .htpasswd:
[bash]cd /home/user/
htpasswd -cb .htpasswd usuario-secreto password-secreta
[/bash]
La sintaxis es bien simple, -cb indica dos cosas, la C (minúscula)  está para crear el archivo mientras que la B indica que se escribe la  password directamente en la línea de comandos. Si se omite B, se pedirá  el ingreso de contraseña.
Otro modificador útil es -n, que no crea el archivo sino que sólo  imprime la línea a incluir en el .htpasswd, útil ya que C borra todo el  contenido que ya se encuentre en .htpasswd.
El tercer parámetro es el nombre del archivo, el cuarto el nombre de  usuario y el quinto (siempre y cuando no se utilice N) es la contraseña.

Para mayor información, revise `man htpasswd`. Es cortito y fácil de entender.

Con estos pasos queda listo y la próxima vez que queramos ingresar  nos pedirá usuario y contraseña, que en el ejemplo de arriba quedaría:
user: usuario-secreto
pass: password-secreta

Espero que les haya servido.
