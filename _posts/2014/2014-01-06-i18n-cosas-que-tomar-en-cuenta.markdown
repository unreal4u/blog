---
comments: true
date: 2014-01-06 11:00:46+00:00
layout: post
slug: i18n-cosas-que-tomar-en-cuenta
title: 'i18n: Cosas que tomar en cuenta'
wordpress_id: 847
categories:
- Classes
- i18n/L10n
- Linux
- PHP
---

El año pasado (2013 ya!) di una conferencia acerca de las cosas que hay que tomar en cuenta con la internacionalización y la localización. De tal forma como presenté en esa charla, también expondré algunos de los puntos más importantes en mi blog, de forma que quede accesible para todo el mundo.

Lo primero que hay que tomar en cuenta es la diferenciación entre localización e internacionalización. Estos dos términos suelen confundirse fácilmente, cuando tienen una diferencia fundamental entre ellas, así que con esto ya sabrán cómo entretener a sus mamás/esposas/pololas demostrando su vasto conocimiento en cosas que nada les interesa.

La localización es la traducción propiamente tal de cadenas de textos y otros ajustes que tienen que ver con las costumbres y reglas de un cierto lugar. La internacionalización en cambio se refiere al hecho de preparar la aplicación (hacerlo compatible) para que pueda ser localizado. De esta manera, podemos decir que internacionalizamos nuestra aplicación para que pueda ser localizado. 
<!-- more -->



## Elementos a ser localizados



Si bien es cierto en un primer momento podemos decir que lo único que concierne a las cosas específicas de un lugar es la traducción de cadenas de textos, no estamos nada más lejos de la realidad. La verdad es que implica bastante más:





  * Traducciones de cadenas de textos


  * Formateo de números


  * Formateo de monedas (Y todo lo que tenga que ver con manejo de dinero)


  * Encodeos, charsets y collations


  * Sistemas métricos


  * Métodos de redondeo


  * Fuentes


  * Fechas y horas


  * Timezones


  * Imágenes y gestos



Como pueden ver, la localización abarca bastante más que sólo traducciones, es por eso mismo que durante el transcurso de un par de meses, explicaré cada uno de estos puntos en posts separados. Por mientras, algunos puntos bases:

- Cuando trabajen en su código, hagan absolutamente todo en UTF-8: guardar sus archivos fuentes en UTF-8, la conexión hacia la base de datos en UTF-8, las tablas como UTF-8 y mandar la página al cliente en UTF-8. No se olviden de la configuración de Apache y PHP, ya que es posible ajustar ahí el encodeo igual.
- En MySQL, pueden definir un timezone predeterminado. Por razones de comprensión a largo plazo, sugiero dejar esta configuración en tiempo UTC, más que nada porque UTC es 0, por lo que simplemente tendrán que hacer una fácil conversión de más o menos X horas. La directiva que controla esto es `default-time-zone = '+00:00'`. 
- JavaScript tiene un sistema bastante poderoso para trabajar con fechas, que es todo parte del objeto Date. Revísenla y ocúpenla. 



## Preparando su sistema para ser internacionalizado



Uno de los puntos más importantes al preparar su aplicación, es el sistema operativo. El sistema operativo mal que mal es la encargada de adaptar (mediante llamadas nativas a las librerías que ésta tenga instalado) todos los valores y formatos de horas y fechas al sistema en el que el usuario está acostumbrado a verlas.

Si ocupan RedHat como base operativa (o uno de sus derivados), la chance de que las locales ya estén instaladas es bastante alta, si en cambio ocupan algún Debian, tendrán que instalarlas a mano.
Para verificar cuáles locales están instaladas, basta llamar al comando: 
`locale -a`

La explicación de instalación de locales en Debian la [pueden consultar en Google](https://www.google.com/#q=c%C3%B3mo+instalar+locale+en+debian), que es bastante más experimentado que yo en ese campo. 

Otro de los puntos importantes que recomiendo, es trabajar en inglés. No sólo lo que es la salida al usuario final, sino que también la documentación, nombre de variables, funciones, classes y otros: ¿la razón? Bueno, hay múltiples: si van a trabajar con alguien del extranjero en algún momento (lo cual puede ser probable ya que quieren internacionalizar su aplicación), la única forma de que todos puedan entenderse es en inglés. La otra gran razón es que es mil veces más barato conseguir un traductor inglés - húngaro que un traductor árabe - húngaro. 

Para localizar JavaScript, pueden ocupar la librería [i18next](http://i18next.com/). Es bastante completa y está bastante bien testeado por hartas aplicaciones gigantes que hacen uso de esta librería.



## Consideraciones de PHP



La primera gran consideración teniendo en cuenta PHP es que deben trabajar como mínimo con PHP 5.3. Las razones detrás de esto son variadas, pero tengan en cuenta que si están trabajando con alguna versión debajo de esta, están trabajando con software anciano que ya no tiene soporte. Personalmente recomiendo la rama 5.4, que tiene variadas cosas nuevas, que les podrá ayudar a acelerar su aplicación en hasta un 25%. Si quieren vivir al filo de la vida, les recomendaría la rama 5.5 que incorpora dos nuevas classes: `\IntlTimeZone` y `\IntlCalendar`, ambas muy útiles en manejo de timezones y calendario.

Sin embargo, gran parte del trabajo se incorporó en PHP 5.3: se incorporaron las classes `\Locale`, `\intlDateFormatter`, `\NumberFormatter` y `\DateTimeZone`, que son el pilar fundamental hoy en día en cuanto a internacionalización en PHP, estas nuevas classes reemplazan a la función `setlocale()` y sus derivados. 

La segunda gran consideración es instalar la extensión intl, que en RedHat se instala mediante un simple `yum install php-intl`. Esta extensión será la encargada de hacer de puente entre su aplicación y el sistema operativo.

Otro gran plus es conocer y aprender a ocupar bien las funciones [s]printf() que presenta PHP, ya que la internacionalización se basa en gran medida en estas funciones. Como pequeño tip: si antes ocupaban las variables %d o %f para imprimir un número, cámbienlas por %s, ya que las classes ya mencionadas cambian la salida por un string, las entregan listas y no hay que hacer nada de forma manual.

Por último, un buen punto de partida es ocupar mi class `unreal4u\Localization`, disponible en [packagist](https://packagist.org/packages/unreal4u/localization) o directamente en [GitHub](https://github.com/unreal4u/localization).

Recomiendo sin embargo la versión disponible en Packagist, de esta forma, lo único que tendrán que poner en su composer.json es lo siguiente:

{% highlight javascript %}
{
    "require": {
        "unreal4u/localization": "0.3.*@dev"
    }
}
{% endhighlight %}

Recomiendo también echar un pequeño vistazo a las demás classes. 

Como gran meta de este año tengo poder publicar esta serie de posts al respecto, así que estén atentos a más!
