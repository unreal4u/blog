---
author: admin
comments: true
date: 2010-08-16 15:01:04+00:00
layout: post
slug: como-ocupar-ob_start-ob_get_contents-y-otros-relacionados
title: Cómo ocupar ob_start(), ob_get_contents() y otros relacionados
wordpress_id: 384
categories:
- Mundo Web
- PHP
---

Pensando en qué truco podría ser útil, me acordé de los [famosos ob_*](http://cl.php.net/manual-lookup.php?pattern=ob_) que en un principio me parecían bastante complicados e inútiles.

Sin embargo, hoy constituyen un pilar fundamental en mi ambiente de desarrollo, puesto que me permiten ejecutar código, y poder redirigir a alguna página en específico incluso después de haberse ejecutado la página completa. Por supuesto, también me permite insertar contenido Javascript en los headers **después** de haber ejecutado la página.

¿Pero cómo se ocupa? Para esto es este post.
<!-- more -->
Antes de poder imprimir código, debo explicar para qué sirve: ob_* controlan un **buffer**, el cual se puede controlar. La gracia es que la salida puede ser demorada por lo que podemos hacer cualquier acción, ya sea redirigir a otra página o -a mi gusto la más importante- poder controlar las cabeceras en cualquier momento, ideal para una carga por partes de la aplicación, sin caer en cargar partes que ni siquiera ocuparemos.

Existen específicamente 3 funciones que yo diría son las más importantes:



	
  * ob_start(): que le da comienzo al buffer

	
  * ob_flush(): que permite limpiar el buffer imprimiendo toda la salida

	
  * ob_get_contents(): que permite obtener los contenidos del buffer **sin imprimir en pantalla**

	
  * ob_end_clean(): que permite desechar todo el buffer, **sin imprimir en pantalla**


Dicho eso, podemos proceder a nuestro primer ejemplo:

[php]
ob_start();
echo 'hola mundo';
if (!file_exists('chao.txt')) header('Location: http://www.google.com/');
$contenido = ob_get_contents();
ob_end_clean();

include('cabecera.php');
echo $contenido;
include('pie-pagina.php');
[/php]

Este archivo hace lo siguiente: Imprime "hola mundo", revisa si existe el archivo chao.txt y si no existe, redirige a Google. Si existe el archivo entonces sigue la carga normal. ¿Se entendió? Veamos un último ejemplo un poco más completo entonces:

_**index.php**_ 
[php]
include('carga-previa.php');
ob_start();
echo '<h1>Hola mundo</h1>';
if (!is_readable('chao.txt')) $msgStack->add('No se encontró el archivo!');
else $msgStack->add('Se encontró el archivo!');
$titulo = 'Hola mundo';
$contenido = ob_get_contents();
ob_end_clean();

include('cabecera.php');
echo $contenido;
include('pie-pagina.php');
[/php]

_**carga-previa.php**_
[php]
class messages() {
  private $aMsgs = array();

  public function add($msg) {
    if (!empty($msg)) $this->aMsgs[] = '<h4 class="mensaje">'.$msg.'</h4>';
    return TRUE;
  }

  public function print() {
    if (count($this->aMsgs) > 0) {
      foreach($this->aMsgs AS $a) {
        echo $a;
      }
    }
    return TRUE;
  }
}

$msgStack = new messages();
[/php]

_**cabecera.php**_
[php]
echo '<html><head><title>';
if (!empty($titulo)) echo $titulo;
else echo 'sin titulo';
echo '</title></head><body>';
$msgStack->print();
[/php]

Ahora bien, no tiene ninguna gracia tener que escribir 20 líneas de código cuando nos podríamos ahorrar todo eso en un solo paso. Veamos nuevamente cómo podemos ahorrar bastante código y a la vez tener control absoluto sobre la aplicación:

_**index.php**_
[php]
include('carga-previa.php');
include('cabecera.php');
echo $contenido;
include('pie-pagina.php');
[/php]

_**carga-previa.php**_
[php]
class messages() {
  // etc
}

if (empty($_GET['a'])) $_GET['a'] = 'index';
else $_GET['a'] = strtolower($_GET['a']);

$msgStack = new messages();
$myLink   = new DB_mysql();
// cargar otras classes

ob_start();
if (is_readable('includes/paginas/'.$_GET['a'].'.php')) include('includes/paginas/'.$_GET['a'].'.php');
else include('includes/paginas/not-found.php');
$contenido = ob_get_contents();
ob_end_clean();
[/php]

Las demás páginas siguen tal cual como venían originalmente. Ahora lo único que debemos hacer para cargar por ejemplo las noticias es poner en nuestro navegador 
**www.ejemplo.com/?a=noticias** o bien 
**www.ejemplo.com/?a=login** 
para cargar el login. Si a esto le sumamos un poco de htaccess, podemos cargar la página respectiva mediante 
**www.ejemplo.com/noticias/** o 
**www.ejemplo.com/login/**.

En resumen: ob_* permite crear páginas con mucho poder de administración, fáciles y super rápidas de hacer en cuanto a desarrollo, de una forma elegante y cómoda.
