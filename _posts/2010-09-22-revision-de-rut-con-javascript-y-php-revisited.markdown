---
author: admin
comments: true
date: 2010-09-22 03:24:42+00:00
layout: post
slug: revision-de-rut-con-javascript-y-php-revisited
title: Revisión de RUT con Javascript y PHP (Revisited)
wordpress_id: 445
categories:
- i18n/L10n
- Javascript/jQuery
- Mundo Web
- PHP
---

Sí, es cierto, [ya existe un post al respecto](http://blog.unreal4u.com/2010/03/verificar-rut-con-javascript-y-php/).  Pero estoy viendo en Google Analytics que todo lo relacionado con el  RUT es uno de los términos más buscados para la página, así que  aprovecho de pegarle una revisión a ese post que ya tiene un poco más de  6 meses, así que bienvenido a la revisión del RUT como debería haber  sido.

Como ese título siempre me ha llamado bastante la atención, es que le  daré un enfoque bastante especial a que así sea. Este artículo estará  separado en 3 partes: la primera, una explicación teórica sobre qué es  el RUT y cuál es su función. La segunda, el algoritmo en JavaScript y el  tercero, el algoritmo en PHP, obviamente estas últimas dos han sido  revisados, actualizados y mejorados con respecto a su primera versión,  desde la llamada inicial que se hace a la función, pasando por  expresiones regulares y la detección automática del tipo de  identificador: RUT o RUN.
<!-- more -->


## Barniz teórico


En Chile, comúnmente se le denomina RUT tanto al RUT (Rol Único  Tributario) como al RUN (Rol  Único Nacional). La diferencia está en que  el primero es un identificador de empresas mientras que el segundo sólo  es para personas naturales. En este artículo, me referiré  indistintamente a RUT o RUN.

Bueno, este número que consiste en un número de 1.000.000 a  99.999.999 es un identificador único a cada persona o empresa en Chile.  Sé que también existe RUT en Argentina pero como no lo conozco (aunque  tengo entendido que el algoritmo es bastante parecido) tocaré sólo el  RUT chileno.
Además de esto, también se le une un dígito verificador que va de 0 a 9, más la letra K (representando 11), que no es nada más que una operación  matemática basada en el número en sí para filtrar un poco que el número  sea correcto.

Por lo tanto, este número, con la debida importancia que tiene, ha  sido convertido casi en un estándar para la informática: un número único  por persona, con límites conocidos, un checksum conocido y que se puede  guardar fácilmente en una base de datos es lo único que una persona  necesita para implementar el famoso RUT o RUN en su aplicación.

Sin embargo, siendo un número de millones, tenemos que existe una  secuencia de 7 dígitos como mínimo y 12 como máximo que tiene que  ingresar el usuario: tierra fértil para que el usuario se equivoque sin  darse cuenta, más aún tratándose de una combinación de dígitos y signos  de puntuación, y si queremos una base de datos que sea consistente...  pues no podemos darnos el lujo de dejar que el usuario ingrese cualquier  cosa.

Para esto, nació obviamente el último dígito de la secuencia,  separada por una raya para distinguirlo, que lo que hace es revisar que  el RUT ingresado corresponde a un número válido, el cual; mediante un  algoritmo; se puede verificar (Y de ahí el nombre "Dígito Verificador").


## En la práctica: JavaScript


Sin mayores secretos, la que más modificaciones sufrió fue la versión  de Javascript, ya que se quitaron muchas partes que estaban redundantes  y se resolvió que sólo iba a devolver un verdadero o falso. Si desean  en cambio que retorne un número de error, pues con el código de PHP  podrán modificar el de Javascript.
[javascript]function verifica_rut(c){
  var r=false,d=c.value,t=d.replace(/\b[^0-9kK]+\b/g,'');
  if(t.length==8){t=0+t;};
  if(t.length==9){
    var a=t.substring(t.length-1,-1),b=t.charAt(t.length-1);
    if(b=='k'){b='K'};
    if(!isNaN(a)){
      var s=0,m=2,x='0',e=0;
      for(var i=a.length-1;i&amp;gt;=0;i--){
        s=s+a.charAt(i)*m;
        if(m==7){m=2;}
        else{m++;};
      }
      var y=s%11;
      if(y==1){x='K';}
      else{
        if(y==0){x='0';}
        else{e=11-y;x=e+'';};
      };
      if(x==b){r=true;c.value=a.substring(0,2)+'.'+a.substring(2,5)+'.'+a.substring(5,8)+'-'+b};
    }
  }
  return r;
}
[/javascript]
y minificado:
`function verifica_rut(c){var  r=false,d=c.value,t=d.replace(/\b[^0-9kK]+\b/g,'');if(t.length==8){t=0+t;};if(t.length==9){var   a=t.substring(t.length-1,-1),b=t.charAt(t.length-1);if(b=='k'){b='K'};if(!isNaN(a)){var  s=0,m=2,x='0',e=0;for(var  i=a.length-1;i>=0;i--){s=s+a.charAt(i)*m;if(m==7){m=2;}else{m++;};}var   y=s%11;if(y==1){x='K';}else{if(y==0){x='0';}else{e=11-y;x=e+'';};};if(x==b){r=true;c.value=a.substring(0,2)+'.'+a.substring(2,5)+'.'+a.substring(5,8)+'-'+b};}}return  r;};`


## Código en PHP


Aunque sufrió menos cambios que su simil en Javascript, tuvo una modificación bastante grande que fue el empleo de expresiones regulares  para eliminar cualquier caracter que no deba ir en un RUT o RUN: de esa  forma, se elimina cualquier caracter que no sea del 0 al 9, o la letra k  (mayúscula o minúscula).
[php]function verifica_RUT($rut='') {
  $sep = array();
  $multi = 2;
  $suma = 0;
  if (empty($rut)) return 1;
  $tmpRUT = preg_replace('/[^0-9kK]/','',$rut);
  if (strlen($tmpRUT) == 8 ) $tmpRUT = '0'.$tmpRUT;
  if (strlen($tmpRUT) != 9) return 2;
  $sep['rut'] = substr($tmpRUT,0,8);
  $sep['dv']  = substr($tmpRUT, -1);
  if ($sep['dv'] == 'k') $sep['dv'] = 'K';
  if (!is_numeric($sep['rut'])) return 3;
  if (empty($sep['rut']) OR $sep['dv'] == '') return 4;
  for ($i=strlen($sep['rut']) - 1; $i &amp;gt;= 0; $i--) {
    $suma = $suma + $sep['rut'][$i] * $multi;
    if ($multi == 7) $multi = 2;
    else $multi++;
  }
  $resto = $suma % 11;
  if ($resto == 1) $sep['dvt'] = 'K';
  else {
    if ($resto == 0) $sep['dvt'] = '0';
    else $sep['dvt'] = 11 - $resto;
  }
  if ($sep['dvt'] != $sep['dv']) return 5;
  return 0;
}
[/php]


## Uniendo todos los cabos sueltos


Por supuesto que el ejemplo completo que incluye revisión (y formateo  de RUT) en Javascript como en PHP es algo que no podía faltar:
[php]&lt;?php
function verifica_RUT($rut='') {
  $sep = array();  $multi = 2;  $suma = 0;
  if (empty($rut)) return 1;
  $tmpRUT = preg_replace('/[^0-9kK]/','',$rut);
  if (strlen($tmpRUT) == 8 ) $tmpRUT = '0'.$tmpRUT;
  if (strlen($tmpRUT) != 9) return 2;
  $sep['rut'] = substr($tmpRUT,0,8);
  $sep['dv']  = substr($tmpRUT, -1);
  if ($sep['dv'] == 'k') $sep['dv'] = 'K';
  if (!is_numeric($sep['rut'])) return 3;
  if (empty($sep['rut']) OR $sep['dv'] == '') return 4;
  for ($i=strlen($sep['rut']) - 1; $i &gt;= 0; $i--) {
    $suma = $suma + $sep['rut'][$i] * $multi;
    if ($multi == 7) $multi = 2;
    else $multi++;
  }
  $resto = $suma % 11;
  if ($resto == 1) $sep['dvt'] = 'K';
  else {
    if ($resto == 0) $sep['dvt'] = '0';
    else $sep['dvt'] = 11 - $resto;
  }
  if ($sep['dvt'] != $sep['dv']) return 5;
  return 0;
}
?&gt;&lt;html&gt;&lt;head&gt;&lt;title&gt;Verificar RUT&lt;/title&gt;
&lt;script type=&quot;text/javascript&quot; src=&quot;http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.min.js&quot;&gt;&lt;/script&gt;
&lt;script type=&quot;text/javascript&quot;&gt;
function verifica_rut(c){var r=false,d=c.value,t=d.replace(/\b[^0-9kK]+\b/g,'');if(t.length==8){t=0+t;};if(t.length==9){var a=t.substring(t.length-1,-1),b=t.charAt(t.length-1);if(b=='k'){b='K'};if(!isNaN(a)){var s=0,m=2,x='0',e=0;for(var i=a.length-1;i&gt;=0;i--){s=s+a.charAt(i)*m;if(m==7){m=2;}else{m++;};}var y=s%11;if(y==1){x='K';}else{if(y==0){x='0';}else{e=11-y;x=e+'';};};if(x==b){r=true;c.value=a.substring(0,2)+'.'+a.substring(2,5)+'.'+a.substring(5,8)+'-'+b};}}return r;};
&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;

&lt;?php
if (isset($_GET['rut'])) {
  $error = verifica_RUT($_GET['rut']);
  switch($error) {
    case 0 : echo 'Todo bien!!'; break;
    case 1 : echo 'RUT viene vacío'; break;
    case 2 : echo 'El RUT no viene con el mínimo de caracteres necesarios para validarlo'; break;
    case 3 : echo 'El RUT no viene en un formato numérico'; break;
    case 4 : echo 'El RUT o el dígito viene vacío.'; break;
    case 5 : echo 'El RUT y el dígito verificador no coinciden'; break;
    default: echo 'Error de la décimanovena dimensión!!! Corran en círculos!!!'; break;
  }
}
?&gt;
&lt;form action=&quot;&lt;?php echo $_SERVER['SCRIPT_NAME']; ?&gt;&quot; method=&quot;get&quot; id=&quot;formu&quot;&gt;
&lt;input type=&quot;text&quot; maxlength=&quot;12&quot; id=&quot;rut&quot; name=&quot;rut&quot; value=&quot;&quot; /&gt;
&lt;input type=&quot;button&quot; value=&quot;Validar RUT&quot; id=&quot;verificar_rut&quot; /&gt;
&lt;/form&gt;

&lt;script&gt;
$(&quot;#verificar_rut&quot;).click(function(){
  if(verifica_rut(document.getElementById(&quot;rut&quot;))==true){
    $(&quot;#formu&quot;).submit();
  }
  else{
    alert(&quot;El RUT es invalido, intente nuevamente&quot;);
    $(&quot;#rut&quot;).select().focus();
  };
});
&lt;/script&gt;

&lt;/body&gt;&lt;/html&gt;
[/php]
Y eso sería todo amigos :)
