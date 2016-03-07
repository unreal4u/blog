---
comments: true
date: 2016-03-07 01:00:00
layout: post
slug: lvm-parse-error-y-como-solucionarlo
title: LVM parse error (y cómo solucionarlo)
categories:
- Linux
---

En la casa estoy montando un servidor y hoy le quise agregar un nuevo disco duro. Sin embargo, cada vez que intentaba
hacer algo con <code>pvs</code>, <code>vgs</code> o <code>lvs</code> me salía el siguiente problema: 

<pre>
  Parse error at byte 969 (line 58): unexpected token
  Error parsing metadata for VG vg_home.
  Cannot process volume group vg_home
  Internal error: VG lock centos must be requested before vg_home, not after.
  Can't get lock for centos
  Cannot process volume group centos
  Internal error: VG lock vg_gdrive must be requested before vg_home, not after.
  Can't get lock for vg_gdrive
  Cannot process volume group vg_gdrive
  Internal error: Volume Group vg_home was not unlocked
</pre>

Haz click en el título de esta entrada para ver la entrada completa.

<!-- more -->

Buscando por internet di con muchos problemas parecidos, pero que justo no eran. El único tip que sí funcionó, fue el
que encontré en un [bug reportado aquí](https://bugzilla.redhat.com/show_bug.cgi?id=1119045) donde un comentario
bastante equeto dio en el clavo: "[LVM does not cope with hostnames that span several lines...](https://bugzilla.redhat.com/show_bug.cgi?id=1119045#c29)".

Esto fue la clave para que mirara en mi <code>/etc/hostname</code> y efectivamente, los contenidos de este archivo eran:

<pre>
localhost.localdomain
server.home.unreal4u.com
</pre>

Cambié este archivo por lo siguiente: 

<pre>
server.home.unreal4u.com
</pre>

Y violà, sin siquiera reiniciar ahora puedo ver de nuevo mis discos y particiones: 

<pre>
[root@localhost ~]# pvs
  PV         VG        Fmt  Attr PSize   PFree
  /dev/sda2  centos    lvm2 a--   37.00g    0
  /dev/sda3  vg_home   lvm2 a--   24.09g    0
  /dev/sdb   vg_gdrive lvm2 a--  298.09g    0
</pre>

Es increíble como la informática interrelaciona todo. Un nombre de un servidor repercutiendo en el funcionamiento de 
discos duros. Con esto declaro que ya lo he visto casi todo.
