# Reconstruir una VM Ubuntu Server en Proxmox

## Objetivo
Levantar una máquina virtual funcional donde trabajar en distintos proyectos de forma aislada, sin afectar a otros entornos. Al terminar, la VM tiene Ubuntu Server 24.04 actualizado, nombre y zona horaria correctos, herramientas básicas y entrada por SSH con clave.

## Qué necesitas
- Un servidor **Proxmox** con acceso a su interfaz web y espacio para una VM de 25 GB.
- La ISO `ubuntu-24.04.5-live-server-amd64.iso` ya subida a Proxmox.
- Un ordenador con **Windows PowerShell** y el cliente SSH (`ssh`, `ssh-keygen`).

## Valores de la VM
| Ajuste | Valor |
|---|---|
| Nombre | `NOMBRE-VM` (ej. `devops-lab2`) |
| Sistema | ISO Ubuntu Server 24.04 LTS |
| QEMU Guest Agent | marcado (pestaña *System*) |
| Disco | 25 GB, bus SCSI, `iothread` activado |
| CPU | 2 cores, 1 socket, tipo `host` |
| Memoria | 4096 MB |
| Red | modelo VirtIO, puente `vmbr0` |

## Pasos

1. **Crear la VM en Proxmox**
   - Botón *Create VM* y rellenar cada pestaña con los valores de la tabla anterior.
   - **Comprobación:** en la pestaña *Confirm*, los valores coinciden con la tabla antes de pulsar *Finish*.

2. **Instalar Ubuntu desde la Consola**
   - Arrancar la VM (*Start*) y abrir **Consola** (*Console*). La VM nueva está vacía: sin el instalador no hay IP.
   - En el instalador: nombre de servidor `NOMBRE-VM`, usuario `USER`, marcar **OpenSSH server** y **no** marcar ningún *snap*.
   - **Comprobación:** al terminar y reiniciar, la consola muestra el prompt `login:`.

3. **Instalar y activar el agente de QEMU, dentro de la VM** (se hace en la consola de Proxmox)
   ```bash
   sudo apt update && sudo apt install -y qemu-guest-agent
   sudo systemctl enable --now qemu-guest-agent
   ```
   - **Comprobación:** `systemctl status qemu-guest-agent` muestra `active (running)`, y la pestaña *Summary* de Proxmox ya enseña la IP.

4. **Snapshot `instalacion-limpia`**
   - En Proxmox: pestaña *Snapshots* → *Take Snapshot*, nombre `instalacion-limpia`, **sin** RAM.
   - **Comprobación:** aparece en la lista de snapshots.

5. **Entrar por SSH con contraseña**
   - Anota la IP (de *Summary*, o con `hostname -I` dentro de la VM) **fuera del repo**.
   - En Windows PowerShell:
     ```powershell
     ssh USER@IP_DE_LA_VM
     ```
   - La primera vez pregunta si confías en la máquina: `yes`.
   - **Comprobación:** pide la contraseña de la VM y el prompt pasa a `USER@NOMBRE-VM:~$`.

6. **Crear la clave SSH en Windows** (PowerShell de Windows, **no** la VM)
   ```powershell
   dir $env:USERPROFILE\.ssh
   ssh-keygen -t ed25519
   ```
   - Primero mira si ya tienes `id_ed25519`: si existe, **no sigas** con `ssh-keygen` o la sobrescribirías.
   - Cuando pregunte dónde guardarla, pulsa Enter. Pon una *passphrase*.
   - **Comprobación:** `type $env:USERPROFILE\.ssh\id_ed25519.pub` devuelve **una** línea que empieza por `ssh-ed25519`. Cópiala entera.

7. **Preparar la carpeta de claves en la VM**
   ```bash
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh
   ```
   - `700` = lectura, escritura y entrada solo para el dueño.
   - **Comprobación:** `ls -ld ~/.ssh` empieza por `drwx------`.

8. **Autorizar la clave en la VM**
   ```bash
   echo 'PEGA_AQUÍ_TU_CLAVE_PÚBLICA' >> ~/.ssh/authorized_keys
   chmod 600 ~/.ssh/authorized_keys
   ```
   - `>>` añade una línea al archivo (o lo crea si no existe). La clave debe quedar en **una sola línea**.
   - `600` = solo el dueño lee y escribe.
   - **Comprobación 1:** la huella es la misma en los dos lados.
     ```bash
     ssh-keygen -lf ~/.ssh/authorized_keys
     ```
     ```powershell
     ssh-keygen -lf $env:USERPROFILE\.ssh\id_ed25519.pub
     ```
   - **Comprobación 2:** en **otra** ventana de PowerShell, `ssh USER@IP_DE_LA_VM`. Ahora pide la **passphrase de la clave**, no la contraseña de la VM. Si pide la contraseña de la VM, revisa `ls -ld ~/.ssh` y `ls -l ~/.ssh/authorized_keys` (deben ser `700` y `600`).
   - La contraseña de la VM **sigue activa**: no se desactiva todavía.

9. **Actualizar los paquetes**
   ```bash
   sudo apt update
   apt list --upgradable
   sudo apt upgrade
   ls /var/run/reboot-required
   ```
   - Si recibes `Could not get lock`, espera 1 minuto y repite.
   - **Comprobación:** `apt list --upgradable` vacío. Puede que queden paquetes sin actualizar: Ubuntu los reparte por tandas (*phasing*) y `apt upgrade` avisa con `deferred due to phasing`. No es un error; les llegará la actualización más adelante.
   - `ls /var/run/reboot-required` responde `No such file or directory` si no hace falta reiniciar.

10. **Nombre de host**
    ```bash
    hostnamectl
    sudo hostnamectl set-hostname NOMBRE-VM
    ```
    - Si el instalador ya puso el nombre correcto, este paso no cambia nada visible.
    - **Comprobación:** `hostnamectl` muestra el nombre en `Static hostname`. Sal con `exit`, vuelve a entrar y el prompt lo refleja.

11. **Zona horaria**
    ```bash
    timedatectl
    timedatectl list-timezones | grep Madrid
    sudo timedatectl set-timezone Europe/Madrid
    ```
    - **Comprobación:** `timedatectl` muestra `Time zone: Europe/Madrid` y `date` da la hora local.

12. **Herramientas básicas**
    ```bash
    sudo apt install curl vim htop git
    ```
    - Si ya estaban, `apt` lo avisa. Si pregunta `[Y/n]`, pulsa `Y`.
    - **Comprobación:** `curl --version`, `vim --version`, `htop --version` y `git --version` muestran sus versiones.

13. **Reiniciar, comprobar y snapshot `aprovisionada`**
    ```bash
    sudo reboot
    ```
    - Espera 1 minuto y vuelve a entrar desde PowerShell con `ssh USER@IP_DE_LA_VM`.
    - **Comprobación:** entra con la **clave** (pide la passphrase, no la contraseña de la VM), lo que demuestra que todo es persistente. Después `hostnamectl`, `date`, `id`, `git --version` y `apt list --upgradable` dan lo mismo que antes del reinicio.
    - En Proxmox: *Snapshots* → `aprovisionada`, sin RAM. Sirve para **deshacer**, no es una copia de seguridad.

## Problemas que me encontré

| Problema | Qué pasó | Qué hacer |
|---|---|---|
| Sin IP tras *Start* | La VM nueva está vacía: sin Ubuntu instalado no hay red. | Abrir la **Consola** e instalar Ubuntu antes de esperar la IP. |
| *Shutdown* da `timeout` | Con el agente activado en Proxmox pero sin instalar dentro, el apagado educado espera una respuesta que no llega. | En una VM vacía o colgada usar **Stop** (corta la corriente) en vez de *Shutdown*. |
| Error al escribir el usuario en el instalador | Saltó un error que no pude identificar; el *reboot* dejó la VM «pensando». | Rehacer la VM desde cero (*Destroy* y *Purge*). **Si vuelve a pasar, captura el texto exacto del error.** |
| No tenía apuntada la IP | Tuve que volver a buscarla. | Apuntarla al crear la VM, fuera del repo público. |
| Comando de Windows pegado en la VM | `dir $env:USERPROFILE\.ssh` es de PowerShell de Windows; en la VM no existe. | El paso 6 (crear la clave) va en PowerShell de Windows; los pasos 7 y 8 (carpeta y `authorized_keys`), dentro de la VM. |
| Paquetes sin actualizar tras `upgrade` | Quedaron 3 paquetes (`phasing`). | No es un error; esperar. |

## Cuánto tardé

- Primera VM, con los errores: 15 min.
- Segunda VM (instalación y agente): 10 min.
- Configuración (pasos 5 a 13): 10 min.
- Total: 35 min.
