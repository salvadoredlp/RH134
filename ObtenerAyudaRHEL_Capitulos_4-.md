### Páginas del manual de Linux ###

Las páginas se almacenan en subdirectorios del directorio /usr/share/man.

#### Secciones del manual Linux #### 

| Sección  | Tipo de contenido  | Descripción |
|----------|--------------------|-------------|
| 1 | Comandos del usuario | Ejecutables y programas de la shell. |
| 2 | Llamadas al sistema | Rutinas del kernel invocadas desde el espacio del usuario |
| 3 | Funciones de la librería | Proporcionadas por librerías de programas |
| 4 | Archivos especiales | Como archivos de dispositivos |
| 5 | Formatos de archivo | Para muchos archivos y estructuras de configuración |
| 6 | Juegos y protectores de pantalla |Sección histórica destinada a programas de entretenimiento |
| 7 | Convenciones, estándares y páginas varias | Protocolos y sistemas de archivos |
| 8 | Comandos de administración del sistema y con privilegios | Tareas de mantenimiento |
| 9 | API del kernel de Linux | Llamadas internas del kernel |

Para mostrar el tema de la página del manual de una sección específica, puede usar el comando man
section topic. Por ejemplo, **man 5 passwd** muestra passwd(5)

#### Navegar por las páginas del manual ####

| Comando | Resultado |
|----------|------------|
| Spacebar | Avanzar (abajo) una pantalla. |
|PageDown | Avanzar una pantalla. |
| PageUp | Retroceder (arriba) una pantalla. |
| DownArrow | Avanzar una línea. |
| UpArrow | Retroceder una línea. |
| D | Avanzar media pantalla. |
| U | Retroceder media pantalla. |
| /string | Buscar hacia delante una cadena en la página del manual. |
| N | Repetir búsqueda anterior hacia delante en la página del manual. |
| Shift+N | Repetir la búsqueda anterior más atrás en la página del manual. |
| G | Ir al inicio de la página del manual. |
| Shift+G | Ir al final de la página del manual. |
| Q | Salir del manual y regresar al prompt de shell de comandos. |

**Se pueden usar expresiones regulares para buscar en el man**

Para buscar páginas del man con una palabra se usa la opción **-k** del man.

```console
[user@host ~]$ man -k passwd
chgpasswd (8) - update group passwords in batch mode
chpasswd (8) - update passwords in batch mode
fgetpwent_r (3) - get passwd file entry reentrantly
getpwent_r (3) - get passwd file entry reentrantly
...
passwd (1) - update user's authentication tokens
passwd (1ossl) - OpenSSL application commands
passwd (5) - password file
passwd2des (3) - RFS password encryption
```

La opción **-K** (K MAYUSCULAS) busca la palabra clave en la página de texto completo, no solo en los titulos y descripciones. **Consume mas recursos y lleva mas tiempo**

Esta busqueda se basa en un indice generado por el comando **mandb** que debe ejecutarse como *root*
El servicio **man-db-cache-update** ejecuta automáticamente el comando mandb al instalar cualquier paquete con las páginas del manual.


## Redireccionamiento de la salida a un archivo o programa ## 

Página 122 del manual 124 en castellano
