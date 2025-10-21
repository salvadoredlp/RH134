
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


Capitulo 8 página 213
