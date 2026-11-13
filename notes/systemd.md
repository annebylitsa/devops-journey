# Cómo leo journalctl -u

`Nov 13 15:02:51 devops-lab latido.sh[861]: latido: servicio en marcha`

- `Nov 13 15:02:51`: cuando pasó
- `devops-lab`: dónde, en qué VM
- `latido.sh[861]`: quién lo dice, con su PID
- `latido: servicio en marcha`: qué dice

En el journal hablan dos voces:
- `systemd[1]`: cuenta lo que ha hecho él: Started, Stopping, Scheduled restart job...
- `latido.sh[861]`: es el propio programa contando lo suyo

1. Si quiero saber si el servicio esta bien ahora, uso `systemctl status servicio` y miro `Active` 
2. Si quiero saber qué pasó mientras no miraba, uso `journalctl -u servicio -n 10`
3. Si falló, busco una línea con un aviso como 203/EXEC.
4. Si se reinició solo, lo sé porque aparece una frase referente al reinicio en `journalctl` y también se queda grabado en el `systemctl status` del servicio. 

### Extra
- `start` hace que el servicio comience en el momento en el que ejecuto el comando  y `enable` hace que el servicio comience cada vez que se enciende el ordenador. Se puede tener el uno sin el otro.
- Un ejemplo de hoy: tras el reinicio de la VM, un servicio estaba `enabled` y arrancó sólo, sin que ejecutara `start`
- `daemon-reload` sirve para que `systemd` vuelva a leer los archivos de unidad. No reinicia nada: para aplicar un cambio hace falta además `restart`. Si lo olvido, `systemctl status` me avisa con `Warning: The unit file ... changed on disk`.
- `Restart=on-failure` reinicia con `SIGKILL` pero no con `SIGTERM` porque es una forma de desactivar un servicio agresiva que hace saltar error, y con error, se reinicia. 
