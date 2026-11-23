# La escalera de diagnóstico

La regla es **subir de peldaño en peldaño; el primero que falla es donde buscar.** Si el peldaño 3 funciona y el 4 no, el problema está **entre** los dos (por ejemplo, el router de tu casa y tu operador).

## Peldaños


| #   | Capa                        | La pregunta                               | Comando                                                        | Si todo va bien…                                      | Si falla…                                                                                                                                                                              |
| --- | --------------------------- | ----------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Enlace (1-2)                | ¿Hay conexión con mi red local?           | `ip -brief link`                                               | la interfaz sale `UP`                                 | `DOWN`: cable, interfaz o configuración de la VM en Proxmox                                                                                                                            |
| 2   | Red (3)                     | ¿Tengo una IP de mi red?                  | `ip -brief address`                                            | una IPv4 de tu rango (la de tu red, no `169.254.x.x`) | Sin IPv4 o con `169.254.x.x`: no recibió IP (DHCP, el servicio que reparte las IP)                                                                                                                             |
| 3a  | Red (3)                     | ¿Sé adónde mandar lo que no es de mi red? | `ip route show`                                                | hay una línea `default via ...`                       | Sin `default`: `Network is unreachable` al salir                                                                                                                                       |
| 3b  | Enlace/red (2-3)            | ¿Responde mi puerta de enlace?            | `ping -c 3 IP_DE_LA_PUERTA` y `ip neighbor show`               | respuestas, y el vecino `REACHABLE` o `STALE`         | 100 % de pérdida y vecino `FAILED` o `INCOMPLETE`: el problema está en tu red local (router, Wi-Fi/cable, switch)                                                                      |
| 4   | Red (3)                     | ¿Llego a internet **por IP**?             | `ping -c 3 1.1.1.1`                                            | respuestas                                            | Gateway bien y esto no: tu router o tu operador (usa `traceroute -n 1.1.1.1` para ver dónde se corta)                                                                                  |
| 5   | Aplicación (7)              | ¿Se resuelven los **nombres**?            | `getent ahosts example.com`                                    | imprime direcciones                                   | Peldaño 4 bien, esto vacío: problema es el DNS                                                                                                                                           |
| 6   | Transporte/aplicación (4-7) | ¿Responde el **servicio**?                | `curl -sS --connect-timeout 5 http://example.com \| head -n 5` | unas líneas de HTML                                   | **`timed out`/`Timeout`**: los paquetes no llegan (filtro o camino cortado). **`Connection refused` / `Couldn't connect to server`**: **llegas**, pero **nadie escucha** en ese puerto |


## Casos resueltos

### Caso 1: no me abre example.com

- **Síntoma:** el navegador no carga la web.
- **Comandos y resultado:** peldaños 1 a 3 bien (interfaz `UP`, IP de mi rango, `default via`, la puerta responde). Peldaño 4: `ping -c 3 1.1.1.1` responde. Peldaño 5: `getent ahosts example.com` no imprime nada.
- **Dónde falla:** peldaño 5 (nombres).
- **Causa:** el equipo llega a internet por IP, pero el servidor DNS que usa no contesta.
- **Arreglo:** comprobar qué servidor DNS tiene configurado y si contesta; si es el del router, revisar el router.

### Caso 2: la web responde a ping pero mi aplicación en el puerto 8080 no responde

- **Síntoma:** una aplicación en el puerto 8080 de un servidor no responde.
- **Comandos y resultado:** peldaños 1 a 5 bien. Peldaño 6: `curl -sS --connect-timeout 5 http://example.com:8080` termina con `timed out` tras 5 segundos.
- **Dónde falla:** peldaño 6 (servicio).
- **Causa:** al llegar a ese puerto nadie contesta, así que algo lo filtra (un cortafuegos) o no hay camino hasta él. Si hubiera dicho `Connection refused`, habría llegado a la máquina y nadie escucharía en ese puerto.
- **Arreglo:** abrir ese puerto en el cortafuegos del servidor, o comprobar que el servicio está en marcha y escucha.
