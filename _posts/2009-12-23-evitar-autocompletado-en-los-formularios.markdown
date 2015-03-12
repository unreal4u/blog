---
author: admin
comments: true
date: 2009-12-23 18:42:53+00:00
layout: post
slug: evitar-autocompletado-en-los-formularios
title: Evitar autocompletado en los formularios
wordpress_id: 248
categories:
- Mundo Web
- PHP
---

Hoy en el [foro de CHW](http://www.chw.net/foro/webmasters-f91/292343-que-el-navegador-no-guarde-la-informacion.html), preguntaron si acaso era posible que una aplicación (sea PHP, ASP, .NET, etc) pudiera ser forzado a NO recordar lo escrito en una caja de texto. 

Lo primero en que pensé fue en la propiedad `autocomplete="off"`, pero tiene algunos problemas asociados y no funciona de la mejor manera que digamos. Por ende, tratando de juntar un par de ideas, pensé en lo siguiente. 

Click en "leer más" o "ver artículo completo" o como sea que esté traducido para ver la solución propuesta :)
<!-- more -->



## Primera solución



Dije que podríamos haberlo hecho con `autocomplete="off"`. Veamos cómo funciona: 

[xhtml]<input type="text" autocomplete="off" id="asdf" name="asdf" value="" />[/xhtml]

Con esa sola línea se supone que debería quedar desactivado el autocompletado, pero tiene un solo gran problema: no es compatible con la W3C. 
Sin embargo, no hay que olvidarse que la W3C no revisa JavaScript, por lo que poniendo en el evento onload del body lo siguiente, sí se retorna un documento válido que cumplirá el mismo propósito: 

[javascript]document.getElementById("asdf").setAttibute("autocomplete","off");[/javascript]

Peeeero... aquí ocurre el otro problema: no todos los navegadores aceptan este tipo de instrucción, por lo que por ejemplo Firefox va a seguir llenando el input. Mal que mal, es una característica que tiene que funcionar a toda prueba para el navegador cierto? 



## Segunda solución (Siempre funciona)



El problema se genera justamente en el cliente: firefox o cualquier otro navegador simplemente toma en cuenta el nombre del elemento y lo llena de acuerdo a lo que tiene guardado. De esta forma, se hace posible que por ejemplo, mi nombre de usuario aparezca en todos los foros vBulletin, ya que muchos de ellos tienen el mismo nombre en el input. 

Por lo tanto, una solución sería poder darle un nombre único a cada elemento que no queremos que se autocomplete. Sin embargo, hay que tener en cuenta que muchas veces un input de tipo texto que no haga nada es algo inútil: necesitamos hacer algo con ese elemento. El único problema es que... ¿cómo se llama dicho elemento si su nombre es totalmente al azar? 
Bueno, para eso lo que se puede hacer es lo siguiente: tenemos que ver de qué manera podemos generar un nombre único pero a la vez tenemos que ver cómo lo podemos rescatar referenciándolo por ese nombre. 

El truco en sí es bastante fácil: basta poner un elemento de tipo hidden y darle de valor el nombre del elemento y punto. Así de corta. 

[php]<?php
// Rescatando o revisando que el formulario enviado no sea vacío
  if (isset($_POST['qwerty']) AND !empty($_POST[$_POST['qwerty']])) {
// No es vacío, por lo que procedemos a imprimir o cualquier cosa
// que se haga dentro de un formulario enviado
    echo 'El valor es: <strong>'.$_POST[$_POST['qwerty']].'</strong><br />';
  }
?><form action="<?php echo $_SERVER['SCRIPT_NAME']; ?>" method="post">
<?php
// Primero establecemos un nombre único. "uniqid" retorna
// una id única (con 14 decimales de precisión gracias al
// segundo argumento), precedida por el tiempo en segundos
// Finalmente, a todo eso se le aplica un md5. 
  $nombre = 'asdf_'.md5(uniqid(time(),TRUE));
// Imprimo la caja que se verá públicamente con su nombre único
  echo '<input type="text" name="'.$nombre.'" />';
// Imprimo un elemento de tipo hidden con valor == $nombre
  echo '<input type="hidden" name="qwerty" value="'.$nombre.'" />';
// Siempre es bueno desocupar la mayor cantidad de memoria posible
  unset($nombre);
?>
<br /><input type="submit" value="Aceptar" />
</form>[/php]

Por si lo quieren probar, [aquí está la prueba de concepto](http://unreal4u.com/test/form-llenado/index.php). 

Saludos !!
