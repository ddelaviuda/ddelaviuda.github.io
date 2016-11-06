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

Habilitación de virtualización en la BIOS
-----------------------------------------

Es necesario acceder a la BIOS de nuestra máquina y habilitar la capacidad de virtualización si es que no estuviera ya realizada.

Instalación de KVM
------------------

El siguiente comando instalará lo necesario.

{% highlight bash %}
$ sudo apt-get install qemu-kvm libvirt-bin virtinst virt-viewer
{% endhighlight %}

Es muy probable que sea necesario añadir nuestro usuario al grupo libvirt-qemu, para coprobar si es así lanzaremos el comando id para ver si somos miembros del grupo.

{% highlight bash %}
$ id $USER | grep libvirt
{% endhighlight %}

Debemos añadirnos al grupo manualmente para poder gestionar los huéspedes de nuestra máquina anfitrion. Para ello ejecutamos esto.

{% highlight bash %}
$ sudo adduser $USER libvirt-qemu
$ sudo adduser $USER kvm
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

Crear una máquina Virtual desde el terminal
-------------------------------------------

La configuración de una instalación KVM se guarda en un archivo con formato XML, en el ejemplo lo llamaremos debian8.xml. Una explicación de este formato se puede leer [aquí](https://libvirt.org/formatdomain.html).

{% highlight xml %}
<domain type='kvm'>
  <name>debian8</name>
  <uuid>f5b8c05b-9c7a-3211-49b9-2bd635f7e2af</uuid>
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
    
    <disk type="file" device="cdrom">
      <driver name="qemu" type="raw"/>
      <source file="/media/os/debian_8/debian-8.5.0-amd64-DVD-1.iso"/>
      <target dev="hdc" bus="ide"/>
      <readonly/>
      <address type="drive" controller="0" bus="1" target="0" unit="0"/>
    </disk>
    <disk type='block' device='disk'>
      <source dev='/dev/mapper/archivador-asterisk'/>
      <target dev='hda' bus='ide'/>
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

Crearemos la máquina virtual con el siguiente comando y el archivo debian8.xml

{% highlight bash %}
$ sudo virsh create debian8.xml
{% endhighlight %}

Podemos comprobar si está funcionando correctamente con el comando siguiente.

{% highlight bash %}
$ sudo virsh list
 Id    Name                           State
----------------------------------------------------
 3     debian8                       running
{% endhighlight %}

En el archivo hemos definido dos maneras de acceder al la máquina virtual para hacer la instalación, mediante un cliente VNC como vinagre y por consola.

Alternativamente podemos hacer los siguiente.

{% highlight bash %}
# mkdir -p /media/kvm/images

# virt-install \
--name template \
--ram 4096 \
--disk path=/media/kvm/images/template.img,size=30 \
--vcpus 2 \
--os-type linux \
--os-variant debianwheezy \
--network bridge=br0 \
--graphics none \
--console pty,target_type=serial \
--location 'http://ftp.jaist.ac.jp/pub/Linux/debian/dists/jessie/main/installer-amd64/' \
--extra-args 'console=ttyS0,115200n8 serial'
Starting install...# installation starts


# En la ventana inicial pulsar "e"

 +----------------------------------------------------------------------------+
 |*Debian GNU/Linux                                                           |
 | Advanced options for Debian GNU/Linux                                      |
 |                                                                            |
 |                                                                            |
 |                                                                            |
 |                                                                            |
 |                                                                            |
 |                                                                            |
 |                                                                            |
 |                                                                            |
 |                                                                            |
 |                                                                            |
 +----------------------------------------------------------------------------+

      Use the ^ and v keys to select which entry is highlighted.
      Press enter to boot the selected OS, `e' to edit the commands
      before booting or `c' for a command-line.

# Añadir consola virtual y pulsar  "Ctrl + x" para continuar.

 +---------------------------------------------------------------------------------------+
 |          search --no-floppy --fs-uuid --set=root  82c9915f-4b67-46cc-ac63-c6dd4d7662d1|
 |                                                                                       |
 |        else                                                                           |
 |          search --no-floppy --fs-uuid --set=root 82c9915f-4b67-46cc-ac63-c6dd4d7662d1 |
 |                                                                                       |
 |        fi                                                                             |
 |        echo        'Loading Linux 3.16.0-4-amd64 ...'                                 |
 |        linux        /vmlinuz-3.16.0-4-amd64 root=/dev/mapper/debian--vg-root ro quiet\|

 |                     console=ttyS0,115200n8                                            |

 |        echo        'Loading initial ramdisk ...'                                      |
 |        initrd        /initrd.img-3.16.0-4-amd64                                       |
 |                                                                                       |
 +---------------------------------------------------------------------------------------+

      Minimum Emacs-like screen editing is supported. TAB lists
      completions. Press Ctrl-x or F10 to boot, Ctrl-c or F2 for
      a command-line or ESC to discard edits and return to the GRUB menu.
  Booting a command list

# Después de arrancar habilitar el servicio como sigue.

# systemctl enable getty@ttyS0

Created symlink from /etc/systemd/system/getty.target.wants/getty@ttyS0.service to /lib/systemd/system/getty@.service.
# Instalación terminada
{% endhighlight %}

Haciendo un dumpxml tras esto obtenemos.

{% highlight bash %}
$ sudo virsh dumpxml template template.xml
{% endhighlight %}

{% highlight bash %}
<domain type='kvm' id='2'>
  <name>template</name>
  <uuid>ff93fe12-eb9b-446d-842a-8be2d63d507d</uuid>
  <memory unit='KiB'>4194304</memory>
  <currentMemory unit='KiB'>4194304</currentMemory>
  <vcpu placement='static'>2</vcpu>
  <resource>
    <partition>/machine</partition>
  </resource>
  <os>
    <type arch='x86_64' machine='pc-i440fx-2.1'>hvm</type>
    <boot dev='hd'/>
  </os>
  <features>
    <acpi/>
    <apic/>
    <pae/>
  </features>
  <cpu mode='custom' match='exact'>
    <model fallback='allow'>Opteron_G3</model>
  </cpu>
  <clock offset='utc'>
    <timer name='rtc' tickpolicy='catchup'/>
    <timer name='pit' tickpolicy='delay'/>
    <timer name='hpet' present='no'/>
  </clock>
  <on_poweroff>destroy</on_poweroff>
  <on_reboot>restart</on_reboot>
  <on_crash>restart</on_crash>
  <devices>
    <emulator>/usr/bin/kvm</emulator>
    <disk type='file' device='disk'>
      <driver name='qemu' type='raw'/>
      <source file='/media/kvm/images/template.img'/>
      <backingStore/>
      <target dev='vda' bus='virtio'/>
      <alias name='virtio-disk0'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x04' function='0x0'/>
    </disk>
    <controller type='usb' index='0' model='ich9-ehci1'>
      <alias name='usb0'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x7'/>
    </controller>
    <controller type='usb' index='0' model='ich9-uhci1'>
      <alias name='usb0'/>
      <master startport='0'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0' multifunction='on'/>
    </controller>
    <controller type='usb' index='0' model='ich9-uhci2'>
      <alias name='usb0'/>
      <master startport='2'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x1'/>
    </controller>
    <controller type='usb' index='0' model='ich9-uhci3'>
      <alias name='usb0'/>
      <master startport='4'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x2'/>
    </controller>
    <controller type='pci' index='0' model='pci-root'>
      <alias name='pci.0'/>
    </controller>
    <interface type='bridge'>
      <mac address='52:54:00:57:d6:2f'/>
      <source bridge='br0'/>
      <target dev='vnet0'/>
      <model type='virtio'/>
      <alias name='net0'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x0'/>
    </interface>
    <serial type='pty'>
      <source path='/dev/pts/1'/>
      <target type='isa-serial' port='0'/>
      <alias name='serial0'/>
    </serial>
    <console type='pty' tty='/dev/pts/1'>
      <source path='/dev/pts/1'/>
      <target type='serial' port='0'/>
      <alias name='serial0'/>
    </console>
    <input type='tablet' bus='usb'>
      <alias name='input0'/>
    </input>
    <memballoon model='virtio'>
      <alias name='balloon0'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x0'/>
    </memballoon>
  </devices>
</domain>
{% endhighlight %}

Acceso a la MV mediante vinagre
-------------------------------

Primero debemos ver en qué puerto escucha el servidor VNC, para ello ejecutamos este comando.

{% highlight bash %}
$ sudo lsof -i -P | grep -i "libvirt"
qemu-syst 1326 libvirt-qemu   16u  IPv4  16266      0t0  TCP *:5900 (LISTEN)
{% endhighlight %}

Vemos que es el puerto 5900, configuramos la IP del huesped y el puerto indicado en vinagre y accedemos. Allí podremos configurar debian8 como lo haríamos desde un CD o un USB.