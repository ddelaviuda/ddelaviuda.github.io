---
layout: post
title:  "Iptables en Debian 8"
date:   2016-11-21 09:39:22
categories: sistemas
comments: true
tags:
- seguridad
---
Información obtenidad de [aquí](https://wiki.debian.org/iptables).


Introducción
------------

Iptables es el programa que hace las funciones de cortafuegos en los sistemas Debian (así como en otras distribuciones GNU Linux). Desde hace algunos años está integrado en el 

nucleo del sistema esto implica, al no ser un servicio, que no se puede detener. 

Como se listan las reglas
-------------------------

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

Finalmente, la opción `-t` indica a iptables que tabla emplear, en el ejemplo `-t nat` emplea la tabla nat. Las opciones son filter (por defecto), nat, mangle y raw.

Tablas y cadenas
----------------

Cada una de las tablas tiene ciertas cadenas asociadas, aunque compartan el nombre se trata de cadenas diferentes, además de las cadenas obligatorias se pueden crear nuevas con 

otros nombres.

filter (OUTPUT, INPUT, FORWARD)

raw (OUTPUT, PREROUTING)

nat (INPUT, OUTPUT, PREROUTING, POSTROUTING)

mangle (INPUT, OUTPUT, PREROUTING, POSTROUTING, FORWARD)

Para añadir una regla a iptables la tabla precedida de `-t`, la opción a ejecutar: `-A` para añadir al final o `-I` para insertar en una posición seguida del número o `-D` para 

borrar y las demás opciones como en el ejemplo. Añade al final de las reglas de filter en la cadena INPUT un bloqueo a las ip 20.0.0.4/24 que usen el protocolo udp con destino las ip 192.168.1.1/24, se bloquean (DROP).

{% highlight bash %}
sudo iptables -t filter -A INPUT -p udp -s 20.0.0.0/24 -d 192.168.1.1/24 -j DROP
{% endhighlight %}


