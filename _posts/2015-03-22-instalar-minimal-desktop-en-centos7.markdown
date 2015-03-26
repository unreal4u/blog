---
comments: true
date: 2015-03-22 00:00:00+00:00
layout: post
slug: instalar-minimal-desktop-en-centos7
title: Instalar Minimal Desktop en CentOS7
categories:
- Pensamientos Personales
- Linux
---

Lo único que tenemos que hacer es hacer la instalación mínima, y una vez instalado ejecutar los siguientes comandos como root:

{% highlight bash %}
yum groupinstall "X Window System"
yum install gnome-classic-session gnome-terminal nautilus-open-terminal control-center liberation-mono-fonts
unlink /etc/systemd/system/default.target
ln -sf /lib/systemd/system/graphical.target /etc/systemd/system/default.target
reboot
{% endhighlight %}

[Fuente](https://www.centos.org/forums/viewtopic.php?f=47&t=47088)
