---
layout: post
title:  "Lo básico de Git y GitHub"
date:   2016-07-05 18:39:22
categories: Git GitHub
---

Hoja de referencia rápida
Instalación de Git

sudo apt-get install git

Iniciar repositorio Git

Nos situamos en el directorio de nuestra elección.

cd /path

Y iniciamos el repositorio, este creará un directorio .git

git init

Adición de archivos/directorios al repositorio

Cuando está creado el repositorio podemos ejecutar desde el directorio el comando git status, que nos mostrará todos los archivos que están en él y que no han sido añadidos al repositorio ---más sobre esto después---. Podemos probara a ejecutarlo vacío y tras haber añadido un fichero de prueba, git nos indicará que el fichero no está en el repositorio.

Ejecutando el siguiente comando veremos como el fichero de prueba.txt se al seguimiento del repositorio, todavía no está incluido en él.

git add prueba.txt

Se pueden añadir varios archivos con el comodín * si usamos ''.

git add '*.txt'

Si ejecutasemos después git status git indicará que el repositorio está al día ---siempre que prueba.txt fuese el único archivo en el directorio---.

El fichero prueba.txt todavía sólo está en observación, si queremos añadir --cometer-- todos los ficheros en observación al respositorio ejecutamos el siguiente comando ---la opción -m permite añadir un comentario, es recomendable---.

git commit -m "comentario sobre la comisión"

Si queremos eliminar de observación (stage) un archivo añadido antes de una comisión ejecutamos.

git reset HEAD prueba.txt

Repositorios remotos (GitHub)
Subir

Para guardar nuestro repositorio en remoto y hacer comisiones contra él será necesario darle un nombre y asociarle una URL, el repositorio de GitHub es muy práctico para ello. Se suele usar origin como nombre para el repositorio remoto.

git remote add origin https://github.com/usuario/nombre_repositorio

Para actualizar el repositorio remoto usamos push tal y como sigue ---la opción -u es para que git recuerde los parámetros y usando git push git añada origin y master, más sobre master después---. Es necesario que el directorio remoto no tenga nada en él, de otro modo el comando dará un error.
{% highlight sh %}
$ git push -u origin master
{% endhighlight %}
Bajar

Si se han hecho cambios en el repositorio remoto que ahora queremos tener ejectuamos

git pull origin master

Gestión de las diferencias

¿Queremos saber la diferencia entre el repositorio remoto y nuestro último commit? Ejecutamos

git diff HEAD

Restaurar

Si una vez modificado un archivo deseamos que este se recupere a su estado en la última comisión ejecutamos el siguiente comando indicando el nombre del archivo a recuperar.

git checkout -- octocat.txt

Ramas

Si queremos trabajar en una "versión" diferente del proyecto crearemos una rama para modificarla libremente ---en el ejemplo llamamos a la rama borrador---.

git branch borrador

Para pasar a utilizar la nueva rama ejecutamos.

git checkout borrador

Si queremos enumerar las ramas disponibles ejecutamos

git branch -a

En la nueva rama podremos hacer varias modificaciones, por ejemplo borrar archivos en el directorio y en la rama.

git rm '*'

Una vez borrado, y para que esto se traslade al repositorio, ejecutamos.

git commit -m "Borrado de archivos"

Si queremos que los cambios en una rama se materialicen en la maestra (master) haremos lo que sigue

Accedemos a la rama maestra.

git checkout master

Y unimos la rama alternativa ---llamada borrador en el ejemplo--- a la maestra.

git merge borrador

Si queremos eliminar la rama alternativa ---llamada borrador--- suponiendo que no sea necesaria.

git branch -d borrador

Respaldo remoto

Si queremos sincronizar el repositorio local con un repositorio remoto ejecutamos el proceso en dos pasos.

Búsqueda...

git fetch origin master

... y respaldo.

git reset --hard origin/master

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].

[jekyll-gh]: https://github.com/jekyll/jekyll
[jekyll]:    http://jekyllrb.com
