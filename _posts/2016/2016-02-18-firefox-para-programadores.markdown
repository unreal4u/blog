---
comments: true
date: 2016-02-18 01:00:00
layout: post
slug: firefox-para-programadores
title: Firefox para programadores
categories:
- Pensamientos personales
---

Hace poco cambié mi sistema de hace 5 años (quizás más) por uno nuevo y como tal, aproveché de instalar todo desde cero,
algo que no había hecho hace ya muchos años. Como tal, se perdieron también un montón de plugins y tweaks que le había
hecho a mi querido Firefox. 

Tuve que ir de nuevo a la máquina antigua y revisar cosa por cosa qué había instalado y qué había cambiado a este tan
versátil navegador. La lista de las cosas importantes es bien poca, pero cada una tiene sus detalles. Partimos con:

<!-- more -->

#### Los plugins

Los plugins que día a día ocupo son: 

* [Firebug](http://getfirebug.com/): Esencial. No necesita introducción siquiera, así de importante es.
* [Classic Theme Restorer](https://addons.mozilla.org/en-US/firefox/addon/classicthemerestorer/): Ok, lo reconozco, soy
de aquellos que empezó a ocupar Firefox cuando salió la versión 1.5.0.3 alrededor del 2005. Esos son muchos años de por
medio donde muchas decisiones las aplaudí, pero hubo algunas que también me parecieron aberraciones. El objetivo de este
plugin es deshacer algunas de las aberraciones del pasado y traer de vuelta características que solían funcionar de una 
manera, pero ahora lo hacen de otra.
* [Live HTTP Headers (Fixed by Dan)](https://addons.mozilla.org/en-US/firefox/addon/live-http-headers-fixed-by-dan/): 
Hace tiempo di con este plugin que lamentablemente dejó de funcionar. Al tiempo salió un update de otra persona que lo
hizo funcionar de nuevo. Es hasta el momento el mejor plugin para reproducir llamadas que he encontrado, increíblemente 
simple en su interfaz pero a su vez potente.
* [New Tab Override](https://addons.mozilla.org/en-US/firefox/addon/new-tab-override/): Este es otro típico caso de 
situación que cambiaron con los años y que no me gustó. Yo siempre he ocupado mi propio script con links hacia los 
sitios que para mi en ese momento son los más importantes y Firefox en la versión 41 ó 42 cambió ese comportamiento. 
Como tal, busqué un plugin que reprodujera el comportamiento exacto al que estaba acostumbrado.
* [Tree Style Tab](https://addons.mozilla.org/en-US/firefox/addon/tree-style-tab/): Este es uno de los plugins al que
más rápidamente me he acostumbrado y ya no podría vivir sin él. Organiza los tabs de una forma vertical en vez de una 
horizontal, con soporte de sub-grupos dentro de los tabs. Ideal para gente que constantemente tiene 50+ tabs abiertos.

#### Los tweaks

No podríamos hablar de Firefox sin hablar de <code>about:config</code>. Esta página es sin duda lo mejor que tiene firefox ya que 
permite aprovechar y dejar el browser exactamente de la forma que queremos. Aquí un listado de los settings más 
importantes, pero tengan en cuenta que cualquier modificación en esta área (de las que hago yo) les dejará Firefox más
lento para navegar de forma general y sólo deberían ser activados en caso de que trabajen todo el día en desarrollo web:

* [browser.cache.check_doc_frequency](http://kb.mozillazine.org/Browser.cache.check_doc_frequency): Como soy el 
encargado del sitio web en el trabajo, me toca siempre ajustar los valores de las redirecciones y demases. Como tal, 
echando a perder se aprende pero es bastante irritante que tenga que reiniciar el browser para testear cada redirección 
301, o tener que cerrarlo ya que de forma contraria, siempre recibiré un 304 no modificado en vez de traerlo 
directamente del servidor. Este setting hace justamente eso: desactiva la caché para esos códigos. Yo lo recomiendo 
dejarlo en **<code>1</code>**. 
* [browser.cache.disk.enable](http://kb.mozillazine.org/Browser.cache.disk.enable): Controla la caché en disco de 
Firefox. Si trabajan todo el día en cosas que fácilmente podrían ser cacheables pero necesitan que no lo haga, dejen el
valor de esta variable en **<code>false</code>**.
* [browser.link.open_newwindow](http://kb.mozillazine.org/Browser.link.open_newwindow): Ok, seré sincero. ODIO con todo
el alma aquellos sitios que te abren una ventana distinta por cada resultado de búsqueda que te ofrecen. Yo no soy
tonto y aunque lo fuera me gusta tener el control de qué ventanas abriré sin que una página me incite o me quiera hacer
saber cómo tengo que navegar de una u otra forma en su sitio. Así que este setting sirve para controlar un pequeño 
aspecto: lo dejo en **<code>1</code>** para que de esa forma YO pueda controlar cuándo abrir una nueva pestaña/ventana y cuando no.
* [browser.link.open_newwindow.restriction](http://kb.mozillazine.org/Browser.link.open_newwindow.restriction): Este
ajuste es simplemente un mejor control con respecto al de arriba. Sucede que algunos sitios web, mediante el atributo
<code>_blank</code> quieren decidir por mi cuándo abrir una nueva ventana. Yo lo dejo en **<code>2</code>** para que de esa forma me sobre-
escriba ese atributo pero sin que afecte a pop-ups que fueron bien escritos.

Y esos son los ajustes que le hago a mi browser! Espero les sirva la explicación acerca de qué hace cada cosa.
