---
layout: post
title:  "¿Cómo saber qué puertos están abiertos y qué aplicación los escucha?"
date:   2016-07-17 10:39:22
categories: sistemas
tags:
- seguridad
- redes
comments: true
---

¿Qué comando utilar para analizar los puertos de la máquina donde nos encontramos conectados?

**netstat**
-----------

{% highlight bash %}
sudo netstat -tulpn
{% endhighlight %}

De la ejecución de este comando obtendremos sobre los puertos y protocolos que escucha nuestra máquina, que programa y PID tienen asociado cada puerto y el estado del mismo.

**ss**
------
Alternativamente se puede emplear el más moderno y potente comando `ss` con el que se pueden obtener resultados similares.

{% highlight bash %}
ss -a | grep LISTEN --color
{% endhighlight %}

**fuser**
---------

Si sabemos ya el puerto que queremos comprobar el PID del programa que escucha en un puerto lo podremos hacer con el comando siguiente, donde cambiaremos **#puerto** por el número de puerto, poniendo *tcp* o *udp* dependiendo del protocolo que nos interese.

{% highlight bash %}
sudo fuser #puerto/tcp
cat /proc/xxx y ps
{% endhighlight %}

Si queremos saber el programa asociado al PID obtenido ejecutaremos el comando que sigue cambiando `#pid` por el número obtenido.

{% highlight bash %}
sudo ls -l /proc/#pid/exe
{% endhighlight %}

Si con el nombre obtenido aún no tenemos claro que programa es podremos ejecutar `man “programa”` o `whatis “programa”`. Aunque siempre nos quedará *google*…

Si lo que queremos saber es el propietario del programa ejecutaremos lo siguiente.

{% highlight bash %}
sudo ls -l /proc/#pid/cwd
{% endhighlight %}

O también …

{% highlight bash %}
sudo ps aux | grep #pid
{% endhighlight %}

**nmap**
--------

Si queremos comprobar los puertos de una máquina remota podremos ejecutar el siguiente comando.

{% highlight bash %}
sudo nmap -sN -p T:#puerto URL
{% endhighlight %}

Donde T se refiere a *tcp* y puede cambiarse por U para comprobar el protocolo *udp*, **#puerto** es el número de puerto y *URL* es la dirección del host a comprobar.

Se puede obtener algo más de información con las siguientes opciones.

{% highlight bash %}
sudo nmap -p 1-65535 -T4 -A -v URL
{% endhighlight %}
