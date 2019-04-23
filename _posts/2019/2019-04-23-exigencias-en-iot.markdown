---
comments: true
date: 2019-04-23 01:00:00
layout: post
slug: exigencias-para-mis-dispositivos-de-iot
title: Exigencias para mis dispositivos de IoT
categories:
- IoT
---

En el [post previo](https://blog.unreal4u.com/2019/04/nuevo-enfoque-del-blog/), hablé de forma muy general sobre algunas
exigencias que tengo con un sistema de domótica casero. En este post, explicaré bastante más en detalle cuáles son mis
exigencias y en base a cuáles valores siempre aceptaré o recharazé una solución.

Básicamente, mis requerimientos son los siguientes:  
* **Fácil de usar**. Lo ideal es que no se note que es IoT.
* **A prueba de fallos**. Aunque no haya conectividad con la red, el sistema tiene que hacer lo que sea que tenga que
hacer.
* **Seguro**. No me gusta filtrar nada hacia el exterior de qué aparatos tengo ni cuáles son las capacidades del mismo.
* **No destruir la casa en el intento**.

<!-- more -->

## Los 4 puntos en detalle

### Fácil de usar

La primera de las premisas de todo sistema que coloque en mi casa es que sea fácil de usar: si una persona viene por
primera vez a tu casa, tiene que saber que activar un botón hace que se encienda o apague las luces, y también significa
que tiene que sentirse "natural".

Como ejemplo, [probé estos paneles touch](https://www.aliexpress.com/item/Itead-Sonoff-Touch-EU-US-Wifi-Wall-Touch-Switch-1-Gang-1-Way-Wireless-Remote-Light/32845084099.html?spm=a2g0s.9042311.0.0.27424c4dSLQe9U),
y aunque son de excelente calidad (quizás haré un review más adelante), no me terminan convenciendo ya que al apagar la
luz de noche, uno no puede saber al tacto qué está pasando. Son pequeñas irritaciones pero van sumando al final del día.
Imagínense el mismo escenario bien temprano en la mañana cuando todavía esté oscuro y uno necesita salir rápido de la
casa mientras vas bajando la escalera tratando de encontrar el interruptor que enciende las luces: después de un par de
días uno volvería al sistema antiguo ya que **funciona**.

### A prueba de fallos

Este punto es simplemente el más importante de todos: todo sistema de domótica tiene que ser una unidad independiente y
que pueda seguir trabajando bajo distintas condiciones: si se corta la electricidad, lamentablemente nada que hacer,
pero con este punto no me hago mayor problema ya que un dispositivo "tonto" tampoco funciona sin electricidad. Lo que sí
es importante es que no hayan más factores externos que puedan influir:

#### Sin internet

Uno de mis principales problemas con muchos dispositivos IoT es que éstos tengan que depender del funcionamiento de
Internet. Yo he hecho una cantidad increíble de mejoras en mi red casera de forma de reducir al máximo posible cualquier
corte de Internet, pero aún así me veo a veces reiniciando el servidor (más adelante haré un artículo detallando la
topología y otros dispositivos de mi instalación) ya que voy a instalar por ejemplo un nuevo disco duro.

#### Red cableada

Otro factor muy importante es cablear donde sea posible: el Wi-Fi es un protocolo que todavía está en activo desarrollo,
y nada indica que en el futuro salga un nuevo estándar de seguridad que no sea compatible con algún dispositivo
escondido en una pared cualquiera: la red cableada en cambio sigue funcionando con dispositivos que salieron hace 20
años atrás y es mucho más simple de controlar ya que no tiene una capa de autentificación por encima.

Otras desventajas del Wi-Fi es que es más lento, el rango y la seguridad.

#### Sin red interna

Otro caso bastante común, es la falta de conectividad interna: puede ser que el servidor principal esté abajo, que el
broker (más información sobre esto más adelante) o alguna otra dependencia esté caído, que el cable se haya roto o
simplemente esté mal enchufado, pero el aparato tiene que seguir manteniendo sus funciones normales; o bajo ciertas
condiciones; seguir funcionando a un nivel o con funcionalidades más básicas. Un ejemplo de este último punto puede ser
una ampolleta que se prenda automáticamente cuando la casa detecte que llego a la casa y esté oscuro, pero si el
dispositivo no tiene red alguna, no va a saber que llegué a la casa. El botón de prendido y apagado de la ampolleta debe
seguir funcionando normalmente sin problemas.

### Seguro

Corría el año 2016 cuando el botnet Mirai se hizo público: servicios como Twitter, Netflix, Spotify y Reddit sufrieron
downtime debido a... millones de dispositivos IoT mal protegidos abiertos a Internet.
Cualquier dispositivo que sea capaz de recibir órdenes desde el exterior es susceptible a este tipo de ataque y como
tal, no quisiera que mi casa estuviera involucrado en cualquier incidente que pueda ocurrir.  
Aunque no lo he hecho, lo mejor que uno puede hacer para prevenir este tipo de ataques es dejar todo lo que tenga que
ver con domótica en una red (VLAN) separada sin acceso a Internet.

Por lo mismo, antes de adquirir hardware o algo que ya esté armado y listo para llegar e instalar, lo verifico y además
tengo una fuerte preferencia por dispositivos que se puedan flashear ya sea con Tasmota u otro firmware, de esa forma
puedo controlar y verificar que no sale ni un byte hacia el exterior que yo no quiero.

### No destruir la casa en el intento

Espero que este punto no necesite de mayor explicación. :)

## Conclusión

Esos serían mis 4 puntos principales bajo la cual consideraría instalar o hacer algo. Sin embargo, de vez en cuando es
bueno experimentar. De todos los puntos expuestos, el requerimiento de red cableada es el único que puedo sacrificar
siempre y cuando no se trate de un dispositivo primordial para el funcionamiento de la casa: un ejemplo de esto sería
una luz secundaria en el comedor que sólo se prende para ocasiones especiales: si hay algún problema con la Wi-Fi en la
vida común y corriente, puedo vivir con eso. Si, en cambio, significa que no podré ver nada cuando entro a la casa de
noche, ya cambia la cosa.
