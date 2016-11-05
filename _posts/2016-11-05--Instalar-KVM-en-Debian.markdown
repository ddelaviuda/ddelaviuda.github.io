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

Instalación de KVM
------------------

El siguiente comando instalará lo necesario.

{% highlight bash %}
$ sudo apt-get install qemu-kvm libvirt-bin 
{% endhighlight %}

Es muy probable que sea necesario añadir nuestro usuario al grupo libvirt-qemu, para coprobar si es así lanzaremos el comando id para ver si somos miembros del grupo.

{% highlight bash %}
$ id $USER | grep libvirt
{% endhighlight %}

Debemos añadirnos al grupo manualmente para poder gestionar los huéspedes de nuestra máquina anfitrion. Para ello ejecutamos esto.

{% highlight bash %}
$ sudo adduser $USER libvirt-qemu
{% endhighlight %}

Si todo ha ido bien podremos ejecutar el siguiente comando sin ningún problema de permisos.

{% highlight bash %}
$ virsh list
 Id    Name                           State
----------------------------------------------------
{% endhighlight %}

El resultado debe ser el indicado ya que no hay ninguna máquina virtual funcionando.

Configuración de red
--------------------

La opción que se va a adoptar es configurar una interfaz puente entre las máquinas huésped y el anfitrión. Para llevarlo acabo debemos deshabilitar el Network Manager, ver [aquí](http://blog.telectric.es/redes/2016/07/11/Debian-manual-wifi.html).



Podemos verificar que está deshabilitado observando que las interfazes aparecen como no gestionadas tras ejecutar el siguiente comando.

{% highlight bash %}
$ nmcli device status
Error: NetworkManager is not running. 
{% endhighlight %}
