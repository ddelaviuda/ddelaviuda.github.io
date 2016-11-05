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
$ sudo apt-get install lvm2 dmsetup mdadm reiserfsprogs xfsprogs
{% endhighlight %}

Formateando las unidades de disco
---------------------------------

Damos formato a la unidad de disco con fdisk.

{% highlight bash %}
$ sudo fdisk /dev/sdb

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes):8e
Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): w
{% endhighlight %}

Preparamos la partición para LVM

{% highlight bash %}
$ sudo pvcreate /dev/sdb1
{% endhighlight %}

Si lo deseamos es posible deshacer el cambio mediante el comando.

{% highlight bash %}
$ sudo pvremove /dev/sdb1
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

Creación y listado de grupos
----------------------------

Creamos el grupo llamado servidor_de_archivos.


{% highlight bash %}
$ sudo vgcreate servidor_de_archivos /dev/sdb1
{% endhighlight %}

Listado de configuración VG (*Volume Groups*)

{% highlight bash %}
$ sudo vgdisplay 
  --- Volume group ---
  VG Name               servidor_de_archivos
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               931.51 GiB
  PE Size               4.00 MiB
  Total PE              238466
  Alloc PE / Size       0 / 0   
  Free  PE / Size       238466 / 931.51 GiB
  VG UUID               xxxxxx-xxxx-xxxx-xxxx-xxxx-xxxx-xxxxxx

{% endhighlight %}

