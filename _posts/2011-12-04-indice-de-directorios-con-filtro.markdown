---
author: admin
comments: true
date: 2011-12-04 19:41:16+00:00
layout: post
slug: indice-de-directorios-con-filtro
title: Índice de directorios con filtro
wordpress_id: 581
categories:
- Linux
- PHP
---

Hacía mucho tiempo que no escribía y era por una razón bien práctica: me cambié de país y como tal, he estado muy ocupado tanto con la mudanza, con el traerme mi mujer y trabajando, ya que de alguna forma hay que mantener la máquina andando y eso significa que aparte de todos los trámites regulares que hay que hacer y pagar, también hay que trabajar. 

Lo bueno es que estoy trabajando hace cerca de 2 meses ya, encontré trabajo sumamente rápido y como recién llegado en la empresa, me llenaron de proyectos pasados, presentes y futuros. Cuando estaba trabajando en Chile,  sólo tenía 3 proyectos permanentes, así que trabajar en localhost era bastante simple. Sin embargo, ahora tengo algo más de 18 proyectos y recordar el nombre de cada una no es tan simple como parece. Por eso, ya instalado en mi nueva casa y aprovechando un ratito de ocio, escribí un script que recorre un directorio y crea un link por cada directorio que encuentra para que de esa forma, pueda visitar http://localhost/ y hacer click en el proyecto que me interesa trabajar. Hasta ahí, no tiene ninguna maravilla; sin embargo; este script tiene algo especial, ya que permite esconder ciertos directorios y asignarles un alias a otros para que el listado no quede tan feo a primera vista.
<!-- more -->

En sí, consta de 2 archivos: un index.php y un .index (predeterminadamente el "." indica que se trata de un archivo oculto en los UNIX). 

Veamos el primer archivo: 
[php]<html><head><title>Proyectos en unreal4u</title></head><body>
<h1>Proyectos</h1>
<p>Edit .index for index options</p><ul><?php
$aliasFile = explode("\n",file_get_contents('.index'));
foreach($aliasFile AS $aliasString) {
	$aliasFileTmp = explode('|',$aliasString);
	if (!empty($aliasFileTmp[1]) AND substr($aliasFileTmp[1],0,1) != '#') {
		$aliasFileCompleted[$aliasFileTmp[0]] = $aliasFileTmp[1];
	}
}

$itemHandler = opendir('.');
$output = '';
while (($item = readdir($itemHandler)) !== false) {
	if (substr($item,0,1) != '.' AND is_dir($item)) {
		$linkText = $item;
		if (!empty($aliasFileCompleted[$item])) $linkText = $aliasFileCompleted[$item];

		if ($linkText != '.') {
			$output .= '<li><a href="'.$item.'">'.$linkText.'</a></li>';
		}
	}
}
echo $output;
?></ul></body></html>[/php]

En ella primero abrimos nuestro archivo .index y creamos un pequeño arreglo con los datos (válidos) que rescatamos de ella. En seguida, abrimos el directorio actual y empezamos a recorrerlo verificando primero que se trate de un directorio y que no sea un archivo oculto (o el directorio actual). Si estas condiciones se cumplen, se verifica por último en nuestro arreglo si tiene un alias y si debemos esconderlo o no. Si no lo debemos esconder, lo mostramos.

El segundo archivo es bastante simple, es un archivo en texto plano que indica el nombre del directorio y un alias. Opcionalmente, el alias lo podemos nombrar con "." para que de esa forma lo esconda. La estructura sería: 
[code]
# Hide logs and sessions folder
logs-sistema|.
sesiones|.
RemoteSystemsTempFiles|.

# Assign alias to some folders
00.general-tests|+++ Test Dir +++
doc|Technical Documentation
framework|Framework
zf|Pr&aacute;ctica Zend Framework
[/code]

De esta forma, cuando visitamos localhost, tendremos la siguiente vista (a la izquierda navegador, a la derecha explorador de archivos): 

[singlepic id=15 w=320 h=240 float=center]

Espero que les haya servido el tip, en cuanto tenga más tiempo escribiré más tips y continuaré un poco con la actualización más constante de este blog.
