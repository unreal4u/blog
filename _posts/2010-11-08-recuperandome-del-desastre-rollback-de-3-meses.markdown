---
author: admin
comments: true
date: 2010-11-08 15:49:22+00:00
layout: post
slug: recuperandome-del-desastre-rollback-de-3-meses
title: 'Recuperándome del desastre: Rollback de 3 meses'
wordpress_id: 464
categories:
- Pensamientos Personales
---

Estaba anoche trabajando en varias cosas, entre ellas ver la forma de actualizar mi instalación de Wordpress. Como siempre se aconseja respaldar todo, respaldé todo lo que eran datos del disco y por supuesto ejecuté lo que yo creía que era un mysqldump (para respaldar) pero me equivoqué en esto último, restaurando el último respaldo que saqué cuando me cambié de hosting, es decir, hubo un rollback de cerca de 3 meses.
Sucede que siempre he tenido la costumbre de respaldar periódicamente la base de datos: para esto, ejecuto un simple "mysqldump -p db_wp > respaldo.sql" mediante la cual rescato la base de datos "db_wp" a respaldo.sql. En un Linux cualquiera, para no ingresar el comando a cada rato, es simplemente flecha hacia arriba durante un rato para encontrar el comando y darle enter. Hasta ahí no hay ningún problema, sólo que hace poco me cambié de hosting y nunca había hecho un respaldo: sí había importado la base de datos cuando me cambié por lo que el comando guardado era "mysql -p db_wp < respaldo.sql". Muy poca diferencia para las dos tremendas diferencias que hay entre ambas.
<!-- more -->
Como es bastante desilusionante perderlo todo, voy a aprovechar el espacio para aconsejar qué hacer en caso de desastre.



  * Si tienes habilitado el log binario de mysql, estás salvado: en ella se guardan todos los datos, incluso los que pasaste a borrar con un "DROP TABLE usuarios". Ojo que puede no servir para condoros que impliquen gigas de información. Revisen en Google acerca de mysqlbinlog. En mi caso no existía este archivo :(


  * Soñar para que el administrador de turno haya activado un respaldo diario. Aunque en teoría debería haber estado haciéndolo, este servidor se instaló hace poco y al parecer nadie se acordó de activar esto :(


  * Confiar en la caché de Google: aunque la gran mayoría de los artículos estaban online, los últimos 3 no estaban :(


  * Publicar los artículos completos en tu RSS y salvarlos desde ahí.


Aunque no lo crean, fue esta última opción la que me salvó, pero eso sí: se perdieron los comentarios :( Sin embargo, agradezco a Thunderbird por ser lo suficientemente inteligente como para no borrar los artículos siendo que el RSS "nuevo" que había no tenía aquellos artículos.

Es así como se restauraron los artículos de los últimos 3 meses y estamos con los artículos actualizados. Tengan por seguro que NO me volverá a pasar lo mismo :P
