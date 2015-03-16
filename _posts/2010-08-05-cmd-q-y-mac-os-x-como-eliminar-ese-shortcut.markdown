---
comments: true
date: 2010-08-05 18:06:42+00:00
layout: post
slug: cmd-q-y-mac-os-x-como-eliminar-ese-shortcut
title: CMD+Q y Mac OS X... cómo eliminar ese shortcut
wordpress_id: 369
categories:
- Apple/Mac
- Pensamientos Personales
---

Hace meses que no escribo y ha sido por una razón: Hace meses que quiero cambiar de hosting, pero no me he dado la paja de hacerlo. Tengo un respaldo de hace un par de meses y todavía no lo monto. Prometo que hoy en la noche respaldo nuevamente y subiré todo al nuevo hosting. Bueno, quizás no hoy en la noche, pero sí durante el fin de semana.

En estos meses que llevo en Santiago, la vida me ha cambiado. Ya no trabajo en el mismo escritorio que tenía de los 15 años, sino que trabajo en un piso 15. La vista a la pared fue reemplazada por una vista hacia la cordillera por un lado y hacia el cerro Santa Lucía por otro. Por supuesto, en vez de mi PC ahora tengo Macbook Pro, y ha sido una de las mejores máquinas en las cuales he trabajado: si bien es cierto al cabo de algunas semanas igual tengo que reiniciar, no se pega como Windows lo hace. El trackpad es lejos de lo más filete que he tenido: funciona a la perfección y en general no me ha decepcionado para nada. Los que alegan contra Apple es sólo porque nunca han trabajado con una máquina de Apple, se nota que le pusieron mucho empeño al crear máquinas tan maravillosas. 
<!-- more -->
Sin embargo, la razón de ser de este post no es para hablar de las maravillas de Apple, sino más bien de una de sus desventajas: El famoso CMD+Q. 

Para los que no tienen Mac, el CMD+Q viene a reemplazar el famoso ALT+F4 de Windows, pero de manera silenciosa. Es decir: la aplicación **NO** va a preguntar amablemente si desea salir de la aplicación, simplemente va a salir. En sí, es bastante bueno, a no ser por que CMD+Q está al lado de CMD+W, que sirve para cerrar pestañas. Para más remate, ALT+Q es la famosa @, y ALT está al lado de CMD. En resumen: hay hartas oportunidades de echarse la ventana completa simplemente tratando de escribir la @ o bien tratando de cerrar la pestaña. 

Pero eso era hasta hoy: debido a lo mismo, me puse a investigar en Internet cómo hacer que ese shortcut lo pudiera cambiar por otro un poco más "difícil" de teclear. Fue así como llegué al sitio [Mac OS X Hints](http://www.macosxhints.com/) y fue donde pillé la siguiente información: 

{% highlight bash %}
defaults write NSGlobalDomain NSUserKeyEquivalents '{"Quit Firefox" = "@Q";}'
{% endhighlight %}

Sin embargo, esto no funcionó... Suceden dos cosas: 



	
  * La primera es que Mac OS X entiende la tecla @ como el CMD, y las demás teclas? Fácil: 
    * @ = Command
    * $ = Shift
    * ~ = Option
    * ^ = Control

	
  * La otra razón es que mi Mac OS X está en español. Por lo tanto, ese comando jamás existió en ningún programa.



Estas dos razones hacen que, en su conjunto, haya cambiado un poco el comando para que terminara siendo el siguiente: 
{% highlight bash %}
defaults write NSGlobalDomain NSUserKeyEquivalents '{"Salir de TextEdit" = "@$Q"; "Salir de Firefox" = "@$Q"; "Salir de Terminal" = "@$Q";}'
{% endhighlight %}

Ahora se reinicia la aplicación y quedaría todo listo. Ahora la salida del programa sin confirmación se hace con CMD+Shift+Q.

Nota importante: La única gran lata es que tienen que hacerlo programa por programa... Ya que cada vez que ingresamos este comando, borra las anteriores y las crea de nuevo. Pueden echar un vistazo con el siguiente comando: 

{% highlight bash %}
defaults read NSGlobalDomain NSUserKeyEquivalents
{% endhighlight %}

Y ese sería el largo tip de hoy.
