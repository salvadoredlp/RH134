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

**Solo se pueden crear archivos duros (hard links) dentro del mismo sistema de archivos.

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

**Solo se pueden usar enlaces duros a archivos normales, no se puede a directorios**

**df** Muestra información acerca del sistema de archivos en donde se aloja cada archivo o, por defecto, de todos ellos.

### Soft Links ###

Un enlace simbólico no es un archivo regular, sino un tipo de archivo especial que apunta
a un archivo o a un directorio existente. Se usa el comando ***ln*** pero con la opción ***-s*** .

```console
[user@host ~]$ ln -s /home/user/newfile-link2.txt /tmp/newfile-symlink.txt
[user@host ~]$ ls -l newfile-link2.txt /tmp/newfile-symlink.txt
-rw-rw-r--. 1 user user 12 Mar 11 19:19 newfile-link2.txt
lrwxrwxrwx. 1 user user 11 Mar 11 20:59 /tmp/newfile-symlink.txt -> /home/user/
newfile-link2.txt
[user@host ~]$ cat /tmp/newfile-symlink.txt
Symbolic Hello World
```

**Pueden vincular archivos en diferentes sistemas de archivos.**
**Pueden apuntar a un directorio o a un archivo especial.**

>[!NOTA]
>Cuando se elimina el archivo regular original, el enlace simbólico seguirá apuntando al archivo, 
>pero el destino desaparece. Un enlace simbólico que apunta a un archivo que falta se denomina "enlace simbólico colgante" (dangling symbolic link).
> Si creamos un fichero con el mismo nombre que el que hemos borrado el enlace simbolico ya no estara colgando ya que apuntara al nuevo fichero
>
> - Un hard link (enlace duro) apunta a un nombre a los datos de un dispositivo de almacenamiento
> - Un enlace simbólico apunta un nombre a otro nombre, que apunta a datos en un dispositivo de almacenamiento.


```console
[user@host ~]$ rm -f newfile-link2.txt
[user@host ~]$ ls -l /tmp/newfile-symlink.txt
lrwxrwxrwx. 1 user user 11 Mar 11 20:59 /tmp/newfile-symlink.txt -> /home/user/
newfile-link2.txt
[user@host ~]$ cat /tmp/newfile-symlink.txt
cat: /tmp/newfile-symlink.txt: No such file or directory
```

Al entrar en un enlace simbolico que apunta a directorio nuestra ruta sera la del enlace simbolico no real a la que apunta, si queremos actulizar para que apuntemos a la real tendremos que usar la opción **-P** de **cd**

```console
[user@host ~]$ ln -s /etc /home/user/configfiles
[user@host ~]$ cd /home/user/configfiles
[user@host configfiles]$ pwd
/home/user/configfiles
[user@host configfiles]$ cd -P /home/user/configfiles
[user@host etc]$ pwd
/etc
```

### Expansiones de las líneas de comando ###

***Tabla de metacaracteres y coincidencias***

| Patrón        |    Coincidencias |
|---------------|-------------------|
| *            | Cualquier cadena de cero o más caracteres |
| ?            | Cualquier carácter individual             |
| [abc…]       | Cualquier carácter en la clase incluida (entre corchetes) |
| [!abc…]      | Cualquier carácter que no esté en la clase incluida |
| [^abc…]      | Cualquier carácter que no esté en la clase incluida |
| [[:alpha:]]  | Cualquier carácter alfabético |
| [[:lower:]]  | Cualquier carácter en minúsculas |
| [[:upper:]]  | Cualquier carácter en mayúsculas |
| [[:alnum:]]  | Cualquier dígito o carácter alfabético |
| [[:punct:]]  | Cualquier carácter imprimible que no sea un espacio o alfanumérico |
| [[:digit:]]  | Cualquier dígito de 0 a 9 |
| [[:space:]]  | Cualquier carácter de espacio en blanco, que puede incluir tabulaciones,nuevas líneas, retornos de carro, fuentes de formulario o espacios |

**Ejemplos** 

```console
[user@host glob]$ ls a*
able alfa
[user@host glob]$ ls *a*
able alfa baker bravo cast
[user@host glob]$ ls [ac]*
able alfa cast charlie
```

```console
[user@host glob]$ ls ????
able cast easy echo
[user@host glob]$ ls ?????
baker bravo delta
```

### Expansión de llaves ###

```console
[user@host glob]$ echo {Sunday,Monday,Tuesday,Wednesday}.log
Sunday.log Monday.log Tuesday.log Wednesday.log
[user@host glob]$ echo file{1..3}.txt
file1.txt file2.txt file3.txt
[user@host glob]$ echo file{a..c}.txt
filea.txt fileb.txt filec.txt
[user@host glob]$ echo file{a,b}{1,2}.txt
filea1.txt filea2.txt fileb1.txt fileb2.txt
[user@host glob]$ echo file{a{1,2},b,c}.txt
filea1.txt filea2.txt fileb.txt filec.txt
[user@host glob]$ echo file{a{1..3},b,c}.txt
filea1.txt filea2.txt filea3.txt fileb.txt filec.txt
```

Se puede usar para crear directorios de forma rápida

```console
[user@host glob]$ mkdir ../RHEL{7,8,9}
[user@host glob]$ ls ../RHEL*
RHEL7 RHEL8 RHEL9
```

### Expansión de variables ###

Puede asignar datos como valor de una variable se usa  la siguiente sintaxis:

[user@host ~]$ VARIABLENAME=value

Se puede usar la expansión de variables para convertir el nombre de la variable a su valor en la línea
de comandos. Si una cadena comienza con un signo de dólar ($), entonces la shell intentará usar el
resto de esa cadena como un nombre de variable y reemplazarlo con el valor de la variable.

```console
[user@host ~]$ USERNAME=operator
[user@host ~]$ echo $USERNAME
operator
[user@host ~]$ USERNAME=operator
[user@host ~]$ echo ${USERNAME}
operator
```

**Se aconseja poner la variable entre llaves {} después del $ para evitar errores debidos a otras expansiones de shell, puede poner el nombre de la variable**
**Las variables no pueden empezar por números**

### Sustitución de comandos ###

Se pueden usar la forma $(comando) que se sustituira por la salida del comando encerrado entre parentesis.

```console
[user@host glob]$ echo Today is $(date +%A).
Today is Wednesday.
[user@host glob]$ echo The time is $(date +%M) minutes past $(date +%l%p).
The time is 26 minutes past 11AM.
```

>[!NOTE]
> Para evitar que la shell expanda el siguiente caracter se usa el caracter de barra invertida de escape (\).
>```console
>[user@host glob]$ echo The value of $HOME is your home directory.
>The value of /home/user is your home directory.
>
>[user@host glob]$ echo The value of \$HOME is your home directory.
>The value of $HOME is your home directory.

Para proteger las cadenas de caracteres más extensas, puede usar comillas simples (') o dobles (") para encerrar las cadenas. Tienen efectos ligeramente diferentes. Las comillas simples
detienen toda la expansión de shell. Las comillas dobles detienen la mayor parte de la expansión de shell.

Las comillas dobles evitan que los caracteres especiales que no sean el signo dólar ($), la barra
invertida (\), la comilla invertida (`) y el signo de exclamación (!) operen dentro del texto entre comillas. Las comillas dobles bloquean la expansión del nombre de ruta, pero aún permite que ocurra la sustitución de comandos y la expansión de variables.

```console
[user@host glob]$ myhost=$(hostname -s); echo $myhost
host
[user@host glob]$ echo "***** hostname is ${myhost} *****"
***** hostname is host *****
Use comillas simples para interpretar todo el texto entre comillas literalmente.
[user@host glob]$ echo "Will variable $myhost evaluate to $(hostname -s)?"
Will variable host evaluate to host?
[user@host glob]$ echo 'Will variable $myhost evaluate to $(hostname -s)?'
Will variable $myhost evaluate to $(hostname -s)?
```



