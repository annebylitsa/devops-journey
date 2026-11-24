# Diario día 14

## Qué aprendí
- Qué son los puertos, ahora de verdad.
- Qué son los protocolos TCP y UDP y su diferencia, el three-way handshake y cuánto cuesta, por qué DNS y DHCP usan UDP.
- Las banderas `[S]` SYN, `[S.]` SYN-ACK, `[.]` ACK, `[P.]` PUSH, `[F.]` FIN.
- Cómo leer los resultados del comando `tcpdump` y, por supuesto, como capturar.
- El comando `ss` y sus opciones.

## Práctica
- Hoy la práctica ha sido un poco lío porque  he tenido que trabajar con tres sesiones de VM abiertas para poder comprobar cosas. Un ejercicio muy interesante fue crear como un pequeño chat entre dos sesiones con el comando `nc -l IP PUERTO` en una sesión y `nc IP PUERTO` en otra. Lo repetí luego con `nc -u -l...`, entendiendo gracias a esto más a fondo la diferencia entre TCP y UDP.
- La mayoría de los comandos que he practicado hoy, si no todos, son más bien de lectura y observación.


## Errores y cómo los resolví
- Sin errores destacables.


## Resumen del día
- Si bien hoy no ha sido un día denso, siento que me ha costado entender las cosas que iba aprendiendo. Por suerte, la práctica me ayuda a entenderlo de verdad y ver la lógica. Aunque siento que las cosas se están complicando más y más y necesito tomarme esto con calma para poder seguir avanzando sin estancamientos.