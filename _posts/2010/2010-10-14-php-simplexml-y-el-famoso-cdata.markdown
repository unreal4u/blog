---
comments: true
date: 2010-10-14 14:02:52+00:00
layout: post
slug: php-simplexml-y-el-famoso-cdata
title: PHP, SimpleXML y el famoso CDATA
wordpress_id: 456
categories:
- Classes
- Mundo Web
- PHP
---

PHP tiene -a mi gusto- una de las mejores librerías para trabajar  con XML: SimpleXML. Crear un documento XML con SimpleXML es realmente  simple, ya que basta agregar nodos hijos con sus respectivos atributos y  estaría listo el documento. Sin embargo, SimpleXML sólo tiene una caída  bien fea: el no saber tratar documentos que contengan CDATA.

El CDATA dentro de los documentos XML sirve para expresar contenido  en HTML y le indica al navegador (también a Adobe Flash) que no debe  parsear su contenido, esto quiere decir que debe interpretar la cadena  "Hola mundo" dentro de un párrafo así:

{% highlight bash %}
<texto><[CDATA[<p>Hola mundo</p>]]></texto>
// y no así:
<texto>&gt;p&lt;Hola mundo&gt;/p&lt;</texto>
{% endhighlight %}

<!-- more -->

Estuve buscando bastante por todos lados para encontrar alguna forma  de poder incorporar este tipo de dato en un documento XML, y para mi  mala suerte, lo único que encontré fue cómo leerlo, pero no la  escritura.
Eso hasta que me encontré con un [post bastante corto pero conciso](http://coffeerings.posterous.com/php-simplexml-and-cdata) que hablaba sobre el tema y que para mejor suerte mía, tenía un pedazo de código que me pareció obvio... una vez que lo vi:

{% highlight php %}
<?php
class SimpleXMLExtended extends SimpleXMLElement {
  public function addCDATA($cData) {
    $node = dom_import_simplexml($this);
    $no = $node->ownerDocument;
    $node->appendChild($no->createCDATASection($cData));
  }
}
{% endhighlight %}

Esa class, que extiende las funcionalidades de la implementación  predeterminada de SimpleXML en PHP, nos permite agregar una función que  nos permite combinar SimpleXML con DOM (que sí tiene soporte para CDATA)  y devuelve un objeto SimpleXML.

De esta forma, lo único "distinto" es que en vez de ocupar:

{% highlight php %}
<?php
$xml_raiz = simplexml_load_string('<?xml version="1.0" encoding="UTF-8"?><categorias/>');
// Ahora será:
$xml_raiz = new SimpleXMLExtended('<?xml version="1.0" encoding="UTF-8"?><categorias/>');
// Agregando CDATA:
$texto = $xml_raiz->addChild('el_html');
$texto->addCDATA(htmlentities('<p>El contenido en HTML</p>'));
{% endhighlight %}

Lo de arriba devolvería algo muy parecido a:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
  <categorias>
    <el_html><[CDATA[<p>El contenido en HTML</p>]]></el_html>
  </categorias>
{% endhighlight %}

Para finalizar, un pequeño tip para poder habilitar la lectura de  CDATA en SimpleXML: basta abrir el XML con una directiva especial,  LIBXML_NOCDATA:
{% highlight php %}
<?php

$lol = simplexml_load_file('el_archivo.xml', NULL, LIBXML_NOCDATA);
{% endhighlight %}

Fuente: [coffeering's posterous](http://coffeerings.posterous.com/php-simplexml-and-cdata)
