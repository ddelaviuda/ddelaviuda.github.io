---
layout: post
title:  "Configuración manual Wi-Fi en Debian"
date:   2016-07-11 18:39:22
categories: redes
---
Comandos de comprobación
------------------------

Verificamos si el *kernel* detecta el *hardware*.

{% highlight bash %}
sudo  lshw -C network
{% endhighlight %}

El resultado debería ser algo similar a esto.

{% highlight bash %}
*-network DISABLED
       description: Wireless interface
       product: AR9287 Wireless Network Adapter (PCI-Express)
       vendor: Qualcomm Atheros
       physical id: 0
       bus info: pci@0000:04:00.0
       logical name: wlan0
       version: 01
       serial: xx:xx:xx:xx:xx:xx
       width: 64 bits
       clock: 33MHz
       capabilities: pm msi pciexpress bus_master cap_list ethernet physical wireless
       configuration: broadcast=yes driver=ath9k driverversion=3.16.0-71-generic firmware=N/A latency=0 link=no multicast=yes wireless=IEEE 802.11bgn
       resources: irq:17 memory:f0400000-f040ffff
{% endhighlight %}

En la salida de texto anterior debemos comprobar que hay una cadena de texto junto a **driver=** y comprobaremos con **lsmod** si el driver está cargado. Para nuestro ejemplo será.

{% highlight bash %}
sudo lsmod | grep -i ath9k
{% endhighlight %}

Si aparece sólo uno son buenas noticias y podemos continuar. Si aparecen varios hay que quitar uno con sudo blocklist nombre_driver y si no hay ninguno tendremos que cargarlo con un comado similar a este.

{% highlight bash %}
sudo modprobe ath9k_htc
{% endhighlight %}

Ahora descartamos que no haya ningún bloqueo *hardware* o *software*.

{% highlight bash %}
sudo rfkill list
{% endhighlight %}

Gestión de las interfaces
-------------------------

Para saber el nombre que el sistema le ha asignado a la interfaz WiFi ejecutamos esto.

{% highlight bash %}
/sbin/iw dev
{% endhighlight %}

Obteniendo algo como esto

{% highlight bash %}
phy#0
	Interface wlan0
		ifindex 3
		type managed
{% endhighlight %}

El nombre es wlan0 y el timpo managed indica que es una estación que se conecta a un punto de acceso.

Hora comprobamos si la interfaz está levantada.

{% highlight bash %}
ip link show wlan0
{% endhighlight %}

La salida.

{% highlight bash %}
3: wlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 90:f6:52:27:11:5b brd ff:ff:ff:ff:ff:ff
{% endhighlight %}

En la salida no tenemos *<...,UP>* por lo tanto tendremos que activarla, para ello ejecutamos.

{% highlight bash %}
sudo ip link set wlan0 up
{% endhighlight %}

Ahora sí vemos que aparece *UP*.

{% highlight bash %}
ip link show wlan0
3: wlan0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN mode DEFAULT group default qlen 1000
    link/ether 90:f6:52:27:11:5b brd ff:ff:ff:ff:ff:ff
{% endhighlight %}

Comprobamos el estado de la conexión

{% highlight bash %}
/sbin/iw wlan0 link
{% endhighlight %}

Obtenemos *“Not connected.”* ya quen no está establecida todavía.

Buscar redes cercanas
---------------------

Para buscar las redes disponibles ejecutamos lo siguiente.

{% highlight bash %}
sudo /sbin/iw wlan0 scan
BSS xx:xx:xx:xx:xx:xx (on wlan0)
        ... eliminado ...
	freq: 2412
	SSID: mi_red
        ... eliminado ...
	RSN:	 * Version: 1
		 * Group cipher: CCMP
		 * Pairwise ciphers: CCMP
		 * Authentication suites: PSK
		 * Capabilities: (0x0000)
        ... eliminado ...
{% endhighlight %}

La información más importante de lo anterior es el SSID o nombre del punto de accedos y RSN que se refiere al tipo de conexión WPA2.

Establecimiento de la conexión WPA2
-----------------------------------

Lo primero es crear un archivo que contenga la el nombre de la red “mi_red” y el la clave pública, contraseña o “psk”. Para ello.

{% highlight bash %}
sudo -s wpa_passphrase mi_red >> /etc/wpa_supplicant.conf
{% endhighlight %}

Una vez ejecutado la línea de comando espera la indroducción de texto, escribimos la contraseña o psk. El archivo tendrá la siguiente apariencia.

{% highlight bash %}
cat /etc/wpa_supplicant.conf
{% endhighlight %}

Mostrando …

{% highlight bash %}
# reading passphrase from stdin
network={
	ssid="mi_red"
	#psk="contraseña_de_prueba"
	psk=75951eb58b775d53e2fe915d986c569e9d915a7f1375b5879121a498af792b3d
}
{% endhighlight %}

Ahora para conectarnos ejecutamos el comando.

{% highlight bash %}
sudo wpa_supplicant -B -D wext -i wlan0 -c /etc/wpa_supplicant.conf
{% endhighlight %}

Y comprobamos que estamos conectados con el comando.

{% highlight bash %}
/sbin/iw wlan0 link
{% endhighlight %}

El resultado deberá dar algo como esto.

{% highlight bash %}
Connected to xx:xx:xx:xx:xx:xx (on wlan0)
SSID: mi_red
freq: 2437
RX: 771824 bytes (6565 packets)
TX: 1413 bytes (19 packets)
signal: -45 dBm
tx bitrate: 1.0 MBit/s

bss flags: short-slot-time
dtim period: 0
beacon int: 100
{% endhighlight %}

Obtención de IP por DHCP
------------------------

Para comenzar con el protocolo DHCP ejecutamos el comando siguiente.

{% highlight bash %}
sudo dhclient wlan0
{% endhighlight %}

Para comprobar la dirección IP asignada a la interfaz wlan0

{% highlight bash %}
ip addr show wlan0
{% endhighlight %}

Resultando …

{% highlight bash %}
3: wlan0:  mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether xx:xx:xx:xx:xx:xx brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.55/24 brd 192.168.1.255 scope global wlan0
       valid_lft forever preferred_lft forever
    inet6 xxxx::xxxx:xxxx:xxxx:xxxx/64 scope link 
       valid_lft forever preferred_lft forever
{% endhighlight %}

Si queremos añadir una puerta de enlace a mano –192.168.1.254 en el ejemplo– ejecutaremos:

{% highlight bash %}
sudo ip route add default via 192.168.1.254 dev wlan0
{% endhighlight %}

Tras esto podemos probar a hacer ping al servidor DNS de Google Inc. (8.8.8.8)

{% highlight bash %}
ping -c 4 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=53 time=3.83 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=53 time=3.92 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=53 time=22.5 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=53 time=6.07 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 3.833/9.096/22.547/7.817 ms
{% endhighlight %}

Opciones adicionales
--------------------

Cómo cambiar la MAC de la interfaz

{% highlight bash %}
sudo ifconfig 'interface' down
sudo ifconfig 'interface' hw ether 'mac'
sudo ifconfig 'interface' up
{% endhighlight %}

Asociación al punto de acceso WEP

Configuración del ESSID

{% highlight bash %}
sudo iwconfig 'interface' essid 'essid_name'
{% endhighlight %}

Añadimos la contraseña WEP

{% highlight bash %}
sudo iwconfig 'interface' key 'hex_password
{% endhighlight %}

Forzamos la asociación con el ESSID

{% highlight bash %}
sudo iwconfig 'interface' ap 'essid_MAC'
{% endhighlight %}

Forzamos la optención de IP mendiante el servidor DHCP

{% highlight bash %}
dhclient 'interface'
{% endhighlight %}

Si por otro lado deseamos realizar la configuración manual de la IP y su máscara

{% highlight bash %}
sudo ifconfig 'interface' 'IP' netmask 'mask'
{% endhighlight %}

**Asociación al punto de acceso WPA en arranque**

Editamos el archivo interfaces

{% highlight bash %}
sudo nano /etc/network/interfaces
{% endhighlight %}

Y añadimos las líneas que se muestran a continuación.

{% highlight bash %}
auto wlan0
iface wlan0 inet static
address 'your_ip'
netmask 'your_netmask'
gateway 'gateway_ip'
wpa-ssid 'your_ssid_name'
wpa-key-mgmt WPA-PSK
wpa-group TKIP CCMP
wpa-psk 'your_password'
{% endhighlight %}

Lo que aparece como *‘your_password’* tras **wpa-psk** debe ser sustituido por la *Pre-shared key* obtenida como resultado del siguiente comando:

{% highlight bash %}
sudo wpa_passphrase 'ssid_name' 'passphrase'
{% endhighlight %}

Si el comando no está disponible debe ser instalado con **apt-get**:

{% highlight bash %}
sudo apt-get install wpasupplicant
{% endhighlight %}

Finalmente es necesario configurar el servidor dns para ello debemos añadir la siguiente linea al ficher **/etc/resolv.conf**.

{% highlight bash %}
nameserver 192.168.1.1
{% endhighlight %}

Alternativamente podemos crear un archivo que se suele llamar y dejar en **/etc/wpa_supplicant.conf** con la siguiente información.

{% highlight bash %}
network= {
          ssid="el_nombre_del_ssid"
          psk="la_contraseña"
}
{% endhighlight %}

Para forzar la conexión con la red ejecutaremos el comando.

{% highlight bash %}
wpa_supplicant -B -i 'interfaz' -c /etc/wpa_supplicant.conf
{% endhighlight %}
