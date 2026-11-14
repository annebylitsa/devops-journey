# Qué hacer cuando un disco está lleno

1. ¿Qué sistema de archivos está lleno?
`df -h` para buscar el que tiene el `Use%` más alto mirando su columna correspondiente

2. ¿Es espacio o inodos?
`df -i` para ver inodos

3. ¿Cuál es la carpeta grande de primer nivel?
`sudo du -xh RUTA --max-depth=1 2>/dev/null | sort -rh | head` el `-x` no cruza a otros sistemas de archivos montados

4. ¿Qué hay dentro? (repetir)
`sudo du -xh RUTA --max-depth=1 2>/dev/null | sort -rh | head`

5. ¿Qué decido y cómo compruebo que se ha arreglado?
`ls -lh` para ver de qué se trata, si es un log viejo puedo comprimirlo con `gzip`, si es una cache la puedo vaciar... cuando decida lo que hacer, ejecuto de nuevo `df -h` para ver si el `Use%` ha bajado

Importante: Si no se lo que es, no lo borro, pregunto antes.
