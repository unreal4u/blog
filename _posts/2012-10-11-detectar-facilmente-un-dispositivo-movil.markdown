---
comments: true
date: 2012-10-11 21:46:00+00:00
layout: post
slug: detectar-facilmente-un-dispositivo-movil
title: Detectar fácilmente un dispositivo móvil
wordpress_id: 702
categories:
- i18n/L10n
- Mundo Web
- PHP
---

Hace ya algunos meses atrás, [WURFL cambió las políticas para ocupar su sistema](http://en.wikipedia.org/wiki/WURFL), y aunque normalmente soy más asiduo a la fantástica solución y sobretodo variada información que ofrecía la misma (con una altísima tasa de fiabilidad), [con los precios actuales](http://www.scientiamobile.com/pricing) todo el interés se me vino abajo. Llegó la hora de empezar a buscar nuevas soluciones a precios más aterrizados, ojalá open-source, lo cual es justamente terreno mío: encontrar soluciones óptimas a menor costo posible.
Es así como, en resumidas cuentas, ideé un plan donde si bien es cierto había que pagar, se puede hacer pagando el más barato de [WURFL Cloud](http://www.scientiamobile.com/cloud), pero manteniendo e incluso incrementando la velocidad de detección de un dispositivo móvil, sin sacrificar (mucho) la fiabilidad que se consigue con WURFL. 
Es así como surgió este post, que servirá dos propósitos: si sólo quieren una detección rápida les espera la solución completa, pero si también quieren saber con precisión cuál es el dispositivo móvil que el usuario está ocupando en ese momento, sigan leyendo que aquí sabrán cómo hacerlo de la forma más barata posible.
<!-- more -->



## Presentando los contrincantes


Como en toda arena o batalla campan, los contrincantes son dos: 

[php-mobile-detect](http://code.google.com/p/php-mobile-detect/): Es una class dedicada a hacer una rápida detección y presenta además algunos métodos para determinar si el dispositivo detectado es un iPhone u otro dispositivo popular.

[detectMobileBrowsers.com](http://detectmobilebrowsers.com/): Es una regla donde mediante 2 expresiones regulares se determina si el dispositivo presentado es móvil o no. 

De ambos, podemos predecir que php-mobile-detect debería ser el que mejor detecta dispositivos móviles ya que además del user-agent (el string que todo navegador manda para identificarse) también realiza algunos checks sobre las cabeceras. 
Por el otro lado, también podemos predecir que detectMobileBrowsers será el más rápido, ya que sólo aplica algunas reglas y listo.

Sin embargo, como veremos no todo es como se predice :)



## Midiendo fiabilidad


Tal como decía en la introducción, WURFL es hasta este momento la base de datos más completa que existe para determinar si un dispositivo es móvil o no, y esto se debe en gran parte a su completísima base de datos en XML que pesa la no friolera cantidad de 17,6MB. 
En este XML se presentan cerca de 15.000 distintos user-agents (para ser exactos y sin contar con algunos no válidos 14.911) junto con la descripción completa de cada una de ellas. Lo único malo es que para testeo sería bien ineficiente tener que recurrir al XML así que creé un pequeño script que pasa todos los user-agents a la base de datos, para así poder hacer testeos de forma más eficiente.

Una vez traspasados los user agents, (el código del traspaso me pareció irrelevante, si de todas formas lo quieren, dejen un comentario), era hora de evaluar cuál de las dos herramientas era el mejor en cuanto a rendimiento y precisión. 



## Los resultados


Primero rendimiento. Mientras WURFL se demoraba cerca de 88 segundos en procesar 15.000 user-agents, php-mobile-detect se demoró algo más de 7 segundos. Sin embargo, la corona se la lleva el script de detectMobileBrowsers con un tiempo de 0.6 segundos. Absoluto ganador.

En cuanto a precisión, asumiendo que WURFL entrega resultados 100% correctos, php-mobile-detect detectó cerca del 70% de los 15.000 correctamente.
Sin embargo, la gran sorpresa llegó en este punto, cuando detectMobileBrowsers detectó bien el 93,5% de los user-agents. Después de algunos tweaks menores, pude elevar esta detección a un 94,7% de los casos.

Así que proclamo aquí como ganador absoluto, tanto en rendimiento como en precisión, a detectMobileBrowsers. El código adaptado es el siguiente: 

{% highlight php %}
<?php

function isMobileUA($useragent) {
	$return = false;
	if (preg_match('/android|andro id|i\-mobile|nintendo|ericsson|ipad|itouch|MSIE (5|6)\.0; (KDDI|nitro|epoc).+Opera (6|7|8)\.|tablet browser/i', $useragent) || 
		preg_match('/android.+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|meego.+mobile|midp|mmp|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows (ce|phone)|xda|xiino/i',$useragent)||preg_match('/1207|6310|6590|3gso|4thp|50[1-6]i|770s|802s|a wa|abac|ac(er|oo|s\-)|ai(ko|rn)|al(av|ca|co)|amoi|an(ex|ny|yw)|aptu|ar(ch|go)|as(te|us)|attw|au(di|\-m|r |s )|avan|be(ck|ll|nq)|bi(lb|rd)|bl(ac|az)|br(e|v)w|bumb|bw\-(n|u)|c55\/|capi|ccwa|cdm\-|cell|chtm|cldc|cmd\-|co(mp|nd)|craw|da(it|ll|ng)|dbte|dc\-s|devi|dica|dmob|do(c|p)o|ds(12|\-d)|el(49|ai)|em(l2|ul)|er(ic|k0)|esl8|ez([4-7]0|os|wa|ze)|fetc|fly(\-|_)|g1 u|g560|gene|gf\-5|g\-mo|go(\.w|od)|gr(ad|un)|haie|hcit|hd\-(m|p|t)|hei\-|hi(pt|ta)|hp( i|ip)|hs\-c|ht(c(\-| |_|a|g|p|s|t)|tp)|hu(aw|tc)|i\-(20|go|ma)|i230|iac( |\-|\/)|ibro|idea|ig01|ikom|im1k|inno|ipaq|iris|ja(t|v)a|jbro|jemu|jigs|kddi|keji|kgt( |\/)|klon|kpt |kwc\-|kyo(c|k)|le(no|xi)|lg( g|\/(k|l|u)|50|54|\-[a-w])|libw|lynx|m1\-w|m3ga|m50\/|ma(te|ui|xo)|mc(01|21|ca)|m\-cr|me(di|rc|ri)|mi(o8|oa|ts)|mmef|mo(01|02|bi|de|do|t(\-| |o|v)|zz)|mt(50|p1|v )|mwbp|mywa|n10[0-2]|n20[2-3]|n30(0|2)|n50(0|2|5)|n7(0(0|1)|10)|ne((c|m)\-|on|tf|wf|wg|wt)|nok(6|i)|nzph|o2im|op(ti|wv)|oran|owg1|p800|pan(a|d|t)|pdxg|pg(13|\-([1-8]|c))|phil|pire|pl(ay|uc)|pn\-2|po(ck|rt|se)|prox|psio|pt\-g|qa\-a|qc(07|12|21|32|60|\-[2-7]|i\-)|qtek|r380|r600|raks|rim9|ro(ve|zo)|s55\/|sa(ge|ma|mm|ms|ny|va)|sc(01|h\-|oo|p\-)|sdk\/|se(c(\-|0|1)|47|mc|nd|ri)|sgh\-|shar|sie(\-|m)|sk\-0|sl(45|id)|sm(al|ar|b3|it|t5)|so(ft|ny)|sp(01|h\-|v\-|v )|sy(01|mb)|t2(18|50)|t6(00|10|18)|ta(gt|lk)|tcl\-|tdg\-|tel(i|m)|tim\-|t\-mo|to(pl|sh)|ts(70|m\-|m3|m5)|tx\-9|up(\.b|g1|si)|utst|v400|v750|veri|vi(rg|te)|vk(40|5[0-3]|\-v)|vm40|voda|vulc|vx(52|53|60|61|70|80|81|83|85|98)|w3c(\-| )|webc|whit|wi(g |nc|nw)|wmlb|wonu|x700|yas\-|your|zeto|zte\-/i',substr($useragent,0,4))
	) {
		$return = true;
	}
	
	return $return;
}
{% endhighlight %}

La tercera línea es lo que yo agregué para aumentar la precisión, hasta el momento no me ha dado problemas, pero de todas formas sugiero que testeen antes de ponerlo en producción para evitar falsos positivos.



## Detección de dispositivos


Esto nos deja sin embargo con la gran duda de qué hacemos para detectar dispositivos de la misma forma en que lo (hacía) WURFL. Sin embargo, la cruda verdad es que no hay ningún clon o reemplazo en PHP que haga lo mismo con la misma fiabilidad y rapidez que WURFL.

De esta forma, lo único que queda por hacer si desean detección de dispositivos de forma barata es suscribirse por una módica suma mensual a WURFL Cloud. Si es un sitio sin fines de lucro con pocas visitas, puede que hasta puedan alcanzar para el plan más básico, pero no se los recomiendo ya que sólo podrán rescatar dos características, lo cual es bastante poco. 
Lo bueno de todo el asunto es que con unas pocas líneas de código podrán tener detección de dispositivos de forma fiable, después de suscribirse a su sitio, basta hacer lo siguiente: 

{% highlight php %}
<?php

class wurflCommunicator {
	public function doWurflCloudRequest() {
		$capabilities = array();
		
		try {
			$wurflConfig = new WurflCloud_Client_Config();
			// Constante definida en otro lado, se las dan cuando se suscriben
			$wurflConfig->api_key = WURFL_APIKEY;
			
			$wurflClient = new WurflCloud_Client_Client($wurflConfig);
			$wurflClient->detectDevice();
			
			$capabilities = $wurflClient->capabilities;
		} catch (Exception $e) {
			// Logear el problema si ocurre alguno, en la empresa lo hacemos así:
			//$sistProblemIdentifier = new sistProblemIdentifier();
			//$sistProblemIdentifier->addProblem('wurflCloudRequestProblem: '.get_class($e), $e->getMessage());
		}
		
		if (!empty($capabilities)) {
			foreach($capabilities AS $key => $capability) {
				$this->{'wurfl_'.$key} = $capability;
			}
		}
	}
}

// Acceder a las variables como sigue:

$wurflDetection = new wurflCommunicator();
$wurflDetection->doWurflCloudRequest();

if ($wurflDetection->wurfl_is_wireless_device) {
	echo 'You are using a '.$wurflDetection->wurfl_brand_name.' '.$wurflDetection->wurfl_model_name;
}
{% endhighlight %}

Nótese que en el pequeño echo de arriba ya estoy usando 3 capacidades: `is_wireless_device` (para detectar si se trata de un dispositivo móvil, esto incluye tablets), `brand_name` que tiene la marca del dispositivo y `model_name` que contiene el nombre del modelo. Para un listado completo de capacidades, sírvanse visitar [esta página que las lista todas](http://www.scientiamobile.com/wurflCapability/tree) y además explica para qué sirve cada uno. 
El programa gratuito sólo les dejará elegir 2 capacidades.



## Conclusiones


Detectar si un dispositivo es móvil o no se puede hacer de forma relativamente fácil, y lo que es más importante: de forma certera. 
Sin embargo, lo realmente interesante es que pueden mezclar ambos conceptos: con una tasa de acertividad de casi un 95%, pueden usar el primer script para determinar si un dispositivo es móvil o no de forma rápida, y pueden realizar el request a WURFL sólo en aquellos casos donde el dispositivo ya es móvil: de esta forma, se aseguran de mantener un bajo ratio de requests a WURFL y además pueden rescatar a mi juicio los dos campos más importantes que son la marca y el modelo del dispositivo y así mantenerse en el plan gratuito. 

Sin embargo, y debido a que estaría publicando código propietario de la empresa donde trabajo, no puedo publicar esa class y tendrán que hacerla ustedes mismos. De todas formas, difícil no es, anímense a escribir su propia!
