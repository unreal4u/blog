---
author: admin
comments: true
date: 2012-12-03 22:55:07+00:00
layout: post
slug: informacion-de-utilidad-publica-mi-cuenta-en-github
title: 'Información de utilidad pública: Mi cuenta en Github'
wordpress_id: 743
categories:
- Classes
- Control de versiones
---

Esta es una noticia cortita para avisarles que desde hace como 2 años tengo cuenta en Github pero que hasta el momento nunca la había ocupado. Como ya les había contado en otro post, cuando fui a la última conferencia me sorprendió mucho lo bien que hablaban de git así que decidí subirme al carro y de esa forma, aprovechar de liberar mis propias classes para que estén abiertos y disponibles al mundo entero. A la vez, actualizarlas me va a ser ahora mucho más fácil que antes.
Así, ya era hora de tirarme a la piscina y así es que subí mi primer proyecto libre a github: mis propias classes. Mi dirección es: 
[https://github.com/unreal4u/](https://github.com/unreal4u/)
y en ella podrán encontrar todas mis classes. Si les interesa saber qué hace cada uno, presiona leer más para ver qué es lo que hace cada class.<!-- more -->



## Disclaimer


Para empezar, contaré que aproximadamente la mitad de las classes nunca fue hecho con la intención de salir al público y por lo tanto han sido muy poco probados y no cuentan con una buena documentación (todavía). Ahora estoy en proceso de aprender más de git y de paso actualizar las classes y hacer que todas funcionen de forma autónoma una de otra. A la vez, estoy haciendo Unit Testing y asegurándome de que funcionen sin que tenga que tener por detrás el framework de la empresa. Dicho eso, vamos por orden.



## Automatic-DB-Updater_v0.1


Tal como podrán darse cuenta por el número de versión, esta class nunca ha sido liberada al público y es también muy poco probable que el desarrollo de la misma siga. Estoy considerando seriamente eliminarla debido a que ya no es relevante y actualmente hago las cosas de forma totalmente distinta.



## CSStacker_v1.3


Esta fue una de las primeras classes que publiqué en [phpclasses.org](http://unreal4u.users.phpclasses.org/browse/author/721318.html/). Lo que hace es tomar varios archivos CSS, comprimirla, minificarla y crear uno nuevo. Dudo de que vaya a tener algún update en el futuro cercano, pero quizás sí la tendrá en el mediano plazo.



## HTMLUtils_v1.0


Esta class [también está publicada](http://unreal4u.users.phpclasses.org/package/6313-PHP-Create-HTML-documents-programmatically.html) en phpclasses.org. La gran gracia de esta class es que facilita bastante la creación de un documento HTML válido, con soporte para incluir hasta documentos flash, manteniendo la [validación en la W3C](http://validator.w3.org/). Presenta funciones para incluir casi cualquier etiqueta compatible hasta XHTML 1.0. HTML5 lamentablemente no existía todavía, aunque puede ser que dentro de poco la actualice.



## cacheManager_v2.1


Esta es mi última class y la verdad un orgullo. Sólo es compatible con una versión de PHP igual o mayor a 5.3 y la principal característica de esta class es que permite poner en cache casi cualquier cosa que se nos ocurra: desde simples documentos HTML hasta objetos completos. Incluidos vienen dos módulos de cache distintos: una que funciona con APC y otra que funciona en base a archivos. Lo genial de esta class es que uno puede programar su propio wrapper e incluirla y usarla de igual forma que las otras, haciendo de esta class una de las más transportables y extensibles que existen. En activo desarrollo y de seguro escribiré un post más largo al respecto.



## db-mysqli_v4.0.0


Esta versión es nuevecita de paquete y mejora muchas falencias de la [antigua class](http://unreal4u.users.phpclasses.org/package/5812-PHP-MySQL-database-access-wrapper-using-MySQLi.html). Es así como ahora soporta nuevos tipos de datos que antes no soportaba y toda la parte de cache en XML se eliminó, en gran parte porque ahora se encarga la class de arriba de eso, disminuyendo así bastante el footprint y por lo tanto la velocidad de ejecución. En activo desarrollo y pronto escribiré más al respecto.



## extended-pgsql_v1.1.0


Básicamente lo mismo que la anterior, pero ocupando PDO para PostGreSQL. Pronto le espera la misma actualizada que la de MySQL, así que prepárense.



## messageStack_v1.0.2


Esta class es super vieja y nunca antes había salido de la empresa. En estos momentos me encuentro ocupado creando la versión 1.1.0, que es capaz de correr de forma autónoma y pretendo incorporar algunos conceptos recién aprendidos a esta nueva versión.



## mysql-paginator_v1.1


Otra class que nunca antes había salido de la empresa. Está tan mal escrito que ni siquiera he querido abrirlo para ver el estado. Pronto se actualizará.



## pid_v1.2


Este es un class bastante chico para evitar concurrencia en procesos batch, consultando las APIs que ofrece el sistema operativo para consultar por ellas, tanto en Windows como en Unix. Esta es una de las últimas versiones aunque pronto se actualizará con una nueva versión que incluye los últimos bugfixes.



## rut_v1.0


Esta class es también una de las más viejas y nunca se lanzó al público general. Ahora la estoy probando y preparando para la salida al público. Paciencia mientras la versión 1.1 saldrá.
