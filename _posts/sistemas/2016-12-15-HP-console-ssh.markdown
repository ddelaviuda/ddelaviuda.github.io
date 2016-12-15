---
layout: post
title:  "Acceso de consola vía ssh en iLO"
date:   2016-12-14 09:39:22
categories: sistemas
comments: true
tags:
- redes
---

Conexión ssh
------------

Lo primero será conectar con la interfaz iLO mediante ssh. Para ello ejecutamos lo siguiente.

{% highlight bash %}
$ ssh -l usuario_iLO ip_iLO
usuario_iLO@ip_iLO's password:
{% endhighlight %}

Introduciremos la contraseña y accederemos a iLO por ssh, ahora podemos mostrar las opciones disponibles.

{% highlight bash %}
</>hpiLO->help
help    : Used to get context sensitive help.
show    : Used to display values of a property or contents of a collection target.
show  -a  : Recursively show all targets within the current target.
show  -l <level>  : Recursively show targets within the current target based on 'level' specified.
         Valid values for 'level' is from 1 to 9.
create  : Used to create new instances in the name space of the MAP.
 Example: create /map1/accounts1 username=<lname1> password=<pwd12345> name=
 <dname1> group=<admin,config,oemhp_vm,oemhp_rc,oemhp_power>

delete  : Used to destroy instances in the name space of the MAP.
 Example: delete /map1/accounts1/<lname1>

load    : Used to move a binary image from an URL to the MAP.
 Example: load /map1/firmware1 -source http://192.168.1.1/images/fw/iLO4_100.bin

reset   : Causes a target to cycle from enabled to disabled and back to enabled.

set     : Used to set a property or set of properties to a specific value.
start   : Used to cause a target to change state to a higher run level.
stop    : Used to cause a target to change state to a lower run level.
cd      : Used to set the current default target.
   Example: cd targetname
date    : Used to get the current date.
time    : Used to get the current time.
exit    : Used to terminate the CLP session.
version : Used to query the version of the CLP implementation or other CLP elements.

oemhp_ping    : Used to determine if an IP address is reachable.
Example : oemhp_ping 192.168.1.1

oemhp_loadSSHKey    : Used to authorize a SSH Key File from an URL.
Example : oemhp_loadSSHKey -source http://user:pwd@192.168.1.1/images/SSHkey1.pub
oemhp_deleteSSHKey    : Used to remove a SSH Key associated with a user
Example : oemhp_deleteSSHKey

HPE CLI Commands:

POWER    : Control server power.
UID      : Control Unit-ID light.
ONETIMEBOOT: Access One-Time Boot setting.
NMI      : Generate an NMI.
VM       : Virtual media commands.
LANGUAGE : Command to set or get default language
VSP      : Invoke virtual serial port.
TEXTCONS : Invoke Remote Text Console.
TESTTRAP : Sends a test SNMP trap to the configured alert destinations.
{% endhighlight %}

Como se puede observar la opción VSP invoca la consola serial virtual. Si ejecutamos ese comando accederemos a la consola del sistema.

{% highlight bash %}
</>hpiLO-> vsp

Virtual Serial Port Active: COM2

Starting virtual serial port.
Press 'ESC (' to return to the CLI Session.
{% endhighlight %}

Cómo encontrar las ip de las interfaces ilO en nuestra red
----------------------------------------------------------

El siguiente script facilita la tarea de encontrar que IP le fue asignada por un --supuestamente existente-- servidor dhcp.

{% highlight bash %}
#!/bin/bash
function buscador_ilo() {
	if [ -z $1 ]; then
		echo "ilo_list/<subred>"
		echo "ejemplo: buscador_ilo 192.168.1.0/24"
	else
		sudo nmap -n -P0 -sS -p 17988 -oG - $1 | fgrep /open/ | awk '{print $2}'
	fi
}

buscador_ilo $1
{% endhighlight %}

