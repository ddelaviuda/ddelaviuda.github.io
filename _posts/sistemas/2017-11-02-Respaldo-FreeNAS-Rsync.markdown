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

*Configuración de acceso ssh mediante clave pública/privada*

Generación de las claves en el servidor ssh, se pedirá por pantalla confirmar la localización de los archivos a generar y una contraseña (passphrase) que se podrá omitir o introducir dos veces.

{% highlight bash %}
ssh-keygen -b 4096
{% endhighlight %}

La localización por defecto es `~/.ssh/id_rsa` para la clave privada y `~/.ssh/id_rsa.pub` para la clave pública.

Apagado remoto de servidor Linux.

{% highlight bash %}
ssh -t root@10.0.0.10 'shutdown -p now'
{% endhighlight %}