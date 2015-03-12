---
author: admin
comments: true
date: 2013-08-03 16:49:25+00:00
layout: post
slug: sobre-los-hooks-en-git
title: Sobre los hooks en Git
wordpress_id: 787
categories:
- Control de versiones
- Linux
---

Hacía bastante tiempo que no escribía algo en el blog, así que parto hoy nuevamente con un nuevo post, aprovechando de celebrar el hecho que [haya sido seleccionado](http://www.pfcongres.nl/sprekers#sperberg) para dar [una charla en PFCongres 2013 en Utrecht](http://www.pfcongres.nl/programma), referente a cómo moverse lentamente de SVN a Git. 

Una de las temáticas que tocaré en dicha charla, será el traspaso íntegro de un repositorio en SVN, con hooks y otros checks, a Git, que tiene dos tipos de hooks. 
La primera de estos, es el que ocurre de forma local: ella se referencia hacia la carpeta `.git/hooks/` donde se almacenan todos los hooks locales. Sin embargo, estos hooks locales tienen un gran problema: no se traspasan al servidor remoto cuando hacemos un push, con lo cual los únicos que ejecutaremos dichos hooks seremos nosotros mismos: los demás no podrán ejecutarlos. 
<!-- more -->

El razonamiento detrás de esta problemática tiene dos razones de ser muy potentes: no todos ocupamos el mismo sistema y por el otro lado; obviamente teniendo en cuenta que Git fue escrito por el mismo caballero que puso a disposición del mundo un kernel libre; otros quieren tener siempre el total control de lo que ocurre y se ejecuta en sus equipos. Referente a eso, aprovechen de ver el [video completo de una charla de Linus en Google](http://yovisto.com/video/20275) para una explicación más o menos razonable sobre las razones para cambiarse hacia Git. Tengan en cuenta eso si que esa charla es de Mayo de 2007, o sea que muchas falencias descritas en esa charla ya han sido solucionadas / implementadas hace bastante tiempo ya.



## Activando los checks


Tal como mencioné, en `.git/hooks/` se guardan todos los checks que se puedan hacer en nuestra máquina. Ya vienen algunos ejemplos los que, cambiándole el nombre, podemos ejecutar. Lo único que tenemos que hacer es cambiar el nombre de `pre-commit.sample` a `pre-commit`. En seguida, si ahora intentamos hacer commit primero pasará por el checker y éste devolverá un estado de 0 si todo está bien o de cualquier otra cosa si hubo alguna violación a las reglas establecidas en ese archivo.

Para nuestros amigos de Windows, he de comunicarles que lamentablemente no he encontrado alguna forma genérica de poder llamar a un script por igual en Windows como en UNIX, si alguien conoce alguna forma, bienvenido sea de ponerlo en los comentarios.
También he de comunicarles que ya que dejé de trabajar con Windows hace mucho tiempo atrás (así como todos en la oficina), se asume en todo este artículo que ocuparemos Linux o Mac OS X (que trabaja igual en este sentido). Y si usted todavía está "trabajando" con Windows, le aconsejo; como siempre; se interiorice con el mundo Linux ya que se está perdiendo la mitad de su vida y por supuesto la gran oportunidad de optar a trabajos mucho mejor pagados.



## Primer check


Supongamos que tenemos un check increíblemente simple en nuestro pre-commit (el que se ejecuta justo ANTES de hacer el commit, que es la etapa donde uno debería revisar por errores lógicos, este archivo lo debemos guardar en `.git/hooks/pre-commit`: 

[php]
#!/usr/bin/php
<?php

$output = shell_exec('git diff --name-only HEAD .');
if (empty($output) || strpos($output, '.php') === false) {
    printf('You need to commit at least one PHP file!'.PHP_EOL);
    exit(2);
}

exit(0);
[/php]

Este check revisa si existe algún archivo con extensión .php y si no hay ninguno, muere entregando un simple "2" como respuesta. De lo contrario, muere entregando un "0" como respuesta. 
Aunque ustedes no lo crean, el hecho de morir con un "2" o un "0" marca la gran diferencia: si el script muere con cualquier cosa distinta de "0", se cancela la acción, es decir, no se realiza el commit. El número con el cual muere el script es totalmente de su elección.



## Compartiendo los checks


El check de arriba sólo sirve en la máquina local. En este caso, es bastante simple y por lo demás, resulta trivial poder decirle a un compañero que simplemente copie y pegue un pedazo de código en su propia máquina. Sin embargo, para proyectos más grandes por lo general se requieren también más y mejores chequeos: en el trabajo por ejemplo, todas las siguientes condiciones se deben cumplir para que un commit tenga éxito: 




  * Si es un archivo PHP, que compile


  * Que no existan espacios al final


  * Que no termine con ?>


  * Que todo el archivo a hacerle commit, sólo contenga diferencias en el espaciado


  * Estilo propio de la empresa: un espacio entre keyword (if, switch, else, etc) y el siguiente paréntesis / llave / corchete


  * Dos condiciones anteriores son válidos sólo para php y javascript, y siempre y cuando sean nuestro código. Código externo no pasa por estos validadores


  * Si el archivo es un CSS, que; entre otros; si la declaración contiene un #, que empiece con uno. Los id son únicos y por lo tanto, ayuda bastante al rendereador que las declaraciones únicas contengan las menores parientes posibles


  * Si es un XML, que compile



Todas esas verificaciones no son tan triviales como verificar que venga un archivo que termine en .php. A su vez, estas reglas están sujetas a modificaciones debido a que de repente se encuentran casos nuevos que no habían sido considerados anteriormente y que, por lo tanto, se tienen que modificar.



## Preparando la máquina



Lo mejor que se puede hacer es crear un directorio `.hooks` (o cualquier otro que ustedes escogan y que no sea dentro de `.git`) donde tendremos al menos dos archivos base: uno para crear el symlink hacia nuestro hook y el otro es el hook propiamente tal. Se elije por esta estructura debido a que todo lo que está dentro de la carpeta `.git` no se comparte con los demás y sólo es de ustedes, por lo tanto, para que todos puedan usar el mismo hook, se debe elegir una ubicación distinta para que de esta forma, se incluya en el repositorio de todas formas. Aunque no hay ninguna regla que diga que la carpeta tiene que tener uno u otro nombre, por convención generalizada se ocupa el nombre `.hooks`.

[bash]
#!/bin/bash

# Do not work as root, but as a normal user
if [[ $EUID -eq 0 ]]; then
    echo "You must run this command as a normal user!"
    exit 1
fi

# Find out relative route of where we are
SOURCE="${BASH_SOURCE[0]}"
while [ -h "$SOURCE" ]; do # resolve $SOURCE until the file is no longer a symlink
  TARGET="$(readlink "$SOURCE")"
  if [[ $SOURCE == /* ]]; then
    SOURCE="$TARGET"
  else
    DIR="$( dirname "$SOURCE" )"
    SOURCE="$DIR/$TARGET" # if $SOURCE was a relative symlink, we need to resolve it relative to the path where the symlink file was located
  fi
done

# Get current directory and hook directory
CWD="$( cd -P "$( dirname "$SOURCE" )" && pwd )"
HOOK_DIR="$CWD/../.git/hooks"

if [ -f $HOOK_DIR/pre-commit ]; then
    mv $HOOK_DIR/pre-commit $HOOK_DIR/pre-commit.bck
    echo "Backed up previous pre-commit hook as $HOOK_DIR/pre-commit.bck"
fi
ln -s -f $CWD/pre-commit.hook.php $HOOK_DIR/pre-commit
echo "Symlink(s) and options set, enjoy the programming!"
exit 0
[/bash]

Parece bastante código, pero en sí es bastante simple: este script verifica primero que no estén trabajando como root, luego averigua dónde está parado, para que de esta forma se pueda ir a la raíz del repositorio, hacia la carpeta `.git` y una vez ahí, crea el symlink respaldando el symlink/archivo previo si lo hubiere. Una vez hecho eso, establece algunas opciones y con eso estaremos listos para poder ejecutar nuestro hook.



## Conclusiones



Un proyecto grande puede ser bastante complejo, requiriendo de variadas herramientas: no sólo es necesario que el repositorio controle todo el código, sino que muchas veces además hay que seguir ciertas líneas y códigos de conducta en el código: así puede ser que por ejemplo sea necesario repasar todo el código por [PHPUnit](http://phpunit.de/manual/current/en/index.html), para que los tests den positivo antes que el código se ponga online, evitando con ello mayores desastres que pudieran surgir que si se colocara el código de inmediato en línea. 
Aparte de los obvios tests de integridad, muchas veces también se controla con [PHP_CodeSniffer](http://pear.php.net/package/PHP_CodeSniffer/), buscando que todo el código se "vea" de forma similar. Más de estas ideas ingeniosas pueden encontrar [en esta página](http://code.google.com/p/phpcheckstyle/wiki/Documentation).

Sin embargo, para que se cumplan todas estas reglas a rajatabla, no basta con hacer una audición de vez en cuando: es necesario que antes de cada commit se verifiquen. La forma de hacer esto, es mediante los famosos hooks: acciones que se ejecutan antes o después de otra acción.

Por supuesto que existe un problema mayor y otro menor: 
El mayor problema ocurre que la configuración de hooks es única para cada usuario: no hay forma de forzar que localmente todos los usuarios deban pasar por las mismas pruebas en cada commit. Por el otro lado, sí es posible realizar una revisión sobre el producto final en el server centralizado, pero por experiencia propia eso sólo hace que se incrementen de forma exponencial los commits tipo "Arreglo por estúpido hook". De todas formas, hay formas de hacer que los usuarios puedan de forma fácil y rápida habilitar los hooks en sus propias máquinas.

El problema más pequeño que pudieran encontrar con los hooks sería en los clientes nativos de los IDE: generalmente no incorporan la funcionalidad de revisar hooks. Por lo tanto, lo único que pueden hacer en este caso es hacer commit desde la línea de comandos, aunque [existen también herramientas](http://www.phpsrc.org/) (principalmente Eclipse) que permite ejecutar comandos en cuanto guardas un documento.

Espero que estos tips les puedan ayudar en la creación de hooks tanto para ustedes como para sus compañeros de trabajo. Hasta la próxima!
