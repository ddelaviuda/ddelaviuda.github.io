---
layout: post
title:  "CCNA: Dominios de colisión y broadcast"
date:   2016-07-08 10:39:22
categories: redes
comments: true
---

Dominio de colisión
-------------------

Los dominios de colisión son los puntos de la red en que los mensajes pueden “chocar”, este chocar debe entenderse como el momento en que dos o más mensajes son enviados compartiendo el mismo medio físico a la vez. Cuando esto pasa, el mensaje a priori habrá sido alterado o no se puede asegurar que el receptor lo haya recibido correctamente, es por esto que *Ethernet* implementa CSMA/CD (*Carrier sense multiple access with collision detection*) para detectar las colisiones, descartar los tramas (*frames*) —las colisiones están asociadas a la capa 2 del modelo OSI— y proceder con la secuencia de reenvío retrasada un tiempo aleatorio.

![collision_domains]({{ site.url }}/img/redes/collision_domains.jpg)



El el gráfico superior se puede observar la red de ordenadores con elipses sobre las áreas que comparten el mismo dominio de colisión. Se puede extraer de esta imagen que los *hubs* (equipo con la H) —que se encuentran en deshuso— no dividen el dominio de colisión, más bien lo amplian. Mientras, los puertos de los *Switches* (SW) y de los *Routers* (R1) si separan los dominios de colisión.

Queda añadir que cuando nos referimos a dominio de colisión y su medio de transmisión, estamos hablando de cualquier medio físico, no sólo a los cables UTP/STP o coaxiales, estas colisiones también se pueden producir en las transmisiones radio como WiFi que emplea CSMA/CA (*Carrier sense multiple access with collision avoidance*) para atajar este problema.

Dominio de *broadcast*
------------------------

Un dominio de *broadcast* —que podríamos traducir como dominio de difusión— es una separación lógica dentro de la red de ordenadores en la que los mensajes, normalmente paquetes de capa 3 en el modelo OSI, pueden ser difundidos —siempre que cumplan ciertas condiciones— para que todos los equipos dentro de ese espacio, definido lógicamente, los puedan recibir. Los equipos que no limitan estos dominios, además de los *Hub*, son los *switches* y los *bridges* —equipos con pocos puertos y menor ‘inteligencia’ que los *switches*—.

![broadcast_domains]({{ site.url }}/img/redes/broadcast_domains.jpg)

Tal y como se puede ver en la figura, los equipos que limitan los dominios de *broadcast* por antonomasia son los *router* —aunque también hay *switches* que pueden limitar esta difusión creando VLANs—. En la imagen se ve como los puertos del *router* son los que marcan la división de los dominios de *broadcast* y para remarcarlo, al igual que se hizo en el dominio de colisión, se han empleado elipses para señalar cada uno de estos dominos de *broadcast*.
