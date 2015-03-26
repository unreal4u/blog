---
comments: true
date: 2010-08-19 14:24:06+00:00
layout: post
slug: busqueda-de-php-net-en-firefox-y-trucos-de-vim
title: Búsqueda de PHP.net en Firefox y trucos de vim
wordpress_id: 391
categories:
- Mundo Web
- PHP
---

Hoy me di cuenta de algo que no me había dado cuenta. (duuuh)
Siempre, cuando necesitaba buscar alguna función en PHP.net, hacía la típica: iba a Google, tipeaba el nombre de la función, ubicaba PHP.net y hacía click.
Bueno, como casi todos los grandes descubrimientos de la humanidad, estaba en la portada de PHP.net y por accidente hice clic donde no debía. Descubrí esto:

![](http://blog.unreal4u.com/wp-content/uploads/2010/08/php.png)

Sin embargo, el verdadero potencial de este post está por verse, veamos algunos comandos semi-avanzados de vim que nos hará la vida mucho más fácil al trabajar con este editor.<!-- more -->

Lo primero, (y más importante) a mi gusto, es instalar el manual de PHP en vim. Para esto, debemos tener instalado man y pear. De ahí, se procede a la instalación del manual.

En  CentOS, todo esto se haría como:

{% highlight bash %}
# yum install php-pear man
# pear install doc.php.net/pman
{% endhighlight %}

Y estaríamos con el 80% de la pega adelantada ya. Lo más importante, sin embargo, viene ahora.

Debemos, primero que nada, abrir el archivo ~/.vimrc. Si no existe, lo creamos. En ella, podemos configurar a nuestro gusto total vim, incluyendo opciones tan avanzadas como agrupar código (en una sola línea), poner el número de línea del código o consultar el manual de PHP con una tecla mientras estamos en la función. (Muy al estilo man).

Esta es mi configuración actual de vim:

<pre>
set tabstop=2
set autoindent
set incsearch
set number
set keywordprg=pman
autocmd FileType php let php_folding=1
au BufWinLeave * mkview
au BufWinEnter * silent loadview
</pre>

Explicaré cada línea: 

_set tabstop=2_: Esto nos permite que, cuando apretemos tab, inserte espacios en vez de un tab. La cantidad de espacios es definida después del signo igual.  
_set autoindent_: Esta muy útil función permite que vim siga las reglas de indentación que tiene el archivo. Muy útil.  
_set incsearch_: permite que, muy al estilo búsqueda rápida de Firefox, se vaya ubicando automáticamente el cursor en cuanto estemos buscando alguna frase.  
_set number_: le pone número a las líneas.  
_set keywordprg=pman_: esta línea hace que integremos pman con vim, para que de esta manera, al apretar "K" (k mayúscula) sobre alguna función en específico aparezca el manual.

El resto: Activa la agrupación de código. La primera línea inicia la característica en sí, mientras que las dos siguientes guardan y cargan automáticamente las agrupaciones respectivamente.
Se debe utilizar en conjunto con algunos comandos:


**:20,40 fo**  
Para agrupar de la línea 20 a la 40.  
**zfa}**  
Para agrupar de aquí a la próxima llave, aunque ojo: se debe estar posicionado sobre la llave que abre!  
**zd**  
Para eliminar el fold actual  
**zm**  
Para refoldear todos los folds anteriores.Para más comandos útiles, visitar la tercera fuente.

Hay bastantes más consejos para vim, pero no todos me funcionaron. Una de las más populares que he visto dando vueltas por internet es activar la vista de código HTML o queries dentro de los string, pero no me funcionaron. Tampoco me funcionó desactivar los tags cortos, así que ojo con eso.

Fuentes: 
[PHPSLACKER](http://phpslacker.com/2009/02/05/vim-tips-for-php-programmers/)
[bjori doesn't blog](http://bjori.blogspot.com/2010/01/unix-manual-pages-for-php-functions.html)
[Linux.com](http://www.linux.com/archive/feature/114138)

