


### Subscripciones ###

***rhc*** sustituye a ***subscription-manager*** a partir de la verisón 8.8 o para registrar sistemas con un Red Hat Satellite Server
  Se puede usar con las opciones -a la clave de activación y -o el identificador númerico

```console
root@host ~]# rhc connect -a host_key -o 117018
Connecting host.lab.example.com to Red Hat.
This might take a few seconds.
● Connected to Red Hat Subscription Management
● Connected to Red Hat Insights
● Activated the Red Hat connector daemon
Manage your Red Hat connector systems: https://red.ht/connector
```
Tambien se puede usar con el nombre de usuario y contraseña

```console
rhc connect -u <_username_> -p <_password_>
```

***rhc disconnect*** para anular el registro del sistema.

#### Suscripciones ####

***subscription-manager register --username <yourusername>*** Sirve para  adjuntar un sistema a las suscripciones compatibles que mejor coincidan con su sistema

***subscription-manager list --available*** Listar las suscripciones disponibles

***subscription-manager attach --auto*** Adjuntar suscripciones automáticamente

***subscription-manager attach --pool=poolID*** Adjuntar una suscripción de un conjunto (pool) específico de la lista de suscripciones disponibles:

***subscription-manager list --consumed*** Visualizar las suscripciones consumidas

***subscription-manager unregister*** Eliminar la suscripción de un sistema:

En Red Hat los certificados digitales almacenan información actual sobre los derechos en el sistema local. El sistema registrado almacena los certificados de derechos en el directorio /etc/pki.

• Los certificados /etc/pki/product indican los productos de Red Hat instalados.  
• Los certificados /etc/pki/consumer identifican la cuenta de Red Hat para el registro.  
• Los certificados /etc/pki/entitlement indican cuáles son las suscripciones que están adjuntadas.  

El comando rct inspecciona los certificados y el comando subscription-manager examina las suscripciones adjuntas en el sistema.

### Paquetes de software y RPM ###

***rpm -qa***          Enumera los paquetes instalados.
***rpm -qf FILENAME*** Determina qué paquete proporciona el FILENAME.
***rpm -q***           Detalla la versión del paquete instalado actualmente.
***rpm -qi***          Brinda información detallada sobre un paquete.
***rpm -ql***          Enumera los archivos instalados con el paquete.
***rpm -qc***          Lista solo la ficheros de configuración de los paquetes instalados
***rpm -qd***          Lista los paquetes de documentación que tiene ese paquete. 
***rpm -q --scripts*** Lista de los shell scripts que se ejecutan antes o despues de instalar los paquetes

***rpm -q --changelog*** Lista los cambios en el registro que ha hecho el paquete.

***rpm -qlp***       Enumera los archivos instalados con el paquete local.

```console
[user@host ~]$ rpm -qlp podman-4.0.0-6.el9.x86_64.rpm
/etc/cni/net.d
/etc/cni/net.d/87-podman-bridge.conflist
/usr/bin/podman
...output omitted...
```

#### Instalar RPM Packages ####

***rpm -ivh***    Instalar los paquetes rpm 

```console
[root@host ~]# rpm -ivh podman-4.0.0-6.el9.x86_64.rpm
Verifying...
################################# [100%]
Preparing...
################################# [100%]
Updating / installing...
podman-2:4.0.0-6
################################# [100%]
```

***rpm2cpio*** Extraer ficheros de un paquete rpm, hay que usar el comando ***cpio -idv*** para extraer los ficheros 

```console
[user@host tmp-extract]$ rpm2cpio httpd-2.4.51-7.el9_0.x86_64.rpm | cpio -idv
./etc/httpd/conf
./etc/httpd/conf.d/autoindex.conf
./etc/httpd/conf.d/userdir.conf
./etc/httpd/conf.d/welcome.conf
./etc/httpd/conf.modules.d
```

La opción ***-t*** de cpio sirve para listar los paquetes rpm que hay en el paquete.

### DNF ### 

***dnf*** Sustituye a yum y sirve para instalar paquetes y sus dependencias.

***dnf list*** Nos muestra información de paquetes instalados con el nombre que le digamos. 

Buscara todos los paquetes http 

```console
[user@host ~]$ dnf list 'http*'
```

***dnf search all 'keyword'*** Lista todos los paquetes que tienen el nombre o en su descripción la palabra clave que buscamos

***dnf info nombrepaquete*** Nos da información sobre el paquete que instalara


***dnf provides PATH*** Muestra paquetes que coinciden con el nombre de ruta especificado (que a menudo , incluye caracteres comodines). 

```console
[user@host ~]$ dnf provides /var/www/html
httpd-filesystem-2.4.51-5.el9.noarch : The basic directory layout for the Apache
HTTP Server
Repo : rhel-9.0-for-x86_64-appstream-rpms
Matched from:
Filename : /var/www/html
```

***dnf install NOMBREPAQUETE*** Obtiene e instala el paquete de software indicado junto con sus dependencias.

***dnf update PACKAGE*** obtiene e instala la versión posterior del paquete especificado, si no se pone nombre del paquete instala todas las actualizaciones relevantes.

***dnf list kernel*** Lista todos los kernels instalados y disponibles

***uname -r*** Muestra versión del kernel utilizada 

***uname -a*** Muestra el lanzamiento e información adicional del kernel

***dnf remove PACKAGENAME*** Quita los paquetes enumerados y cualquier paquete que requiere qu ese eliminen los paquetes.


Los grupos son colecciones de software , colecciones de paquetes.
Los grupos tienen software de tres tipos:

- mandatory (obligatorios) Se tienen que instalar con el grupo
- default Se instalan en el grupo
- optional No se instalan cuando se instala el grupo o a menos que se lo solicite
  
***dnf group list***

***dnf group info NOMBRE DE GRUPO DE PAQUETES***

***dnf group list hidden*** Lista los grupos ocultos que no son los predeterminados

***dnf group install "nombre del grupo***

#### Visualización de las transacciones ####

Todas las transacciones de instalacion de paquetes se registran en ***/var/log/dnf.rpm.log***

***dnf history*** muestra las últimas transacciones realizadas, de instalación o eliminación.

***dnf history undo 6*** Invierte la transacción hecha en 6º lugar


Página 447  427
