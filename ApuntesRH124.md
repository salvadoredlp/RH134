
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
