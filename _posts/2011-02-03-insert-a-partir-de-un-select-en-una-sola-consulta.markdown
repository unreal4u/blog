---
author: admin
comments: true
date: 2011-02-03 19:52:22+00:00
layout: post
slug: insert-a-partir-de-un-select-en-una-sola-consulta
title: INSERT a partir de un SELECT, en una sola consulta
wordpress_id: 497
categories:
- Bases de Datos
---

Trabajando en la empresa privada, uno se da cuenta de cuán importante resulta llegar a ser optimizar bien una tabla, ya sea organizándola al estilo "sábana" (mucha repetición de registros, pero super rápido al tener un par de índices livianos bien ubicados), o bien crearla con un [estilo EAV](http://en.wikipedia.org/wiki/Entity-attribute-value_model) (permite crear infinitas propiedades de un objeto sin crear tablas gigantes que guarden ese atributo). Ninguna de las dos es ideal ya que en la primera si bien es cierto se puede hacer una sola consulta que se genere rápidamente se repiten demasiados datos y la segunda es, por lo general, un parto hacer consultas optimizadas ya que el modelo en sí no lo permite fácilmente.

Sin embargo, este post no se tratará de modelos de datos, sino más bien un tipo de consulta en particular frente a una problemática particular y que permitirá, en una sola consulta, insertar un registro nuevo a partir de un select.<!-- more -->
Antes de continuar, un poco de contexto: 
Sucede que un sistema de despachos como el que estoy haciendo actualmente resulta bastante complejo al tener que analizar todas las variables: primero hay que programar la hora, después hay que ver en qué camión se va (a su vez, esto implica quién conduce, costos implicados -tales como activo fijo, petróleo, neumáticos, etc-, paradas, y se complica aún más cuando tenemos en cuenta GPS), hora de salida, hora de llegada y si finalmente el despacho se entregó al cliente o no.
Y es justamente en este último paso donde está la parte más crítica: toda la documentación anexa tiene como punto crítico la id del despacho que "ya no fue", y hay que empezar todo el ciclo de nuevo.

Fue en este punto donde me pregunté si acaso existiría alguna forma un poco más óptima de ingresar un registro nuevo sin tener que rescatar los datos y posteriormente almacenarlos en una variable temporal para luego volver a insertarlos como un nuevo registro... y la buena noticia es que, tal como [actualizamos en el caso de ya existir con una sola consulta](http://blog.unreal4u.com/2011/01/fallback-en-mysql-actualizar-en-caso-de-ya-existir/), insertar un registro a partir de un select es posible y bastante fácil.

Supongamos la siguiente tabla (simple): 
[sql]
CREATE TABLE despachos (
  id_despacho INT AUTO_INCREMENT NOT NULL,
  from_bodega INT NOT NULL DEFAULT '3',
  recibe VARCHAR(255) NOT NULL,
  direccion VARCHAR(511) NOT NULL,
  rechazado VARCHAR(255) NULL,
  recibido TINYINT(1) NOT NULL DEFAULT '0',
  PRIMARY KEY(id_despacho) -- ,
 -- FOREIGN KEY blabla (el from_bodega)
);

-- Insertando datos de forma normal: 
INSERT INTO despachos (recibe,direccion) VALUES ('Camilo','en su casa');
[/sql]

Ese registro recibirá la ID Nº 1. Pero, supongamos que Camilo NO estaba en su casa y haya que generar nuevamente una orden: 
[sql]
UPDATE despachos SET rechazado = 'no estaba' WHERE id_despacho = 1;
INSERT INTO despachos (from_bodega,recibe,direccion,rechazado,recibido) 
  SELECT from_bodega,recibe,direccion,NULL,0 FROM despachos WHERE id_despacho = 1;
[/sql]

Si se fijan en la consulta, aquellos valores que deban ir distinto, simplemente se cambian por ese valor que ustedes quieran. 

Espero que les haya servido, y para mayores dudas, puedan dirigirse al manual oficial, está en la fuente.

Fuente: [MySQL Reference Manual](http://dev.mysql.com/doc/refman/5.0/en/insert-select.html)
