---
comments: true
date: 2009-11-24 08:01:40+00:00
layout: post
slug: los-pro-y-contra-de-wordpress
title: Los pro y contra de WordPress
wordpress_id: 72
categories:
- Mundo Web
- Pensamientos Personales
- PHP
tags:
- contra
- pro
- WordPress
---

La verdad es que siempre me han cargado los CMS, en gran parte porque ya viene todo hecho y no hay ninguna entretención en armarlo uno mismo. Digo: es frustante tener que pasar horas y horas programando algo sólo para darte cuenta que el hosting o servidor final en el que lo vas a montar, no presenta cierta característica necesaria para el correcto funcionamiento de la página. Eso significa "[Back to the drawing board](http://en.wikipedia.org/wiki/Drawing_board#Modern-day_idiom)" ya que es difícil (por lo general) convencer al hosting de que implemente cierta cosa que a ellos les podría jugar en contra en cuanto a seguridad. En el caso de tener un servidor privado es más fácil, aunque de repente cuesta convencer al administrador de ese servidor. (Ya me tocó tratar de convencer a un administrador que deshabilitara el safe mode de PHP que no ayuda en nada a la seguridad, y por esa misma confusión de nombres es que lo van a sacar definitivamente en PHP6).
<!-- more -->


## Los pro



WordPress es uno de esos programas que en muchos casos es plug-and-play. Uno llega, lo baja, descomprime y se va a la página principal. Lo único que hay que hacer es darle los accesos a la base de datos y después el nombre del blog. WordPress se encarga del resto. 

Después ya viene la configuración del blog. Y para eso es salvador: hace todo fácil y es simplemente llegar e instalar. Cada vez me sorprende más la facilidad con que hace las cosas: ¿Quieres alguna funcionalidad extra? Bajas el plugin. ¿No te gusta el aspecto? Cambia de theme. 

Como pequeño ejemplo: revisando Google Webmasters, me di cuenta que me había faltado algo vital: el sitemap. El sitemap es -tal como su nombre lo indica- un mapa del sitio; en un conveniente XML; que le indica a los buscadores qué páginas existen en una determinada página para que de esta manera, no se pierda ninguna al querer indexarla. 
Predeterminadamente, WordPress no cuenta con esta característica de poder generar un sitemap por lo que hay que bajar un plugin para poder disfrutar de esta característica. Le pregunté al omnipresente Google si acaso conocía de algún plugin para WordPress que hiciera esta gracia, y me respondió que sí. Lo bajé, lo instalé, configuré algunas cosas básicas y generé mi sitemap. Listo. Fin del cuento. And that was the big deal? 

Escribir noticias es también muy fácil en WordPress: ya que incorpora TinyMCE, que desde ya es bastante poderoso como editor WYSIWYG, WordPress lo lleva un paso más allá y lo hace aún más fácil. De partida no existe un botón de HTML y la supuesta vista de HTML es muy amigable también, ya que si bien es cierto muestra el código como HTML, no es necesario indicarle los saltos de línea, ya que lo hace auto-mágicamente. ¿Qué más fácil que eso? Para mí resulta el editor WYSIWYG más intuitivo que he visto. 



## Los contra



El gran contra de WordPress es su optimización. Ok, hace las cosas mucho más fáciles, pero a costa de rendimiento. Casi quedé horrorizado cuando, después de recién haber instalado el blog y visitado la página principal caí en la cuenta de que se demoraba medio segundo en generarme la página y haber hecho 33 consultas a la base de datos. 

Hoy no más leí en alguna parte que el mejor sistema era el que utilizando una base de datos, hiciera las menores consultas posibles. Las consultas son lentas y toman tiempo. El tiempo es oro y mientras más pesada es la aplicación, más memoria gasta. 

Lo otro que encuentro pésimo de WordPress es su galería multimedia. Las indicaciones son fáciles: crea una carpeta llamada uploads, dale permisos 777 y el mismo WordPress irá creando toda la estructura de árbol que necesita para funcionar. 
Indicaciones fáciles? Demás. El único problema es que por más chmod -R 777 uploads/ que le diera, nunca me funcionó poder subir una imagen. No había caso en que me creara subcarpetas dentro de una carpeta que tenía permisos 777. Afortunadamente, salió al rescate el plugin [Next-Gen](http://wordpress.org/extend/plugins/nextgen-gallery/) que es un excelente administrador de galería multimedia. De acuerdo: es probable que el 90% de lo que esta galería es capaz de hacer nunca lo voy a ocupar, pero es parte del daño colateral. De todas maneras no lo cambio. 

Otra cosa que es horrible en WordPress es cambiar de nombre la carpeta wp-admin. Esta carpeta se encuentra codificada en duro en muchos archivos de WordPress y hay que cambiarlo a mano. Gracias a Dios que existe el comando 

    
    find . | xargs grep "wp-admin"



En Linux con lo cual la tarea se alivianó muchísimo. (BTW: Ese comando busca la cadena wp-admin en todos los archivos del directorio actual e imprime la cadena y el nombre del archivo, demasiado útil para trabajar con CMS's y buscar alguna función)

Después en vi era simplemente

    
    :%s/wp-admin/nombre-carpeta/g



Y listo, todas las coincidencias cambiadas. 
Pero eso no era todo: cada plugin que instalé después, también traía codificada en duro el nombre de la carpeta. Vuelta a hacer todo el show para ese plugin cosa que se pudiera instalar como la gente. No me imagino tener que hacer esto mismo en Windows :?

Hay algo que todavía no me ha pasado: en Firefox existe una falla que ocurre al azar con WordPress: al querer escribir o editar alguna noticia, en el cuadro de las categorías trata de cargar la página de inicio del administrador, lo cual crea un conflicto del espacio y tiempo más o menos heavy y de esa manera, al final termina cargando una ventana en blanco. La verdad es que ignoro si han solucionado esto, por que me pasaba eso de vez en cuando en WordPress v2.7.X, es probable que en la 2.8 lo hayan solucionado finalmente. Bueno, si me pasa nuevamente: hay que limpiar cookies y problema resuelto. 

Otra cosa que tampoco es tan fácil como se ve, es poder meter código PHP dentro de ciertas páginas o posts. Claro, existe el plugin [Exec-PHP](http://wordpress.org/extend/plugins/exec-php/) que permite ejecutar arbitrariamente código PHP pero tampoco es la idea tener que instalar un plugin para un CMS que está hecha en PHP! Además, creo que debe utilizar eval() para ejecutar el código, función que se encuentra desactivado en el 99% de los servidores por temas de seguridad. Espero que no sea así. 
Este último punto sí se podría rebatir por un tema práctico. Existe una enormidad de plugins para WordPress que están hechas bajo unas regulaciones bastante estrictas: es necesario seguir estas líneas para que todo sea ordenado. Imagínense el desorden que se crearía si no se impusiera esta regla y todo el mundo pudiera crear archivos donde más le conviene para poder ejecutar su código. Sería una maraña de códigos PHP dispersos por todo el sistema lo cual dificultaría enormemente una de las barreras que WordPress ha superado con creces: la actualización del CMS en sí. 



## Conclusiones



WordPress es genial para lo que hace: publicar noticias. Lamentablemente, y justamente por el último punto es que no es un CMS todo-en-uno. Aunque podría servir para muchísimas cosas, quizás todavía le falta y se extraña un WordPress "Light", que sólo contenga la capacidad de escribir noticias y nada más. El otro día tenía que hacer una pequeña página que iba a contener "muchas" páginas estáticas (cinco) y solo una página que presentara un montón de brevenotas de no más de 400 caracteres. Mi primera opción fue WordPress, pero al final lo deseché más que nada porque era tratar de mover un Mercedes con el motor de una moto Toyota, uno de 125cc. Menos mal que opté por hacer yo mismo el sistema, ya que lo terminé en una semana y media. Si iba a customizar WordPress, era probable que me iba a demorar cerca de un mes. 

Saludos !!
