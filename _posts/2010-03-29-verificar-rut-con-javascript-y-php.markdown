---
author: admin
comments: true
date: 2010-03-29 04:08:59+00:00
layout: post
slug: verificar-rut-con-javascript-y-php
title: Verificar RUT con Javascript y PHP
wordpress_id: 359
categories:
- i18n/L10n
- Javascript/jQuery
- Mundo Web
---

### Atención! Este es una versión antigua! Existe una [versión más nueva disponible aquí](http://blog.unreal4u.com/2010/09/revision-de-rut-con-javascript-y-php-revisited/)



Sí, estamos totalmente de acuerdo: hay por lo menos unos 50 validadores distintos si buscamos en Internet. Sin embargo, este validador es uno distinto: lo hice yo :P 

Fuera de bromas, es un script bastante cortito (menor transferencia == página más rápida para el usuario) que realiza la verificación simple del RUT, es decir, un RUT tipo 11.111.111-1 seguirá siendo válido para este algoritmo. Lamentablemente el tiempo para hacer el completo no me sobra y creo que este blog es fiel reflejo de ello: hace muchísimo tiempo que no lo actualizo con alguna noticia.

También anda bastante lento el algoritmo en general, aunque eso puede ser problema de mi propio PC también. Sin embargo, se puede optimizar bastante más. La base fue tomada (la idea no más en realidad) del sitio del BancoEstado, [específicamente este iframe](https://www.bancoestado.cl/imagenes/comun2008/lg_prs.html). La versión de BancoEstado pesa 8309 bytes mientras que la versión realizada por mi pesa 1232 bytes (Un nada miserable 85% menos!). Manejando los mensajes de otra forma creo que se puede llegar al límite de un solo paquete de 1024 bytes.
A continuación, el algoritmo del RUT chileno en Javascript y en PHP.
<!-- more -->


## Javascript


[javascript]
function ff(a){b=a;setTimeout("b.focus();",0);setTimeout("b.select();",0);return true;};
/* BORRAR COMENTARIO:: Lo de arriba es un arreglo para Firefox */
function revisa_RUT(c,e){
var t="";var b=0;var w=c.value;for(i=0;i<w.length;i++){if(w.charAt(i)!=' '&&w.charAt(i)!='.'&&w.charAt(i)!='-'){t=t+w.charAt(i)};};if(t.length==8){t=0+t;};
if(t.length!=9){return 'Lo sentimos, pero el RUT no corresponde. Por favor intente nuevamente.';};
if(e=='e'){b=1;};a=t.substring(t.length-1,-1);d=t.charAt(t.length-1);if(d=='k'){d='K';};
if(isNaN(a)){return 'Lo sentimos, pero el RUT contiene caracteres invalidos. Por favor intente nuevamente.';};
if(b==1&&a>50000000){return 'Lo sentimos, pero el RUT ingresado no corresponde a un RUT de persona natural.';};
//if(b==0&&a<50000000){return 'Lo sentimos, pero el RUT ingresado no corresponde a un RUT de empresa.';};
if(!revisa_DV(a,d)){return 'Lo sentimos, pero el RUT es incorrecto. Por favor intente nuevamente.';};
c.value=a.substring(0,2)+'.'+t.substring(2,5)+'.'+t.substring(5,8)+'-'+d;
return true;};
function revisa_DV(a,b){if(a==null||b==null){return false;};
var s=0;var m=2;var d='0';var e=0;
for(i=a.length-1;i>=0;i--){s=s+a.charAt(i)*m;if(m==7){m=2;}else{m++;};};
var r=s%11;if(r==1){d='K';}else{if(r==0){d='0'}else{e=11-r;d=e+'';};};
if(b!=d){return false;};return true;};
/*
Llamar:
onblur="javascript:g=revisa_RUT(this,'n');if(g!=true){alert(g);ff(this);};"

donde:
this == el elemento input del RUT
'e' == tipo de persona: natural o jurídica. "n" para natural, "e" para jurídica.

TODO:::
llamar a una función separada que dé el formato indicado.
Optimizar.
*/
[/javascript]



## PHP


[php]function verifica_RUT($rut='') {
  $tmpRUT = '';
  $sep = array();
  $multi = 2;
  $suma = 0;
  if (empty($rut)) return 1;
  for ($i = 0; $i < strlen($rut); $i++) {
    if ($rut[$i] != ' ' AND $rut[$i] != ' ' AND $rut[$i] != '.' AND $rut[$i] != '-') $tmpRUT .= $rut[$i];
  }
  if ( strlen($tmpRUT) == 8 ) $tmpRUT = '0'.$tmpRUT;
  if (strlen($tmpRUT) != 9) return 2;
  $sep['rut'] = substr($tmpRUT,0,8);
  $sep['dv']  = substr($tmpRUT, -1);
  if ($sep['dv'] == 'k') $sep['dv'] = 'K';
  if (!is_numeric($sep['rut'])) return 3;
  if (empty($sep['rut']) OR $sep['dv'] == '') return 4;
  for ($i=strlen($sep['rut']) - 1; $i >= 0; $i--) {
    $suma = $suma + $sep['rut'][$i] * $multi;
    if ($multi == 7) $multi = 2;
    else $multi++;
  }
  $resto = $suma % 11;
  if ($resto == 1) {
    $sep['dvt'] = 'K';
  }
  else {
    if ($resto == 0) {
      $sep['dvt'] = '0';
    }
    else {
      $sep['dvt'] = 11 - $resto;
    }
  }
  if ($sep['dvt'] != $sep['dv']) return 5;
  return 0;
}
// --------------------------------------------------------
// Hasta aquí llega, lo de abajo fue una prueba de concepto
// no más, que pueden copiar tal cual.

$rut = '';
if (isset($_POST['rut'])) {
  $error = verifica_RUT($_POST['rut']);
  switch($error) {
    case 0 : echo 'Todo bien!!'; break;
    case 1 : echo 'RUT viene vacío'; break;
    case 2 : echo 'El RUT no viene con el mínimo de caracteres necesarios para validarlo'; break;
    case 3 : echo 'El RUT no viene en un formato numérico'; break;
    case 4 : echo 'El RUT o el dígito viene vacío.'; break;
    case 5 : echo 'El RUT y el dígito verificador no coinciden'; break;
    default: echo 'Error de la décimanovena dimensión!!! Corran en círculos!!!'; break;
  }
  $rut = $_POST['rut'];
}
?><html><head><title>Probando...</title></head><body onload="document.getElementById('rut').focus();">
<form  action="<?php echo $_SERVER['SCRIPT_NAME']; ?>" method="post">
<input type="text" id="rut" name="rut" value="<?php echo $rut; ?>" /><input type="submit" value="aceptar" /></form></body></html>
[/php]



## Conclusiones


Si sé, es un trabajo cochino que es muy muy muy probable que se pueda pulir mucho más que como está, pero este fue una volada que hice a la rapidita (especialmente la de PHP). A ver si algún día me animo a mejorarlo y optimizarlo. También le faltan muchas cosas todavía, pero por el momento es lo que hay.
