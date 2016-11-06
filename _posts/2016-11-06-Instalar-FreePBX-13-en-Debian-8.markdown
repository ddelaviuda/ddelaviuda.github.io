---
layout: post
title:  "Instalación de FreePBX 13 en Debian 8"
date:   2016-11-04 18:39:22
categories: sistemas
comments: true
---
Información obtenidad de [aquí](http://wiki.freepbx.org/display/FOP/Installing+FreePBX+13+on+Debian+8.1).


Configuración inicial
---------------------

{% highlight bash %}
$ sudo sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config
$ sudo service sshd restart
{% endhighlight %}
