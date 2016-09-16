---
layout: post
title:  "Iptables en Debian 8"
date:   2016-11-21 09:39:22
categories: sistemas
comments: true
---
Información obtenidad de [aquí](https://wiki.debian.org/iptables).


Introducción
------------

Iptables es el programa que hace las funciones de cortafuegos en los sistemas Debian (así como en otras distribuciones GNU Linux). Desde hace algunos años está integrado en el nucleo del sistema esto implica, al no ser un servicio, que no se puede detener. 

Listado de reglas
-----------------

Para mostrar las reglas configuradas en iptables existen diversas opciones `-L` lista y `--line-numbers` muestra el número de la línea.

{% highlight bash %}
$ sudo iptables -L --line-numbers -t nat
Chain PREROUTING (policy ACCEPT)
num  target     prot opt source               destination
1    DNAT       tcp  --  anywhere             anywhere             tcp dpt:http to:192.168.1.1:80

Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination

Chain POSTROUTING (policy ACCEPT)
num  target     prot opt source               destination
1    MASQUERADE  all  --  anywhere             anywhere
{% endhighlight %}

La opción `-S` muestra las reglas como el comando que se emplea para añadirlas.

{% highlight bash %}
$ sudo iptables -S -t nat
-P PREROUTING ACCEPT
-P INPUT ACCEPT
-P OUTPUT ACCEPT
-P POSTROUTING ACCEPT
-A PREROUTING -i eth0 -p tcp -m tcp --dport 80 -j DNAT --to-destination 192.168.1.1:80
-A POSTROUTING -o wlan0 -j MASQUERADE
{% endhighlight %}

Finalmente, hay una opción que indica a iptables que tabla emplear `-t nat` emplea la tabla nat, las otras son filter (por defecto), nat, mangle y raw.
