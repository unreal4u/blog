---
comments: true
date: 2013-08-03 17:40:49+00:00
layout: post
slug: automatizando-la-subida-de-codigo-con-phing
title: Automatizando la subida de código con Phing
wordpress_id: 812
categories:
- Control de versiones
- Linux
- PHP
---

Desde que me cambié de trabajo, trabajo (valga la redundancia) con un equipo bastante más grande de gente y uno de los problemas más comunes al que tenemos que hacerle frente es cómo subir código nuevo al servidor final. Hay varios proyectos en paralelo andando y cada desarrollador necesita tener la habilidad de poder subir sus cosas al servidor en producción.

Esta metodología de trabajo si bien es cierto es rápida, también puede provocar un sinfín de otros problemas, aunque los más graves han sido hasta el momento de sincronización: dos personas que trabajan en el mismo archivo y que, al momento de realizar la sincronización con el servidor en producción, topan en la problemática de que aparecen archivos con cambios en los cuales hay un ancestro en común pero del que no se sabe si está 100% listo para subirlo al servidor de producción o no.

Durante mi búsqueda de solución a este problema, di con algunos candidatos de forma de poder automatizar este proceso. Entre los candidatos estaban Capistrano, Phing o Fabric. Sin embargo, por su facilidad de uso y su nula dependencia Phing resultó el ganador absoluto.
<!-- more -->



## Instalando Phing



Uno de los mejores puntos de Phing es que es sumamente fácil de instalar. Sólo se necesita de PEAR [`yum install php-pear`] en los clientes, (en el servidor no es necesario) y ejecutamos como root:

{% highlight bash %}
pear channel-discover pear.phing.info
pear install phing/phing
pear install VersionControl_Git-alpha
{% endhighlight %}

Disclaimer: el método que emplearemos en realidad no necesita de VersionControl_Git-alpha pero de todas formas queda pendiente (de mi parte) revisar cómo se pueden integrar mejor estas dos herramientas, así que opté por instalarlo igual ya que en algún punto me gustaría estudiarlo.



## Configurando Phing



Lo siguiente que necesitamos es un archivo de configuración en cada repositorio que tengamos, de forma de poder decirle de alguna forma a Phing qué acciones deseamos realizar.

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<project name="example" basedir="." default="deployDev">
    <target name="deploy" depends="deployDev,deployLive" />

    <target name="deployDev">
       <echo msg="Deploying on DEV server" />
       <exec command="ssh example@example.com 'cd ~/sites/dev/ &amp;&amp; [[ -z $(git status --porcelain) ]] &amp;&amp; git pull --progress || (echo Dirty tree detected! Not going to update; git status; echo Please login and clean tree manually up)'" logoutput="yes"/>
    </target>

    <target name="deployLive">
        <echo msg="Deploying on LIVE server" />
        <exec command="ssh example@example.com 'cd ~/sites/www/ &amp;&amp; [[ -z $(git status --porcelain) ]] &amp;&amp; git pull --progress || (echo Dirty tree detected! Not going to update; git status; echo Please login and clean tree manually up)'" logoutput="yes"/>
    </target>
</project>
{% endhighlight %}

Sálvenlo en la raíz del repositorio como `build.xml` y desde ahora en adelante, podrán subir código de forma increíblemente fácil al servidor de producción ejecutando solo un comando.
Sin embargo, si estudian la sintaxis del archivo XML se darán cuenta del poder que se puede obtener al controlar un poco de código en bash y combinándolo con Git de forma nativa debe ser aún mejor pero por el momento lo dejaremos de lado.

Enfoquémonos en el archivo que tenemos. 
Para subir código al servidor de testeo, lo único que tenemos que hacer es navegar hasta la raíz de nuestro proyecto, y en una terminal, escribir: 

{% highlight bash %}
phing deployDev
{% endhighlight %}

Lo que hará entonces, será conectarse via SSH y ejecutará algunos comandos: primero verificará si el repositorio que está online está limpio o no (`git status --porcelain`) y retornará un código. De ese código dependerá de si llamará un `git pull` o nos mostrará un mensaje incluyendo además un pequeño resumen de qué está mal. Esto se hace para descartar cualquier conflicto que pudiera surgir al tener código en el servidor que no tengamos en el repositorio. De esa forma, nos obligará también a trabajar de manera más limpia. 

De forma similar, si llamamos `phing deployLive` hará exactamente lo mismo, pero esta vez con el servidor en producción.

Sin embargo, lo mejor viene para el final y es un botón de muestra de qué se puede hacer con un sistema automatizado: y es que podemos decirle que haga ambas al mismo tiempo, y además podemos decirle también a phing cuál va a ser la acción predeterminada a tomar. De esta forma, cuando ejecutemos: 

{% highlight bash %}
phing
{% endhighlight %}

La acción a ejecutarse de forma predeterminada será intentar actualizar nuestro servidor de prueba. Pero si llamamos: 

{% highlight bash %}
phing deploy
{% endhighlight %}

Actualizará ambos servidores. 



## Conclusiones



Subir contenido nuevo a un servidor en producción no debería ser difícil; sobretodo considerando la [corriente de integración continua](http://en.wikipedia.org/wiki/Continuous_integration); y con herramientas como estas de todas formas se hace más fácil. Un ejemplo más avanzado de esto sería también poder implementar alguna forma de realizar un rollback (proporcionando alguna revisión específica por ejemplo) o [poder sincronizar también bases de datos](http://acme-tech.net/blog/deploying-code-and-db-migration-via-phing/). También le pueden echar un ojo a la [documentación oficial](http://www.phing.info/docs/guide/trunk/chapters/GettingStarted.html) donde explican algunas de las opciones básicas pero de forma más extendida.
