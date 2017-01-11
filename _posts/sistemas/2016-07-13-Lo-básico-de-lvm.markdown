---
layout: post
title:  "Lo básico de LVM (Logical Volume Manager) en Debian" 
date:   2016-07-13 10:39:22
categories: sistemas
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

Otro comando similar con salida más breve es este.

{% highlight bash %}
$ sudo vgscan
  Reading all physical volumes.  This may take a while...
  Found volume group "servidor_de_archivos" using metadata type lvm2
{% endhighlight %}

Podemos cambiar el nombre del grupo servidor_de_archivos

{% highlight bash %}
$ sudo vgrename servidor_de_archivos archivador
{% endhighlight %}

Es posible borrar el grupo mediante el comando siguiente.

{% highlight bash %}
$ sudo vgremove data
{% endhighlight %}

Creación y listado de volúmenes
-------------------------------

{% highlight bash %}
$ sudo lvcreate --name data --size 35G archivador
{% endhighlight %}

Mostramos el nuevo volúmen con este comando.

{% highlight bash %}
$ sudo lvdisplay
  --- Logical volume ---
  LV Path                /dev/archivador/data
  LV Name                data
  VG Name                archivador
  LV UUID                xxxxxx-xxxx-xxxx-xxxx-xxxx-xxxx-xxxxxx
  LV Write Access        read/write
  LV Creation host, time AMD-II, 2016-11-06 00:19:27 +0000
  LV Status              available
  # open                 0
  LV Size                35.00 GiB
  Current LE             8960
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
{% endhighlight %}

Trambién puede ser útil lvscan.

{% highlight bash %}
$ sudo lvscan
{% endhighlight %}

Podemos cambiar el nómbre a los volúmenes de un grupo mediante el comando.

{% highlight bash %}
$ lvrename archivador data datos
{% endhighlight %}

Podremos eliminar un volúmen mediante este comando.

{% highlight bash %}
$ lvremove /dev/archivador/datos
{% endhighlight %}

Podemos aumentar el tamaño de un volúmen.

{% highlight bash %}
$ sudo lvextend -L40G /dev/archivador/data
  Size of logical volume archivador/data changed from 35.00 GiB (8960 extents) to 40.00 GiB (10240 extents).
  Logical volume data successfully resized
{% endhighlight %}

O reducirlo.

{% highlight bash %}
$ sudo lvreduce -L35G /dev/archivador/data
  WARNING: Reducing active logical volume to 35.00 GiB
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce data? [y/n]: y
  Size of logical volume archivador/data changed from 40.00 GiB (10240 extents) to 35.00 GiB (8960 extents).
  Logical volume data successfully resized
{% endhighlight %}

Formateando los volúmenes
-------------------------

Damos formato Ext4 al volúmen /dev/archivador/data.

{% highlight bash %}
$ sudo mkfs.ext4 /dev/archivador/data
{% endhighlight %}

Montando los volúmenes en ficheros
----------------------------------

Creamos un directorio donde montar el volúmen.

{% highlight bash %}
$ sudo mkdir /media/data
{% endhighlight %}

Podemos montar ahora el volúmen.

{% highlight bash %}
$ sudo mount /dev/archivador/data /media/data
{% endhighlight %}

Si queremos que las unidades se monten en el arranque debemos editar /etc/fstab añadiendo lo que sigue al final del archivo.

{% highlight bash %}
/dev/archivador/data    /media/data      ext4   rw,noatime    0 0
{% endhighlight %}

Extra: Swap en un lvm
---------------------

Creamos el volúmen swap.

{% highlight bash %}
lvcreate --name swap --size 8G archivador
{% endhighlight %}

Le damos formato.

{% highlight bash %}
mkswap /dev/archivador/swap 
Setting up swapspace version 1, size = 8388604 KiB
no label, UUID=e16c3452-5bbf-4dde-a5a1-8f03ebacb5aa
{% endhighlight %}

Añadimos los siguiente a `/etc/fstab`.

{% highlight bash %}
/dev/mapper/archivador-swap none            swap    sw              0       0
{% endhighlight %}

Y reiniciamos o `swapon -va`.

Para comprobar si está funcionando correctamente ejecutamos lo siguiente.

{% highlight bash %}
cat /proc/swaps
free -h
{% endhighlight %}