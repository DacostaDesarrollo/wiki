# GIT – Guía rápida

**Notas:**

- Cambios locales se refiere a los ficheros del directorio de trabajo (working dir) que hayan sido modificados desde el último commit.
- COMMIT es un indicador cualquiera en el repositorio: SHA1 de un commit, tag, HEAD (ultimo commit de la rama actual), HEAD~1 (antecesor de HEAD)… La mayoría de las veces, si no se indica se asume HEAD (último commit de la rama actual).


**Enlaces:**
- [http://git-scm.org](http://git-scm.org)
- [http://www-cs-students.stanford.edu/~blynn/gitmagic/intl/es/ch02.html](http://www-cs-students.stanford.edu/~blynn/gitmagic/intl/es/ch02.html)
- [https://www.edy.es/dev/docs/git-guia-rapida/](https://www.edy.es/dev/docs/git-guia-rapida/)

## Inicializar repositorio
```bash
$ git init
$ git add .                           # Marca todos los ficheros para el próximo commit.
$ git commit -m "Estado inicial"      # Crea un nuevo commit con los ficheros marcados.
```

## Crear ramas
Una rama es un puntero a un commit específico en el repositorio.
```bash
$ git branch <nombre>                 # Crear la rama en el punto actual. Es necesario hacer checkout a la misma.
$ git branch <nombre> <COMMIT>        # Crea la rama a partir del commit dado. Es necesario hacer checkout para cambiar a la rama.
$ git checkout -b <nombre>            # Crear rama en el punto actual y hacerle checkout.
$ git checkout -b <nombre> <COMMIT>   # Crear la rama a partir del commit dado y hacerle checkout.
$ git branch -m <actual> <nuevo>      # Renombrar la rama
$ git branch -d <nombre>              # Borrar la rama
```

Al crear una rama nueva y hacerle checkout los cambios locales se trasladan a esa rama, con lo que el siguiente commit será sobre la rama nueva.

## Listar ramas

```bash
$ git branch                          # Listar todas las ramas
$ git branch -v                       # Mostrar último commit en cada rama y su situación respecto a su rama remota (si hay)
$ git branch --merged                 # Mostrar ramas que se han fusionado con la actual, y por tanto pueden borrarse
$ git branch --no-merged              # Mostrar ramas con trabajos sin funsionar. Intentar borrarlas dará un error.
```

## Moverse a una rama o a un commit específico

```bash
$ git checkout <COMMIT>            # No toca los cambios locales
$ git checkout -f <COMMIT>         # Sobreescribe los cambios locales
```
Se puede hacer una rama desde el commit actual para continuar el desarrollo (git branch). Si se hacen cambios en un commit intermedio (no es un HEAD) y se commitean sin hacer una rama, se crea un commit separado que sale del actual (rama sin nombre).

## Fusionar ramas (merge)
```bash
$ git merge <nombre>               # Fusiona la rama indicada en la rama actual
```
Las diferencias se resuelven automáticamente si es posible. En caso de conflictos (código o ficheros binarios modificados en ambas ramas) el proceso se detiene (merging) a la espera de una resolución manual.

##### Resolver conflictos de fusionado:

Dentro de cada fichero en conflicto se añaden marcas alrededor del código conflictivo, mostrando al mismo tiempo la versión de una y otra rama (excepto en ficheros binarios).

```bash
$ git status                       # Muestra la situación actual del merge (Unmerged paths)
$ git diff                         # Muestra los ficheros conflictivos y las diferencias
$ git add <file>                   # Marca el fichero como corregido una vez resuelto el conflicto
$ git rm <file>                    # Marca el fichero como eliminado en la revisión resuelta
```
La sección “Unmerged paths” de git status muestra los ficheros que requieren atención. Debe resolverse cada conflicto manualmente dentro del fichero (eliminando las marcas agregadas por git) y marcarlo como resuelto con git add.

En vez de editar los ficheros es posible escoger una de las dos versiones disponibles (rama actual o rama que se está fusionando):

```bash
$ git checkout --ours -- <file>    # Obtener la versión del fichero en la rama actual
$ git checkout --theirs -- <file>  # Obtener la versión del fichero en la rama que se está fusionando con la actual
```

Para abortar la acción o anularla una vez realizada:

```bash
$ git reset --hard HEAD            # Abortar el proceso y volver a la situación anterior al intento de merge
$ git reset --hard ORIG_HEAD       # Deshacer si ya se había confirmado con git commit actual
```
Una vez resueltos todos los conflictos se confirma el proceso:
```bash
$ git commit                       # Confirmar la fusión (merge) una vez resueltos todos los conflictose merge
```