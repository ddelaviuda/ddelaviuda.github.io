---
layout: post
title:  "¿Cómo crear un disco de arranque con Linux en el terminal?"
date:   2016-07-18 10:39:22
categories: sistemas
comments: true
---

Información basada en [kali](http://docs.kali.org/downloading/kali-linux-live-usb-persistence)

**dd**
------

Este programa permite copiar exactamente la información desde un archivo --`.iso` en nuestro caso-- a un disco/memoria. Suponiendo que se ha descargado una imagen que llamaremos `linux.iso`, por otro lado tendremos una memoria USB. Cuando se intruduce la mencionada memoria en un USB libre el sistema Linux una forma de saber el pseudo archivo que le asigna el sistema es mediante el siguiente comando.

{% highlight bash %}
dmesg | tail
{% endhighlight %}

El comando mostrará los últimos eventos del sistema, entre ellos se observará uno que indica la introducción de la memoria y su asignación. La línea hará mención de algo del timpo sdb o sdc, aunque la última letra podría variar dependiendo del número de discos/memorias ya conectadas.

Con esa información ahora procederemos a copiar el fichero en la memoria USB. Hay que tener precaución y estar seguro que nombre asignó Linux a nuestra memoria (sbx). Si se ejecuta el siguiente comando contra el disco duro que contiene el sistema de arranque la información se perderá. En el comando siguiente se supondrá que la memoria USB se encuentra en `/dev/sdc`.

{% highlight bash %}
sudo dd if=linux.iso of=/dev/sdc bs=512k
{% endhighlight %}

El proceso tardará cierto tiempo, dependiendo del tamaño de la imagen `linux.iso`, pero el comando no devolverá ninguna salida hasta que termine, habrá pues que tener paciencia.

Aprovechamiento de espacio adicional
------------------------------------

Siguiendo el proceso anterior ya sería suficiente para crear un disco de arranque, pero quedaría espacio perdido en la memoria. En este apartado se mostrará como hacer para poder usarlo.

Mediante el siguiente comando veremos el estado inicial de la memoria, suponiendo como antes que esta se encuenta asignada a `/dev/sdc`.

{% highlight bash %}
sudo fdisk -l /dev/sdc

Disk /dev/sdc: 14.9 GiB, 16008609792 bytes, 31266816 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x0c7b9850

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1  *         64 5570559 5570496  2.7G 17 Hidden HPFS/NTFS
/dev/sdc2       5570560 5752895  182336   89M  1 FAT12
{% endhighlight %}

Como se puede ver en la salida del comando observamos que la memoria tiene una capacidad de 14.9 GB pero las particiones apenas emplean 3GB. Para recuperar los gigas perdidos haremos lo siguiente.

Primero veremos cuanto ocupa la imagen que hemos grabado con `dd` y la pasaremos a una variable del sistema que llamaremos inicio.

{% highlight bash %}
read inicio _ < <(du -bcm linux.iso | tail -1)
{% endhighlight %}

Ahora crearemos una variable con la capacidad final que tiene la memoria, en ejemplo diremos que es 14GB, pero si fuese mayor o menor deberemos modificar el valor.

{% highlight bash %}
export fin=14gb
{% endhighlight %}

Finalmente invocamdos a `parted` para que genere la nueva partición. Será necesario resonder *Yes* e *Ignore*.

{% highlight bash %}
sudo parted /dev/sdc mkpart primary $inicio $fin

Warning: You requested a partition from 2810MB to 14.0GB (sectors 5488281..27343750).
The closest location we can manage is 2945MB to 14.0GB (sectors 5752896..27343750).
Is this still acceptable to you?
Yes/No? Yes
Warning: The resulting partition is not properly aligned for best performance.
Ignore/Cancel? I                                                          
Information: You may need to update /etc/fstab.
{% endhighlight %}

Una vez terminado lo anterior ahora habrá que dar formato a la nueva partición, para ello haremos lo siguiente --La parte de '-L memoria' es una etiqueta, es opcional y se puede cambiar--.

{% highlight bash %}
sudo mkfs.ext3 -L memoria /dev/sdc3 
{% endhighlight %}

Otra forma de darle una etiqueta es mediante `e2label`.

{% highlight bash %}
e2label /dev/sdc3 persistence
{% endhighlight %}

Verificación
------------

Podremos comprobar si los cambios anteriores han surtido efecto mediante el siguiente comando.

{% highlight bash %}
sudo fdisk -l /dev/sdc

Disk /dev/sdc: 14.9 GiB, 16008609792 bytes, 31266816 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x0c7b9850

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sdc1  *         64  5570559  5570496  2.7G 17 Hidden HPFS/NTFS
/dev/sdc2       5570560  5752895   182336   89M  1 FAT12
/dev/sdc3       5752896 27343750 21590855 10.3G 83 Linux
{% endhighlight %}


Cifrado
-------

En caso de que deseemos cifrar el contenido de la partición mediante una contraseña, una vez creada la partición y antes de darle formato --esto es: inmediatamente despues de ejecutar `parted`-- debemos ejecutar el siguiente comando.

{% highlight bash %}
sudo cryptsetup --verbose --verify-passphrase luksFormat /dev/sdc3
{% endhighlight %}

**Nota:** Suponemos que la partición a encriptar está en `/dev/sdc3` y que **cryptsetup** está instalado, de no estarlo instalarlo con `sudo apt-get install cryptsetup` Debian y con `sudo yum install cryptsetup` en CentOS.

Tras ejecutar el comando anterior se nos pedirá la contraseña dos veces para verificar que es la misma.

El siguiente paso es abrir el canal cifrado en la partición mediante LUKS, para ello hacemos lo siguiente --miUSB es el nombre que hemos seleccionado pero valdría cualquier otro--:

{% highlight bash %}
sudo cryptsetup luksOpen /dev/sdb3 miUSB
{% endhighlight %}

Ahora creamos una partición de tipo **ext3**.

{% highlight bash %}
sudo mkfs.ext3 -L memoria_usb /dev/mapper/miUSB
{% endhighlight %}

Lo etiquetamos.

{% highlight bash %}
sudo e2label /dev/mapper/miUSB memoria_usb
{% endhighlight %}

Mediante los siguientes crearemos un directorio en `/mnt/miUSB`, montamos la unidad y la desmontamos. Si queremos añadir algún michero o directorio el momento será después de montarlo.

{% highlight bash %}
sudo mkdir -p /mnt/miUSB
sudo mount /dev/mapper/miUSB /mnt/miUSB
sudo umount /dev/mapper/miUSB
{% endhighlight %}

Finalmente cerramos el canal cifrado con el comando siguiente:

{% highlight bash %}
sudo cryptsetup luksClose /dev/mapper/miUSB
{% endhighlight %}





