---
comments: true
date: 2011-04-03 20:21:54+00:00
layout: post
slug: migrar-usuarios-linux-de-un-sistema-a-otro
title: Migrar usuarios Linux de un sistema a otro
wordpress_id: 520
categories:
- Linux
---

En este preciso momento me encuentro en una tarea bien tediosa: actualizando de un webserver a otro por cambio de hardware. Bueno, también aprovechamos de meter ese webserver al VMware pero ese es otro tema: lo importante es que esa actualización había que hacerla.
¿Lo único malo? 44 cuentas de usuario y 70GB de datos que transferir. Así que mientras comprimo el /home/ me di el tiempo de empezar a escribir esta entrada.

La idea de la migración es que sea algo (casi) totalmente transparente para los usuarios, lo cual implica; además de cambiar los archivos de lugar y respaldar los datos de la base de datos; mantener las cuentas de usuario entre máquina y máquina y quizás más importante: mantener las contraseñas y permisos sobre los archivos.
<!-- more -->



## Empezando


Lo primero que hay que hacer es obvio: tener la nueva máquina lista, configurada y andando sólo con lo básico: NO debemos crear usuarios y tampoco crear bases de datos. Hasta el momento no deberá tener alguna cuenta adicional a root y las predeterminadas con UID debajo de 500. (Suponiendo que estamos ocupando un redhat-based OS, en Debian creo que el inferior parte en 1000). De esta forma, al copiar los archivos nos aseguramos de que siempre pertenezcan a los mismos usuarios al cual pertenecían originalmente.
Tampoco deberemos tocar la configuración de Apache, MySQL o PHP, ya que todas ellas las reemplazaremos.



## Respaldando todo en la máquina de origen


Los archivos que hay que respaldar son /etc/group, /etc/passwd, /etc/shadow y /etc/gshadow que raramente se ocupa pero puede darse el caso. También hay que respaldar las bases de datos y el mail, siendo estas últimas dos operaciones bastante básicas. Por último, se debe respaldar los homes.

De los primeros archivos a respaldar, la idea es no machacar aquellas cuentas del sistema, así que ahí está la complejidad: para esto, filtramos con awk los resultados de ese backup.

Todos los comandos se ejecutan como root:

{% highlight bash %}
mkdir /root/migracion
cd /root/migracion/
export UGIDLIMIT=500
awk -v LIMIT=$UGIDLIMIT -F: '($3>=LIMIT) && ($3!=65534)' /etc/passwd > passwd.mig
awk -v LIMIT=$UGIDLIMIT -F: '($3>=LIMIT) && ($3!=65534)' /etc/group > group.mig
awk -v LIMIT=$UGIDLIMIT -F: '($3>=LIMIT) && ($3!=65534) {print $1}' /etc/passwd | tee - |egrep -f - /etc/shadow > shadow.mig
cp /etc/gshadow gshadow.mig
cp /etc/httpd/conf/httpd.conf httpd.conf.mig
cp /etc/php.ini php.ini.mig
cp /etc/my.cnf my.cnf.mig
tar cvpf mails.tar /var/spool/mail
tar cvpf homes.tar /home
mysqldump -uroot -p --all-databases --flush-privileges --comments --disable-keys --lock-all-tables --lock-tables --result-file=respaldo_dbs.sql
# Debemos crear la carpeta /root/migracion/ en la nueva máquina y hacemos:
scp * root@ip-nueva-maquina:/root/migracion/
{% endhighlight %}

Nótese que cuando creamos el home no estamos comprimiendo (opción z), sino sólo creando un archivo. De esta forma, es mucho más rápido.
Hasta el momento hemos creado un snapshot completo y estamos transfiriendo todo a la segunda máquina. Lo único que falta es parar los servicios como Apache, mailing y bases de datos para que mientras montemos el segundo sistema no hayan diferencias con el primer respaldo.



## En la segunda máquina


El único trabajo que queda es instalar la nueva máquina. Ya en la máquina anterior vimos cómo se traspasó todo pero falta descomprimir todo e instalar. Todo se debe ejecutar como root.

{% highlight bash %}
cd /root/migracion/
mkdir respaldo-nuevo
cp /etc/group /etc/passwd /etc/shadow /etc/gshadow /etc/httpd/conf/httpd.conf /etc/php.ini /etc/my.cnf respaldo-nuevo/
tar xf homes.tar  -C /
cat passwd.mig >> /etc/passwd
cat group.mig >> /etc/group
cat shadow.mig >> /etc/shadow
cp gshadow /etc/gshadow
tar xv mails.tar -C /
mysql -uroot < respaldo_dbs.sql
cp httpd.conf.mig /etc/httpd/conf/httpd.conf
cp php.ini.mig /etc/php.ini
cp my.cnf.mig /etc/my.cnf
/etc/init.d/mysqld restart
/etc/init.d/httpd restart
{% endhighlight %}

Y eso sería todo amigos! Ahora sólo nos quedará probar si todo está bien: prueben metiéndose con algún usuario en específico (del que se conoce la contraseña), revisen si Apache y MySQL está realmente andando, cambien IP o renueven sus DNS y estaría todo listo para realizarle un format C: (irony lvl: 200%) a la primera máquina y seguir todas las operaciones en la segunda.

Espero les haya servido.

Fuente: [NixCraft](http://www.cyberciti.biz/faq/howto-move-migrate-user-accounts-old-to-new-server/)
