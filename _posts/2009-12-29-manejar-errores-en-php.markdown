---
author: admin
comments: true
date: 2009-12-29 18:00:35+00:00
layout: post
slug: manejar-errores-en-php
title: Manejar errores en PHP
wordpress_id: 207
categories:
- Mundo Web
- PHP
---

El otro día, mediante twitter, leí un título proveniente del DeveloperWorks de IBM que prometía mucho, pero que finalmente resultó en una farsa. El título decía algo así como: "Aprenda cómo manejar errores en PHP" pero al final no mostraba nada de código y además creo que promocionaban uno de estos software de IBM que vale como USD $999,99 si lo compraba en ese mismo instante. (LLAME YA!)
Sin embargo, el tema me pareció muy interesante, tanto, que me comprometí a algún día escribir al respecto. Y bueno... este es el resultado. Es un artículo largo, así que para variar se separará por páginas.

<!-- more -->

La primera regla de los errores es que es el usuario (final) **jamás** debe ver un error en pantalla. No es una buena idea que vea una pantalla en blanco que dice algo así como:


<blockquote>**Fatal error N° 1002**: Couldn't connect to MySQL, server responded: MySQL error 44: to busy

on line **3412** in file **/home/asdf/public_html/includes/mysql.class.php**


> 
> * * *
> 
> 
Apache v2.0.54 / PHP v5.2.12 on unreal4u.com</blockquote>


Principalmente por 3 razones:



	
  * Puede no estar en el mismo idioma

	
  * El mensaje de error generalmente es críptico, y asusta al usuario. El resultado es que el usuario cerrará la ventana y nunca más visitará el sitio pensando que fue él el que ocasionó el error. (Si el mensaje fuera el de arriba, parcialmente es así de todas formas xD)

	
  * Por último, el más importante. Hay mucha información: sale la ruta del archivo que produjo el error, sale la línea y por último, si se encuentra habilitado, el servidor muestra qué está corriendo.


Por lo tanto, si vamos a mostrar un error, mínimo que sea en la misma página donde se produjo, en el idioma del usuario, con un error en lenguaje más humano, algo así como esto:


![ ](http://unreal4u.com/~tesis/i/warning.png)Lo sentimos, pero en este momento el servidor tiene demasiado tráfico





## Bonita introducción... ¿pero cómo lo haces?


Primero que nada debo confesar que obviamente esto no es trabajo de un día a otro ni tampoco existe una regla en especial, ya que cada CMS que se construya es distinto uno del otro. Pero, siguiendo unas cuantas reglas generales, se puede lograr un bonito acabado de errores de manera bastante eficaz.

Lo primero que habrá que definir es dónde se mostrará el o los errores presentes. Si será siempre al principio de la página, entonces todos felices ya que la solución es más simple. Este caso se puede dar cuando, por ejemplo, debemos siempre mostrar un formulario, que el usuario ingrese datos y al hacer la verificación de los datos, tirar el o los posibles errores que pudieran surgir al principio de la página para luego seguir cargando el resto del formulario para que de esta manera corriga las posibles entradas incorrectas.

El estilo que se manejará para los errores es el siguiente:

[css]
p.error{
background:#AF4C4C;
color:#FFF;
text-align:center;
width:99%;
font-size:1.3em;
padding-bottom:4px;
line-height:30px
}
p.error img{
display:table-cell;
vertical-align:middle;
padding:0 10px 0 0
}
[/css]

que producirá una salida muy parecida a esta:


![ ](http://unreal4u.com/~tesis/i/warning.png)Lo sentimos, pero en este momento el servidor tiene demasiado tráfico


Por último, cabe destacar que la imagen no la dejé de fondo mediante background:url(), sin embargo; y aplicando algunas reglas más en el CSS; esta imagen se puede dejar como parte del CSS.

En las dos siguientes páginas, se analizará en mayor profundidad este esquema.

Ahora bien, el cómo se guardan los errores es bastante fácil: basta con crear un arreglo bastante simple que después se recorre. Este arreglo no será nada más que un simple

[php]
error (
    1 => 4,
    2 => 5,
    3 => 34
);
[/php]

donde 4, 5 y 34 serán los códigos de error. Este arreglo se genera cuando se detecta algún error, y no será nada más que:

[php]$err[] = 66;[/php]

Para que de esta manera, se sume a los demás errores que puedan existir previamente.




## Mostrando los errores de forma amigable


A continuación cada uno de los contenidos de los archivos, de forma muy simplificada:
El contenido de `/index.php`:

[php]<?php
  include('includes/sesiones.php');
  if (isset($_POST['(formulario)'])) {
    // Ejecutar todas las acciones de verificación y otros.
  }
  include('includes/header.php');
// Contenido de la página propiamente tal 
?>
<form action="<?php echo $_SERVER['SCRIPT_NAME']; ?>" method="post">
<input type="text" />
(...)
<input type="submit" value="Aceptar" />
</form>
<?php
  include('includes/footer.php');
?>
[/php]


Contenido de `/includes/header.php`:

[php]<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" lang="es">
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
  <base href="http://unreal4u.com/" />
  <title>Título de la página</title>
</head>
<?php
  include('includes/errores.php');
?>
[/php]

Ahora bien, la gracia de todo el sistema para mostrar errores está justamente en `/includes/errores.php`, que contiene el siguiente código:

[php]
<?php
if (isset($_GET['err'])) $err[] = $_GET['err']; // Reviso si existe error en GET y agrego al arreglo

if (isset($err)) { // Si existen errores...
  $pantalla = '<p class="error">'.c_img('i/warning.png').'&nbsp;'; // Empezamos
  foreach($err AS $a) { // Por cada uno de los errores en $err
    switch($a) { // Reviso de cuál se trata
      CASE 1  : $pantalla .= 'Lo sentimos, pero necesita logearse antes de poder continuar'; break;
      CASE 2  : $pantalla .= 'Debe llenar el formulario'; break;
      CASE 3  : $pantalla .= 'Es probable que se haya intentado inyección SQL'; break;
      CASE 4  : $pantalla .= 'Debe llenar todos los campos'; break;
      CASE 5  : $pantalla .= 'Lo sentimos, pero el usuario y contrase&ntilde;a no corresponden'; break;
      CASE 6  : $pantalla .= 'El tiempo de la sesi&oacute;n ha expirado'; break;
      CASE 7  : $pantalla .= 'Para acceder a esta &aacute;rea debe ser administrador'; break;

      CASE 403:
      CASE 404: $pantalla .= 'La p&aacute;gina solicitada no existe, verifique el link'; break;
      DEFAULT : $pantalla = ''; break;
    }
  }
  if ($pantalla != '') echo $pantalla.'</p>'; // Imprimo
  unset($a,$err,$pantalla); // Limpiamos
}
[/php]

Por último, la función que retorna el código HTML de la imagen (c_img) [ya la habíamos visto en un post anterior](http://blog.unreal4u.com/2009/12/una-funcion-que-siempre-ocupo/). 





## Algunas consideraciones



Hasta el momento hemos visto cómo mostrar correctamente un error de forma bonita, pero lo que no hemos hecho es saber cómo capturarlo. 
En PHP existen diversas formas para hacer esto, dependiendo siempre de la manera que se haga. Si, por ejemplo, quisiéramos abrir un archivo, tenemos dos posibilidades: la primera es simplemente abrirlo y preguntar si se trata de un puntero a archivo válido o la otra es primero saber si existe, y después abrirlo. 

[php]
// Forma NO recomendada. 
if (!$fp = @fopen('archivo.txt','r')) $err[] = 34; 
else {
  // ejecutar las demás acciones
}

// Forma recomendada
if(file_exists('archivo.txt')) {
  $fp = fopen('archivo.txt','r');
  // Otras acciones
}
else $err[] = 34;
[/php]

Sin embargo, este es un solo caso de apertura de archivo y nada más. Aunque ojo: ¡También es posible que este archivo tenga problemas de permisos, por lo cual `$fp` seguirá siendo un puntero inválido!

PHP en realidad tiene dos formas de mostrar errores: primero están los errores que generan un error en cuanto se compilan, y también están los errores que nosotros podemos cometer como programadores. 
Lo primero tiene una fácil solución que se llama _php.ini_. 

Este archivo contiene todas las directivas de configuración del ambiente completo. ¿Qué les puedo aconsejar? Cuando trabajen en un servidor de desarrollo, activen cualquier tipo de error que pudiera surgir, mediante las siguientes directivas: 

`expose_php = On
error_reporting = E_ALL
display_errors = On
display_startup_errors = On
ignore_repeated_errors = Off
ignore_repeated_source = Off
report_memleaks = On
track_errors = On
html_errors = On`

De esa forma, se puede evitar en gran medida cualquier tipo de error que pudiera ocurrir en forma de compilación. 

Luego, cuando traspasen el resultado al sitio final, simplemente se desactiva el mostrario de errores. Mediante esta técnica, y por experiencia propia, se puede eliminar hasta el 80% de los posibles errores que pudieran producirse. 
Sin embargo, aquí es donde empieza lo bueno: ¿Cómo capturamos los errores que no han podido ser capturados antes? Suponiendo que ya no existen errores de compilación, esta pregunta tiene una fácil respuesta que implica la función `trigger_error()`, `set_error_handler()` y una pequeña función hecha por nosotros.

La primera función que veremos es `trigger_error()`. 
[Esta función acepta](http://www.php.net/manual/en/function.trigger-error.php) dos parámetros y devuelve un booleano: 
`bool trigger_error  ( string $mensaje  [, int $tipo = E_USER_NOTICE  ] )`
Donde: 
`$mensaje` es el mensaje que le queremos mostrar al usuario. 
`$tipo` es el tipo de error que se elevará, que puede ser del tipo: E_USER_ERROR, E_USER_WARNING o (predeterminadamente) E_USER_NOTICE. 

Supongamos el siguiente código: 
[php]
<?php
trigger_error('este es un error!',E_USER_ERROR);
?>
[/php]

Ahora bien, si ejecutamos ese archivo, nos dará un error feo muy al estilo de PHP, lo cual es justamente todo lo contrario de lo que queremos, ya que muestra demasiada información que no queremos mostrar. 

Para solucionar esto, podemos hacer uso de `set_error_handler()`. 

Esta función, acepta dos parámetros, aunque siempre la he ocupado con uno solo (El segundo argumento limita el tipo de error a solo algunos, predeterminadamente, toma todos los errores). Ese único parámetro es el nombre de la función a ejecutarse cuando ocurre un error. ¿Para qué sirve esto? Básicamente, sirve para no pasar por el generador de errores de PHP, sino que tener uno hecho por nosotros. De esta manera, podemos hacer lo que queramos con el error sin que salgan errores feos de parte de PHP. 

Supongamos el siguiente código:
[php]// Esto será lo primero que debemos invocar en nuestros archivos
function dramas($errno, $errstr, $errfile, $errline, $errctx) {
    mail('su-email@ejemplo.com',
         'Error en la página',
         'Ocurrió un error N° '.$errno.' que dice: '.$errstr.' en el archivo '.$errfile.' en la línea '.$errline
    );
    die('Ocurrió un error!');
    return true;
}
set_error_handler("dramas");

// Finalmente, en nuestra aplicación, generamos un error (archivo no encontrado)
include('archivo-que-no-existe.php');
[/php]

Con el código de arriba, ocurren hartas cosas interesantes: 


  1. Primero definimos que nuestro error_handler sea la función llamada `dramas()`.


  2. Esta función, lo primero que hará al recibir un error, será mandarnos un mail con detalles acerca del error.


  3. Enseguida, para la ejecución del script con un mensaje trivial.


  4. Luego, retorna true para que de esta manera, se ignore el handler de errores de PHP.



Los resultados? En la página: 
![2009-12-29-u4u-001](http://blog.unreal4u.com/wp-content/gallery/errores/2009-12-29-u4u-001.png)

Y en nuestro mail: 
![2009-12-29-u4u-000](http://blog.unreal4u.com/wp-content/gallery/errores/2009-12-29-u4u-000.png)

Por último, se pueden hacer muchísimas cosas más, ya que en vez de enviar un mail, se podría guardar a un archivo XML o bien guardar los errores en la base de datos, pero la idea general se entiende. También se puede logear la hora, la IP del visitante y un montón de otras cosas más haciendo buen uso de estas dos funciones. 

Ahora bien, combinando nuestro pequeño código con la función `trigger_error()` y también aplicando todo lo aprendido hasta el momento podemos armar errores mucho más completos y fáciles de entender. Un pequeño ejemplo: 

[php]function error_handler($errno, $errstr, $errfile, $errline, $errctx) {
    switch($errno) {
      CASE 256: // alias de E_USER_ERROR
                die('Error fatal producido por el usuario!');
                break;
      CASE 512: // alias de E_USER_WARNING
                $err[] = 33;
                break;
      CASE 1024: // alias de E_USER_NOTICE
                $err[] = 77;
                break;
      DEFAULT: // cualquier otro tipo de error
              mail('su-email@ejemplo.com',
                   'Error en la página',
                   'Ocurrió un error N° '.$errno.'
              );
              die('Ocurrió un error!');
              break;
    }
    return true;
}
set_error_handler("error_handler");

// Supongamos que el usuario haya ingresado como año 2004:
$usuario_ingresa = '2004';

// Y el acceso a la página es sólo para mayores de 18 años:
$edad = date('Y') - $usuario_ingresa;
if ($edad < 18) trigger_error('asdf',E_USER_NOTICE);

// Por último, ya al mostrar la página, incluimos los errores: 
include('includes/errores.php');
[/php]

Con todo lo que se ha aprendido, ya podremos implementar una buena forma de capturar errores sin que les salga mensajes feos al usuario. 





## Conclusiones



Construir un buen sistema de errores es una tarea simple, sin embargo, require un tiempo adicional considerable si queremos considerar todas las posibles consideraciones. 
Fuera del juego de palabras previo, es importante verificar todas las entradas, sea por el método que sea: con extensiones como LiveHTTPHeaders para Firefox, es bastante simple alterar el contenido de un formulario con $_POST ya que esta extensión en particular, aparte de mostrar cabeceras, también permite enviarlas. 
Especial cuidado también se debe tener con $_GET, puesto que permite una entrada de datos más que directa, lo cual puede ser no muy conveniente en algunos casos. En general, apliquen la siguiente regla: ¿Quieren que la página se pueda guardar directamente como un marcador o favorito? En ese caso, usen $_GET para llegar a ella. Sin embargo, si no se quiere que la página sea guardable como marcador o favorito, ocupen $_POST para llegar a ella. 

Pero continuando un poco con el tema original, existe casi una infinidad de errores que pueden producirse y todas ellas tienen distintas maneras de evitarse. Lo único que puedo recomendarles es siempre tratar de verificar absolutamente todo: si se espera un número, **verifiquen** que efectivamente lo sea **antes** de ejecutar cualquier acción. ([Existen un montón de funciones "is_" para verificar las entradas](http://www.php.net/manual-lookup.php?pattern=is_&lang=en) y por supuesto, también está el poderosísimo [filter_var()](http://www.php.net/manual/en/function.filter-var.php)). Cuando hagan alguna consulta, verifiquen primero que se haya conectado bien a la base de datos y después verifiquen si el número de registros retornados sea mayor a cero y retornen un código de error que pueda ser ejecutado centralizadamente para mayor facilidad y consistencia de la aplicación como un todo.

Por último, una consideración que encuentro muy importante que puede tomarse hasta como una reflexión personal: una vez, mientras me encontraba trabajando en una empresa, a uno de los programadores se le ocurrió borrar la base de datos completa. A los dos segundos de haber hecho eso, empezaron las llamadas de parte de todas las sucursales informando acerca de todo tipo de errores extraños. En el momento mismo no sabíamos qué había pasado, pero bastó con una pequeña reunión de un par de segundos (que fue algo así como "Sorry cabros, pasé a borrar toda la base de datos, mientras la restauro, avísenle a los vendedores") para darnos cuenta de qué había pasado. 
Se me ocurrió la idea de decir: "ya, pero en vez de echarnos la culpa, ¿por qué simplemente no decimos que fue una falla del sistema?". Fue ahí donde me dijeron algo a lo cual le encontré toda la razón: **nunca** se puede decir algo como eso, porque el usuario pierde confianza en el sistema, lo cual es vital en el correcto funcionamiento de una empresa. Es decir: siempre es preferible echarse la culpa uno mismo, porque mal que mal es bastante difícil que el lenguaje en que está hecho el sistema efectivamente tenga un bug y, admitámoslo, si ocurre alguna falla, lo más probable es que haya sido debido a que nosotros no lo hayamos tomado en cuenta. 

Frente a la pregunta acerca de quién había sido el culpable, simplemente hicimos caso omiso: aunque la culpa efectivamente haya sido de alguien en específico, se debe mantener cierto nivel de privacidad para el mismo, y es mejor echarse la culpa como equipo. 
