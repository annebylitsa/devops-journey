# Método de Subnetting

- Una subred es un trozo de una red, con su propia dirección de red y su máscara

- En el último octeto, la máscara es uno de los 8 valores (0, 128, 192, 224, 240, 248, 252, 255) y las subredes **caen cada «bloque» de números**. El **tamaño del bloque** es el **peso del último 1** de la máscara:

| Prefijo | Máscara (último octeto) | **Bloque** | Hosts utilizables | Subredes que caben en un /24 |
|---|---|---|---|---|
| `/24` | 0 | 256 | 254 | 1 |
| `/25` | 128 | **128** | 126 | 2 |
| `/26` | 192 | **64** | 62 | 4 |
| `/27` | 224 | **32** | 30 | 8 |
| `/28` | 240 | **16** | 14 | 16 |
| `/29` | 248 | **8** | 6 | 32 |
| `/30` | 252 | **4** | 2 | 64 |

## La receta (prefijos entre /24 y /30)

1. **Bloque** = el de la tabla.
2. **Red** = el mayor múltiplo del bloque que sea **menor o igual** que el último octeto de la IP. (Los múltiplos de 64: 0, 64, 128, 192. Los de 32: 0, 32, 64, 96, 128, 160, 192, 224.)
3. **Broadcast** = red + bloque − 1.
4. **Rango** = de red + 1 a broadcast − 1.
5. **Hosts** = bloque − 2.

#### Ejemplo: `192.168.10.77/26`
- Bloque: 64
- Red: 64, que es el mayor múltiplo del bloque que es menor o igual que el último octeto, que es `.77`.
- Broadcast: 127, que es la suma de de y bloque restándole 1 (64 + 64 - 1)
- Rango: 65-126, no puede ser 64 porque es la dirección de red, ni tampoco 127 porque es el broadcast. Estos números identifican a la subred y a todo a la vez.
- Hosts: 62, que es el bloque - 2.

### Para dividir en subredes:

- El **bloque** marca los saltos (múltiplos del bloque). Dividir `192.168.20.0/24` en `/26` → bloque 64 → subredes que empiezan en **.0, .64, .128, .192**, cada una de 64 direcciones (la primera acaba en .63, la segunda en .127, etc.).

#### Ejemplo: dividir `192.168.30.0/24` en `/26`
- El bloque de `/24` es 256, el de `/26` es 64. Divides 256/64 y da 4 que son las cuatro subredes que saldrías:
    `192.168.30.0/26`: red=0, broadcast=63
    `192.168.30.64/26`: red=64, broadcast=127
    `192.168.30.128/26`: red=128, broadcast=191
    `192.168.30.192/26`: red=192, broadcast=255

### Elegir prefijo según equipos necesarios

- Buscar el **menor** prefijo cuyos hosts utilizables **llegan**, es decir, el prefijo con el número más alto que aún da hosts suficientes. (recuerda restar 2). 

#### Ejemplo: 50 equipos 
- `/27` da 30 (no llega) → `/26` da 62 (sí llega).
