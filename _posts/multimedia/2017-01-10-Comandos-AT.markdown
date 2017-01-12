---
layout: post
title:  "Comandos AT y SMS con modem GSM y Debian"
date:   2017-01-09 18:39:22
categories: multimedia
tags:
- voz
comments: true
---

Probado con:
------------

**Debian 8.6 (Jessie)**
**Módem ZTE WCDMA Technologies MSM**

Comandos AT
-----------
-----------

Configuración del módem
-----------------------

Debemos comprobar al indroducir nuestro módem GSM USB que se detecta correctamente por Debian.



Será necesario instalarlo en caso de que sea necesario el empleo de tarjetas.

{% highlight bash %}
lsusb
Bus 002 Device 005: ID 19d2:2003 ZTE WCDMA Technologies MSM 
{% endhighlight %}

Si aparece una línea en la que se incluye una cadena de texto como esta `19d2:2000` en lugar de `19d2:2003` , esto indica que el dispositivo ha sido reconocido como almacenamiento, para cambiarlo instalaremos el programa usb-modeswitch 

{% highlight bash %}
apt-get install usb-modeswitch
{% endhighlight %}

Después de instalado el programa ejecutaremos el comando siguiente para que el dispositivo sea reconocido como módem.

{% highlight bash %}
usb_modeswitch --default-vendor 0x19d2 --default-product 0x2000 --target-vendor 0x19d2 --target-product 0x0031  --message-endpoint 0x01 --message-content 55534243123456782000000080000c85010101180101010101000000000000
{% endhighlight %}

Una vez hecho lo anterior reiniciamos la máquina Debian y volvemos a comprobar si el dispositivo se reconoce como módem.


{% highlight bash %}
reboot
lsusb
{% endhighlight %}

Deberíamos obtener algo como esto `19d2:2003`, para comprobar que el dispositivo se ha detectado como módem GSM, para comprobarlo ejecutamos lo siguiente.


{% highlight bash %}
dmesg | grep -i usb
{% endhighlight %}

Y entre el resultado deberíamos encontrar algo como esto.

{% highlight bash %}
usb 1-1.3: GSM modem (1-port) converter now attached to ttyUSB0
{% endhighlight %}

Ahora podemos ir al directorio `/dev` y comprobar si realmente aparece la interfaz ttyUSB0 con `ls /dev | grep -i usb`. Estas interfaces serie se pueden emplear con un programa que gestione el puerto serie como `minicom`.

Cómo realizar una llamada de prueba con minicom y los comando AT
----------------------------------------------------------------

Primero instalamos y configuramos `minicom` para que emplee el puerto serie que deseamos, en nuestro ejemplo ttyUSB3.

{% highlight bash %}
apt-get install minicom
minicom -s
{% endhighlight %}

Seleccionamos 'Serial port setup' y en el siguiente menú pulsamos ‘A’ para seleccionar el puerto, para este ejemplo será /dev/ttyUSB3 pulsamos intro dos veces, después seleccionamos ‘Save setup as .. ‘ e introducimos una cadena de caracteres, por ejemplo gsm. Al terminar y salir usaremos el siguiente comando:

{% highlight bash %}
minicom gsm
{% endhighlight %}

Alternativamente también se puede emplear el comando `cu` con la opción `l` indicando después el puerto de USB, a partir de este momento se podrán enviar los comando AT y ver la respuesta en la pantalla.

{% highlight bash %}
cu -l /dev/USB0
{% endhighlight %}

Envío de comandos AT
--------------------

Test de comunicación que debe responder OK

{% highlight bash %}
AT
{% endhighlight %}

Introducimos el PIN de la tarjeta SIM debe responder OK

{% highlight bash %}
AT+CPIN=1234
{% endhighlight %}

Realizamos llamada de prueba al número de teléfono ficticio 123456789.

{% highlight bash %}
ATD123456789;
{% endhighlight %}

Existen muchos otros comandos AT interesantes que se pueden emplear y que se pueden consultar en internet, no obstante no me gustaría terminar la entrada sin indicar como cortar la llamada realizada lo que puede ser útil de cara a implementar sistemas que te avisen mediante llamadas perdidas.
Con la llamada el primer comando evita que se ignore el comando de hang up y el segundo es el el comando para colgar propiamente dicho.

{% highlight bash %}
AT+CVHU=0
ATH
{% endhighlight %}

Recepción y envío de de SMS de forma automatizada
-------------------------------------------------
-------------------------------------------------


Vamos a emplear `smstools` para ello instalamos el software, más información sobre la herramienta [aquí](http://smstools3.kekekasvi.com).

{% highlight bash %}
apt-get install smstools
{% endhighlight %}

Para rellenar el archivo de configuración necesitamos saber el puerto como hicimos antes, del timo `ttyUSB0`.

La configuración necesita editar un archivo .conf lo haremos así `nano /etc/smsd.conf`.

Para añadir lo siguiente,

{% highlight bash %}
devices = GSM1
outgoing = /var/spool/sms/outgoing
checked = /var/spool/sms/checked
incoming = /var/spool/sms/incoming
logfile = /var/log/smstools/smsd.log
infofile = /var/run/smstools/smsd.working
pidfile = /var/run/smstools/smsd.pid
outgoing = /var/spool/sms/outgoing
checked = /var/spool/sms/checked
failed = /var/spool/sms/failed
incoming = /var/spool/sms/incoming
sent = /var/spool/sms/sent
receive_before_send = no
autosplit = 3

[GSM1]
device = /dev/ttyUSB0
incoming = yes
baudrate = 115200
memory_start = 1
pin = 1111
{% endhighlight %}

El campo `boudrate` depende del modem, en el caso del ejemplo era un ZTE MF190.

Una vez configurado habrá que reiniciar el servicio con `systemctl restart smstools.service`. He observado que es necesario reiniciar una vez más el sericio para que lleguen los SMS.

Se puden leer los SMS recibidos en el directorio configurado por ejemplos así: `ls -l /var/spool/sms/incoming`. Si queremos ver que falla es útil revisar el log `/var/log/smstools/smsd.log`.

Envío de SMS
------------

Será tan sencillo como situar un archivo de texto en el directorio `/var/spool/sms/outgoing` con el formato siguiente.

{% highlight bash %}
To: 34666666666

Texto del SMS que deseamos enviar.
{% endhighlight %}

El teléfono debe incluir el prefijo del país sin el '+' y el texto debajo. Así de sencillo. Cuando la aplicación envía el SMS el programa mueve el fichero de texto a `/var/spool/sms/sent`.

Gestores de eventos y alarmas
-----------------------------

Se puede encontrar un ejemplo de gestor hecho por el desarrollador en `/usr/share/doc/smstools/examples/scripts/smsevent`. Para que funcione hay que indicarle al programa dónde encontrar el gestor/guión. Para ello añadimos con `nano /etc/smsd.conf` lo siguiente.

{% highlight bash %}
eventhandler = /home/yo/smstools/mi_gestor_de_eventos
{% endhighlight %}

El gestor de alarmas es muy parecido pero se dispara si ocurre un error. Añadimos a `/etc/smsd.conf` lo siguiente.

{% highlight bash %}
alarmhandler = /home/yo/smstools/mi_gestor_de_alarmas
{% endhighlight %}
