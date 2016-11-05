---
layout: post
title:  "KVM en Debian 8, instalación y otras hierbas"
date:   2016-11-04 18:39:22
categories: sistemas
comments: true
---
Información obtenidad de [aquí](http://xmodulo.com/use-kvm-command-line-debian-ubuntu.html).


Verificación de hardware compatible con la virtualización
---------------------------------------------------------

El siguiente comando aclarará, de aparecer alguna de las cadenas coloreadas (vmx,svm), si es que nuestra CPU se puede emplear para KVM.

Para mostrar las máquinas viturales que están activadas en XEN se usa el siguiente comando.

{% highlight bash %}
$ egrep '(vmx|svm)' --color /proc/cpuinfo
{% endhighlight %}

Creación
--------


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
