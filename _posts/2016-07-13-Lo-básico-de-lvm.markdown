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

Instalación
-----------

Para instalar los programs necesarios ejecutamos lo siguiente.

{% highlight bash %}
sudo apt-get install lvm2 dmsetup mdadm reiserfsprogs xfsprogs
{% endhighlight %}

Formateando las unidades de disco
---------------------------------

{% highlight bash %}
sudo fdisk /dev/sdb

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes):8e
Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): w
{% endhighlight %}

Preparamos la partición para LVM

{% highlight bash %}
pvcreate /dev/sdb1
{% endhighlight %}

Si lo deseamos es posible deshacer el cambio mediante el comando.

{% highlight bash %}
pvremove /dev/sdb1
{% endhighlight %}

Comandos para mostrar el estado de LVM
--------------------------------------

Un comando para mostrar el estado de las máquinas vituales.

{% highlight bash %}
$ sudo pvdisplay 
  "/dev/sdb1" is a new physical volume of "931.51 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name               
  PV Size               931.51 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               xxxxxx-xxxx-xxxx-xxxx-xxxx-xxxx-xxxxxx
{% endhighlight %}

Comando para ver el listado de todos los volúmenes virtuales:

{% highlight bash %}
$ sudo lvmdiskscan 
  /dev/sda1 [     555.88 GiB] 
  /dev/sdb1 [     931.51 GiB] LVM physical volume
  0 disks
  1 partition
  0 LVM physical volume whole disks
  1 LVM physical volume

{% endhighlight %}

Listado de configuración VG (*Volume Groups*)

{% highlight bash %}
sudo vgdisplay
{% endhighlight %}

