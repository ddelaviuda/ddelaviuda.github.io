---
layout: post
title:  "Criptografía elemental"
date:   2016-09-05 18:39:22
categories: Seguridad
---

[Hoja de referencia rápida][referencia]

The *Gamma function* satisfying $\Gamma(n) = (n-1)!\quad\forall
n\in\mathbb N$ is via through the Euler integral

$$
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,.
$$

Instalación de Git
------------------

{% highlight bash %}sudo apt-get install git{% endhighlight %}

Iniciar repositorio Git
-----------------------

Nos situamos en el directorio de nuestra elección.

{% highlight bash %}cd /path{% endhighlight %}

Y iniciamos el repositorio, este creará un directorio .git

{% highlight bash %}git init{% endhighlight %}

Adición de archivos/directorios al repositorio
----------------------------------------------
Cuando está creado el repositorio podemos ejecutar desde el directorio el comando git status, que nos mostrará todos los archivos que están en él y que no han sido añadidos al repositorio ---más sobre esto después---. Podemos probara a ejecutarlo vacío y tras haber añadido un fichero de prueba, git nos indicará que el fichero no está en el repositorio.

Ejecutando el siguiente comando veremos como el fichero de prueba.txt se al seguimiento del repositorio, todavía no está incluido en él.

{% highlight bash %}
git add prueba.txt
{% endhighlight %}
Se pueden añadir varios archivos con el comodín * si usamos ''.
{% highlight bash %}
git add '*.txt'
{% endhighlight %}
Si ejecutasemos después git status git indicará que el repositorio está al día ---siempre que prueba.txt fuese el único archivo en el directorio---.

El fichero prueba.txt todavía sólo está en observación, si queremos añadir --cometer-- todos los ficheros en observación al respositorio ejecutamos el siguiente comando ---la opción -m permite añadir un comentario, es recomendable---.
{% highlight bash %}
git commit -m "comentario sobre la comisión"
{% endhighlight %}
Si queremos eliminar de observación (stage) un archivo añadido antes de una comisión ejecutamos.
{% highlight bash %}
git reset HEAD prueba.txt
{% endhighlight %}
Repositorios remotos (GitHub)
-----------------------------
**Subir**

Para guardar nuestro repositorio en remoto y hacer comisiones contra él será necesario darle un nombre y asociarle una URL, el repositorio de GitHub es muy práctico para ello. Se suele usar origin como nombre para el repositorio remoto.
{% highlight bash %}
git remote add origin https://github.com/usuario/nombre_repositorio
{% endhighlight %}
Para actualizar el repositorio remoto usamos push tal y como sigue ---la opción -u es para que git recuerde los parámetros y usando git push git añada origin y master, más sobre master después---. Es necesario que el directorio remoto no tenga nada en él, de otro modo el comando dará un error.
{% highlight bash %}
git push -u origin master
{% endhighlight %}
**Bajar**

Si se han hecho cambios en el repositorio remoto que ahora queremos tener ejectuamos
{% highlight bash %}
git pull origin master
{% endhighlight %}
Gestión de las diferencias
--------------------------
¿Queremos saber la diferencia entre el repositorio remoto y nuestro último commit? Ejecutamos
{% highlight bash %}
git diff HEAD
{% endhighlight %}
**Restaurar**

Si una vez modificado un archivo deseamos que este se recupere a su estado en la última comisión ejecutamos el siguiente comando indicando el nombre del archivo a recuperar.
{% highlight bash %}
git checkout -- octocat.txt
{% endhighlight %}
**Ramas**

Si queremos trabajar en una "versión" diferente del proyecto crearemos una rama para modificarla libremente ---en el ejemplo llamamos a la rama borrador---.
{% highlight bash %}
git branch borrador
{% endhighlight %}
Para pasar a utilizar la nueva rama ejecutamos.
{% highlight bash %}
git checkout borrador
{% endhighlight %}
Si queremos enumerar las ramas disponibles ejecutamos
{% highlight bash %}
git branch -a
{% endhighlight %}
En la nueva rama podremos hacer varias modificaciones, por ejemplo borrar archivos en el directorio y en la rama.
{% highlight bash %}
git rm '*'
{% endhighlight %}
Una vez borrado, y para que esto se traslade al repositorio, ejecutamos.
{% highlight bash %}
git commit -m "Borrado de archivos"
{% endhighlight %}
Si queremos que los cambios en una rama se materialicen en la maestra (master) haremos lo que sigue:

Accedemos a la rama maestra.
{% highlight bash %}
git checkout master
{% endhighlight %}
Y unimos la rama alternativa ---llamada borrador en el ejemplo--- a la maestra.
{% highlight bash %}
git merge borrador
{% endhighlight %}
Si queremos eliminar la rama alternativa ---llamada borrador--- suponiendo que no sea necesaria.
{% highlight bash %}
git branch -d borrador
{% endhighlight %}
**Respaldo remoto**

Si queremos sincronizar el repositorio local con un repositorio remoto ejecutamos el proceso en dos pasos.

Búsqueda...
{% highlight bash %}
git fetch origin master
{% endhighlight %}
... y respaldo.
{% highlight bash %}
git reset --hard origin/master
{% endhighlight %}

[referencia]: https://services.github.com/kit/downloads/es_ES/github-git-cheat-sheet.pdf
