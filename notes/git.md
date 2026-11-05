# Chuleta de Git

## Las tres zonas 
- Working Directory: copia de un repositorio donde edito y hago cambios
- Staging Area: archivos preparados para ser confirmados
- Git Directory: la parte más importante, lo que se copia para poder trabajar en el proyecto

## Los tres estados
- Modified: modificado, cambiado
- Staged: preparado para ser confirmado
- Committed: confirmado

## Ciclo
- Status: comprobar el  estado de los archivos
- Add: prepara archivos para ser confirmados
- Commit: confirma los archivos preparados previamente

## Ver cambios
- Para ver cambios podemos utilizar git diff para una vision general o git diff --staged para comparar los cambios con los archivos preparados.
- El parámetro HEAD me permite actuar sobre el commit en el que estoy ahora, HEAD~1 me permite atuar sobre el commit anterior al que estoy ahora. 
- Por otro lado tenemos git log que permite ver el historial de los commit, el parámetro --oneline permite verlo de forma más clara.

## Deshacer
- Para deshacer cambios en un archivo podemos utilizar git restore, para deshacer el git add podemos utilizar git restore --staged/
- Para corregir el mensaje del último commit podemos utilizar git commit --amend
- Para deshacer un commit podemos utilizar git reset --soft, que mantendrá los archivos preparados; o git reset --mixed que quitará los archivos del área de preparación.
- Para rehacer un commit con cambio podemos utilizar git revert

## .gitignore
- Ignora archivos que suelen crearse de forma automática 
- También permite ocultar archivos delicados y no subirlos por error
- Con git rm --cached podemos dejar de rastrear archivos que están siendo rastreados, los saca de control de versiones pero no los borra

## Advertencias
- git reset --hard borra datos de forma definitiva

## Ramas y conflictos
- Una rama es un espacio de trabajo donde puedo contribuir a un proyecto de forma local sin miedo a romperlo. La rama main/master es la que viene por defecto al crear un repositorio nuevo, la siguiente rama que cree será una copia de esta en su último commit.
- Para crear una rama nueva puedo utilizar git branch (nombre), pero esto no me mueve a la rama directamente y tendría que utilizar git switch o git checkout para moverme a esta
- Los comandos git checkout -b y git switch -c crean una rama nueva y se mueve directamente a esta
- El comando git merge permite fusionar ramas, si al crear una rama a partir de main sólo he confirmado en la nueva y no he actualizado main, al hacer merge desde main este será fast-forward. En cambio si sí han habido commits nuevos en ambas ramas, Git tendrá que hacer trabajo extra buscando un ancestro común ideal para hacer una fusión que tendrá dos parent
- En ocasiones, pueden surgir conflictos al hacer un merge, como por ejemplo cuando se ha editado una misma línea de un archivo en dos ramas diferentes. En ese caso Git pausará el merge y esperará a que resuelvas el conflicto de forma manual. Deja indicaciones para facilitar el trabajo. Una vez resuelto esto, se hace un commit.
- También se puede ejecutar el comando merge --abort para parar la fusión
- Origin es el alias por defecto del término "remoto". Cuando vemos en GitHub origin/main es que se trata de la rama main en remoto.
- El comando push -u asigna una rama local a una remota

## Ciclo de PR
1. Creo una rama
2. Trabajo en la rama, hago cambios y confirmo
3. Creo una Pull Request
4. Reviso el feedback
5. Hago merge de la Pull Request
6. Borro la rama
