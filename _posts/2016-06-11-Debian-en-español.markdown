---
layout: post
title:  "Cambiar idioma a Castellano en Debian"
date:   2016-07-11 18:39:22
categories: sistemas
---

Ejecutamos el programa *dpkg*

{% highlight bash %}
dpkg-reconfigure locales
{% endhighlight %}

De entre las opciones presentadas optamos por selecionar es_ES.UTF-8

Para comprobar que todo funciona correctamente ejecutamos.

{% highlight bash %}
env | grep LANG
{% endhighlight %}

Hecho.

