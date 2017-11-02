---
layout: post
title:  "Respaldo con FreeNAS usando rsync desde Debian"
date:   2017-11-02 09:39:22
categories: sistemas
comments: false
tags:
- proxy
---

Se parte de que en FreeNAS se ha activado y configurado rsync en Services --> Rsync --> Add Rsync Module


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