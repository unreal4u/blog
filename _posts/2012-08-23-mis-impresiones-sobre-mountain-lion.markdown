---
comments: true
date: 2012-08-23 21:52:09+00:00
layout: post
slug: mis-impresiones-sobre-mountain-lion
title: Mis impresiones sobre Mountain Lion
wordpress_id: 643
categories:
- Apple/Mac
- Pensamientos Personales
---

Hace aproximadamente un mes atrás, salió al público general el nuevo sistema operativo de Apple: [Mountain Lion](http://www.apple.com/osx/), y esta vez, hice algo que normalmente nunca hago: comprar e instalar en los primeros días. 
Sin embargo, me había abstenido de escribir algo al respecto porque estaba teniendo serios problemas con el nuevo sistema operativo, tanto así que estuve a punto de volver a Lion, pero, debido a que al parecer se solucionó con los últimos ajustes que hice (sin contar el parche que salió hoy 23 de agosto), creo que me quedaré con él. 
Lo que sigue entonces son mis impresiones de Mountain Lion comparándolo con Lion. Te invito a leer más.

<!-- more -->


## Lo bueno


En breves palabras, puedo decir que Mountain Lion es bastante mejor que Lion. Ha sido una mejor experiencia, bastante más estable, más fluido y al parecer con menor temperatura también. Entre las grandes mejoras destacan sobretodo el Notification Center donde estoy esperando ansiosamente que los demás programas se suban al carro y aunque Mail no sufrió una mejora considerable, sí se agradece el gesto de que ahora existan contactos VIP de forma que queden más fácilmente separados del resto, útil considerando que tengo más de 50 grupos en gmail y aunque en la interfaz web los tengo diferenciados por color, en Mail todavía no se puede hacer. Es como una capita extra para facilitarte el trabajo, lo cual siempre se agradece. 

Otras mejoras incluyen una mejor integración con el sistema operativo en cuanto a la App Store (las actualizaciones se hacen ahora 100% a través de la App Store) y mejores opciones para controlar el trackpad. También en aplicaciones que lo soporten, auto-guardado y lo mejor de todo: control de versiones. De esta forma, TextEdit nos puede mostrar todas las versiones de un documento y podemos elegir restaurar a una copia anterior. Digno de mencionar también es el pequeño detalle de poder renombrar el documento abierto directamente desde la barra de título, lo cual ahorra tiempo al no tener que ubicar el archivo en Finder. Hasta el momento no le he sacado el provecho a estas nuevas características pero sin duda se ve algo sumamente interesante. De todas formas si Word y Excel lo soportaran haría mi vida harto más fácil.

Otra novedad bastante agradable es la nueva aplicación "Reminders". Para aquellos familiarizados con iOS, ya saben de qué se trata y les será de gran agrado saber que esta aplicación ahora también se encuentra disponible en Mac OS, aunque [sólo se puede sincronizar con iOS mediante iCloud](http://www.youtube.com/watch?v=8DRszawfYwA). 

Hablando de iCloud, si tienen el último iOS (5.1.1 cuando escribí esta entrada) verán que Mac OS X está ahora mucho mejor integrado con iCloud, lo cual es bueno debido a que casi todo se puede sincronizar con el teléfono: desde los iMessages (puedes hablar y mandar mensajes a personas que tienen iMessage desde el pc y eso se sincroniza con lo que escribes y recibes en el teléfono) hasta los reminders se sincroniza todo a la perfección mediante wifi y/o 3g. En cuanto edite la información de un contacto en el computador, casi al instante se recibe la actualización en el teléfono, sin necesidad de sincronizar a través de iTunes.

Otras pequeñas mejoras son una [herramienta de wifi](http://reviews.cnet.com/8301-13727_7-57487340-263/apple-enhances-wi-fi-diagnostics-in-mountain-lion/) (detecta wifi's alrededor y despliega información bastante útil sobre ellas), y, según dice Apple, [otras 195 nuevas características](http://www.apple.com/osx/whats-new/features.html) que no he nombrado. (Aunque Apple cuenta como "nueva característica" hasta los errores ortográficos parece). 



## Lo malo


La verdad es que este punto tuve que pensarlo bastante bien. No he tenido mayores problemas ocupando el sistema operativo (exceptuando un punto que colocaré dentro de la sección "Lo feo"), y quizás como único punto negativo colocaré el uso excesivo en memoria del proceso del Kernel. En mi caso, tengo 8GB de RAM y el proceso "kernel_task" me ocupa normalmente cerca de 800MB en RAM. En principio no tengo ningún problema en que ocupe 4GB de los 8 ya que la RAM está para ser ocupada, pero cuando yo necesite RAM, Apple debería ser capaz de cederme gran parte de esa RAM que está ocupando.
Explico: cuando estoy trabajando, tengo toda una suite abierta: Firefox y Chrome, Zend Studio, Aqua Data Studio, entre otros lo cual, después de una larga jornada laboral, ya toma cerca de 7,5GB de RAM ocupada, 4GB de los cuales corresponde a memoria **inactiva***.
¿Qué sucede si tengo que levantar una máquina virtual en este punto? Como los ingleses con voz de ultratumba dirían: "but then... disaster". En este punto, cuando VMWare Fusion pide su cuota de 1,5GB en RAM, Mountain Lion parece volverse loco, empieza a correr en círculos y muy, pero muy lentamente empieza a darse cuenta de tal tamaña solicitud. En seguida, empieza a reaccionar (lentamente) hasta el punto en que se da cuenta por fin de que no le queda más RAM y que no le queda otra que ocupar swap. Es acá donde falla la primicia de la buena administración de memoria de los sistemas UNIX ya que el sistema debería darse cuenta que tiene mucha memoria inactiva todavía ocupada y que no necesita. Recién cuando uno, manualmente, invoca el comando "[purge](http://www.electrictoolbox.com/purge-free-inactive-memory-mac-osx/)" el kernel se deshace de esa memoria y la marca como disponible para ser usado nuevamente por el usuario.
Aunque no lo he probado en profundidad, puede ser que la última actualización liberada hoy solucione ese punto, pero hasta el momento no he podido comprobarlo. (El problema mencionado se empieza a notar después de un par de días de uso y sólo después de ocupar intensamente el equipo).

Otro punto malo es que de nuevo se actualizó PHP y uno no tiene la opción de NO actualizar PHP, lo cual puede ser bueno o malo (no queremos quedarnos pegado en 5.1.6 teniendo 5.3.13), y de todas formas si necesitamos una versión muy específica de PHP siempre podremos compilarlo. Lo malo de la actualización de PHP es tener que compilar nuevamente APC, xDebug, intl, etc. pero a estas alturas ya es parte del oficio.



## Lo feo


Y llegamos a la infame sección de lo feo. Y es aquí donde voy a soltar lo que me tiene atorado hace tiempo: **problemas con la wifi**. Paso a proceder a explicar el problema y también su posible solución.

Actualicé yo mi equipo haciendo una instalación limpia e importé los datos de la cuenta en Lion mediante Time Machine. Hasta ahí todo bien.
Sin embargo, mi problema estaba a punto de empezar ya que cada 2 minutos aproximadamente, se me desconectaba la wifi, lo cual es bastante inusual ya que aunque el router me queda algo lejos con Lion jamás había tenido este problema, tampoco con el iPhone y aunque no lo crean, tampoco con Windows en otro pc. 
Me dispuse a buscar una solución a este problema y me encontré con la sorpresa de miles de personas en la misma situación, y mientras que a algunos se les solucionaba el problema sólo con reiniciar, en mi caso no era así. Es así como finalmente terminé recorriendo una lista interminable de consejos para poder tener una wifi estable, lista que reproduzco a continuación: 




  * [Resetear SMC](http://support.apple.com/kb/HT3964?viewlocale=en_US&locale=en_US)


  * [Resetear PRAM](http://support.apple.com/kb/PH11243)


  * [Poner DNS custom](http://www.applefansite.com/2012/07/how-to-fix-os-x-mountain-lions-wifi-issues/)


  * [Reducir MTU](http://osxdaily.com/2012/08/02/fix-os-x-mountain-lion-wireless-connection-problems/)


  * [Renovar DHCP](http://osxdaily.com/2012/08/02/fix-os-x-mountain-lion-wireless-connection-problems/)


  * Ping infinito al router


  * [Crear un nuevo perfil de conexión](http://osxdaily.com/2012/08/02/fix-os-x-mountain-lion-wireless-connection-problems/)


  * [Borrar las claves desde Keychain](http://forums.macrumors.com/showpost.php?p=15341185&postcount=20)


  * [Setear IPv6 a Local-link](http://forums.macrumors.com/showthread.php?p=15326913#post15326913)


  * Setear el router a un canal todavía no ocupado


  * Setear el router explícitamente a 802.11g


  * [Borrar configuración del sistema](http://www.applefansite.com/2012/07/how-to-fix-os-x-mountain-lions-wifi-issues/)


  * Instalar la última actualización de Apple


  * Reinstalar Mountain Lion (No probado y tampoco lo haré)



A excepción del último, ya lo probé todo y ahora al parecer está funcionando bien. (De hecho, creo que el problema se solucionó entre el seteo a 802.11g o la borrada de configuración del sistema). Ah, cabe destacar también que sólo me pasa con la wifi de la casa, con la wifi en el trabajo no tengo absolutamente ningún problema, y crea una conexión sólida como roca.



## Conclusiones


A pesar de que a nivel visual no hay grandes cambios, por detrás el nuevo sistema operativo de la manzana ha sufrido de varias mejoras que le han hecho bastante bien y encuentro que Apple busca una apuesta bastante arriesgada a largo plazo: poder unificar como un mismo sistema operativo lo que uno está acostumbrado a usar en el teléfono y lo que se ocupa en un notebook o pc de escritorio (iMac's), ya que lo que más se compatibilizó fueron los servicios de la nube y también se está trabajando firmemente en unificar interfaces de teléfono y pc para que de esta forma sean vistos como una sola aplicación. Grandes avances de esto se ven sobretodo en el Notification Center como en ciertas aplicaciones como Reminders y las notas. 
¿Y la verdad?... me gusta. Encuentro genial la idea de poder trabajar en uno u otro lado sin tener que preocuparme de que si aplico un cambio en el teléfono después tengo que acordarme también de aplicar el mismo cambio en el computador. Así que señores y señoras: [un fuerte aplauso a Dropbox](http://db.tt/pfNHagyq) (hagan click para que me den más MB :D ) que hizo posible que Apple viera que en realidad no era tan mala idea esta cosa de la unificación. Como siempre, tomaron una idea bastante buena, la hicieron más fácil de entender y de paso la mejoraron.

Y como sistema operativo, es estable, es mejor que Lion, es más rápido, es más fluido y teniendo en cuenta que tengo un equipo que ya está a punto de cumplir 3 años (35 meses desde que salió de la fábrica) y viendo la fluidez y rapidez con que todavía mueve todo el equipo, debo decir que estoy encantado con el nuevo sistema operativo. Además, apenas vale EUR 20 o USD 20: más barato imposible.


-----------------------------------------
* OS X tiene unas cuantas formas de definir un área de memoria, las principales son cuatro: memoria libre, wired, activa e inactiva. Libre (free) es la memoria netamente libre, activa (active) es la memoria que los programas en ese momento están ocupando, wired es la reservada por el kernel para diversas tareas del sistema operativo e inactiva (inactive) es aquella memoria ya liberada por los programas pero que pueden servir para más adelante (por ejemplo, para iniciar una aplicación más rápido). También puede verse como libre ya que en caso de necesitar más RAM se saca desde esta área.
