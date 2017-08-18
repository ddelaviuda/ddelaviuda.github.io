---
layout: post
title:  "Acceso ssh usando clave pública/privada"
date:   2017-11-04 09:39:22
categories: sistemas
comments: false
tags:
- ssh
---

El escenario consiste en un equipo remoto al que queremos acceder --servidor ssh-- con ip_remota y un equipo local --cliente ssh-- e ip_local.


Creación de la clave pública y privada
--------------------------------------

Ejecutar la siguiente línea dentro del servidor local.

{% highlight bash %}
ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/pi/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
{% endhighlight %}

La localización por defecto es `/home/pi/.ssh/id_rsa` pero se puede modificar, también se puede añadir una contaseña --*passphrase*-- para acceder a la clave, en el ejemplo la dejaremos en blanco con la localización y nombre por defecto.

Copia de la clave pública en servidor remoto
--------------------------------------------

La clave pública tendra el nombre `id_rsa.pub` y la copiaremos en el servidor remoto, por ejemplo como sigue:

{% highlight bash %}
scp ~/.ssh/id_rsa.pub usurio_remoto@ip_remota:~/
{% endhighlight %}

Una vez copiada la clave pública debermos añadirla al archivo `~/.ssh/authorized_keys` por ejemplo así:

{% highlight bash %}
ssh -t usurio_remoto@ip_remota 'cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys'
{% endhighlight %}

Una vez hecho esto ya podremos acceder al servidor remoto sin necesidad de contraseña.

{% highlight bash %}
ssh usurio_remoto@ip_remota
{% endhighlight %}
