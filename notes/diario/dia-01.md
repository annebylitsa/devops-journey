# Diario día 1

## Qué aprendí
- Qué es DevOps y por qué es importante
- Qué son las CI/CD pipelines
- Conceptos como Terminal, Shell, VCS, VM
- Linux, su konsole, bash y comandos base

## Práctica
- Accedí por primera vez a Proxmox y creé una máquina virtual de cero
- Empecé a utilizar git
- Configuré autenticación por ssh entre la VM y GitHub

## Errores y cómo los resolví
- Escribí mal un comando (puse mal &&) por lo que no funcionó, error básico que resolví revisando lo escrito y corrigiéndolo.
- Al configurar la ssh keygen recibí el error "Permission denied (publickey)", lo cual resultó ser porque pegué mal la clave generada en GitHub. Simplemente volví a hacer el proceso, eliminando la clave y añadiendo la nueva.
- Otro error con el que me topé, aunque más al principio, fue u nmensaje de fallo en la instalación de la VM "Failed unmounting cdrom.mount...". Al parecer se trata de algo común en estos casos ya que el instalador no pudo expulsar la ISO ya que proxmox lo mantiene insertado. La solución ha sido darle a Enter que era lo que pedía el mensaje. 

## Resumen del día
- Me he adentrado de lleno en materia que es bastante nueva para mi. Estoy familiarizada con JavaScript, por lo que lo que manejarme con esto no ha sido tan complicado. La instalación y configuración ha sido ciertamente complicada sobre todo por la cantidad de pasos que había que dar. Sin embargo, la parte más delicada ya está hecha.
