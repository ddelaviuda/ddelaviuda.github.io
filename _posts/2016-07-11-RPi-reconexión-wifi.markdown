---
layout: post
title:  "Reconexión WiFi automática con Raspberry Pi + Debian (Raspbian)"
date:   2016-07-11 18:39:22
categories: sistemas
---
La configuración por defecto hace que cuando se pierde la conexión con el AP por reinicio o corte Raspbian no intenta conectar de nuevo. Para solucionar este pequeño inconveniente haremos las siguiente operación.

Haremos una copia de seguridad de **/etc/ifplugd/action.d/ifupdown**.

{% highlight bash %}
sudo mv /etc/ifplugd/action.d/ifupdown /etc/ifplugd/action.d/ifupdown.original
{% endhighlight %}

Copiamos otro archivo a este directorio.

{% highlight bash %}
sudo cp /etc/wpa_supplicant/ifupdown.sh /etc/ifplugd/action.d/ifupdown
{% endhighlight %}

Y reiniciamos.

{% highlight bash %}
sudo reboot
{% endhighlight %}

¡Hecho!
