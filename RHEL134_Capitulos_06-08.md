
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

***sealert -a /var/log/audit/audit.log*** Para ver las alertas selinux que se han producido se usa el comando 

 ***ausearch*** Para buscar eventos en el directorio /var/log/audit/audit.log

 Una vez que vemos el problema en /var/log/audit/audit.log o en /var/log/messages buscar en message como resolverlo, lo suele decir


Capitulo 8 página 213
