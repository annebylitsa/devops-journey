# La vida de una petición web

## 1. DNS

1. Un programa, en este caso el navegador, necesita la IP de la web `www.example.com` para acceder a ella así que se la pide al sistema.

2. El sistema primero revisa el archivo local con nombres fijos `/etc/hosts`. Si la encontrase allí, el camino terminaría en este punto.

3. Si no, pregunta al resolutor, que es el servidor DNS que tiene configurado (de normal se lo da DHCP) 

4. El resolutor mira primero su caché en caso de que tuviera la IP apuntada, si ese es el caso contesta y ya.

5. Si no, comienza un recorrido de preguntas empezando por la raíz de `www.example.com.` que es el `.` final:

    - Pregunta al servidor raíz quién lleva `com.`, esto devuelve los servidores `com.`
    - Luego, pregunta a un servidor de `com.` quién lleva `www.example.com`, este devuelve los servidores autoritativos de `www. example.com`
    - finalmente, pregunta a un servidor autoritativo por la IP de `www.example.com`, que devuelve el registro `A` con la IP.

6. El resolutor devuelve la IP y la apunta en caché durante su TTL, y el navegador ya puede abrir la web. 
    - Apunte: el TTL es cuánto tiempo puede guardar la caché esa respuesta antes de volver a preguntar.

### Apuntes extra

#### Tipos:
- `A`: contiene dirección IPv4
- `AAAA`: contiene dirección IPv6
- `MX`: contiene datos de mail-routing, con prioridad: el número menor primero
- `NS`: contiene nombres de servidores autoritativos
- `TXT`: contiene texto libre

## 2. TCP y puertos

### Términos

- **Puerto**: Un puerto es un número entre 0 y 65535 que define a qué programa de un cliente irá la IP.
- **TCP**: Protocolo **con conexión**. Lleva a cabo handshake, confirma (ACK), reenvía lo perdido, ordena y frena si hay congestión.
    - **Three-way handshake**:
        1. El iniciador envía `SYN` con su secuencia de información inicial
        2. El que escucha responde `SYN-ACK` con su propia secuencia de información y reconocimiento
        3. El iniciador devuelve `ACK`
    Cuesta un viaje de ida y vuelta (round trip, RTT)
- **UDP**: Protocolo **sin conexión** ni garantías, envía y ya. Ligero. DNS, DHCP, vídeo, juegos.

### Preguntas

- **¿Por qué DNS usa UDP?** Una pregunta y una respuesta pequeñas: hacer un apretón de manos (un viaje extra) antes duplicaría el coste. 
- **¿Y por qué existe `dig +tcp`?** Porque hay respuestas **demasiado grandes** para un paquete UDP (el servidor lo avisa con una bandera de «truncado» y el cliente repite por TCP), transferencias de zonas enteras y DNS cifrado.
- **¿Por qué DHCP usa UDP (67 y 68)?** Cuando tu VM arranca **todavía no tiene IP**, y un handshake TCP necesita una IP de origen y un servidor concreto. Así que el cliente **grita por broadcast** (UDP) y los puertos son fijos: el servidor escucha en el **67** y el cliente en el **68**.

### Estados de un socket TCP

- **`LISTEN`**: Un servidor **espera** conexiones en ese puerto
- **`ESTAB`**: Conexión **establecida** (hay datos o puede haberlos)
- **`CLOSE-WAIT`**: Cierre a medias: un lado ya dijo FIN. Muchos `CLOSE-WAIT` suelen significar «el programa del otro lado olvidó cerrar el socket»
- **`TIME-WAIT`**: Cerrada: el sistema recuerda la conexión **~60 s** por si llegan paquetes rezagados. **Es normal.**

### Cómo leer `ss` y `tcpdump`

#### `ss` (*socket statistics*)

`ss` lista los **sockets** de tu máquina. Cada **letra** es una opción:

| Opción | Qué hace |
|---|---|
| `-t` | solo **TCP** |
| `-u` | solo **UDP** |
| `-l` | solo los que **escuchan** (*listening*) |
| `-a` | **todos** (escuchando y conectados) |
| `-n` | **números**, sin traducir (`22`, no `ssh`; IP, no nombres) |
| `-p` | muestra el **proceso** dueño (con `sudo` ves los de todos; sin `sudo`, solo los tuyos) |

Se pueden **juntar**: `-tulpn` es `-t -u -l -p -n`. Sin `-l` ni `-a`, `ss` enseña **solo las conexiones establecidas**.

Cómo se lee una fila (**ejemplo inventado**, con direcciones de documentación y puertos que no son de ninguna tarea de hoy):

```
Netid  State   Recv-Q  Send-Q   Local Address:Port    Peer Address:Port   Process
tcp    LISTEN  0       5        0.0.0.0:8080          0.0.0.0:*           users:(("miapp",pid=4321,fd=5))
tcp    ESTAB   0       0        192.0.2.10:51344      198.51.100.7:443
```

- **`Netid`** = protocolo (`tcp`, `udp`). **`State`** = estado (la tabla de 2.1).
- **`Local Address:Port`** = tu extremo. **`Peer Address:Port`** = el otro extremo (`*` = «cualquiera», porque nadie se ha conectado aún).
- **`0.0.0.0`** (y `[::]` en IPv6) significa «escucho en **todas** las direcciones de esta máquina»: se puede llegar **desde la red**. **`127.x.x.x`** (el *loopback*) significa «solo escucho **para esta misma máquina**». (Acuérdate del Día 11: `http.server --bind 127.0.0.1` y el túnel `-L`.) `127.0.0.53%lo` es lo mismo con el nombre de la interfaz (`lo`) pegado.
- **`Recv-Q` / `Send-Q`**: bytes esperando. En `LISTEN`, `Send-Q` es cuántas conexiones puede tener en cola. No hace falta más hoy.
- **`Process`**: `users:(("nombre",pid=NUM,fd=NUM))`: qué programa, su PID (Día 7) y el «número de archivo» del socket (en Linux un socket es un archivo, como viste en la lección de `lsof` del Día 7).

#### `lsof -i`

`lsof` (*list open files*) lo leíste el Día 7. Un socket **es un archivo abierto**, así que `lsof` también los lista. `lsof -i :22` = «¿qué proceso tiene abierto un socket de red en el puerto 22?». Dos letras útiles: `-n` (no traduzcas IP a nombres) y `-P` (no traduzcas puertos a nombres, `22` y no `ssh`). Con `sudo` ves los procesos de todos. Es **la** herramienta para el error clásico **«Address already in use»**: «no puedo arrancar mi programa porque **alguien ya usa ese puerto**: ¿quién?». Lo harás en la Tarea B.

#### `tcpdump`

`tcpdump` **copia al vuelo** los paquetes que pasan por una interfaz de red y los escribe en pantalla (o en un archivo). Necesita **`sudo`**. Su forma:

`sudo tcpdump -i ens18 -n tcp port 80`

- **`-i ens18`** = qué interfaz mirar (la tuya; la comprobarás en la preparación).
- **`-n`** = no traduzcas IP a nombres (más rápido y no genera tráfico DNS).
- **`tcp port 80`** = el **filtro**: solo paquetes TCP del puerto 80. Otros: `udp port 53`, `host 1.1.1.1`, y se combinan con `and`: `host 1.1.1.1 and port 53`.
- **`-c N`** para tras N paquetes; **`Ctrl+C`** para a mano. **`-w archivo`** guarda en vez de mostrar; **`-r archivo`** lee uno guardado.

[!warning] Siempre con filtro
> Si ejecutas `tcpdump` **sin filtro** en una sesión SSH, **ves tu propia sesión SSH**: cada línea que se imprime viaja por SSH, genera paquetes nuevos, que se imprimen, que generan… (un bucle). Y además ves **todo**. Por eso hoy siempre llevará `tcp port 80` o `host … and port 53`.

Cómo se lee una línea (**ejemplo inventado**, una línea de datos, no del apretón):

```
10:15:02.481516 IP 192.0.2.10.51344 > 198.51.100.7.443: Flags [P.], seq 1:518, ack 1, win 502, length 517
```

- Hora · `IP` · **origen** `192.0.2.10` **punto** **puerto** `51344` · `>` · **destino** `198.51.100.7` punto puerto `443` (**el último número tras el punto es el puerto**, no parte de la IP).
- **`Flags`** = qué tipo de mensaje TCP es. **Un punto `.` significa ACK.**

| Bandera | Significa |
|---|---|
| `[S]` | **SYN** |
| `[S.]` | **SYN + ACK** |
| `[.]` | solo **ACK** |
| `[P.]` | datos (*push*) + ACK |
| `[F.]` | **FIN** + ACK (cierre) |
| `[R]` / `[R.]` | **RST** (corta) |

- `seq` / `ack` = números de secuencia y de confirmación; `win` = ventana (la de la lección de flujo); `length` = bytes de datos del paquete (en el SYN es 0). En los paquetes **UDP** no hay `Flags`: pone `UDP, length N`.

> [!warning] Privacidad de las capturas
> - Solo capturas **tu propia VM, con filtros**, y tráfico de prueba contra `example.com`. El **puerto 80 va sin cifrar**: se puede leer su contenido. **No uses** `-A` ni `-X` (muestran el contenido).
> - Tus líneas llevan **la IP privada de tu VM**: **solo en el vault**. Para el repo, **sustitúyela por `VM`** o por `192.0.2.10`. Las IP públicas de `example.com` o `1.1.1.1` no son problema.
> - Un archivo `.pcap` contiene IP **y MAC**: **nunca al repo**, y bórralo al terminar.