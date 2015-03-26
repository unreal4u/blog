---
comments: true
date: 2013-01-21 21:12:49+00:00
layout: post
slug: instalar-nginx-en-mac-os-x-mountain-lion
title: Instalar nginx en Mac OS X Mountain Lion
wordpress_id: 763
categories:
- Apple/Mac
- Linux
---

Desde hace algún tiempo que tenía las ganas de aliviarle un poco de carga a mi Mac y de paso, acelerar el desarrollo en un par de milisegundos por request, aunque la verdad estaba tan cómodo con el rendimiento del equipo que en realidad no hacía falta. Sin embargo, en un rato de ocio me propuse a instalar el famoso nginx como un proxy y servidor de contenido estático, cosa de dejar al apache sólo encargado de la parte PHP. Quise expresamente esta solución ya que he visto nginx sirviendo PHP directamente y puede dar lugar a unos bugs muy pero muy extraños, así que como buen developer que soy, prefiero quedarme con algo estable y bien probado para poder avanzar rápido en mi trabajo a tirarme a probar cosas nuevas que pueden no resultar como quiero y hacerme perder tiempo valioso. 

Siguiendo esa misma filosofía, les presento una de las maneras más rápidas de integrar nginx en el sistema de la manzanita y de paso podrán apreciar que especialmente en lo que concierne contenido estático, andará bastante más liviano el equipo, especialmente teniendo en cuenta que Apache levantará PHP con xdebug, profiler y un montón de cosas más que no necesitamos en un simple CSS, imagen o archivo con JavaScript.
<!-- more -->



## Partiendo con los pre-requisitos


Para no perder la costumbre, haré la instalación de la forma más simple posible sin tener que meterle software adicional que pueda llegar a dar conflictos con el software previo ya instalado. De esa forma, compilaré nginx desde source e instalaré las dependencias necesarias a mano. 

La única dependencia que necesitarán es pcre, que se instala si quieren soporte para el módulo de re-escritura (http_rewrite_module). En mi caso, ya la tenía instalada cuando instalé APC, así que las instrucciones para instalar pcre [están en este post](http://blog.unreal4u.com/2012/09/instalando-las-extensiones-de-php-basicas-en-mountain-lion/). La página oficial de nginx dice que soporta hasta la 8.21 pero yo ya tenía instalada la 8.31 y ahora salió la 8.32 que tampoco debería dar problemas. Si la 8.32 da problemas, instalen la 8.31.

Ya con pcre instalado, podemos proceder a bajar la última versión de nginx desde la página oficial y lo descomprimimos. Una vez hecho eso, invocamos los siguientes comandos, como root:

{% highlight bash %}
./configure --conf-path=/etc/nginx.conf --sbin-path=/usr/sbin/nginx --with-http_ssl_module
make
make install
vi /etc/nginx.conf
{% endhighlight %}

Lo que hice fue asignar el archivo de configuración en `/etc/nginx.conf` (Simplemente porque es lo estándar), asignar el ejecutable en `/usr/sbin/nginx` y compilarlo con soporte para ssl. No creo que alguna vez lo ocupe pero tampoco tengo ganas de recompilar si alguna vez lo necesitara.



## Configuración


El archivo de configuración de nginx lo seteé con los siguientes valores:

{% highlight bash %}
# usuario(espacio)grupo
user _www _www;
daemon off;
worker_processes  2;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile      on;
    keepalive_timeout  10;
    gzip          on;

    server {
        listen       80;
        server_name  localhost;
        charset      utf-8;

        location / {
                root   /Users/unreal4u/html/;
                index  index.php index.html index.htm;
                autoindex on;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
        location ~ \.php$ {
            proxy_pass              http://127.0.0.1:8080;
            proxy_redirect          off;
            proxy_set_header        Host    $host;
            proxy_set_header        X-Real-IP       $remote_addr;
            proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header        remote-addr     $remote_addr;
            client_max_body_size    10m;
            client_body_buffer_size 128k;
            proxy_connect_timeout   90;
            proxy_send_timeout      90;
            proxy_read_timeout      90;
            proxy_buffers           32      4k;
        }
    }
}
{% endhighlight %}

**Punto importante**: Ajusten las rutas a su propia realidad y no se olviden que en mi caso, es una máquina de desarrollo por lo que algunas opciones de seguridad están desactivadas, entregando mucha más información que la de costumbre al visitante en caso de algún error! Si desean usar este setup en un entorno de producción, infórmense antes y seteen los valores apropiadamente. No hay que olvidarse tampoco de instalar algún módulo que transforme las ip como mod_rpaf (para Apache) en sistemas de producción.
El usuario y grupo de nginx lo dejé en el mismo valor que el de Apache, por una razón bastante simple: si esos permisos funcionan con Apache, tendrá que funcionar también con nginx.

Acto seguido, ajustamos la configuración de `/etc/apache2/httpd.conf`:

{% highlight bash %}
(...)
# Dejar apache escuchando sólo en 127.0.0.1 en el puerto 8080
Listen 127.0.0.1:8080
(...)
# Desactivar gzip para Apache (nginx hará ahora ese trabajo)
#LoadModule deflate_module libexec/apache2/mod_deflate.so
{% endhighlight %}

La única otra opción en Apache que modifiqué fue en `/etc/apache2/extra/httpd-default.conf`, donde desactivé KeepAlive, dejándolo como `KeepAlive Off` ya que esto; al igual que la compresión con gzip; lo hará nginx a partir de ahora y en el caso del primero no tiene sentido comprimir algo con Apache para descomprimirlo con nginx y posteriormente comprimirlo nuevamente con nginx antes de enviarlo al cliente.



## Metiéndose a las entrañas de Mac OS X


Hasta ahora, hemos instalado y configurado nginx, pero todavía no lo hemos activado. Para activarlo basta llamar a su ejecutable mediante el comando "nginx" pero esto no es muy elegante y cada vez que reiniciemos el equipo tendremos que ejecutar este comando nuevamente si queremos que se inicie el servidor. También para reiniciarlo es un dolor de cabeza ya que tendremos que matar todos sus procesos a mano e iniciarlo nuevamente.
Por eso mismo, es que me puse a buscar algún script que me permitiera con un solo comando iniciar, reiniciar o parar el servidor. Lo malo de esto son dos puntos:


  * Hay mucha documentación que ya no es válida y/u obsoleta


  * nginx no es un servicio "típico", ya que el comando final para parar el servicio difiere bastante del que se usa para iniciarlo



Sin embargo, después de mucha prueba y error, di finalmente con una manera de instalar un daemon fácilmente ejecutable y que se incorpora al uso estándar del OS de la manzana de forma que el servicio se inicie en conjunto con los demás servicios bajo el mismo estilo. 

Para esto, necesitaremos crear dos archivos, el primero ubicado en: 
`/Library/LaunchDaemons/org.nginx.nginx.plist`

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
                       "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key><string>nginx</string>
    <key>Program</key><string>/usr/sbin/nginx</string>
    <key>KeepAlive</key><true/>
    <key>NetworkState</key><true/>
    <key>StandardErrorPath</key><string>/var/log/system.log</string>
    <key>LaunchOnlyOnce</key><true/>
  </dict>
</plist>
{% endhighlight %}

y el segundo ubicado en `/usr/sbin/nginxd`

{% highlight bash %}
#!/bin/sh

if [ "$1" = "start" ]; then
        launchctl load -F /Library/LaunchDaemons/org.nginx.nginx.plist
elif [ "$1" = "stop" ]; then
        launchctl unload -F /Library/LaunchDaemons/org.nginx.nginx.plist
elif [ "$1" = "restart" ]; then
        launchctl unload -F /Library/LaunchDaemons/org.nginx.nginx.plist
        launchctl load -F /Library/LaunchDaemons/org.nginx.nginx.plist
else
        echo "Usage: nginx (start | stop | restart)"
fi
{% endhighlight %}

A este último archivo le damos `chmod +x /usr/sbin/nginxd`

Por último, y sólo porque soy un flojo cómodo, creamos otro archivo, esta vez llamado `/sbin/webservers` con el siguiente contenido: 

{% highlight bash %}
#!/bin/bash

apachectl $1 && nginxd $1
{% endhighlight %}

Le damos permisos de ejecución (chmod +x) y desde ahora en adelante, si ajustamos algo en la configuración de los servidores web no tenemos que hacer nada más que un simple `webservers restart` desde la consola para reiniciar ambos servicios. Si queremos reiniciar sólo nginx, simplemente ingresamos `nginxd restart` o si queremos reiniciar sólo apache, hacemos el ya conocido `apachectl restart`.

Ahora lo único que nos falta es reiniciar ambos servidores para aplicar los cambios con nuestro recién creado ejecutable: `webservers restart`. Con eso se aplicarán todos los cambios realizados hasta el momento y registraremos el servicio con el sistema operativo.

El próximo paso será instalar y experimentar con Varnish, que ya en el año 2010 [era capaz de entregar 275.000 requests por segundo](http://kly.no/posts/2010_10_23__High_End_Varnish___275_thousand_requests_per_second___.html)! Aunque así como están las cosas, [G-WAN también se ve muy interesante](http://nbonvin.wordpress.com/2011/03/24/serving-small-static-files-which-server-to-use/) debo decir!

Información útil:
[Thoughts on software development](http://musingsonoftwaredevelopment.blogspot.nl/2012/08/building-nginx-from-source-on-mac-osx.html)
[Wiki nginx](http://wiki.nginx.org/InstallOptions)
[Always Get Better](http://www.alwaysgetbetter.com/blog/2010/09/25/give-apache-break-nginx/)
