---
comments: true
date: 2010-08-27 04:02:34+00:00
layout: post
slug: configuracion-de-php-testing-vs-produccion-vs-hosting
title: Configuración de PHP testing vs producción vs hosting
wordpress_id: 403
categories:
- PHP
---

Frecuentemente me he dado cuenta de que sobretodo en algunos hostings masivos, la configuración de PHP quizás no es la más adecuada en cuanto a rendimiento y seguridad. Lo primero por lo general es por un tema de retrocompatibilidad, mientras que lo segundo es para que no los tapen a llamadas debido a que algo no funciona como debe (y no revisan los logs).

Este post tratará fundamentalmente de este tema: se explicarán algunas de las opciones más extrañas o menos documentadas de PHP (última versión de la rama 5.2) y se comparará su uso en un ambiente de testing versus un ambiente de producción. Así que dale a "continuar leyendo" que esto no es fácil de encontrar!
<!-- more -->

A continuación mostraré un archivo php.ini con toda su configuración recomendada para una máquina de testeo. Ojo que van a faltar muchas cosas, sólo pondré las que más pueden confundir.



## El archivo en resumen


{% highlight bash %}
# ------------------ GENERAL -------------------------- #
engine = On
# DESCRIPCIÓN: Habla por si solo... sin esto no hay PHP xD Siempre on.
zend.ze1_compatibility_mode = Off
# DESCRIPCIÓN: Esto es retrocompatibilidad con PHP4... sólo hay que tener consideración con aquellas cosas deprecadas de PHP4 en PHP5.
# PRODUCCIÓN: Recomiendo off.
# HOSTING: En los hosting por lo general le colocan on.
short_open_tag = Off
# DESCRIPCIÓN: Habilita los short tags de PHP, tales como <?=$hola?>, el if corto y otros más. Mala costumbre de programación en todo caso.
# PRODUCCIÓN: Recomiendo off.
# HOSTING: Por lo general está en on.
asp_tags = Off
# DESCRIPCIÓN: Habilita los tags tipo ASP.
# PRODUCCIÓN: Recomiendo off.
# HOSTING: Por lo general está en on.
implicit_flush = Off
# DESCRIPCIÓN: Ver más abajo.
# PRODUCCIÓN y HOSTING: Recomiendo Off, en los hostings por lo general está en Off también.
safe_mode = Off
# DESCRIPCIÓN: Ver explicación de abajo.
# PRODUCCIÓN: Recomiendo off.
# HOSTING: He visto un 50% de hostings con el safe_mode activado y otro 50% desactivado.
disable_functions = show_source, system, shell_exec, passthru, exec, popen, proc_open, dl, get_current_user, getmypid
# DESCRIPCIÓN: Permite deshabilitar algunas funciones que pueden poner en peligro la integridad del Server.
# PRODUCCIÓN: Aparte de las que están arriba, recomiendo agregar phpinfo, eval y todas aquellas relacionadas con el manejo de PHP a nivel de línea de comando.
# HOSTING: En algunos hostings son bastante restrictivos, en otros no pescan esta directiva.
disable_classes =
# DESCRIPCIÓN: Idem que arriba, sólo que se aplica a classes y no a funciones.

# ----------------------------- DEBUG y LOG ------------------------ #
expose_php = On
# DESCRIPCIÓN: Muestra que PHP está corriendo en alguna página que tenga error.
# PRODUCCIÓN: Off, definitivamente.
# HOSTING: Idem que arriba.
error_reporting = E_ALL | E_STRICT
# DESCRIPCIÓN: Muestra los errores en pantalla.
# PRODUCCIÓN: "0", sin comillas.
# HOSTING: Idem que arriba.
display_errors = On
# DESCRIPCIÓN: Determina dónde notifica los errores, ya sea STDOUT, STRERR o ninguno.
# PRODUCCIÓN: stderr (Por comodidad y no tener que andarlo cambiando en runtime).
# HOSTING: Por lo general está en Off, especialmente si el usuario no tendrá acceso via SSH.
display_startup_errors = On
# DESCRIPCIÓN: Muestra los errores de inicialización del <strong>motor</strong> de PHP.
# PRODUCCIÓN: Off
# HOSTING: Nunca lo he visto en On.
log_errors = Off
# DESCRIPCIÓN: Si logear los errores en algún archivo.
# PRODUCCIÓN: Ya que mostraremos en pantalla en la máquina de testing, bajo condiciones de producción hay que habilitarlo.
# HOSTING: Si tienes acceso a SSH, lo más probable es que esté en On. Sino, lo más probable es que esté en Off.
html_errors = Off
# DESCRIPCIÓN: Genera errores a los cuales les puedes clickear la función y te llevará a una página que explica el error o la función en específico. Suena bonito, pero es bastante inútil en el 95% de los casos.
# PRODUCCIÓN: Off.
# HOSTING: Por lo general está en Off.

# ------------------ RETRO-COMPATIBILIDAD ------------------ #
# La gran mayoría de estas funciones tienen el mismo valor tanto en producción como en testeo,
# esto es para programar de acuerdo a las buenas costumbres, si nos toca un hosting o admin
# jodido, podremos estar preparados.
register_globals = Off
# DESCRIPCIÓN: Ver explicación más abajo
# PRODUCCIÓN: Off! SIEMPRE!
# HOSTING: Todavía quedan algunos hostings que le colocan On a esta directiva. Es lo mismo que publicar en lun: "Hackéenme!"
register_long_arrays = Off
# DESCRIPCIÓN: Habilita la forma vieja y anticuada de llamar a las superglobales.
# PRODUCCIÓN: Off también. C'mon, programen de acuerdo a los nuevos estándares!
# HOSTING: Por lo general, está en On. Existe un montón de programadores (y otro montón más de documentación antigua) que todavía referencian los HTTP_*_VARS como vigentes.
magic_quotes_gpc = Off
# DESCRIPCIÓN: Intento fallido de escapar las entradas de datos. Creo que lo iban a sacar incluso.
# PRODUCCIÓN: Off. Produce más problemas que soluciones.
# HOSTING: Adivinen: por compatibilidad, por lo general está en On.
enable_dl = Off
# DESCRIPCIÓN: La función dl() permite cargar una extensión de forma dinámica. Sinceramente, lo encuentro inútil y presenta un riesgo de seguridad.
# PRODUCCIÓN: Off, aunque ya lo tenía desactivado en la directiva disable_functions.
# HOSTING: Por lo general está en Off.
allow_url_fopen = Off
allow_url_include = Off
# DESCRIPCIÓN: Estas dos funciones permiten abrir un archivo desde la Web e incluirlo como parte del código fuente PHP desde la Web respectivamente. 
# PRODUCCIÓN: Ya que es la forma más fácil de hacer ataques XSS (en conjunto con register_globals), fuertemente lo recomiendo en Off.
# HOSTING: En un 99,9% de los casos se encuentra desactivado.

{% endhighlight %}



## Explicaciones más detalladas de algunas opciones


**implicit_flush**: Esta directiva controla si llamar la función flush() después de cada echo o print. Útil en el caso de estar trabajando con ob_start() y otros, inútil para lo demás. Se recomiendo Off, aún en desarrollo.

**safe_mode**: Al contrario de lo que se cree, esta función NO tiene nada que ver con la seguridad del equipo, ya que se puede anular o bypassear fácilmente mediante otros métodos. Lo recomiendo siempre en off, además de que en futuras versiones de PHP igual lo sacarán por la confusión que producía la palabra en sí y por la poca (in)utilidad que ofrecía.

**register_globals**: Ni por nada del mundo se les ocurra dejar activado esto, ni siquiera en ambiente de testeo! Si tenemos por ejemplo una variable que inserta algo en la página (que se llame $inserta), basta con hacer el request de esta manera: 
`http://ejemplo.com/pagina.php?inserta=inserte-aqui-su-ataque-XSS-favorito`
para poder hacer un ataque XSS. 



## Para concluir


Estamos de acuerdo: todavía faltan muchísimas opciones (que le pueden agregar un plus bastante grande en cuanto a seguridad y rapidez), pero estas ya son mucho más avanzadas y dependen netamente de dónde o bajo qué condiciones se monta la aplicación, por lo que no representa un interés de parte de un público un poco más masivo y ciertamente tampoco sirve para un ambiente de desarrollo a menos que se pretenda simular el mismo ambiente que el de producción.

Espero que les haya servido :)
