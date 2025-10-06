## RH123 ##

 El comando ssh con la opción -i se usa para especificar el archivo de clave privada del
usuario, que es mylab.pem. La clave pública coincidente ya está configurada como una clave
autorizada en la cuenta remoteuser.

```console
[user@host ~]$ ssh -i mylab.pem remoteuser@remotehost
[remoteuser@remotehost ~]$
```

>[!NOTE]
>
>- **Alt + F2** en Gnome se abre ventana para  ejecutar un comando.
>
>- **Super + L** Bloqueo de la sesión.
>
>- **$yelp** Abre ayuda de Gnome
>
>- **$yelp help:gnome-help/shell-introduction**  Resumen visual de GNOME

### Comandos ###

*Muchos comandos tienen la opcion --help para mostrar mensajes de ayuda para el uso del comando*

***passwd*** Para cambiar la contraseña del usuario que este activo

```console
[user@host ~]$ passwd
Changing password for user user.
Current password: old_password
New password: new_password
Retype new password: new_password
passwd: all authentication tokens updated successfully
```

Para ver el tipo de fichero se usa el comando file, en linux no es necesaria extensión

```console
$file
``` 
<br>

***cat*** Ver contenido de un archivo. Para ver contenido de varios archivos : $cat file1 file2 file3...

```console
[user@host ~]$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
...output omitted...
```

***less*** Para cuando el fichero es muy largo se puede usar **less** para visualizar el contenido de un fichero 
se usa UpArrow key y DownArrow key para navegar por el contenido y la tecla q para salir.

```console
$less /etc/passwd
```

***head*** y ***tail*** muestran las primeras 10 lineas de un fichero (head) o las 10 últimas (tail) con la opcion -n puedes indicarle cuantas lineas quieres que muestre.

```console
[user@host ~]$ tail -n 3 /etc/passwd
gdm:x:42:42::/var/lib/gdm:/sbin/nologin
gnome-initial-setup:x:980:978::/run/gnome-initial-setup/:/sbin/nologin
dnsmasq:x:979:977:Dnsmasq DHCP and DNS server:/var/lib/dnsmasq:/sbin/nologin
```

***wc*** sirve para contar lineas *-l* , palabras *-w* y caracteres *-c*. Para contar de varios ficheros *wc -opcion file 1 file2 file3*

```console
[user@host ~]$ wc /etc/passwd
41
98 2338 /etc/passwd
[user@host ~]$ wc -l /etc/passwd ; wc -l /etc/group
41 /etc/passwd
63 /etc/group
[user@host ~]$ wc -c /etc/group /etc/hosts
883 /etc/group
114 /etc/hosts
997 total
```

***backslash character (\)*** Sirve para quitar significado al siguiente caracter en caso de que el caracter que le siga sea el salto de linea lo ignorara. Puede servir para escribir comandos de multiples lineas.

```console
[user@host ~]$ head -n 3 \
/usr/share/dict/words \
/usr/share/dict/linux.words
==> /usr/share/dict/words <==
1080
10-point
10th
==> /usr/share/dict/linux.words <==
1080
10-point
10th
```

***history*** Sirve para mostrar los últimos comandos ejecutados en la bash .
***!number*** Para ejecutar el comando que ocupa la posicion que se muestra al ejecutar history***
***!!***      Último comando ejecutado

***Alt+.*** Para insertar la última palabra del anterior comando, se puede pulsar varias veces para ir viendo las últimas palabras.
***Crtl+A*** Saltar al inicio del comando.
***Ctrl+ UpArrow or DownArrow*** Para navegar entre los últimos comandos ejecutados.

#### Métodos abreviados útiles de edición de línea de comandos ####

***Ctrl+A***          Ir al inicio de la línea de comandos.

***Ctrl+E***          Ir al final de la línea de comandos.

***Ctrl+U***          Borrar desde el cursor hasta el principio de la línea de comandos.

***Ctrl+K***          Borrar desde el cursor hasta el final de la línea de comandos.

***Ctrl+LeftArrow***  Ir al inicio de la palabra anterior en la línea de comandos.

***Ctrl+RightArrow*** Ir al final de la palabra siguiente en la línea de comandos.

***Ctrl+R***          Buscar un patrón en la lista de historial de comandos.


### Directorios ###

| Ubicación   |           Proposito                                       |
|--------------|-----------------------------------------------------------|
| /boot     |          Archivos para iniciar el proceso de arranque.      |
| /dev      |          Archivos de dispositivo especiales que el sistema usa para acceder al hardware.|
| /etc        |          Archivos de configuración específicos del sistema.|
| /home       |         Directorio de inicio, donde los usuarios habituales guardan sus datos y los archivos de configuración.|
| /root       |          Directorio de inicio para el superusuario administrativo, root.|
| /run        |          Datos de tiempo de ejecución para procesos que se iniciaron desde el último arranque. Esto incluye archivos de ID de proceso y archivos de bloqueo. El contenido de este directorio se vuelve a crear en el arranque nuevo. En este directorio se consolidan los directorios /var/run y /var/lock de versiones anteriores de Red Hat Enterprise Linux. |
| /tmp        |Un espacio con capacidad de escritura por parte de cualquier usuario del sistema para archivos temporales. Los archivos a los que no se haya accedido, y que no se hayan cambiado ni modificado durante 10 días se eliminan de este directorio automáticamente. El directorio /var/tmp es también un directorio temporal, en el que los archivos que no tuvieron acceso, cambios ni modificaciones durante más de 30 días se eliminan automáticamente. |
| /usr        | Software instalado, librerías compartidas, incluidos archivos y datos de programa de solo lectura. Los subdirectorios importantes del directorio /usr incluyen los siguientes comandos:<br><br>- /usr/bin: Comandos del usuario <br>- /usr/sbin: Comandos de administración del sistema<br>- /usr/local: Software personalizado a nivel local |
| /var |Los datos variables específicos del sistema deberían conservarse entre los arranques. Los archivos que cambian en forma dinámica (por ejemplo, bases de datos, directorios caché, archivos de registro, documentos en cola de impresión y contenido de sitio web) pueden encontrarse en /var. |


***pwd***      Muestra la ruta completa del directorio de trabajo actual.

***ls***       Enumera contenido del directorio actual.<br>
>[!NOTE]
>    **opciones de ls**<br>
>    <br>
>   -l  Formato de enumeración extensa<br>
>   -a  Todos los archivos incluidos los ocultos<br>
>   -R  Recurrente para incluir el contenido de todos los subdirectorios<br>
     
***cd***       Cambiar del directorio actual.

*~*        Directorio de inicio del usuario actual.

***touch***   Actualiza la marca de tiempo de un archivo con respcto a la fecha y hora actual sin modificarlo. Es útil para crear archivos vacíos.

>[!NOTE]
>Los nombres de archivo que comienzan con un punto (.) indican archivos
ocultos

***mkdir*** Sirve para crear directorios. Puedes crear varios en una sola linea 
```console
user@host ~]$ cd Documents
[user@host Documents]$ mkdir ProjectX ProjectY ProjectZ
[user@host Documents]$ ls
ProjectX ProjectY ProjectZ
```

 *mkdir -p* Si el fichero padre no existe, lo crea
 ```console
[user@host Documents]$ mkdir -p Thesis/Chapter1 Thesis/Chapter2 Thesis/Chapter3
[user@host Documents]$ ls -R Thesis/
Thesis/:
Chapter1 Chapter2 Chapter3
Thesis/Chapter1:
Thesis/Chapter2:
Thesis/Chapter3:
```

***cp*** Copiar archivo y crea un archivo en el directorio actual o en otro directorio especificado. El último argumento debe ser un directorio que sera el directorio destino de los ficheros. 

*.*  Directorio actual
*..* Directorio padre
*-r* Para copiar directorio y todo su contenido.

 ```console
[user@host Documents]$ cd ProjectY
[user@host ProjectY]$ cp -r ../Thesis/ .
```

***mv*** Mueve los ficheros de una ubicación a otra. Tambien se puede usar para cambiar de nombre un fichero.

***rm*** Elmina archivos. De manera predeterminada no elimina directorios para eliminar directorios hay que incluir la opción *-r*.<br>
    - **-i** Se usa para pedir confirmación interactiva antes de la eliminación<br>
    - **-f** No pide confirmación, fuerza la eliminación sin confirmar con el usuario. Tiene prioridad sobre *-i*<br>

***rmdir*** Para eliminar directorios vacios. rmdir -r para directorios no vacios


## Creación de enlaces entre archivos ##

Para ver cuantos links tiene un archivo hay que usar la opción *-l* de ***ls***
Para crear enlaces se usa el comando ***ln***

### Hard Links ###


Cuando crea un enlace duro a un archivo, crea otro nombre que apunta a esos
mismos datos. El nuevo enlace duro actúa exactamente igual que el nombre del archivo original.
Después de que se crea el enlace, no puede distinguir la diferencia entre el nuevo enlace duro y el
nombre original del archivo. 

```console
[user@host ~]$ ln newfile.txt /tmp/newfile-hlink2.txt
[user@host ~]$ ls -l newfile.txt /tmp/newfile-hlink2.txt
-rw-rw-r--. 2 user user 12 Mar 11 19:19 newfile.txt
-rw-rw-r--. 2 user user 12 Mar 11 19:19 /tmp/newfile-hlink2.txt
```

>[!NOTE]
>Para determinar si dos archivos tienen un enlace duro, use el comando ls con la opción ***-i*** para enumerar el número de inodo de cada archivo.
>Si los archivos están en el mismo sistema de archivos y sus números de inodo son los mismos, los archivos son enlaces duros que apuntan a los mismos contenidos de los archivos de datos.
>```console
>[user@host ~]$ ls -il newfile.txt /tmp/newfile-hlink2.txt
>8924107 -rw-rw-r--. 2 user user 12 Mar 11 19:19 newfile.txt
>8924107 -rw-rw-r--. 2 user user 12 Mar 11 19:19 /tmp/newfile-hlink2.txt
>```

>[!WARNING]
>Todos los enlaces duros que hacen referencia al mismo archivo tendrán la misma estructura de inodos que el recuento de enlaces, permiso de acceso, pertenencia a usuarios y grupos, marcas de tiempo y contenido de archivo.
>Cuando se cambia esa información para un enlace duro, los otros enlaces duros para el mismo archivo
también muestran la nueva información.
> Esto se debe a que cada enlace duro apunta a los mismos datos en el dispositivo de almacenamiento.
>
>Incluso si se elimina el archivo original, aún puede acceder al contenido del archivo siempre que exista al menos un enlace duro adicional.
> Los datos se eliminan del almacenamiento solo cuando se elimina el último enlace duro.
