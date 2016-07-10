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

Además, debe cumplirse que: ![codigo]({{ site.url }}/_image/definicion_codigo_cripto.png) 
{% raw %}
\\[\forall \,m \in M, k\in K: D(k,E(k,m))=m \\]
$$a^2 + b^2 = c^2$$
{% endraw %}
*Eficiente aquí se refiere a que la ejecución de estos algoritmos debe producirse en tiempo polinomial.

El algoritmo E puede tener una componente aleatoria en su salida pero el algoritmo D siempre es determinista.

Ejemplo característico: Libreta de un sólo uso (*one time pad*)
---------------------------------------------------------------
El "tamaño" del mensaje m, la clave k y el cifrado c son iguales a n.

{% raw %}
$$M={0,1}^n, K={0,1}^n y C={0,1}^n$$
{% endraw %}
El algoritmo de cifrado y de descifrado es la operación o-exclusiva (XOR).

c=k⊕m y m=k⊕c
{% raw %}
\\(\frac{\delta E_{x}}{\delta t} = \frac{\delta f(z-ct)}{\delta t} = f^{\prime}(z - ct)\Big(\frac{\delta(z-ct)}{\delta t}\Big) = -c*f^{\prime}(z - ct)\\)
{% endraw %}
Teoría de la seguridad en la información (Shanon 1949)
------------------------------------------------------

La idea intuitiva es que c no debe revelar información sobre m. Sin embargo la definición formal es más compleja como se verá a continuación.

Un código de cifrado tiene **secreto perfecto** si:
\forall \,m_0,m_1\, con\, y\,\forall c\in C
longitud(m_0)=longitud(m_1)
P(E(k,m_0)=c)=P(E(k,m_1))
k es uniforme en el espacio K, esto es k \xleftarrow{R} K

Teorema: Para que un código posea "secreto perfecto"
----------------------------------------------------
\mid k\mid\geq \mid K\mid

Es decir, la longitud de k debe ser mayor o igual a la longitud de m, el código *one time pad* cumple la condición el el límite.

