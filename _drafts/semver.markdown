---
comments: true
date: 2015-08-10 00:00:00+00:00
layout: post
slug: explando-semantic-versioning
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

La idea detrás de esto suena bastante bien y la verdad es que después de haberlo probado en algunos sistemas de producción; funciona. En muy
resumidas cuentas, podemos ver que una versión tendrá siempre 3 componentes:  
<code>X.Y.Z</code>

Y qué significan cada uno de ellos? Pues eso también es bastante simple:

<code>X</code> representa la <strong>versión mayor</strong>.  
<code>Y</code> representa la <strong>versión menor</strong>.  
<code>Z</code> representa la <strong>versión de parches</strong>.

<!-- more -->

Ahora bien, ¿qué quiere decir esto? ¡Simple! Existen algunas reglas básicas para el incremento de las versiones, que me gusta llamarlas "reglas de convivencia": 

* Cada versión siempre se incrementará de 1 a 1.
* Si se incrementa la versión menor, la versión de paches deberá volver a cero.
* De la misma forma, si se incrementa la versión mayor, tanto la versión menor como la versión de parches deberán volver a cero.
* Una vez que se ha sacado al público una versión, ésta NO debe alterarse por NINGÚN motivo! Si les faltó un parche, se debe crear un nuevo incremento en la versión.

Adicionalmente, hay otro set de reglas que a mi me gusta llamarlas "las leyes", que son:

* Se debe incrementar la versión mayor cuando haces cambios que causen problemas en la compatibilidad retroactiva.
* Se debe incrementar la versión menor sólo si se **agrega** código que no afecte la compatibilidad retroactiva.
* Se debe incrementar la versión de parches si son arreglos que son retroactivamente compatible.

###Versión 1.2.3

Supongamos la versión **1.2.3** de la siguiente función:

{% highlight php %}
<?php

// Versión 1.2.3

class HelloWorld
{
    public $name;
    public $age = 0;
    public function returnString($name, $age = 0)
    {
        $this->name = $name;
        if ($age > 2) {
            $this->age = $age;
        }
        return sprintf('Hello %s, your age is %d', $this->name, $this->age);
    }
}
{% endhighlight %}

###Versión 1.2.4

Una idea para solucionar un bug que sea retroactivamente compatible, es darle un valor predeterminado (string vacío) a <code>$name</code>,
de esa forma no alteramos la funcionalidad base de nuestro script, por lo tanto, la *nueva* versión de nuestro software pasará a ser la 
versión **1.2.4**: 

{% highlight php %}
<?php

// Versión 1.2.4

class HelloWorld
{
    public $name;
    public $age = 0;
    public function returnString($name='', $age = 0)
    {
        $this->name = $name;
        if ($age > 2) {
            $this->age = $age;
        }
        return sprintf('Hello %s, your age is %d', $this->name, $this->age);
    }
}
{% endhighlight %}

###Versión 1.3.0

Ahora supongamos que queramos agregar una nueva función que devuelva un muy simple hola mundo: como estamos agregando nueva funcionalidad,
deberemos incrementar la versión menor de nuestro software, haciendo que la *nueva* versión de nuestro software pasará a ser la versión
**1.3.0**:

{% highlight php %}
<?php

// Versión 1.3.0

class HelloWorld
{
    public $name;
    public $age = 0;
    public function returnString($name='', $age = 0)
    {
        $this->name = $name;
        if ($age > 2) {
            $this->age = $age;
        }
        return sprintf('Hello %s, your age is %d', $this->name, $this->age);
    }

    public function helloWorld()
    {
        return sprintf('Hello world!');
    }
}
{% endhighlight %}

###Versión 2.0.0

Llegados a esta etapa nos dimos cuenta que en realidad hemos estado haciendo mal el cálculo de la edad, y que en realidad debería
haber sido siempre mayor a 0 en vez de 2. Adaptamos nuestro código y enseguida incrementamos el número de versión a **2.0.0** ya que
el cambio que estamos por realizar introducirá un problema en la retrocompatibilidad: código que antes confiaba en que esta funcionalidad
era la implementación buena, puede producir problemas ahora que la lógica detrás de nuestro código cambió. Por si fuera poco, vimos que
la función agregada en la versión 1.3.0 en realidad no debería haber sido, así que se aprovecha de eliminar también, introduciendo de esta
forma en esta nueva versión 2 cambios que no son compatibles retroactivamente: 

{% highlight php %}
<?php

// Versión 2.0.0

class HelloWorld
{
    public $name;
    public $age = 0;
    public function returnString($name='', $age = 0)
    {
        $this->name = $name;
        if ($age >= 1) {
            $this->age = $age;
        }
        return sprintf('Hello %s, your age is %d', $this->name, $this->age);
    }
}
{% endhighlight %}

##Conclusiones

Tal como podemos apreciar, con sólo algunas reglas básicas sabemos precisamente qué esperar de un nuevo software cuando se ha sacado
una nueva versión del mismo. Sabemos cuándo deberemos poner especial atención a updates cuando haya una nueva versión mayor y sabremos
también que hay nueva funcionalidad esperándonos cuando veamos que sólo se ha incrementado la versión menor. También sabremos automáticamente
si hay soluciones a bugs u otros cuando sólo se incremente la versión de parches, caso en el cual puede ser interesante revisar si la 
solución al problema que se solucionó también nos podría afectar.

Por el otro lado, también nos servirá para saber en qué momento incrementar qué versión. Especial importancia en todo esto cobran los 
unit tests, ya que ellos son un indicador claro cuando alteramos alguna funcionalidad con respecto al número de versión a incrementar: era
un test que no habíamos considerado y soluciona un bug? Pues se incrementa la versión de parches... tuvimos que crear nuevos tests que vayan
probando nueva funcionalidad incorporada en nuestro software? Se incrementará la versión menor. Fallan algunos tests que tuvimos que arreglar?
Eso es de inmediato un incremento mayor, ya que nuestro software en determinadas ocasiones ya no hace lo mismo que hacía antes.

Por supuesto que no he cubierto todos los casos acá, pero para eso se pueden referir a las fuentes que cito a continuación. ¡Que tengan buen día!

##Fuentes:  
[semver](http://semver.org/)  

