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

\> Es el simbolo de redireccionamiento
>> Agrega a lo que ya exista a la salida

| Number | Channel name | Description | Default connection | Usage |
|--------|--------------|-------------|--------------------|-------|
| 0 | stdin | Standard input | Keyboard | Read only |
| 1 | stdout| Standard output | Terminal | Write only |
| 2 | stderr | Standard error | Terminal | Write only |
| 3+ | filename | Other files | None | Read, write, or both |

>[!NOTE]
>#### Importante ####
> El orden de las operaciones de redireccionamiento es importante.
> La siguiente secuencia redirige la salida estándar al archivo output.log y, luego, redirige el
> error estándar al mismo lugar que la salida estándar (output.log).
> ```console
>     >output.log 2>&1
> ```
> La siguiente secuencia redirige en el orden opuesto. Esta secuencia redirige los mensajes de error estándar al lugar predeterminado para la salida estándar (la ventana de terminal, de modo que no hay cambios) y luego redirige ***solo*** la salida
> estándar al archivo output.log.
>``` console
>  2>&1 > output.log
>```

#### Ejemplos de redireccionamiento

```console
[user@host ~]$ find /etc -name passwd > /tmp/output 2> /dev/null

[user@host ~]$ find /etc -name passwd &> /tmp/all-message-output

[user@host ~]$ find /etc -name passwd >> /tmp/all-message-output 2>&1

```

### Tuberías ###

Las tuberías y el redireccionamiento de E/S manipulan la salida y la entrada
estándar. Las tuberías (|) envían la salida estándar de un proceso a la entrada estándar
de otro proceso. El redireccionamiento (> o >>)envía la salida estándar a archivos o recibe la
entrada estándar de estos.

##### Ejemplos de tuberías #####

```console
[user@host ~]$ ls -l /usr/bin | less

[user@host ~]$ ls | wc -l

[user@host ~]$ ls -t | head -n 10 > /tmp/first-ten-changed-files
```

### Tuberías en forma de T. Comando Tee ###

En una tubería, tee copia la entrada estándar a la salida estándar y además redirige la salida estándar a los archivos asignados como argumentos para el comando. Si se imaginan los datos como agua que fluye a través de una tubería, se puede visualizar tee como una junta en "T" en la tubería que envía la salida en dos direcciones.

En el siguiente ejemplo, se redirige la salida del comando ls al archivo /tmp/saved-output y se
la pasa al comando less, para que se muestre en el terminal de a una pantalla por vez.

```console
[user@host ~]$ ls -l | tee /tmp/saved-output | less
```

Si usa el comando tee al final de una tubería, el terminal muestra la salida de los comandos en la
tubería y la guarda en un archivo al mismo tiempo.

```console
[user@host ~]$ ls -t | head -n 10 | tee /tmp/ten-last-changed-files
```

Use el comando tee con la opción ***-a*** para anexar el contenido a un archivo en lugar de
sobrescribirlo.

```console
[user@host ~]$ ls -l | tee -a /tmp/append-files
```

>[!NOTE]
>Importante
> Puede redirigir el error estándar por una tubería, pero no puede usar los operadores de redireccionamiento fusionados (&> y &>>). En el siguiente ejemplo, se muestra la manera correcta de redirigir la salida y el error >estándares a través de una tubería:
> ```console
> [user@host ~]$ find / -name passwd 2>&1 | less
> ```

### VIM ###

##### Visual Mode #####

***i*** Entrar en modo edición
***v*** Entrar en modo visual
***Ctrl+V*** Seleccionar bloque
***Shift+V*** Selección multilinea
***:*** Entrar en modo extendido

***u*** Undo, deshacer la útima acción realizada
***x*** Borrar un caracter
***:w*** Salvar el fichero
***:wq*** Salvar  salir de Vim
***!q*** Salir descartando los cambios

***y*** Copiar
***p*** Pegar

La configuración del usuario para usar Vim se encuentra en 

***~/.vimrc*** La configuación común para todos los usuarios se encuentra en ***/etc/vimrc

>[!WARNING]
> Configuración para trabajar con ficheros .yml en vim, añadir al ~/.vimrc
> syntax on
> autocmd fileType yaml setlocal ai ts=2 sw=2 nu et
> set cul
> set cuc
> set title
>


