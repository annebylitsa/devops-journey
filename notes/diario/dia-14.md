# Diario día 14

## Qué aprendí
- Qué es el DHCP y qué hace y el proceso inicial `DORA`.
- Qué es el DNS, qué hace, el comando `resolvectl status`, `resolvectl dns` y `resolvectl revert`.
- He refrescado la escalera de diagnóstico y he visto que si un ping no devuelve error pero tampoco llega también es fallo del peldaño 4.
- El comando `dig` en detalle y cómo leer lo que devuelve.
- Qué ocurre desde que escribo un dominio hasta que obtengo una IP.

## Práctica
- Aparte de poner en práctica lo que he visto de DNS, uno de los ejercicios que más destaco de la parte práctica es haber asignado un servidor DNS que no existe a mi VM. Me ha dado respeto hacerlo pero tenía la snapshot previa de mi VM y otra sesión iniciada en caso de que la liara. Por suerte, no pasó nada malo y pude ejecutar las pruebas sin problemas. He visto cómo afecta a los comandos `getent` y `ping -c 2 nombre` el hecho de no tener un servidor DNS que contestara. Cabe decir que por IP todo funciona, por nombre no. A su vez, he podido revertir el cambio sin problemas y sin romper mi VM.


## Errores y cómo los resolví
- En una prueba donde añadí una IP que no existe con el dominio `mi-web.test` manualmente a `/etc/hosts`, el comando `dig mi-web.test` dio `NOERROR` y no `NXDOMAIN` como yo supuse que pasaría, porque `127.0.0.53 (systemd-resolved)` sí lee `/etc/hosts`.


## Resumen del día
- Hoy, para variar, el temario no ha sido tan denso y he podido acabar de estudiar sin estar exhausta, por lo que la práctica ha ido muy bien y he podido entender lo que hacía. Por supuesto, esto no hace que no cometa errores en mis predicciones de datos, pero lo importante es que, al ver donde fallo, lo entiendo y puedo razonarlo con lo aprendido.