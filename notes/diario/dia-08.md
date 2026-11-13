# Diario día 8

## Qué aprendí
- Qué es el systemd y tambien SysV
- Qué son los servicios, cómo se crean, cómo funcionan, cómo encenderlos, apagarlos...
- Los valores dentro de `[Unit]`, `[Service]` e `[Install]`
- Cron y Systemd Timers

## Práctica
- He creado mis propios servicios y he aprendido cómo verlos y entender su información, aprendiendo a leer el `systemctl status` y `journalctl`
- He practicado el reinicio automático, entendiendo cuándo ocurre y cuando no
- También he practicado a arreglar errores, forzándolos haciendo rutas malas, quitando permisos y olvidando el `daemon-reload` para ver los resultados
- He creado un temporizados y he podido ver la diferencia de uno con el cron
- Hice mi primer reboot y he visto cómo afecta a la VM y los servicios que creé

## Errores y cómo los resolví
- Haciendo ejercicios de calentamiento de cierre del día 7, he ejecutado `grep -E 'Name|State|Ppid'/proc/120148/status` y se ha ejecutado como `grep --color=auto -E 'Name|State|Ppid'/proc/120148/status` y tuve que utilizar Ctrl-Z para salirme. El error ha sido que no he puedo un espacio entre antes de `/proc/...` por lo que creo que he confundido a la shell. Al fijarme y poner el espacio, he recibido el resultado buscado
- Esto hizo que abriera un job nuevo y al salir con Ctrl-Z sólo lo pausé, seguía activo. Para cerrarlo definitivamente utilicé `kill %3`
- `PPid` se escribe con las dos `P` mayúsculas, razón por la cuál en el resultado no me salió el `PPid`

## Resumen del día
- Hoy todo ha ido más fluido, y aunque como siempre ha habido mucha teoría, he podido entender la práctica fácilmente. He descubierto que puedo crear archivos en VSCode sin necesidad de escribir un comando en la terminal (duh), lo cuál me ha permitido editar texto más cómodamente cuando creaba los servicios. Parece mentira pero cuando no conoces algo y es tan técnico como VSCode, da un poco de miedo indagarlo por si la lío y rompo algo. Pero poco a poco voy perdiendo el miedo a ser curiosa en esto.
