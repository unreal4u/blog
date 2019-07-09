---
comments: true
date: 2019-07-10 01:00:00
layout: post
slug: configurando-tp-link-tl-sg108e-con-vlans
title: Configurando TP-Link TL-SG108E con VLANs
categories:
- IoT
---

Este gigabit switch con refrigeración pasiva es un switch de bajo costo (apenas €32 me costó adquirirlo) pero que ofrece
algunas funcionalidades bastante avanzadas para ese precio: trae soporte para VLANs, soporte para LAG y hasta ciertos
niveles (básicos eso sí) de QoS.

Este post se concentrará sobretodo en la capacidad de configurar los VLANs ya que es justamente el objetivo para lo cual
adquirí estas unidades.

Las que me llegaron fue la v4 y el último firmware disponible para estas unidades en mi caso era el firmware
1.0.0 Build 20181120 Rel.40749.

<!-- more -->

Se trata en detalle de la siguiente unidad:  
![TL-SG108E](/assets/tplink-tl-sg108e.jpg)

Ésta unidad con el firmware actual presenta 3 modos para configurar los VLANs que funcionan de manera exclusiva una de
otra: MTU VLAN, VLAN basado en puerto y 802.1Q VLAN. Yo ocupé este último ya que era el mejor se ajustaba a mi caso de
uso.

Para poder configurar este switch es necesaria la siguiente configuración (suponiendo que puerto 1 es la entrada de
todos los VLAN):

![Configuración](/assets/switch-vlans/01-configuration.png)

La interfaz gráfica deja bastante que desear, así que los pasos para conseguir esto son varios:

- Primero habilitamos este modo haciendo click en Enable y en Save.
- Una vez que la configuración se haya aplicado, se creará la VLAN 1 que no podrá ser eliminada. Es a partir de este
momento que podremos configurar la VLAN.
- Escribimos el número de la VLAN en VLAN ID (en mi caso 50), le asignamos un nombre (opcional) y le informamos al
switch qué puertos son parte de la VLAN. Aquí viene justamente el truco: tenemos que decirle que el puerto 1 (la
entrada) también forma parte de esta VLAN, pero untagged.
- Finalmente, procedemos a la siguiente pantalla, iremos al menú izquierdo 802.1Q PVID Setting y configuramos la
pantalla de la siguiente forma:

![Asociación](/assets/switch-vlans/01-association.png)

Con esto hecho, podremos disfrutar (en mi caso) del puerto 2 al 6 de la VLAN 50.

Espero que les sirva esta pequeña guía!
