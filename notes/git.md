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
- Para ver cambios podemos utilizar git diff para una vision general o git diff --staged para comprar los cambio con los archivos preparados. No termino de entender el parámetro HEAD. 
- Por otro lado tenemos git log que permite ver el historial de los commit, el parámetro --oneline permite verlo de forma más clara.

## Deshacer
- Para deshacer cambios en un archivo podemos utilizar git restore, para deshacer el git add podemos utilziar git restore --staged/
- Para corregir el mensaje del último commit podemos utilizar git commit --amend
- Para deshacer un commit podemos utilizar git reset --soft, que mantendrá los archivos preparados; o git reset --mixed que quitará los archivos del área de preparación.
- Para rehacer un commit con cambio podemos utilizar git revert

## .gitignore
- Ignora archivos que suelen crearse de forma automática 
- Con git rm --cached podemos limpiar los archivos ignorados

## Advertencias
- git reset --hard borra datos de forma definitiva
