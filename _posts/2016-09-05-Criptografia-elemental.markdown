---
layout: post
title:  "Criptografía elemental"
date:   2016-07-08 18:39:22
categories: Seguridad
---
Definición de código criptográfico
----------------------------------

Un código definido en K, M y C con un par de de algoritmos eficientes* (E,D) donde:

E=KxM→C

D=KxC→M

Además, debe cumplirse que: ![codigo]({{ site.url }}/_image/definicion_codigo_cripto.png) \forall \,m \in M, k\in K: D(k,E(k,m))=m.

*Eficiente aquí se refiere a que la ejecución de estos algoritmos debe producirse en tiempo polinomial.
