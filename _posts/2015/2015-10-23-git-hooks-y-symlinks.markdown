---
comments: true
date: 2015-10-23 18:00:00
layout: post
slug: git-hooks-y-symlinks
title: Git hooks y symlinks
categories:
- Git
- Linux
---

Nuestro repositorio es un repositorio privado, montado con Gitolite (de lo cual [pueden leer algo más por acá](http://blog.unreal4u.com/2013/08/sobre-los-hooks-en-git/))
y lo bueno es que nos permite obtener un control absoluto sobre nuestro código como del acceso.

Sin embargo, esto también nos puede proporcionar dolores de cabeza: últimamente habíamos estado teniendo algunos problemas cuando nos tocaba subir symlinks, ya que
por alguna extraña razón nuestro validador de XML y JSLint denegaban el commit diciendo que el archivo no compilaba.

Este post tratará justamente sobre ese caso y de cómo lo resolvimos.

<!-- more -->

#### El problema

El validador de XML (y también el de JSLint) funciona de la siguiente manera: 

* Primero necesitamos una copia del archivo en el commit nuevo.
* Se copia ese archivo a <code>/tmp/</code>.
* Luego tiramos el validador por encima de ese archivo temporal.
* Eliminamos el archivo temporal.
* Notificamos si el archivo es inválido o lo dejamos pasar si es válido.

Para el primer apartado bastaba hacer lo siguiente:

{% highlight php %}
<?php
    protected function getFileContents($filename, $sha) {
        return shell_exec('git cat-file -p '.$sha.':'.$filename.' 2> /dev/null');
    }
?>
{% endhighlight %}

¿Fácil verdad? Bueno, en el caso de un archivo común y corriente, esto funciona de las mil maravillas. Sin embargo, cuando tenemos
un symlink, Git no lo entiende y por lo tanto devuelve el contenido del symlink: es decir, la ruta hacia el archivo al cual tiene
que apuntar, lo cual; una vez que se entiende; suena increíblemente lógico.

#### La solución

El subcomando [<code>cat-file</code>](https://git-scm.com/docs/git-cat-file) de Git puede llegar a entender symlinks, pero requería mucho más estudio del que apliqué para
la solución final, así que descarté esa posibilidad y me dediqué a solucionarlo de otra manera. Lo que hice fue bastante simple:

* Primero almacenar el contenido del archivo en una variable local.
* Después revisar si el contenido de eso no está vacío o si contiene un retorno de línea.
* Si no contiene ningún retorno de línea, crear un listado de archivos, filtrando por lo que nosotros obtuvimos.
* Si dicho listado de archivos no está vacío, entonces cargo ese archivo.
* Sigo con el resto del proceso.

El revisar si el contenido original contiene un salto de línea o no es un check que en la gran mayoría de los casos será bastante útil: un symlink
siempre contendrá una sola línea y es bastante improbable que un archivo contenga una sola línea de código. Aún así, esa sola línea de código tendría
que ser un nombre de archivo válido en el commit que estamos revisando.

En código esto se ve así: 

{% highlight php %}
<?php
    protected function getFileContents($filename, $sha) {
        $filteredTree = '';
        $possibleFileContents = shell_exec('git cat-file -p '.$sha.':'.$filename.' 2> /dev/null');
        if (!empty($possibleFileContents) && strpos($possibleFileContents, PHP_EOL) === false) {
                $filteredTree = shell_exec('git ls-tree --name-only '.$sha.' | grep \''.$possibleFileContents.'\'');
        }

        if (empty($filteredTree)) {
                return $possibleFileContents;
        } else {
                return shell_exec('git cat-file -p '.$sha.':'.$possibleFileContents.' 2> /dev/null');
        }
    }
?>
{% endhighlight%}

#### Problemas con esta solución

* Mi arreglo no considera más niveles de symlinks. Por un lado, es bueno ya que si uno crea symlink detrás de otro symlink debería
haber; a mi juicio; otra forma de arreglar el problema.
* Otro posible problema con esta solución es si el contenido original contiene una sola línea con un nombre de archivo válido. En
este caso, se cargará otro archivo en vez del original. Esto se puede arreglar y detectar con <code>git ls-tree</code> pero es por
ahora un caso que al menos en nuestro código no se dará.
