---
author: admin
comments: true
date: 2011-01-04 22:00:37+00:00
layout: post
slug: fallback-en-mysql-actualizar-en-caso-de-ya-existir
title: 'Fallback en MySQL: Actualizar en caso de ya existir'
wordpress_id: 482
categories:
- Bases de Datos
---

Estos meses he estado bastante ocupado trabajando, y es por lo mismo que no he escrito nada. Sin embargo, he estado atento a sus comentarios y aprobándolos cuando hay.

La solución propuesta hoy me surgió en un momento de extrema ociosidad y flojera, buscando una forma de hacerme la vida más fácil: alguien más tendría que haber tenido la duda de que era posible; en MySQL; insertar un registro, pero si éste registro ya estaba, actualizarlo sin tener que:



	
  * Hacer la consulta inicial

	
  * Verificar que exista

	
  * Actualizar el registro en vez de insertarlo

	
  * Revisar que se actualizó correctamente



Bueno, esto es posible, y es bastante fácil de implementar. Para saber cómo, siga leyendo :)
<!-- more -->
La clave está en las palabras reservadas "INSERT ... ON DUPLICATE KEY UPDATE" (disponibles a partir de MySQL 4.1.0) que le indican a MySQL los pasos a seguir en el caso que nuestro insert falló. Supongamos una simple tabla con la siguiente estructura:
[sql]
CREATE TABLE ejemplo (
  id_ejemplo INT(11) NOT NULL AUTO_INCREMENT,
  descripcion VARCHAR(255) NOT NULL,
  PRIMARY KEY(id_ejemplo)
);
[/sql]

Si deseáramos editar sus registros sin tener conocimiento previo de si está insertado o no pero a la vez manteniendo una sola consulta, se haría de la siguiente forma: 
[sql]
INSERT INTO ejemplo (id_ejemplo,descripcion) 
  VALUES (34,'qwerty') 
  ON DUPLICATE KEY 
    UPDATE descripcion = 'qwerty';

SELECT descripcion FROM ejemplo WHERE id_ejemplo = 34;
-- Esto devuelve: qwerty

INSERT INTO ejemplo (id_ejemplo,descripcion) 
  VALUES (34,'asdf') 
  ON DUPLICATE KEY 
    UPDATE descripcion = 'asdf';

SELECT descripcion FROM ejemplo WHERE id_ejemplo = 34;
-- Esto ahora devuelve: asdf
[/sql]

Así de fácil y así de simple. Por supuesto que aquí lo presento en su forma más básica y explicado con manzanitas, pero se puede complicar bastante, sobretodo considerando llaves únicas compuestas y actualizando varios campos (donde la única diferencia es que tenemos que separar los campos a actualizar con una coma). Para esos casos, por favor remítase a la fuente principal de donde saqué información para este artículo.

Fuente: [MySQL](http://dev.mysql.com/doc/refman/5.5/en/insert-on-duplicate.html).
