


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


