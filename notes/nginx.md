# nginx: servidor web con dos sitios

Laboratorio del Día 17 (VM Ubuntu, nginx 1.24). Los archivos de configuración están en `labs/nginx/`.

## 1. Qué hace cada ruta
| Ruta | Para qué sirve |
|---|---|
| `/etc/nginx/sites-available/` | Archivos de configuración de cada sitio (guardados, no activos) |
| `/etc/nginx/sites-enabled/` | Enlaces simbólicos a los de `sites-available`: solo lo que está aquí se aplica |
| `/var/www/` | Carpetas con el contenido de cada sitio (`root`) |
| `/var/log/nginx/` | `access.log` (cada petición) y `error.log` (fallos) |

## 2. Cómo añadir un sitio (5 pasos)
1. Crear la carpeta y la página: `sudo mkdir -p /var/www/SITIO` y `sudo nano /var/www/SITIO/index.html`.
2. Crear el archivo del sitio con `listen`, `server_name`, `root` e `index`: `sudo nano /etc/nginx/sites-available/SITIO`.
3. Activarlo: `sudo ln -s /etc/nginx/sites-available/SITIO /etc/nginx/sites-enabled/SITIO`.
4. Comprobar la configuración: `sudo nginx -t`.
5. Aplicarla: `sudo systemctl reload nginx`.

Solo un sitio por puerto puede llevar `default_server`.

## 3. Diagnóstico: síntoma, qué miro
| Síntoma | Qué miro |
|---|---|
| `404` | `root` + ruta de la petición: ¿existe ese archivo? Y `error.log` |
| `403` | Permisos del archivo y del usuario de los `worker` (`www-data`) |
| `502` | El servicio de detrás del proxy: ¿está encendido? Y `error.log` |
| `nginx -t` falla | El archivo y el número de línea que indica |

## 4. Por qué `-t` antes de `reload`
`nginx -t` revisa la configuración sin aplicarla. Con una errata, el `reload` no la aplica y nginx sigue con la antigua, así que antes lo compruebo para saber si el cambio va a entrar. Es la misma idea que `sudo sshd -t` del Día 11.

## 5. Nota sobre la Tarea E
`miweb` incluye un `location /app/` con `proxy_pass` a un servidor de Python en el puerto 8000. Si ese servidor está apagado, nginx devuelve `502`.
