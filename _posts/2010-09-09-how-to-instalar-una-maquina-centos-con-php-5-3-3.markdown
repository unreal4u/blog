---
comments: true
date: 2010-09-09 18:00:44+00:00
layout: post
slug: how-to-instalar-una-maquina-centos-con-php-5-3-3
title: '[HOW-TO] Instalar una máquina CentOS con PHP 5.3.3'
wordpress_id: 441
categories:
- Linux
- PHP
---

Configurar bien una máquina puede ser una tarea tediosa,  especialmente por el hecho de que muchas veces nos podemos olvidar de  más de algún detalle.

La presente es una instalación de PHP 5.3.3 en una máquina CentOS 64  bits limpia (instalación servidor) que implementa además dos extensiones  fundamentales a mi parecer: APC y Suhosin. El primero sirve como [opcode](http://es.wikipedia.org/wiki/Opcode) cache y el segundo funciona para frenar algunas malas prácticas en la  programación de PHP, especialmente aquellas que tienen que ver con  overflows. También le agrega una capa adicional de seguridad que  predeterminadamente no viene integrada a PHP.

Sin embargo, no hay que olvidar que los repositorios oficiales todavía  están con PHP 5.1.6 así que hay que habilitar un repositorio que tenga  la última versión de PHP. Para este how-to, voy a ocupar el repositorio  de remi, aunque cualquier otro también puede servir. (Sé que Webtatic  también tiene la última versión, tanto en rama 5.2 como en 5.3). Por  último, también aprovecho de instalar el repositorio de rpmforge por  tener mucho software adicional que no viene en la base.
<!-- more -->
Antes del resumen de comandos, me gustaría aclarar que personalmente  he testeado muchísimo la última versión de PHP (rama 5.3) y anda super  estable y bastante bien, incluso mejor que la ya madura rama 5.2, y  entre la 5.1.6 y la 5.3.3 o la 5.2.14, es mucho mejor opción la última  versión en cualquiera de sus dos ramas: no solo anda más rápido, sino  que también tiene muchas características nuevas y correcciones de bugs  que las versiones anteriores no tenían. No hay que olvidar que la 5.1.6  se lanzó hace bastante tiempo y que ya es hora de ir actualizándola.

Por último, aunque lo veo difícil, DISCLAIMER: no me hago responsable si su server explota.

Todos los comandos se ejecutan con root:

{% highlight bash %}
cd /etc/yum.repos.d/
wget http://rpms.famillecollet.com/enterprise/remi.repo
yum install yum-priorities
cd /tmp/
wget http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.1-1.el5.rf.x86_64.rpm
rpm -Uvh rpmforge-release-0.5.1-1.el5.rf.x86_64.rpm
vi /etc/yum.repos.d/CentOS-Base.repo
vi /etc/yum.repos.d/remi.repo
vi /etc/yum.repos.d/rpmforge.repo
yum erase mysql mysql-server
yum -y update
yum install php-devel mysql mysql-server mysql-devel httpd-devel php-pear htop lynx vim-enhanced
wget http://download.suhosin.org/suhosin-0.9.32.1.tar.gz
tar zfvx suhosin-0.9.32.1.tar.gz
cd suhosin-0.9.32.1
phpize
./configure
make
make install
pecl install apc-beta
vim /etc/php.d/apc.ini
vim /etc/php.d/suhosin.ini
/etc/init.d/httpd restart
mysql
# una vez dentro de mysql:
GRANT ALL ON *.* TO 'root'@'localhost' IDENTIFIED BY 'una contraseña muy muy segura';
FLUSH PRIVILEGES;
quit
exit
{% endhighlight %}

Un par de observaciones:

* Primero instalé yum-priorities y le di máxima prioridad a remi, para  sobre-escribir el PHP y MySQL predeterminado de base. Esto se hace  editando cada repo y asignándole una prioridad, ejemplo:
<pre>
[base]
priority=5
[centosplus]
priority=6
</pre>
Donde 1 es el más importante, y 99 el menos importante. A remi.repo le  asigno priority=3, a Base priority=5 y por último a rpmforge le asigno  priority=6.

* Después borré mysql, debido a que habían algunos conflictos entre  la versión instalada y la que se quería instalar. Al borrar MySQL y  luego instalándola desde cero se solucionó ese problema.

* La versión de APC que instalo es la beta. Esto es debido a que la  versión normal de APC está con una pifia desde marzo del 2009 que  solucionaron en la beta, pero nunca en la final. [Más información](http://pecl.php.net/bugs/bug.php?id=16078).

* En apc.ini y suhosin.ini basta con incluir la siguiente línea:
<pre>
extension=apc.so
extension=suhosin.so
</pre>

* Sí, hay una extensión de Suhosin y también hay un Patch. Hubiese  sido mejor haber instalado el patch, pero eso implicaba recompilar PHP  y... ehmm... paso. El sitio que se instalará finalmente tampoco es tan masivo y sólo si ocurre algún problema, se aplicará el parche.

* Para las contraseñas de root, mysql y otros recomiendo [este password generator](http://www.thebitmill.com/tools/password.html).

* Omití los ajustes que normalmente hago en php.ini y en  httpd.conf, estos corren por su cuenta ya que dependen netamente de cómo  quieran configurar su máquina. Recomiendo [echarle un ojo a este post](../2010/08/configuracion-de-php-testing-vs-produccion-vs-hosting/) con respecto a la configuración de PHP.

Espero que les haya servido.
