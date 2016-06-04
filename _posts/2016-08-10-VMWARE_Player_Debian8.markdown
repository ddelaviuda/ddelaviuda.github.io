---
layout: post
title:  "Instalación de VMWARE Player en Debian 8"
date:   2016-08-10 10:39:22
categories: redes
comments: true
---

Descarga del *bundle*
---------------------

Descargamos la última versión de VMWARE Player de la página `www.vmware.com`, en el momento de escribir esta entrada era `http://www.vmware.com/go/tryplayerpro-linux-64`

Instalación de requerimientos
-----------------------------

Instalamos el complilador de C.

{% highlight bash %}
sudo apt-get install gcc
{% highlight bash %}

Y las cabeceras del núcleo.

{% highlight bash %}
sudo apt-get install linux-headers-$(uname -r)
{% highlight bash %}

Instalación
-----------

Una vez descargado damos permisos de ejecución al archivo.

{% highlight bash %}
chmod +x VMware-Player-12.1.1-3770994.x86_64.bundle
{% endhighlight %}


Despúes, mandamos a `sh` que ejecute el *bundle*.
{% highlight bash %}
sudo ./VMware-Player-12.1.1-3770994.x86_64.bundle
{% endhighlight %}


En el menú del sistema buscamos VMWARE para ejecutar el programa.

