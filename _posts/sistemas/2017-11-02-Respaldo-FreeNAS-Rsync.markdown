---
layout: post
title:  "Respaldo con FreeNAS usando rsync desde Debian"
date:   2017-11-02 09:39:22
categories: sistemas
comments: false
tags:
- freenas
---

Se parte de que en FreeNAS se ha activado y configurado rsync en `Services --> Rsync --> Add Rsync Module`


Copiado desde Debian hacia FreeNAS
----------------------------------

Ejecutar la siguiente línea, donde


{% highlight bash %}
rsync -avz --delete /nombre_directorio_local IP_servidor_freenas::nombre_module_rsync
{% endhighlight %}

Copiado desde FreeNAS hacia Debian
----------------------------------

Ejecutar la siguiente línea, donde


{% highlight bash %}
rsync -avz IP_servidor_freenas::nombre_module_rsync /nombre_directorio_local
{% endhighlight %}

Cron es tu amigo
----------------

Editamos crontab como root para que haga el trabajo por nosotros cada día a las 18:30.

{% highlight bash %}
sudo crontab -e
{% endhighlight %}

Y añadimos la siguiente línea.
{% highlight bash %}
30 18 * * * rsync -az --delete  /nombre_directorio_local IP_servidor_freenas::nombre_module_rsync
{% endhighlight %}

Encendido y apagado autmático
-----------------------------

Para el encendido se podría usar `wol` --*wake on lan*-- pero en este caso se optará por mandar comando a la ILO de un Proliant GEN8 quedando:

Para encender:

{% highlight bash %}
ssh -t usuario@ip_ilo power on
{% endhighlight %}

Para apagar enviando el comando a freenas.

{% highlight bash %}
ssh -t root@10.0.0.10 'shutdown -p now'
{% endhighlight %}

El encendido del sistema local se programará en la bios, el apagado con el comando siguiente añadido al final del fichero `/etc/rc.local` indicando la hora y minuto en que se realizará el apagado.

{% highlight bash %}
shutdown -h #horas:#minutos
{% endhighlight %}


