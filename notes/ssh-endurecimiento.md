# Cerrar la puerta de la contraseña

- Cualquier máquina con SSH abierto a internet recibe intentos de entrar por fuerza bruta, lo cuál no está muy bien la verdad. Una contraseña se puede adivinar, pero una clave ed25519 no, así que hay que endurecer el SSH para proteger nuestras máquinas.
- Apunte: el nombre del servicio en Ubuntu es `ssh`, en otras distribuciones se llama `sshd`

## Dónde se configura

- El servidor lee `/etc/ssh/sshd_config` y esa configuración incluye todos los archivos `/etc/ssh/sshd_config.d/*.conf`.
    - Pequeño apunte: el `*` es un comodín que representa cualquier cadena de caracteres.
- **Regla importante**: si una opción aparece varias veces, gana la primera que lee. En mi caso, cree un archivo 00-endurecer.conf, por lo que se leerá primero.

## Los 4 ajustes

- Estos son los que he tocado yo por ahora:
1. `PasswordAuthentication` (valor: no) El servidor no acepta contraseña por SSH. Sólo clave.
2. `KbdInteractiveAuthentication` (valor: no) Otra puerta por la que se pueden meter contraseñas.
3. `PermitRootLogin` (valor: no) Nadie entra directamente como root por SSH.
    - Apunte: `root` en Ubuntu ya viene con la cuenta bloqueada y el `PermitRootLogin` en `prohibit-password` por defecto así que no permite contraseña.
4. `PubkeyAuthentication` (valor: yes) Asegura que la clave sí se acepta.

## Antes de empezar
- Sesión 1 abierta hasta el final.
- Snapshot o copia de la VM.
- Acceso por consola del hipervisor por si acaso.
- Mirar antes qué hay en `/etc/ssh/sshd_config.d/: ls /etc/ssh/sshd_config.d/` y `sudo cat` de lo que haya. Un archivo de nube puede tener PasswordAuthentication yes y por lo tanto, dependiendo de su nombre, puede ser leído antes que el que se vaya a crear.

## Proceso
1. Crear archivo `00-endurecer.conf` con el siguiente comando: `sudo tee /etc/ssh/sshd_config.d/00-endurecer.conf` y escribir las cuatro líneas de los ajustes mencionados previamente.
    - Importante, escribir una línea y pulsar enter para escribir la siguiente. No vale pegar porque el texto puede salir desordenado, por lo que no valdrá.
    - Si se prefiere una forma más sencilla que si permite copiar y pegar usar el comando `printf 'PasswordAuthentication no\nKbdInteractiveAuthentication no\nPermitRootLogin no\nPubkeyAuthentication yes\n' | sudo tee /etc/ssh/sshd_config.d/00-endurecer.conf`
    - Comprobar con `cat /etc/ssh/sshd_config.d/00-endurecer.conf` que aparecen las cuatro líneas:

```
    PasswordAuthentication no
    KbdInteractiveAuthentication no
    PermitRootLogin no
    PubkeyAuthentication yes
```

2. Hecho esto, comprobar sintaxis antes de aplicar con `sudo sshd -t`, si no devuelve nada es que está bien. No seguir si imprime error, hay que comprobar lo que ocurre, corregir o eliminar el archivo.

3. Aplicar el cambio con `sudo systemctl reload ssh`.
    - Comprobar con `systemctl status ssh` para ver si el servicio está `active`

4. Comprobar que se ha aplicado lo que se ha establecido en `00-endurecer.conf` con el comando `sudo sshd -T | grep -Ei '^(passwordauthentication|kbdinteractiveauthentication|permitrootlogin|pubkeyauthentication) '`, si devuelve los valores tal y como los establecimos, está bien.

## Comprobaciones

- Las comprobaciones se hacen desde una sesión nueva, en el ordenador cliente, no la VM.
- Entrar en la VM normalmente y comprobar que se sigue pudiendo entrar con la clave, si no, aplicar salida de emergencia.
- Intentar entrar en la VM con `ssh -o PubkeyAuthentication=no -o PreferredAuthentications=password ann@IP_DE_LA_VM`, si devuelve `Permission denied (publickey)` es que la configuración se aplicó.
- Intentar entrar en la VM con `root` usando `ssh root@IP_DE_LA_VM`.
    - Esto no demuestra que `PermitRootLogin no` funciona. Como mencioné arriba más en detalle, en Ubuntu `root` viene bloqueado de base y no permite contraseña.
- Comprobar también que `sudo` sigue funcionando, entrando en la VM y ejecutando `sudo whoami`. Debe pedir la contraseña de usuario.