---
layout: post
title:  "Debian 8 funcionando como router (iptables)"
date:   2016-12-04 10:39:22
categories: redes
comments: true
---

Escenario
---------

Disponemos de una Raspberry Pi B --de ahora en adelante RPI-- conectada (eth0) por un cable ethernet a nuestro ordenador (eth0) Debian 8 --de ahora en adelante solo Debian-- que dispone de conexión a Internet a través de la interfaz wlan0, el objetivo es que RPI disponca de acceso a Internet y para ello nuestro ordenador tendrá que hacer funciones de router.

Configuración de equipo (Raspberry Pi)
--------------------------------------

En un primer momento debemos configurar la interfaz para asignarle una IP una máscara y un Gateway, para realizar esta tarea será suficiente con emplear el comando ifconfig para lo que se puede seguir esta entrada.

Si no se ha establecido un Gateway todavía una forma de hacerlo es como sigue, habrá que sustituir 1.1.1.1 por la IP del siguiente salto y eth0 por la interfaz de conexión:

{% highlight bash %}
$ sudo ip route add default via 1.1.1.1 dev eth0
{% endhighlight %}

A partir de este momento todo el tráfico por defecto se cursará por esta interfaz, una forma de comprobarlo es con el comando:

{% highlight bash %}
$ sudo ip route show
{% endhighlight %}

O bien:

{% highlight bash %}
$ sudo route
{% endhighlight %}

Configuración de router (Debian 8)
----------------------------------

En Debian configuraremos la interfaz eth0 dentro de la subred correspondiente y procederemos a permitir el reenvío de paquetes, comando como superusuario `sudo su`.

{% highlight bash %}
echo 1 > /proc/sys/net/ipv4/ip_forward
{% endhighlight %}

O bien de forma permanente.
{% highlight bash %}
$ sudo nano /etc/sysctl.conf
{% endhighlight %}

Eliminando la # (uncoment) delante de ‘net.ipv4.ip_forward = 1’.

Después, añadiendo a ‘iptables’ la siguiente configuración:
{% highlight bash %}
$ sudo iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
$ sudo iptables -A FORWARD -i wlan0 -j ACCEPT
{% endhighlight %}
    
La interfaz wlan0 debe sutituirse por la interfaz con acceso a Internet.

Si se quiere desactivar iptables --eliminando todas las reglas-- lanzaremos esto.
{% highlight bash %}
$ sudo iptables -P INPUT ACCEPT
$ sudo iptables -P OUTPUT ACCEPT
$ sudo iptables -P FORWARD ACCEPT
$ sudo iptables -F
{% endhighlight %}

Servidor DNS
------------

Una vez en este punto tendremos conexión a Internet y si hacemos ping a www.google.com no podrá resolver el nombre, pero llegará a su IP porque PRI no tendrá ni idea de qué le estamos hablando, no tiene acceso a un servidor DNS que le ‘traduzca’ los nombres.

{% highlight bash %}
$ ping 8.8.8.8
{% endhighlight %}

Para definir el servidor DNS al que solicitar los nombres procedemos a editar el archivo siguiente.
{% highlight bash %}
$ sudo nano /etc/resolv.conf
{% endhighlight %}

Y añadiremos las siguientes líneas, que son las IP de los servidores de nombres de Google.

{% highlight bash %}
nameserver 8.8.8.8
nameserver 8.8.4.4
{% endhighlight %}

Si solo queremos resolver algunos nombres y vincularlos a direcciones IP editaremos el archivo
{% highlight bash %}
$ sudo nano /etc/hosts
{% endhighlight %}

A la izquierda situaremos la dirección IP y a la derecha el URL correspondiente. Siguiendo el ejemplo.

{% highlight bash %}
10.0.0.1	nombre-equipo-diez-cero-cero-uno
{% endhighlight %}
