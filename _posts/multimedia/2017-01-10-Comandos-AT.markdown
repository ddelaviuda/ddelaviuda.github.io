---
layout: post
title:  "Comandos AT modem GSM y Debian"
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