
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
# El comando chcon define el contexto de archivo del directorio /virtual en el tipo
httpd_sys_content_t.
[root@host ~]# chcon -t httpd_sys_content_t /virtual
[root@host ~]# ls -Zd /virtual
unconfined_u:object_r:httpd_sys_content_t:s0 /virtual
# La ejecución del comando restorecon restablece el contexto al valor predeterminado de
default_t. Observe el mensaje Relabeled.
[root@host ~]# restorecon -v /virtual
# Relabeled /virtual from unconfined_u:object_r:httpd_sys_content_t:s0 to
unconfined_u:object_r:default_t:s0
[root@host ~]# ls -Zd /virtual
unconfined_u:object_r:default_t:s0 /virtual
```
