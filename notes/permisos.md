# Chuleta de permisos

## Cómo leer ls -l
Los 10 caracteres:
- 1 de tipo + 9 de permisos
- Cada grupo de tres representa: primero al dueño del archivo (u), segundo a los miembros del grupo asignado al archivo (g), tercero a los demás (o)
- R representa readable, W respresente writable, X representa executable que permite en directorio permite entrar y acceder a lo que hay dentro. W + X juntos permite crear borrar y renombrar entradas.

### Octal:
- r=4, w=2, x=1
- Los ocho digitos son: 7 (rwx), 6(rw-), 5(r-x), 4(r--), 3(-wx), 2(-w-), 1(--x), 0(---)
- A veces al hacer ls -l puede salir algo como 'drwxrwxrwx', en este caso la d no cuenta puesto que su función simplemente es indicar que se trata de un directorio.

### Bits especiales:
- setuid=4 | junto a chmod sería 4xxx | aparece como una s sustituyendo la x del dueño (-rws------) el programa se ejecuta con los permisos del dueño 
- setgid=2 | junto a chmod sería 2xxx | aparece como una s sustituyendo la x del grupo de miembros de grupo (-rwxrws---)
- sticky=1 | junto a chmod sería 1xxx | aparece como una t sustituyendo la x del grupo de otros (-rwxrwxrwt) 

### Ejemplos: 
- chmod 640 | -rw-r----- | el dueño del archivo puede leerlo y escribir en él, los miembros del grupo pueden leerlo y los demás no pueden hacer nada
- chmod 755 | -rwxr-xr-x | el dueño tiene todos los permisos, los miembros del grupo pueden leer los archivos y ejecutarlos  y los demás también pueden leer y ejecutarlos.
- chmod u=rw,g=r,o= | 640 | -rw-r----- | indica que el dueño puede leer y escribir, y los miembros del grupo sólo leer, los demás no tienen nada
- /srv/compartido con 2770 | el 2 indica que tiene el bit especial setgid (lo que se cree dentro hereda el grupo de la carpeta) y el 770 indica que tanto el dueño como los miembros del grupo tienen los permisos máximos
- /srv/publico con 1777 | el 1 indica que tiene el bit especial sticky (en una carpeta, sólo el dueño de un archivo o el dueño del directorio o el usuario root, pueden borrarlo o renombrarlo) y el 777 indica que los tres grupos (dueño, miembros de grupo, otros) tienen todos los permisos (leer, escribir, ejecutar)
- Si el directorio tiene permisos 777 pero se crea un archivo de texto 644 implica que un usuario que no es el dueño del archivo no puede escribir en él, pero puede renombrarlo o eliminarlo puesto que eso lo rigen los permisos del diretorio. Esto cambia si los permisos tienen el bit especial sticky.
