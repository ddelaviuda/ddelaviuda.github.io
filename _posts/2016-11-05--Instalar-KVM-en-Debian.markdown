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
DEVICE  TYPE      STATE      CONNECTION         
eth0    ethernet  unmanaged  --                 
wlan0   wifi      connected  mi_WiFi            
vmnet1  ethernet  unmanaged  --                 
vmnet8  ethernet  unmanaged  --                 
lo      loopback  unmanaged  -- 
{% endhighlight %}

Ahora instalaremos en software y crearemos la interfaz puente br0.

{% highlight bash %}
$ sudo apt-get install bridge-utils
$ sudo brctl addbr br0
{% endhighlight %}

Añadimos al fichero /etc/network/inteces algo como esto.

{% highlight bash %}
auto lo
iface lo inet loopback

auto br0
iface br0 inet static
address 1.0.0.100
netmask 255.255.255.0
network 10.0.0.0
bridge_ports eth0
bridge_stp off
bridge_fd 0
{% endhighlight %}

Será necesario modificar los valores de IP o bien activar el dhcp según convenga, no se va a detallar aquí como hacerlo.

Para que apliquen los cambios realizados es conveniente reiniciar la interfaz.
{% highlight bash %}
$ sudo ifup br0
{% endhighlight %}

Un comando útil para verificar 	que el puente está funcionando correctamente es el siguiente.
{% highlight bash %}
$ sudo brctl show
bridge name	bridge id		STP enabled	interfaces
br0		8000.10bf4872a827	no		eth0
{% endhighlight %}
Trambién es necesario comprobar que la interfaz br0 tiene la IP desada y la interfaz eth0 no tiene IP.

Crear una máquina Virtual desde consola
---------------------------------------

La configuración de una instalación KVM se guarda en un archivo con formato XML. 

{% highlight xml %}
<domain type='kvm'>
  <name>alice</name>
  <uuid>f5b8c05b-9c7a-3211-49b9-2bd635f7e2aa</uuid>
  <memory>1048576</memory>
  <currentMemory>1048576</currentMemory>
  <vcpu>1</vcpu>
  <os>
    <type>hvm</type>
    <boot dev='cdrom'/>
  </os>
  <features>
    <acpi/>
  </features>
  <clock offset='utc'/>
  <on_poweroff>destroy</on_poweroff>
  <on_reboot>restart</on_reboot>
  <on_crash>destroy</on_crash>
  <devices>
    <emulator>/usr/bin/kvm</emulator>
    <disk type="file" device="disk">
      <driver name="qemu" type="raw"/>
      <source file="/home/dev/images/alice.img"/>
      <target dev="vda" bus="virtio"/>
      <address type="pci" domain="0x0000" bus="0x00" slot="0x04" function="0x0"/>
    </disk>
    <disk type="file" device="cdrom">
      <driver name="qemu" type="raw"/>
      <source file="/home/dev/iso/CentOS-6.5-x86_64-minimal.iso"/>
      <target dev="hdc" bus="ide"/>
      <readonly/>
      <address type="drive" controller="0" bus="1" target="0" unit="0"/>
    </disk>
    <interface type='bridge'>
      <source bridge='br0'/>
      <mac address="00:00:A3:B0:56:10"/>
    </interface>
    <controller type="ide" index="0">
      <address type="pci" domain="0x0000" bus="0x00" slot="0x01" function="0x1"/>
    </controller>
    <input type='mouse' bus='ps2'/>
    <graphics type='vnc' port='-1' autoport="yes" listen='0.0.0.0'/>
    <console type='pty'>
      <target port='0'/>
    </console>
  </devices>
</domain>
{% endhighlight %}
