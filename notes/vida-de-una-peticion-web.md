# La vida d euna petición web

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

## 2. Apuntes extra

### Tipos:
- `A`: contiene dirección IPv4
- `AAAA`: contiene dirección IPv6
- `MX`: contiene datos de mail-routing, con prioridad: el número menor primero
- `NS`: contiene nombres de servidores autoritativos
- `TXT`: contiene texto libre