---
layout: post
title:  "XEN en Debian: Operaciones básicas"
date:   2016-07-12 18:39:22
categories: sistemas
---
Información obtenidad de [aquí](http://wiki.xenproject.org/wiki/Main_Page).
------------------------------

Emplearemos la herramienta xl, la única disponible en la versión de Xen 4.5 y posteriores.
Listado

Para mostrar las máquinas viturales que están activadas en XEN se usa el siguiente comando.

{% highlight bash %}
$ sudo xen list
Name           ID   Mem VCPUs	State	Time(s)
Domain-0            0  1012     4     r-----     216.7
nombre1             1  1024     2     -b----      28.2
nombre2             2  1024     2     -b----      72.4
nombre3             4  1024     2     -b----       2.8
{% endhighlight %}
Creación

Para crear una máquina virtual –en el ejemplo se usa direccionamiento estático y la distribución debian jessie– ejecutamos el comando siguiente:

{% highlight bash %}
sudo xen-create-image --hostname=nombre \
  --size=8Gb --memory=1024Mb --swap 1024Mb \
  --ip=10.0.0.2 \
  --netmask=255.255.255.0 \
  --gateway=10.0.0.1 \
  --vcpus=2 \
  --lvm=vg0 \
  --pygrub \
  --dist=jessie
{% endhighlight %}

Al terminar la creación se genera texto con un resumen de la configuración de red y la contraseña root del sistema.
Arranque

El comando de creación del apartado anterior genera una archivo `.cfg` que debe ser invocado para cargar la máquina virtual creada. En el siguiente comando se muestra un ejemplo.

{% highlight bash %}
sudo xl create /path/archivo_generado.cfg
{% endhighlight %}

Para que una máquina creada arranque al inicio con dom0 será necesario crear un enlace simbólico de su archivo de configuración al directorio `/etc/xen/auto`.

{% highlight bash %}
sudo ln -s /etc/xen/archivo_generado.cfg /etc/xen/auto/nombre_enlace.cfg
{% endhighlight %}
