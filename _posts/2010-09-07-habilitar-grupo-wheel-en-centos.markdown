---
author: admin
comments: true
date: 2010-09-07 14:53:06+00:00
layout: post
slug: habilitar-grupo-wheel-en-centos
title: Habilitar grupo wheel en CentOS
wordpress_id: 438
categories:
- Linux
---

Estaba instalando un servidor hoy y lo principal es la seguridad  en esa máquina. Por lo tanto, hay que restringirlo lo más posible; lo  cual implica desde cambiar el puerto predeterminado en el cual SSH  escucha hasta los usuarios que pueden invocar su.

Tocaremos principalmente dos archivos por mientras:
`/etc/ssh/sshd_config
/etc/pam.d/su`

Para los primeros pasos, hacemos todo con nuestra cuenta de root.
<!-- more -->
En el primer archivo, descomentamos:
[bash]Port 55331
Protocol 2
PermitRootLogin no
IgnoreRhosts yes
PermitEmptyPasswords no
X11Forwarding no
[/bash]
Mientras que en el segundo, descomentamos:

    
    auth           required        pam_wheel.so use_uid
    


A su vez, hacemos:
[bash]adduser unreal4u
passwd unreal4u
usermod -G wheel unreal4u
[/bash]
Finalmente, limpiamos:
[bash]/etc/init.d/sshd restart
exit
[/bash]
Ahora ya podemos logearnos como unreal4u y sólo ese usuario podrá  hacer su (tanto a root como a otros usuarios). Los demás usuarios del  sistema que no se encuentren en el grupo wheel si bien es cierto podrán  hacer uso del comando, sólo les saldrá un mensaje de que la contraseña  fue incorrecta, aunque haya sido correcta.

Por último, debemos tomar en cuenta que desde ahora en adelante, de  la única manera (remota) en que podamos hacer uso de root, es haciendo:
[bash]ssh -p 55331 unreal4u@[host]
# una vez adentro
su -
[/bash]
Espero les haya servido :)
