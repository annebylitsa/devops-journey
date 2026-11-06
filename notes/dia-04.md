#Diario día 4

## Qué aprendí
- Más comandos aún, si cabe
- Directorios de Linux
- Wildcards
- Hard links y symbolic links

## Práctica
- Hoy he jugado a OverTheWire Bandit, donde he puesto en práctica sobre todo el comando find 
- También me he vuelto a pelear con el comando cp, volverá a pasar
- He practicado conectarme con SSH y el comando exit y he machacado el comando find hasta la saciedad

## Errores y cómo los resolví
- Intenté eliminar un archivo no trackeado con git rm, no funcionó, lógicamente. Lo resolví ejecutando el comando rm en solitario
- He entendido la lógica del comando find y de las wildcards errando una y otra vez. Intentaba buscar un archivo por nombre sin poner el nombre completo, suponiendo que lo encontraría de todos modos cuando, realmente, debo especificar una wildcard si pretendo que me busque archivos que contentan ese nombre. Así pues, ejecutando find -name "*.md" por fin di con la solución
- Una vez más, el comando cp me pone en mi lugar. Intentaba copiar un archivo de un directorio al que estaba ocupando en ese momento. Ocurrió que no indicaba bien el path, cosa que quiero pensar que me ha quedado clara por fin. O bien especifico el path completo, no sólo desde el punto que creo que es la partida, sino desde el inicio, o utilizo la maravillosa abreviación de "..". Por lo tanto sería: "../(directorio)/(archivo)"

## Resumen del día
- Pensaba que no sería posible después del día de ayer, pero hoy me he enfrentado a aún más información, y eso que mucha de esta era repetida. Ha sido tedioso, indagar en comandos que pensaba que conocía para descubrir que me faltaba entenderlos ha sido todo un viaje. Sin embargo, creo que por fin me van quedando las cosas claras, o al menos eso he podido ver en la práctica. Enfrentarme a los niveles de Bandit ha sido menos complicado de lo que esperaba a pesar de que, aun así, me he estancado en algunas cosas. Poco a poco me hago más resolutiva y veo más formas de salir addelante con lo que conozco, aunque me tenga que apoyar en documentación.
