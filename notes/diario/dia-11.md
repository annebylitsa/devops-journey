# Diario día 11

## Qué aprendí
- Qué es realmente SSH y cómo funciona y qué comprobaciones hace
- La diferencia entre Host y Hostname
- El archivo `~/.ssh/config`
- Qué es el `ssh-agent`, qué hace y cómo configurarlo
- Copiar archivos con scp y rsync
- Qué son los puertos, cómo funcionan y qué son los túneles
- Por qué hay que endurecer el SSH y por qué

## Práctica
- Hoy me he peleado, sobre todo, con todo lo que tiene que ver con `ssh`. He configurado los valores de los ajustes que afectan a las formas de entrar en la máquina virtual
- He estado constantemente entra la VM y la PowerShell, lo cuál ha llegado a ser lioso porque he terminado poniendo comandos que deberían ir en la PowerShell en la VM, por lo que no funcionaban y me atascaba
- He configurado la forma de entrar en la VM por alias, sin tener que escribir el comando entero
- También he aprendido a poner en funcionamiento el `ssh-agent`, teniendo que activarlo primero en mi ordenador ya que venía desactivado. Para ello tuve que entrar en la PowerShell como administrador y ejecutar `Set-Service ssh-agent -StartupType Manual` y luego `Start-Service ssh-agent`. Comprobé que estaba funcionando con `Get-Service ssh-agent` y vi que estaba `Running`, todo en orden
- Cerré la puerta de la contraseña, haciendo que la única forma válida de entrar sea por clave
- Practiqué los comandos `scp` y `rsync`, viendo cómo funcionan. Sobre todo `rsync` y su forma de liarla si no pongo bien las barras
- Creé mi primer túnel SSH

## Errores y cómo los resolví
- En vez de añadir la información de la nueva VM en el archivo `config` dentro de `\.ssh`, sólo añadí la fila de `IdentityFile` a los datos de la VM que ya figuraba en ese archivo. Eso hizo que no pudiera entrar en la VM con `ssh devops-lab2` porque no era un host válido. Tuve que reeditar el archivo, añadiendo todos los datos necesarios.
- En el comando `sudo tee /etc/ssh/sshd.config.d/00-endurecer.conf` en vez de escribir lo que necesitaba manualmente y hacer saltos de línea manuales con enter, lo pegué, lo cuál desordenó la información. Terminé el proceso y simplemente eliminé el archivo y repetí el comando, esta vez bien.
- También escribí el scp dentro de la PowerShell sin destino ni ~, no me libré de mis errores con cp, los he traído también a scp.

## Resumen del día
- Tenía un poco de miedo de empezar a ver cosas de la red y contraseñas y todo lo que tiene que ver con esto ya que son cosas delicadas e intento ir con pies de plomo para no romper nada ni cometer errores garrafales. Esto ha resultado en que casi no he resuelto cosas por mi misma, por miedo, por no querer romper nada. Sin embargo, creo que a pesar de todo tampoco me he estancado demasiado y he avanzado más o menos bien. Me falta práctica y experiencia para quitarme este miedo.
