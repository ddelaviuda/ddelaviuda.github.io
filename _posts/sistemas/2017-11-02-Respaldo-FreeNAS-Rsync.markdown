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
Copiado desde Debian hacia FreeNAS
----------------------------------

Ejecutar la siguiente línea, donde 


{% highlight bash %}
rsync -avz --delete IP_servidor_freenas::nombre_module_rsync /nombre_directorio_local
{% endhighlight %}

