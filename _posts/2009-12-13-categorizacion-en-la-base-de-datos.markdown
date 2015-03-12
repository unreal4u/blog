---
author: admin
comments: true
date: 2009-12-13 03:01:17+00:00
layout: post
slug: categorizacion-en-la-base-de-datos
title: Categorización en la base de datos
wordpress_id: 125
categories:
- Bases de Datos
---

No es un secreto de que siempre me ha gustado programar mis propios CMS. Ok, estamos de acuerdo, este blog no es el mejor ejemplo de ello, pero sitios como por ejemplo unreal4u.com, mi tesis y muchos otros trabajos que he hecho se han basado en este principio.

Sin embargo, en algunas de esas pegas me he encontrado con la sorpresa amarga de que muchas veces el **diseño** de la base de datos es **horrible**.
<!-- more -->


# Modelo antiguo


El modelo antiguo sigue una tendencia bastante básica. Tanto así, que una vez que me hice cargo de un sistema más o menos grandote me di cuenta que tenía 25 tablas extra con información del tipo:

    
      id    | nombre
      1     | primero
      2     | segundo
      3     | tercero


Cabe destacar que este modelo **lo encontré pésimo**. Tenía que haber alguna solución más fácil que sólo implicara una tabla donde pudiera guardar descripciones cortas de todo tipo, así que me puse a pensar en alguna solución que fuera un poco más definitiva.


[![2009-11-24-u4u-0017](http://blog.unreal4u.com/wp-content/gallery/base-de-datos-diseno/thumbs/thumbs_2009-11-24-u4u-0017.png)](http://blog.unreal4u.com/wp-content/gallery/base-de-datos-diseno/2009-11-24-u4u-0017.png)Modelo clásico: una ensalada de tablas chicas



Este modelo emplea el tipo de categorización de todas partes con tablas muy chicas, todas separadas.


## Ventajas de este modelo





	
  1. _Fácil de programar_. Si quiero hacer una consulta que me retorne el nombre de usuario y el nombre del grupo al cual pertenece, basta con un simple join:
[sql]SELECT a.login, b.descripcion
FROM alumnos AS a LEFT JOIN grupos AS b ON a.id_grupo = b.id_grupo;[/sql]




## Desventajas de este modelo





	
  1. _Difícil de mantener_. Si tengo 20 ó 30 tablas que tienen la misma declaración y quiero cambiar por ejemplo una falta ortográfica en una de ellas o convertir todas las categorías a mayúsculas, significa que tendré que cambiar 20 ó 30 tablas. Además, se podrán imaginar que si existen 30 tablas de categorizaciones, eso significa que existirán muchas más tablas en el sistema.

	
  2. _Velocidad_. Aquí podrá haber muchísima discusión al respecto, pero personalmente creo que una sola tabla **bien optimizada** es más rápida que 30 tablas por separado que pueden no seguir un mismo estándar entre todas. Aunque la gran mayoría de las tablas sólo es un índice con una breve descripción, donde la primera será una clave primaria, pueden haber casos donde; por omisión del programador; esta regla general no se aplica y por lo tanto, se estén perdiendo valuosos recuros.


En la próxima página, el modelo propuesto.



# Modelo propuesto


Hasta el momento sólo he hablado acerca del modelo antiguo... ¿pero cuál es la propuesta super hiper maravillosa entonces?



[![2009-12-12-u4u-0000](http://blog.unreal4u.com/wp-content/gallery/base-de-datos-diseno/thumbs/thumbs_2009-12-12-u4u-0000.png)](http://blog.unreal4u.com/wp-content/gallery/base-de-datos-diseno/2009-12-12-u4u-0000.png)Modelo Propuesto: menos tablas, más fácil para la vista



Este esquema nos presenta al menos a primera vista, algo bastante más simple que tener 20 a 30 tablas para distintas cosas: todo se reúne en una sola tabla, que en realidad es una subparte de otra.


## Las ventajas de este modelo





	
  1. _Mejor mantención_. En vez de tener 20 ó 30 tablas con categorías, ahora sólo se mantiene una, lo cual cumple al 100% del propósito de una base de datos: juntar en un solo lugar de manera optimizada mucha información. Por lo tanto, es posible manejar esa información mediante un administrador de categorías, con la cual podrán agregar, quitar o editar categorías de una forma más fácil que editando directamente la base de datos realizando cambios. De todas formas, si por ejemplo, deseara convertir todas las descripciones de las categorías a mayúscula, basta una consulta para convertirlos todos, sin equivocarse o sin olvidarse de una tabla.

	
  2. _Mejores índices (Estandarizados)_. Aunque en gran parte esta parte ya es cosa de cada uno, personalmente prefiero ocupar tres índices:

    * La primera para "_acceso_directo_", que guarda la cadena de 3 dígitos alfanuméricos cuya combinación es única.

    * La segunda para la tabla categorias, que es la clave primaria de esa tabla. Esta clave primaria es la que se referenciará en las demás tablas, así que es como importante.

    * La tercera, para la tabla categorías. Es una clave única compuesta por "_acceso_directo_" y "_descripción_" que opcionalmente se puede dejar como índice. (Sí: un índice **NO ES** lo mismo que una clave única, la clave única sólo asegura que el o los registros a los cuales se les hace el control no se vaya a repetir, pero no es implícitamente indexada a diferencia de una clave primaria, ese trabajo nos queda a nosotros). De esa forma, nos aseguramos de que las descripciones no estén repetidas, pero sí se puedan repetir en el caso que estén en distinta categoría.

En cuanto a este índice opcional, hay que verlo caso a caso porque motores más potentes como PostGreSQL, que cumple 100% con ACID, saca el índice desde la tabla a la cual es referenciada, por lo que esta clave sería redundante, innecesaria e incluso puede perjudicar el rendimiento de la base de datos en vez de acelerarla. De todas formas se puede dejar sólo como una clave única, sin indexarla.







## Las desventajas de este modelo





	
  1. _Consultas más complicadas_. Aquí podrían haber dos casos: la primera y la segunda ( :retard ). Fuera de bromas, la primera es que desee rescatar la descripción asociada a una id y la segunda es que desee rescatar todas las categorías de un cierto tipo. (Por ejemplo, para elegir alguna opción de entre varios). De esta manera, para cada ejemplo, las respectivas consultas:

{% highlight sql %}
-- Primer caso: uniendo dos tablas
SELECT a.login, b.descripcion
FROM usuarios AS a LEFT JOIN categorias AS b ON a.id_grupo = b.id_cat

-- Segundo caso: rescatando todas las categorías de un cierto tipo
SELECT id_cat, descripcion FROM categorias
WHERE acceso_directo = 'etc'
{% endhighlight %}

Por último, un pequeño truco si deseara rescatar por ejemplo la descripción de la id del curso **Y** la descripción del grupo:
{% highlight sql%}
SELECT a.login, b.descripcion, c.descripcion
FROM usuarios AS a, categorias AS b, categorias AS c
WHERE a.id_grupo = b.id_cat AND a.id_curso = c.id_cat
{% endhighlight %}




# Conclusiones


El diseño de la base de datos es importante y uno siempre tiene que pensar a lo grande. No sirve de nada pensar siempre en datos limitados, con entradas limitadas y sin esperanza de que crezca: si el sistema es bueno, **va a crecer en algún punto**.
Es por esto que prefiero para cualquier sistema, estandarizar las entradas y posibles salidas que tenga el sistema, y una de ellas es acortando la pega a la larga para el programador y además asegurando una _consistencia en los datos_.

Puede que uno de estos días hable en mayor profundidad acerca de eso. Pero por el momento, es lo que hay.

Saludos !!
