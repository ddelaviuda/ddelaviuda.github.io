---
layout: post
title:  "Lo básico de LVM (Logical Volume Manager) en Debian" 
date:   2016-07-13 10:39:22
categories: 
---
Información [aquí](https://www.howtoforge.com/linux_lvm_p2) y [aquí](https://wiki.debian.org/LVM#List_of_LVM_commands).

Definiciones
------------

PV : *Physical Volumes*. Disco duro, particiones de disco duro, RAID o LUNs de una SAN son ejemplos de volúmenes físicos.

VG : *Volume Groups*. El conjunto de uno o varios PV.

LV : *Logical Volumes*. Son particiones lógicas dentro de un VG.

Comandos
--------

Comando para ver el listado de todos los volúmenes virtuales:

{% highlight bash %}
sudo lvmdiskscan
{% endhighlight %}

Listado de configuración VG (*Volume Groups*)

{% highlight bash %}
sudo vgdisplay
{% endhighlight %}

