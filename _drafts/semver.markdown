---
comments: true
date: 2015-07-02 00:00:00+00:00
layout: post
slug: semver
title: Explicando Semantic Versioning
categories:
- PHP
---

Desde hace un buen tiempo que el mundo de PHP en general se está profesionalizando a un ritmo inigualable. Después de la creación de
[composer](/2015/06/entendiendo-e-implementando-composer-en-tu-codigo-php/) y [PHPFIG](http://www.php-fig.org/) es que todo cambió para
bien y de esta forma se están creando y adaptando estándares que prometen hacer todo mejor. 

Uno de estos estándares es el tema de este post y tiene que ver con Semantic Versioning, o semver para hacerlo más corto. El objetivo es
bastante simple: nos permite saber rápidamente si cuando nos toca actualizar alguna parte de un package podemos hacerlo de forma segura o no,
sin tener que preocuparnos de posibles problemas de retrocompatibilidad que pudieran surgir.

<!-- more -->

La idea detrás de suena bastante bien y la verdad es que después de haberlo probado en algunos sistemas de producción; funciona. En muy resumidas
cuentas, podemos ver que una versión tiene 3 componentes:  
X.Y.Z

Donde:  
<code>X</code> representa la <strong>versión mayor</strong>.  
<code>Y</code> representa la <strong>versión menor</strong>.  
<code>Z</code> representa la <strong>versión de parches</strong>.



Fuentes:  
[semver](http://semver.org/)  

