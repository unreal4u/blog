---
comments: true
date: 2010-09-30 15:26:23+00:00
layout: post
slug: chequear-sintaxis-de-my-cnf-y-httpd-conf
title: Chequear sintaxis de my.cnf y httpd.conf
wordpress_id: 452
categories:
- Bases de Datos
- Linux
---

Aunque por lo general ningún servidor me da problemas, la máquina  que aloja este blog está dando un poco de jugo. El proceso del Apache  tiene la mala costumbre de tomarse el 100% del procesador por algunos  segundos (hasta 30) y durante ese lapso de tiempo simplemente no aparece  nada en el cliente. Sin embargo, ese error es tan vago que no da para  nada más. En los logs tampoco aparece nada inusual, así que simplemente  aplicaré regla de oro en la informática: ir probando para ver si puedo  replicar el error. (Que manera tan elegante de decir: "prueba y error").
<!-- more -->
Para hacer esto, frecuentemente estaré retocando algunos archivos de  configuración, primordialmente la de MySQL y por supuesto la de Apache y  PHP, pero... como se trata de una máquina en producción, no puedo darme  el lujo de retocar el archivo de configuración, reiniciar el servicio y  darme cuenta que no se levantó debido a que existe un error en la  sintaxis. ¿Cómo se hace entonces para verificar que el archivo de  configuración está correcto?
La respuesta a esta interrogante es bastante fácil de responder, aunque en MySQL es bastante más vago que en Apache.

Apache trae incorporado un comando que permite reiniciar el servicio  realizando una verificación de sintaxis previa. Si la sintaxis está  bien, cargará a RAM las nuevas directivas. Si la sintaxis es incorrecta,  muestra un mensaje de error y no cargará a RAM la nueva sintaxis. El  comando para hacer esto es:

    
    /etc/init.d/httpd reload
    


Con esto podemos comprobar los cambios que hemos hecho en httpd.conf y en php.ini.

Para MySQL es un poco más complicado, porque no existe una  herramienta que compruebe bien la sintaxis. Sin embargo, podemos  ejecutar:

{% highlight bash %}
mysqld --help
# opcionalmente
mysqld --help --verbose
{% endhighlight %}

Nótese que si no se encuentra el programa, se debe hacer un `whereis mysqld` y ejecutar la ruta completa en vez de la relativa.

Por mientras seguiré analizando qué es lo que pasa y más importante aún: por qué pasa.
