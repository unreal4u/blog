---
author: admin
comments: true
date: 2013-08-25 11:57:21+00:00
layout: post
slug: instalando-centos-6-con-php-5-4-17-y-mysql-5-5-31
title: Instalando CentOS 6 con PHP 5.4.17 y MySQL 5.5.31
wordpress_id: 800
categories:
- Linux
- Mundo Web
- PHP
---

Hacía ya algún tiempo que no me tocaba instalar un webserver, y justo estos días me tocó instalar tres (con otro más por hacer en el horizonte), así que decidí hacer una nueva entrada [actualizando el antiguo](http://blog.unreal4u.com/2010/09/how-to-instalar-una-maquina-centos-con-php-5-3-3/) y de esa forma ahorrarme algún que otro problema que pudiera surgir en el futuro. 

Lo que haremos con esta máquina es instalar Apache y la última versión estable de tanto PHP como MySQL. Además configuraremos algunos valores bases de SELinux e iptables, y finalmente instalaremos Logwatch para recibir un informe día a día de la actividad en el servidor. Aprovecharemos asimismo de instalar también [OPcache para PHP](http://www.php.net/manual/en/intro.opcache.php), el cual ya viene incorporado de forma predeterminada en PHP 5.5, pero para la rama 5.4 todavía no.
<!-- more -->

Una vez que la instalación mínima está terminada, ejecutamos los siguientes comandos, todos como root:
[bash]
yum install httpd php php-mysqli php-pdo git mysql-server php-pear wget lynx vim-enhanced logwatch bind-utils
## Si vamos a instalar Gitolite, también: ##
yum install git-daemon perl perl-Time-HiRes
adduser git
## Fin instalación Gitolite ##
/etc/init.d/mysqld start
chkconfig --levels 235 mysqld on
mysql -uroot
## Configurar mysql, como tip: ##
## GRANT ALL ON *.* TO 'root'@'localhost' IDENTIFIED BY 'clave-muy-complicada'; ##
## FLUSH PRIVILEGES; ##
## DROP DATABASE test; ##
## quit ##
rm ~/.mysql_history
adduser u4u
passwd u4u
## Agregar al grupo wheel: sólo u4u podrá hacer su ##
usermod -G wheel u4u
vi /etc/pam.d/su
## Descomentar línea que dice: auth        required    pam_wheel.so use_uid ##
vi /etc/ssh/sshd_config
## Editar los siguientes valores: ##
## PermitRootLogin no ##
## PermitEmptyPasswords no ##
## UseDNS no ##
## Siguiente línea para darle acceso SSH sólo al usuario u4u ##
## AllowUsers u4u ##
## Si instalamos Gitolite, también dar acceso al usuario git ##
/etc/init.d/sshd restart
/etc/init.d/httpd start
chkconfig --levels 235 httpd on
vi /etc/httpd/conf/httpd.conf
vi /etc/httpd/conf.d/php.conf
/etc/init.d/httpd restart
vi /usr/share/logwatch/default.conf/logwatch.conf
## Editar correo y level de logeo, a mi me gusta dejarlo en Med (5) ##
rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
yum update -y
yum install yum-plugin-replace
yum replace mysql --replace-with mysql55
/etc/init.d/mysqld restart
yum remove php php-common php-cli
## Algunos dirán que no tiene sentido instalar PHP para luego desinstalarlo, pero la verdad ##
## es que me dio flojera instalar una VM para comprobar que todas las dependencias se calculan ##
## bien considerando que instalamos PHP desde una repo no oficial... ##
yum install php54w php54w-cli php54w-common php54w-intl php54w-pecl-zendopcache php54w-mysql55 php54w-gd
vi /etc/php.ini
## Editar a gusto, recomiendo encarecidamente desactivar allow_url_fopen (dejar en Off) ##
vi /etc/php.d/opcache.ini
## Editar a gusto ##
/etc/init.d/httpd restart
[/bash]

Con esto tendremos una máquina ya casi funcional. No se olviden de darle acceso a Apache mediante `chcon`, ya que de otra forma SELinux no les dará acceso. Recomiendo revisar [este artículo al respecto](http://wiki.centos.org/HowTos/SELinux) y tener en cuenta la salida de `/var/log/audit/audit.log`. No recomiendo desactivar SELinux ya que sirve como capa adicional de seguridad **una vez que se configura bien**, haciendo totalmente innecesario la instalación de herramientas tales como rkhunter u otros.
La configuración de SELinux eso si se las dejo como tema de investigación, en realidad es bastante simple y hay algunos tutoriales increíblemente buenos dando vueltas en la red.
