
### SELinux 

Modos de SELinux

***Enforcing***: SELinux hace cumplir las políticas cargadas.

***Permissive***: SELinux carga las políticas y está activo, pero en lugar de aplicar las reglas de control de acceso, registra las infracciones de acceso. Este modo es útil para probar y solucionar
problemas de aplicaciones y reglas.

***Disabled***: SELinux está desactivado. Las infracciones de SELinux no se rechazan ni se registran. Se desaconseja deshabilitar SELinux.

Las politicas SELinux son reglas de seguridad que definen como los procesos acceden a los ficheros.

Muchos comandos que enumeran reccursos tienen la opción -Z para gestionar los contextos

***ps axZ*** Muestra todos los procesos con su etiqueta SELinux correspondiente
***ls -Z*** Lista ficheros imprimiendo la etiquete SELinux de cada uno

***getenforce*** Para ver el estado actual de SELinux
***setenforce*** Para poner selinux en modo enforcing o permissive

```console
[root@host ~]# getenforce
Enforcing
[root@host ~]# setenforce
usage: setenforce [ Enforcing | Permissive | 1 | 0 ]
[root@host ~]# setenforce 0
[root@host ~]# getenforce
Permissive
[root@host ~]# setenforce Enforcing
[root@host ~]# getenforce
Enforcing
```

***Se recomienda reiniciar el sistema cuando se cambia el modo SELinux***

***/etc/selinux/config*** Fichero para configurar el modo SELinux

SELINUX=enforcing

***/etc/selinux/targeted/contexts/files/*** guarda una base de datos basada en archivos de políticas de etiquetado de archivos en el directorio.

***Copiar un archivo de un directorio a otro puede hacer que su contexto selinux cambie por lo que se recomienda copiarlos con la opción -p --preserve=context de cp en caso de que queramos conservar las etiquetas***

***Si movemos un archvo consevaremos su etiqueta SELinux , esto puede interesarnos a vecces o no , si queremos que la etiqueta SELinux que tenga sea la del directorio origen lo mejor es copiarlo sin -p y luego borrarlos de origen***

#### Cambio de contexto SELinux 

***chcon*** modifica el contexto SELinux es un metodo temporal para probar y depurar. Sobreviven a un reinicio.
***restorecon*** Para aplicar políticas de contexto

El metodo recomendado para cambiar etiquetas SELinux a un fichero es 
***semanage fcontext*** y luego aplicar  el contexto con el comando ***restorecon***

Ejemplo de uso de ***restorecon*** y ***chcon***

  ```console
    # Se crea un directorio con un contexto de SELinux default_t, que heredó del directorio principal /.
    [root@host ~]# mkdir /virtual
    [root@host ~]# ls -Zd /virtual
    unconfined_u:object_r:default_t:s0 /virtual
    # El comando chcon define el contexto de archivo del directorio /virtual en el tipo httpd_sys_content_t.
    [root@host ~]# chcon -t httpd_sys_content_t /virtual
    [root@host ~]# ls -Zd /virtual
    unconfined_u:object_r:httpd_sys_content_t:s0 /virtual
    # La ejecución del comando restorecon restablece el contexto al valor predeterminado de default_t. Observe el mensaje Relabeled.
    [root@host ~]# restorecon -v /virtual
    # Relabeled /virtual from unconfined_u:object_r:httpd_sys_content_t:s0 to unconfined_u:object_r:default_t:s0
    [root@host ~]# ls -Zd /virtual
    unconfined_u:object_r:default_t:s0 /virtual
  ```

***semanage fcontext -l*** Lista los contextos existentes

Para definir una etiqueta selinux se suele usar la expresión regular (/.*)?

***semanage fcontext***
  -a --add      Agregar un registro del tipo de objeto especificado
  -d --delete   Elimnar un registro del tipo de objeto especificado
  -l --list     Muestra registros del tipo de objeto especificado

Para añadir una etiqueta selinux a un directorio
  ```console
       Add file-context httpd_sys_content_t for everything under /web
       # semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
       # restorecon -R -v /web
  ```

### Booleanos de SELinux

Las aplicaciones tienen politicas SELinux especificas para definir su comportamiento  estas pueden incluir opciones que se pueden habilitar o deshabilitar según nos interese . Estas se manejan con los comandos getsebool y setsebool.

```console
[root@host ~]# getsebool httpd_enable_homedirs
httpd_enable_homedirs --> off
```

La configuración de booleanos de SELinux con el comando setsebool sin la opción -P es
temporal y la configuración volverá a los valores persistentes después del reinicio. Vea información
adicional con el comando semanage boolean -l, que enumera los booleanos de los archivos
de política, incluido si un booleano es persistente, los valores predeterminados y actuales y una
breve descripción.

```console
[root@host ~]# semanage boolean -l | grep httpd_enable_homedirs
httpd_enable_homedirs
(off, off) Allow httpd to enable homedirs
[root@host ~]# setsebool httpd_enable_homedirs on
[root@host ~]# semanage boolean -l | grep httpd_enable_homedirs
httpd_enable_homedirs
(on, off) Allow httpd to enable homedirs
[root@host ~]# getsebool httpd_enable_homedirs
httpd_enable_homedirs --> on
```

Para enumerar solo los booleanos con una configuración actual diferente de la configuración predeterminada en el arranque, use el comando semanage boolean -l -C. Este ejemplo tiene el mismo resultado que el ejemplo anterior, sin requerir el filtrado grep.

```console
[root@host ~]# semanage boolean -l -C
SELinux boolean
State Default Description
httpd_enable_homedirs
(on,off) Allow httpd to enable homedirs
```

En el ejemplo anterior se definió temporalmente el valor actual para el booleano httpd_enable_homedirs en on, hasta que el sistema se reinicia. Para cambiar la configuración predeterminada, use el comando setsebool -P para hacer que la configuración sea persistente.

El siguiente ejemplo define un valor persistente y luego visualiza la información booleana del
archivo de política.

```console
[root@host ~]# setsebool -P httpd_enable_homedirs on
[root@host ~]# semanage boolean -l | grep httpd_enable_homedirs
httpd_enable_homedirs
(on,on) Allow httpd to enable homedirs
```

Use el comando semanage boolean -l -C de nuevo. El booleano se muestra a pesar de que la configuración actual y predeterminada es la misma. Sin embargo, la opción -C coincide cuando la configuración actual es diferente de la configuración predeterminada del último arranque.

Para este ejemplo de httpd_enable_homedirs, la configuración de arranque predeterminada original era off.

#### Monitoreo de violaciones SELinux 

Cuando SELinux rechaza una acción se ve reflejada en /var/log/audit/audit.log

Se inspecciona los ficheros ***/var/log/audit/audit.log***  y ***/var/log/messages***

Para encontrar alertas de selinux

```console
[root@host ~]# tail /var/log/audit/audit.log
...output omitted...
type=AVC msg=audit(1649249057.067:212): avc: denied { getattr }
for pid=2332 comm="httpd" path="/var/www/html/mypage"
dev="vda4" ino=9322502 scontext=system_u:system_r:httpd_t:s0
tcontext=unconfined_u:object_r:admin_home_t:s0 tclass=file permissive=0
...output omitted
[root@host ~]# tail /var/log/messages
...output omitted...
Apr 6 08:44:19 host setroubleshoot[2547]: SELinux is preventing /usr/sbin/httpd
from getattr access on the file /var/www/html/mypage. For complete SELinux
messages run: sealert -l 95f41f98-6b56-45bc-95da-ce67ec9a9ab7
...output omitted...
```

En /var/log/messages ejecutaremos la instrucción selinux que nos indique para ver el problema

```console
[root@host ~]# sealert -l 95f41f98-6b56-45bc-95da-ce67ec9a9ab7
SELinux is preventing /usr/sbin/httpd from getattr access on the file /var/www/
html/mypage.
*****
Plugin restorecon (99.5 confidence) suggests
************************
If you want to fix the label.
/var/www/html/mypage default label should be httpd_sys_content_t.
Then you can run restorecon. The access attempt may have been stopped due to
insufficient permissions to access a parent directory in which case try to change
the following command accordingly.
Do
# /sbin/restorecon -v /var/www/html/mypage
*****
Plugin catchall (1.49 confidence) suggests
**************************
If you believe that httpd should be allowed getattr access on the mypage file by
default.
Then you should report this as a bug.
You can generate a local policy module to allow this access.
Do
allow this access for now by executing:
# ausearch -c 'httpd' --raw | audit2allow -M my-httpd
# semodule -X 300 -i my-httpd.pp
Additional Information:
Source Context
Target Context
Target Objects
Source
Source Path
...output omitted...
system_u:system_r:httpd_t:s0
unconfined_u:object_r:admin_home_t:s0
/var/www/html/mypage [ file ]
httpd
/usr/sbin/httpd
Raw Audit Messages
type=AVC msg=audit(1649249057.67:212): avc: denied { getattr }
for pid=2332 comm="httpd" path="/var/www/html/mypage"
dev="vda4" ino=9322502 scontext=system_u:system_r:httpd_t:s0
tcontext=unconfined_u:object_r:admin_home_t:s0 tclass=file permissive=0
type=SYSCALL msg=audit(1649249057.67:212): arch=x86_64 syscall=newfstatat
success=no exit=EACCES a0=ffffff9c a1=7fe9c00048f8 a2=7fe9ccfc8830 a3=100
items=0 ppid=2329 pid=2332 auid=4294967295 uid=48 gid=48 euid=48 suid=48 fsuid=48
egid=48 sgid=48 fsgid=48 tty=(none) ses=4294967295 comm=httpd exe=/usr/sbin/httpd
subj=system_u:system_r:httpd_t:s0 key=(null)
Hash: httpd,httpd_t,admin_home_t,file,getattr
```

***sealert -a /var/log/audit/audit.log*** Para ver las alertas selinux que se han producido se usa el comando 

 ***ausearch*** Para buscar eventos en el directorio /var/log/audit/audit.log . Con la opcion -m AVC buscara eventos AVC y -ts desde un tiempo determinado
 
```console
 [root@host ~]# ausearch -m AVC -ts recent
----
time->Tue Apr 6 13:13:07 2019
type=PROCTITLE msg=audit(1554808387.778:4002):
proctitle=2F7573722F7362696E2F6874747064002D44464F524547524F554E44
type=SYSCALL msg=audit(1554808387.778:4002): arch=c000003e syscall=49
success=no exit=-13 a0=3 a1=55620b8c9280 a2=10 a3=7ffed967661c items=0
ppid=1 pid=9340 auid=4294967295 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0
sgid=0 fsgid=0 tty=(none) ses=4294967295 comm="httpd" exe="/usr/sbin/httpd"
subj=system_u:system_r:httpd_t:s0 key=(null)
type=AVC msg=audit(1554808387.778:4002): avc: denied { name_bind }
for pid=9340 comm="httpd" src=82 scontext=system_u:system_r:httpd_t:s0
tcontext=system_u:object_r:reserved_port_t:s0 tclass=tcp_socket permissive=0
```

 Una vez que vemos el problema en /var/log/audit/audit.log o en /var/log/messages buscar en message como resolverlo, lo suele decir

### Particiones de disco 

Dos tipos de particiones

***MBR*** - Máximo 4 particiones, la cuarta sera extendida que podra contener hasta 15 particiones.  El limite de disco y particion es de 2TiB

***GBR*** - Para sistemas con UEFI firmware. Máximo 128 particiones. Usa un identificador único para cada partición (GUID). Existen dos tablas de partición , una al principio y otra al final de respaldo.


***parted***  Editor de particiones 

***parted dispositivo*** 

***parted dispositivo print*** No imprimira la tabla de particiones del dispositivo indicado.

```console
[root@host ~]# parted /dev/vda print
Model: Virtio Block Device (virtblk)
Disk /dev/vda: 53.7GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
...
```

Si no ponemos nada entraremos en un menu interactivo, si ponemos el dispositivo entraremos en el menu interactivo pero con el dispositivo seleccionado

```console
[root@host ~]# parted /dev/vda
GNU Parted 3.4
Using /dev/vda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print
Model: Virtio Block Device (virtblk)
Disk /dev/vda: 53.7GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:
```

***unit*** De forma predeterminada, el comando parted muestra los tamaños en potencias de 10 (KB, MB,GB). Puede cambiar el tamaño de la unidad con el parámetro unit, que acepta los siguientes valores:
• s para sector
• B para byte
• MiB , GiB o TiB (potencias de 2)
• MB , GB o TB (potencias de 10)

Se puede poner en el prompt o en el menu interactivo

```console
[root@host ~]# parted /dev/vda unit s print
```

***mklabel*** Para particionar una nueva unidad, primero escriba una etiqueta de disco. La etiqueta del disco indica qué esquema de partición usar. Use parted para escribir una etiqueta de disco MBR o una etiqueta de disco GPT.

```console
[root@host ~]# parted /dev/vdb mklabel msdos
[root@host ~]# parted /dev/vdb mklabel gpt
```

***mkpart*** Para crear la particion MBR

Para ver los tipos de particion que podemos crear

```console
[root@host ~]# parted /dev/vdb help mkpart
...output omitted...
mkpart PART-TYPE [FS-TYPE] START END
make a partition
PART-TYPE is one of: primary, logical, extended
FS-TYPE is one of: udf, btrfs, nilfs2, ext4, ext3, ext2, f2fs, fat32, fat16,
hfsx, hfs+, hfs, jfs, swsusp, linux-swap(v1), linux-swap(v0), ntfs,reiserfs, hp-ufs, sun-ufs, xfs, apfs2, apfs1, asfs, amufs5, amufs4, amufs3, amufs2, amufs1, amufs0, amufs, affs7, affs6, affs5, affs4, affs3, affs2, affs1, affs0, linux-swap, linux-swap(new), linux-swap(old)
'mkpart' makes a partition without creating a new file system on the
partition. FS-TYPE may be specified to set an appropriate partition
ID.
```

Cada vez que se crea una partición habra que ejecutar udevadm settle para que el sistema detecte la nueva partición.

Un ejemplo para crear una partición sin necesidad del menu interactivo

```console
[root@host ~]# parted /dev/vdb mkpart primary xfs 2048s 1000MB
```
Para crear particiones GPT se sigue el mismo esquema pero no hay primaria ni extendida

Un ejemplo

```console
[root@host ~]# parted /dev/vdb mkpart userdata xfs 2048s 1000MB

***En la primera partición habra que dejar 2048 sectores (2048s) de espacio que es lo que ocupa la tabla de partición.

```

Luego siempre habra que ejecutar 

```console
[root@host ~]# udevadm settle
```

***rm*** en el menu interactivo y en consola sirve para borrar una partición

```console
[root@host ~]# parted /dev/vdb rm 1
```

Despues de crear una partición lo siguiente sera darle un sistema de ficheros, es decir formatearla 

***mkfs.sistemadeficheros*** Para formatear una partición

```console
[root@host ~]# mkfs.xfs /dev/vdb1
```

***mount*** Para montar una partición de forma temporal

***mount /dispositivo /directorio*** Sin argumentos nos mostrara las particiones montadas

```console
[root@host ~]# mount /dev/vdb1 /mnt

[root@host ~]# mount | grep vdb1
/dev/vdb1 on /mnt type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
``` 

Para montar una partición de forma persistente se usa el fichero /etc/fstab. Consta de seis campos. 

Dispositivo Directorio.de.montaje,Sistema.de.ficheros,opciones,dump,fsck (xfs no usa fsck)

***findmnt --verify*** sirve para comprobar si la sintaxix de /etc/fstab es correcta. Tambien se puede montar de forma manual para comprobar que la linea es correcta

***systemctl daemon-reload*** Se ejecutara siempre que se agregue una linea a fstab para que el sistema se de cuenta de los cambios

Se recomienda el uso de los UUID para hacer referecnia a los dispositivos al montarlos
***lsblk --fs*** Para listar los dispositivos disponibles junto con su UUID correspondiente


### Espacion de intercambio
| RAM                   | Espacio de intercambio | Espacio de intercambio si se permite la hibernación |
|----------------------|------------------------|------------------------------------------------------|
| 2 GB o menos         | Dos veces la memoria RAM | Tres veces la memoria RAM                          |
| Entre 2 GB y 8 GB    | Igual que la memoria RAM | Dos veces la memoria RAM                           |
| Entre 8 GB y 64 GB   | Al menos 4 GB            | 1,5 veces la memoria RAM                           |
| Más de 64 GB         | Al menos 4 GB            | No se recomienda la hibernación                    |

Se pone en el timpo de sistema de ficheros ***linux-swap***

***mkswap partición*** Para formatear una partición SWAP

***swapon -a*** activa todos los espacios de intercambio
***swapon /dev/dispositivo*** Activar la particion de intercambio 
**swapon --free*** 
***free*** Inspecciona los espacios de intercambio disponibles
***swapoff*** Para desactivar la partición de intercambio

Para montarla en /etc/fstab

UUID swap swap defaults o pri=orden de prioridad 0 0 

La prioridad predterminada es -2 . El kernel usa primero  el espacio de intercambio con una prioridad mas alta.

***parted /dev/vda help comando*** Para ayuda de los comandos de parted

### Gestión de la pila de almacenamiento

## LVM

***pv*** Volúmen físico , LVM puede dividir el dispositivo físico en volumenes mas pequeños que forman un bloque de almacenamiento (PV)
***vg*** Grupo de volúmenes. Son conjuntos de almacenamiento formado por uno o mas PVs. Se trata del equivalente a un disco completo físico. Un PV solo puede ser asignado a un VG.
***lv*** Volúmenes lógicos se crean desde extensiones físicas libres en un VG 

Dispositivo Fisico ---> PV (Physical Volume) (Se crea un nuevo dispositivo) --> VG (Volume Group) Se pueden agrupar todos los PVs en un solo VG como si fuera un solo disco --> Creación de LVs (Logical Volume) /dev/project/data, /dev/project/utils,...

***Para identificar particiones en el sistema se usan lsblk, blkid o cat /proc/partitions***

1. Se crean las particiones si no existen el bit lvm tiene que estar en on

  ```console 
  [root@host ~]# parted /dev/vdb mklabel gpt mkpart primary 1MiB 769MiB
  ...output omitted...
  [root@host ~]# parted /dev/vdb mkpart primary 770MiB 1026MiB
  [root@host ~]# parted /dev/vdb set 1 lvm on
  [root@host ~]# parted /dev/vdb set 2 lvm on
  [root@host ~]# udevadm settle
  ```

2. Se crean los PVs con ***pvcreate*** . Se pueden varios en una sola linea

   ```console
   [root@host ~]# pvcreate /dev/vdb1 /dev/vdb2
   Physical volume "/dev/vdb1" successfully created.
   Physical volume "/dev/vdb2" successfully created.
   Creating devices file /etc/lvm/devices/system.devices
   ```
   
3. Se crean los VGs con ***vgcreate***.

   ```console
   [root@host ~]# vgcreate vg01 /dev/vdb1 /dev/vdb2
   Volume group "vg01" successfully created
   ```
   
4. Se crean los LVs en los VGs

   ```console
   [root@host ~]# lvcreate -n lv01 -L 300M vg01
   Logical volume "lv01" created.
   ```
5. Se formatea el LV creado con el sistema de ficheros que queramos ext4,xfs,...

6. Se monta o con mount y si se quiere permanente en /etc/fstab


### Volumen lógico con desduplicación y compresión VDO

1. Se necesita instalar ***vdo*** y ***kmod-vdo***

  ```console
  [root@host ~]# dnf install vdo kmod-kvdo
  ``` 

2. Crear los LVs con  el parametro ***--type vdo*** 

```console
  [root@host ~]# lvcreate --type vdo --name vdo-lv01 --size 5G vg01
  Logical blocks defaulted to 523108 blocks.
  The VDO volume can address 2 GB in 1 data slab.
  It can grow to address at most 16 TB of physical storage in 8192 slabs.
  If a larger maximum size might be needed, use bigger slabs.
  Logical volume "vdo-lv01" created.
```

3. Se formatea y se monta igual que los demas LV

   ```console
   [root@host ~]# mkfs -t xfs /dev/vg01/vdo-lv01
   [root@host ~]# mkdir /mnt/data
   #  Se añade a /etc/fstab 
   /dev/vg01/vdo-lv01 /mnt/data xfs defaults 0 0
   [root@host ~]# mount /mnt/data/
   ```

Para ver los componentes de LVM estan los comandos ***pvdisplay, vgdisplay y lvdisplay***

#### Ampliar un VG

1. Se crea el pv igual que antes
2. Se ejectua el comando ***vgextend*** para añadirlo
   ```console
   [root@host ~]# vgextend vg01 /dev/vdb3
   Volume group "vg01" successfully extended
   ```
#### Ampliar un LV

1. Usar el comando ***lextend*** con la opción ***-L +tamaño*** . Usar ***vgdisplay*** para ver si tiene suficiente espacio para ampliarlo

```console
  [root@host ~]# lvextend -L +500M /dev/vg01/lv01
  Size of logical volume vg01/lv01 changed from 300.00 MiB (75 extents) to 800.00
  MiB (200 extents).
  Logical volume vg01/lv01 successfully resized.
```

***Se puede usar la opción -r de lvextend para hacer los dos pasos a la vez, extend y xfs_growfs***

2. Usar ***xfs_growfs*** para ampliar el LV ampliado en caso de que sea xfs . Se debe montar antes de ampliarlo y el argumento sera el punto de montaje. XFS solo admite ampliación.
   Usar ***resize2fs*** para ampliar el LV o reducirlo. El argumento en este caso es el LV

   ```console
   [root@host ~]# xfs_growfs /mnt/data/
   ...output omitted...
   data blocks changed from 76800 to 204800
   ```

   ```console
   [root@host ~]# resize2fs /dev/vg01/lv01
   resize2fs 1.46.5 (30-Dec-2021)
   Resizing the filesystem on /dev/vg01/lv01 to 256000 (4k) blocks.
   The filesystem on /dev/vg01/lv01 is now 256000 (4k) blocks long.
   ```

#### Ampliación de SWAP

1. Se desconecta el espacio de intercambio
   ```console
   [root@host ~]# swapoff -v /dev/vg01/swap
   swapoff /dev/vg01/swap
   ```
2. Se extiende el espacio con ***lvextend***
   ```console
   [root@host ~]# lvextend -L +300M /dev/vg01/swap
   Size of logical volume vg01/swap changed from 500.00 MiB (125 extents) to 800.00
   MiB (200 extents).
   Logical volume vg01/swap successfully resized.
   ```
3. Se formatea el espacio de intercambio
   ```console
   [root@host ~]# mkswap /dev/vg01/swap
   mkswap: /dev/vg01/swap: warning: wiping old swap signature.
   Setting up swapspace version 1, size = 800 MiB (838856704 bytes)
   no label, UUID=25b4d602-6180-4b1c-974e-7f40634ad660
   ```
4. Se activa el espacio de intercambio
   ```console
   [root@host ~]# swapon /dev/vg01/swap
   ``` 

***pvmove*** Sirve para hacer una copia de seguridad de los metadatos antes de hacer una reducción de un VG , hay que usar la opción -A
```console
[root@host ~]# pvmove -A y /dev/vdb3
```
***vgreduce*** Para eliminar un PV de un VG
```console
[root@host ~]# vgreduce vg01 /dev/vdb3
Removed "/dev/vdb3" from volume group "vg01"
```
### Eliminar almacenamiento LVM 

1. Desmontar con unmount
2. Eliminar LV con ***lvremove***
3. Eliminar VG con ***vgremove***
4. Eliminar Pv con ***pvremove***

### Almacenimiento conectado a la red con NFS

***nfs-utils*** Paquete que hay que instalar para poder montar unidades NFS (Network File System)

```console
[root@host ~]# dnf install nfs-utils
```

NFSv3 usa RPC y el puerto 111 se usa ***showmoun --exports server*** para ver los directorios que hay montados 

```console
[root@host ~]# showmount --exports server
Export list for server
/shares/test1
/shares/test2
```

NFSv4 no usa RPC ***showmount --exports server*** no dara respuesta con NFSv4

Para montar un directorio exportado por un servidor por NFS

1. Creamos el directorio donde lo montaremos la unidad NFS . No se suele recomendar usar /mnt

   ```console
   [root@servera ~]# mkdir /public
   ```
   
3. Montarlo con ***mount -t nfs server:/directorio /directoriolocal

   ```console
   [root@servera ~]# mount -t nfs serverb.lab.example.com:/shares/public /public
   ```
   
4. Comprobar si esta montado

   ```console
   [root@servera ~]# mount | grep public
   serverb.lab.example.com:/shares/public on /public type nfs4
   (rw,relatime,vers=4.2,rsize=262144,wsize=262144,namlen=255,sync,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=172.25.250.10,local_lock=none,addr=172.25.250.11)
   ```
   
5. Para desmontarla usar ***unmount***

   ```console
   [root@servera ~]# umount /public
   ```

6. Montar de forma permanente en ***/etc/fstab***
   
   ```console
   [root@servera ~]# vim /etc/fstab
   # Agregue la línea siguiente al final del archivo:
   serverb.lab.example.com:/shares/public /public nfs rw,sync 0 0
   ```
7. Actualizar systemctl daemon-reload

   ```console
   [root@servera ~]# systemctl daemon-reload
   ```

### Montaje automático de almacenamiento de red

***autofs*** monta automáticamente los sistemas de archivos y las exportaciones a pedido.

Para el automontaje hay que instalar los paquetes ***autofs*** y ***nfs-utils***

```console
[root@host ~]# dnf install autofs nfs-utils
```

Existe ***montaje directo*** y ***montaje indirecto***

Consta de un archivo maestro ubicado en ***/etc/auto.master*** o en ***/etc/auto.master.d***

El fichero tiene que tener extensión ***.autofs*** que tendra la forma 

```console
[root@host ~]# cat /etc/auto.master.d/demo.autofs
mount-point map-file
```
*map-file* sera el fichero de asignación donde configuraremos las propiedades de los puntos de montaje

***indirecto*** Cuando se usa *solo un directorio* para el punto de montaje.
***directo***   Cuando se usa la *ruta completa* para el punto de montaje.

El *map-file* tiene este formato

```console
[root@host ~]# cat /etc/auto.demo
mount-point mount-options source-location
```
***No olvidar instalar autofs y nfs-utils***

### Asignación directa

```console
root@servera ~]# mount -t nfs  serverb.lab.example.com:/shares/direct/external /mnt
[root@servera ~]# ls -l /mnt
total 4
-rw-r--r--. 1 root contractors 22 Apr 7 23:15 README.txt
[root@servera ~]# umount /mnt
```

Crear fichero ***/etc/auto.master.d/direct.autofs***
Editar y poner /- /etc/auto.direct // Es la ruta al fichero donde se montara la unidad
Crear el fichero con la ruta que habremos puesto en /etc/auto.master.d/direct.autofs y montar el dispositivo

Ruta donde lo montaremos   opciones de montaje   Ruta del almacenamiento en el server
/external        -rw,sync,fstype=nfs server:/share/external

### Asignación indirecta

Crear el fichero ***/etc/auto.master.d/indirect.autofs***
Editar y poner la linea
  /CarpetadeMontaje /etc/auto.indirect
Crear fichero /etc/auto.indirect y poner la linea con las opciones de montaje
Asterisco opciones de montaje  Ruta al server terminado en &
\* -rw,sync,fstype=nfs4 server:/share/indirect/&

***No olvidar habilitar autofs***
```console
[root@servera ~]# systemctl enable --now autofs
Created symlink /etc/systemd/system/multi-user.target.wants/autofs.service → /usr/
lib/systemd/system/autofs.service.
```
  



