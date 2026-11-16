# Diario día 10

## Qué aprendí
- Qué es una VM en la nube
- Como funciona entrar con clave SSH
- Leer el comando `free -h`, qué representa cada columna, en qué fijarme para detectar problemas de memoria

## Práctica
- Hoy he construido una VM. Ya lo hice en el primer día, pero entonces no entendía lo que hacía, sólo seguía instrucciones
- He practicado a manejarme con las claves ssh para entrar en la VM
- He puesto en práctica los comandos `sudo apt update`, `apt list --upgradable`, `sudo apt upgrade`, `sudo apt install`, `ssh-keygen`, `chmod`, `hostnamectl` y `timedatectl`.

## Errores y cómo los resolví
- Creé la máquina virtual pero se me pasó que tengo que instalarle Ubuntu, por lo que cuando le di a `Start` esperaba que apareciese la IP pero no era así. Al darle `Shutdown` terminó saltándome el error de que el agente invitado no estaba trabajando y entonces caí en lo que faltaba y pasé a la instalación desde la consola de Proxmox.
- Instalando Ubuntu, mientras escribía lo que sería el usuario y contraseña, saltó un error inesperado que no supe identificar. Le di a reboot y se quedó pensando. Salí de la consola y le di a reboot desde Proxmox y saltó error timeout porque el agente qemu no estaba corriendo. Sin embargo, pude volver a iniciar la instalación desde la consola otra vez, pero se quedaba pensando en el primer paso. 
- Al final mi solución fue empezar desde cero ya que no quería recurrir directamente a Claude para solucionarme el problema. Eliminé y purgué la máquina y empecé a crear una nueva, así también practicaba el proceso. Una vez terminada caí en el error, al no recordar instalar Ubuntu, le di a Start en Proxmox directamente, cuando lo que tenía que haber hecho era entrar en la Consola y empezar desde allí. Haciendo este proceso en la VM nueva, todo ha funcionado a la perfección.

## Resumen del día
- Montar una VM hoy no ha sido un proceso alienígena que me limitaba a seguir unas instrucciones al pie de la letra. Hoy he entendido lo que hacía y me he enfrentado a mis errores con lógica, volviendo sobre mis pasos no sólo para solucionar el problema, sino también para afianzar lo aprendido por el camino y practicar más si cabe. Ha sido un día completo donde he podido comprobar que, aunque las cosas no me salgan a la primera, empiezo a buscar soluciones de forma natural en vez de estamparme y pedir ayuda presa del agobio. 
