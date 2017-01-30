---
layout: post
title:  "Servicio personalizado con un script en systemctl"
date:   2017-01-12 18:39:22
categories: sistemas
tags:
- script
comments: true
---

Creación del guión/script
-------------------------

Creamos un guión para que pase un USB GSM a modalicad modem con el comando `nano /usr/local/sbin/gsm-modem.sh`

{% highlight bash %}
#!/bin/bash
usb_modeswitch --default-vendor 0x19d2 --default-product 0x2000 --target-vendor 0x19d2 --target-product 0x0031  --message-endpoint 0x01 --message-content 55534243123456782000000080000c85010101180101010101000000000000
{% endhighlight %}

Y le damos permisos de ejecución con `chmod +x /usr/local/sbin/gsm-modem.sh`.

Adición para arranque como servicio
-----------------------------------

Ahora creamos un servicio con el comando `nano /etc/systemd/system/gsm-modem-cambio.service`

{% highlight bash %}
[Unit]
Before=smstools.service

[Service]
ExecStart=/usr/local/sbin/modem_gsm.sh

[Install]
WantedBy=default.target
{% endhighlight %}

Una vez creado reiniciamos el demonio systectl con `systemctl daemon-reload` y habilitamos el servicio `systemctl enable gsm-modem-cambio.service`.

Listo para arrancar en el siguiente reinicio.

Con el comando `systemd-analyze plot > /tmp/plot.svg` podemos generar un archivo `.svg` para ver el gráfico del la secuencia de arranque.
