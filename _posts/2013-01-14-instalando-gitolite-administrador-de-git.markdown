---
author: admin
comments: true
date: 2013-01-14 12:25:42+00:00
layout: post
slug: instalando-gitolite-administrador-de-git
title: 'Instalando Gitolite: administrador de Git'
wordpress_id: 725
categories:
- Apple/Mac
- Control de versiones
- Linux
---

Siguiendo con el [artículo previo](http://blog.unreal4u.com/2012/12/las-razones-de-cambiarse-de-svn-a-git/), ahora toca saber cómo instalar ese tan ansiado servidor propio de Git al mismo estilo que SVN, de forma que nosotros podamos controlar los accesos sin el inconveniente de pagarle a Github para que él almacene los proyectos por nosotros. 

Lo primero será un pequeño resumen de todo lo que se verá en este artículo, de modo que vayan preparando sus mentes ávidas de conocimientos:




  * ¿Por qué Gitolite?


  * Instalando Gitolite


  * Configuración de llaves públicas y privadas


  * Creando un repositorio


  * Agregando más clientes al repositorio


  * Notas de instalación en Windows


  * Importando proyectos desde SVN a Git



No son pocos temas y trataré de ser lo más resumido posible sin dejar de explicar ciertas cosas para entender el proceso. Así que partamos.

<!-- more -->



## Por qué Gitolite


Git por si solo es sólo un repositorio. Es capaz de llevar (muy bien) la cuenta de cuántos y cuáles archivos tiene, cuáles fueron los cambios aplicados a esos archivos y por supuesto de unir cambios y mezclarlos para llegar a producir un sistema final. Sin embargo, lo que Git **NO** es, es un sistema de autentificación, capaz de discernir entre usuarios y poder darle (o denegarle) acceso a cierto recurso. Como todo en el mundo Linux, Git está hecho para hacer una sola cosa bien hecha, y delega la tarea de todo lo demás en software externo. Es por esto que ocuparemos Gitolite, que es un especie de túnel entre Git (el programa) y SSH que será el encargado de dejarnos pasar. Pero hay un ligero problema acá debido a que por un lado tenemos el usuario con el que nos conectaremos a SSH y de ahí en adelante usaremos el protocolo propio de Git, por lo que tendremos que montar una infraestructura basada en llaves públicas y privadas ya que el usuario mediante el cual queremos autentificarnos no tendrá contraseña y por lo tanto no se podrá acceder de forma directa como estamos acostumbrados.
Después del paso de autentificación, es donde entra en juego nuestro querido Gitolite, ya que éste será el encargado de traducir los permisos propios de cada usuario que autorizemos para que pueda acceder a los repositorios alojados en la máquina: ya sea para denegarle permiso a ciertas ramas de nuestro repositorio u otras posibilidades. Es Gitolite entonces en definitiva el que se encarga de los permisos internos una vez que el usuario se ha autentificado.



## Instalando Gitolite


Antes de empezar, consíganse una máquina con Linux o Mac OS X, ya que trabajar en Windows es un parto enorme y tendrán que instalar varias cosas (que igual cubriré) pero la primera parte es la más importante y será un alivio tremendo avanzar de forma rápida. Como siempre, recomiendo que si ya llegan a este punto en sus vidas y todavía "trabajan" en Windows, se cambien a Linux y verán como todo es bastante más fácil y rápido. En un principio les costará acostumbrarse, pero en un par de meses verán que los esfuerzos invertidos valdrán la pena... mucho!

Para el servidor, supondré que será una máquina CentOS en su última versión instalada de forma limpia y minimalista. Para el (primer) cliente, supondré una máquina también en Linux o Mac OS X. Partamos para la primera patita, a modo de aclaración, el dominio de esta máquina será `ejemplo-git.com`:
[bash]
yum install git git-daemon perl-Time-HiRes
adduser git
su - git
git clone git://github.com/sitaramc/gitolite.git
mkdir -p $HOME/bin
gitolite/install -to $HOME/bin
[/bash]

Update 2013-07-11: Ahora gitolite también requiere de perl-Time-HiRes. De forma contrario, no podrán correr el instalador. Puede ser que sea algún bug temporal, así que prueben sin el paquete, pero si el equipo reclama en el último paso que: 
"Can't locate Time/HiRes.pm in @INC" entonces deberán instalar perl-Time-HiRes también.

Con estos comandos, hemos terminado la instalación de Gitolite, así que ahora viene la configuración de la misma.



## Configuración de llaves públicas y privadas


Hasta aquí, todo bien. Tenemos Gitolite instalado y corriendo. Sin embargo, tenemos que definir ahora el administrador e inicializar nuestro primer repositorio que será el que controle todos los demás repositorios. (Tal como MySQL se ocupa a si mismo para autentificar y establecer opciones, Gitolite ocupa Git para administrar los repositorios y usuarios).

Así que, en cualquier máquina Linux o Mac OS X, correremos:
[bash]
ssh-keygen -t rsa -C "tu@corr.eo"
# Esto les preguntará dónde desean almacenarlo
# y además les preguntará por una passphrase,
# que es lo que reemplaza la contraseña. 
# Sigan leyendo abajo para ver las implicancias
# de sus decisiones.
#
# Yo supongo todos los valores predeterminados
scp ~/.ssh/id_rsa.pub root@ejemplo-git.com:/root/[su nombre].pub
[/bash]

Lo que hicimos acá fue generar una llave pública y privada. La gracia de esto es que su llave privada asegurará que ustedes son quienes dicen ser. De esta forma, es sumamente importante que nunca la compartan con nadie, ya que si alguien obtiene su clave privada, podrá suplantar su identidad. Por este hecho, puede ser importante que le coloquen una passphrase, para que de esta manera, sea necesario también ingresar una contraseña, lo que será otro pasito en pro a la seguridad. Lo único malo es que IDEs como <del>Zend Studio en Mac OS X no trabajan bien con llaves que contengan passphrases</del>, más que nada porque el Java sobre el cual corre es muy complicado de actualizar y se necesita de Java 1.7 como mínimo para que puedan autentificarse en Zend Studio mediante keys con passphrases.
Update 2013-07-11: Zend Studio 10 corre sobre Java 1.7 y ahora no hay problema alguno con passphrases en llaves privadas.

Ahora nos vamos de vuelta al servidor donde tenemos Git instalado, e ingresamos **como root**: 
[bash]
mv ~/[su nombre].pub /home/git/
su - git
gitolite setup -pk [su nombre].pub
# Si esto no funciona, prueben poniendo directo la ruta
# al binario en la carpeta bin/
[/bash]

Por ahora terminamos en nuestro server y todo lo demás corre por cuenta ahora del cliente. Vamos nuevamente a la consola y ejecutamos:
[bash]
cd /tmp/
mkdir –p gitolite-admin
cd gitolite-admin
git clone git@ejemplo-git.com:gitolite-admin .
[/bash]

A partir de este punto, ya podemos empezar a agregar usuarios agregando su llave pública en el directorio `keydir` y estableciendo permisos y otros en el archivo de configuración `conf/gitolite.conf`



## Creando un repositorio



La configuración en Gitolite es bastante simple, si queremos nada más que las opciones más básicas, sólo tenemos que ingresar la siguiente información: 

[bash]
repo repo-de-unreal4u
    RW+ = unreal4u
[/bash]

Esto creará un repositorio llamado `repo-de-unreal4u` y le dará a la llave pública llamada `unreal4u.pub` (guardada en el directorio `keydir`) acceso a escritura, lectura y borrado. Para una lista más exhaustiva con las opciones de Gitolite, [sírvanse revisar la documentación oficial](http://sitaramc.github.com/gitolite/g2/conf.html). Existen un montón de pequeños trucos que pueden aplicarse en este archivo de configuración, como por ejemplo que nadie pueda hacer push a master (excepto cierto _grupo de usuarios_) o definir quiénes serán los que tengan acceso a ciertos repositorios. Gitolite es bastante flexible en este archivo y permite definir bastante bien quiénes serán los que puedan (o no) tener accesos a ciertos repositorios. 

Cuando hagan commit y finalmente un push al servidor, se aplicarán los cambios y se creará un repositorio llamado `repo-de-unreal4u`. Para clonarlo en su cliente, tienen que irse al directorio deseado y hacer un `git clone git@ejemplo-git.com:repo-de-unreal4u .`. Con eso tendrán ahora una copia del repositorio y la tendrán alojada en un servidor centralizado. Como nombre de repositorio les aconsejo no salirse del alfabeto inglés.



## Agregando más clientes al repositorio


Este será quizás el punto más corto: para agregar un usuario simplemente hacemos un clone de `gitolite-admin` y después de eso agregamos su llave pública en el directorio `keydir`. Finalmente, le damos acceso en `gitolite.conf` a algún repositorio y estaríamos listos. 



## Notas de instalación en Windows


Uno de los requerimientos originales era que el nuevo sistema tenía que trabajar sin distinción en cualquier sistema operativo, lo cual significaba incluir al infame Windows. Para lograr esto, hubo que instalar algunas cosas: 




  * [Git for Windows](http://msysgit.github.com/)


  * [TortoiseGit](http://code.google.com/p/tortoisegit/)


  * Si están ocupando Zend Studio 8, también tendrán que [instalar EGit](http://www.eclipse.org/egit/download/).



Ahora tendrán Git Bash, lo cual es en realidad un mini-entorno Linux mediante la cual podrán trabajar. En este ambiente, vienen programas como git y ssh-keygen que sirve para crear sus claves de la misma forma en que lo haría cualquier Linux. La parte de configuración se las dejo a ustedes, pero para que se hagan una idea me demoré cerca de 4 horas en hacerlo funcional. En Mac OS X y Linux, sólo me tomó 10 minutos.



## Importando proyectos desde SVN a Git


Si bien es cierto soy de la corriente que lo mejor es empezar una nueva tecnología desde cero, esto no se aplica en algunos casos. En el de mi empresa, querían guardar todo el historial así que tuve que investigar una manera de hacerlo y llegué a lo que les explicaré a continuación.

Los dos primeras líneas son para indicarle a Git cuántos recursos como máximo puede ocupar, importando un proyecto de cerca de 10.000 commits en una máquina con 4GB en RAM y 16 procesadores fue demasiado, así que tuve que bajar algunos valores para que pudiera hacer la importación sin problemas.
Después de eso, haremos un archivo que contenga línea a línea cada committer. Si son muchos, les aconsejo que busquen en internet "show list of committers svn" y saquen desde ahí algún script que pueda realizar la recuperación de todos los usuarios que alguna vez estuvieron involucrados en su proyecto. La idea de esto, es que tengamos un pequeño archivo mediante la cual hacemos el traspaso de usuarios desde SVN a Git.
Finalmente, la importación en sí, dentro de una carpeta temporal. Después de eso, un poco de limpieza y conversión: los tags se convertirán a tags, los branches se mantendrán como branches. 
Cuando estemos listos con eso, borraremos el branch "trunk" de SVN (ya que tendremos master ahora). Invocaremos el garbage collector para un poco de limpieza antes de subirlo a Gitolite y finalmente configuraremos un repositorio limpio hacia la cual exportamos nuestro nuevo repositorio recién creado y lo subiremos a Gitolite. Para proyectos grandes, les aconsejo lo hagan en la misma máquina donde está alojado Git.

[bash]
git config --global pack.threads 4
git config --global pack.windowMemory 512m
mkdir ~/tmp/repo-tmp/ && cd ~/tmp/
vi users.txt
--COPY FROM HERE--
csperberg = Camilo Sperberg <lalala@lalala.com>
segundo = Segundo Usuario <segundo@usuario.com>
tercer = Tercer Usuario <tercer@usuario.com>
--COPY TILL HERE--
git svn clone --stdlayout --no-metadata -A users.txt svn://ejemplo.com/ejemplo-repo/ repo-tmp/
cd repo-tmp/
git for-each-ref refs/remotes/tags | cut -d / -f 4- | grep -v @ | while read tagname; do git tag "$tagname" "tags/$tagname"; git branch -r -d "tags/$tagname"; done
git for-each-ref refs/remotes | cut -d / -f 3- | grep -v @ | while read branchname; do git branch "$branchname" "refs/remotes/$branchname"; git branch -r -d "$branchname"; done
git branch -D "trunk"
git gc
cd ..
# la carpeta html es en mi caso, la carpeta donde tengo todos mis proyectos en los que trabajo día a día
git clone repo-tmp/ ~/html/repo-de-unreal4u/
cd ~/html/repo-de-unreal4u/
rm -rf ~/tmp/repo-temp/
git remote rm origin
git remote add origin git@ejemplo-git.com:repo-de-unreal4u
git push origin --all
git push origin --tags
[/bash]

Ya con eso listo, termina el tutorial para montar Gitolite. Espero les pueda servir!
