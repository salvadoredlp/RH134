## Contenedores

***podman*** Herramienta para gestionar contenedores
***podman -v**** Versión de podman que se usa
***podman images*** Images que tenemos guardadas en nuestro sistema
***podman pull registro de imagenes*** Para descargar una imagen contenerizada para despues usarla
***podman run imagen*** Para crear un contenedor a partir de la imagen que tenemos

Podemos ejecutar tras ***podman run*** un comando que se ejecutara dentro del contenedor 

```console
[user@host ~]$ podman run registry.redhat.io/rhel9/rhel-guest-image:9.4 \
echo 'Red Hat'
Red Hat
```
Esta contenedor terminara despues de ejecutar echo ya que no hay otro proceso que lo mantenga en funcionamiento.

Si el contenedor que intentamos crear no lo tenemos en nuestro sistema , podman intentara descargarlo de un registro antes de ejecutarlo.

***podman ps*** Para listar los contenedores creados en funcionamiento
***podman ps --all*** Lista los contenedores creados , los parados y los que estan en funcionamiento
***podman run --rm  imagen comando*** Con la opción -rm le indicamos que borre la imagen una vez que termine de ejecutarse el contenedor

Si no se indica un nombre podman generara un noombre al azar para él. El nombre tiene que ser único.

***podman --name nombrecontenedor*** para indicar el nombre que llevara el contenedor

```console
[user@host ~]$ podman run --name podman_rhel9 \
registry.redhat.io/rhel9/rhel-guest-image:9.4 echo 'Red Hat'
Red Hat
```
Los contenedores se identifican por un UUID único y por el nombre

Para sacar un listado con formato json usar --type json , tambien se puede sacar en formato Go

```
[user@host ~]$ podman ps --all --format=json
{
"AutoRemove": false,
"Command": [
"echo",
"Red Hat"
],
...output omitted...
"Id": "2024...b0b2",
"Image": "registry.redhat.io/rhel9/rhel-guest-image:9.4",
...output omitted...
```

***podman run -p puertoexterior:puertointerior*** Para mapear el puerto que usara el contenedor
***podman run -d*** La opción -d nos permitira que el contenedor no bloquee la consola y que se ejute en segundo plano, devolviendonos el control a la consola
***podman -e VARIABLEENTORNO=valor*** Nos permite definir variables de entorno dentro del contenedor , por convenio van en mayusculas.

Red Hat distribuye imágenes de contenedores mediante dos registros

• registry.access.redhat.com: no requiere autenticación
• registry.redhat.io: requiere autenticación

Cuando extrae una imagen de contenedor, proporciona una serie de detalles. Por ejemplo, el nombre de la imagen registry.access.redhat.com/ubi9/nodejs-18:latest consta de
la siguiente información:

• URL del registro: registry.access.redhat.com
• Usuario u organización: ubi9
• Repositorio de imágenes: nodejs-18
• Etiqueta de la imagen: latest

Los desarrolladores pueden especificar nombres cortos y no calificados que omite la url del registro . 
Por ejemplo puede acortar registry.redhat.io/ubi9/nodejs-18:latest a ubi9/nodejs-18:latest.

```console
[user@host ~]$ podman pull ubi9/nodejs-18
```

Si no se proporciona la url del registro Podman usa el fichero ***/etc/registries.conf*** que podrían contener el nombre de la imagen, Este archivo contiene los registros  para buscar la imagen en 
orden de preferencia

***skopeo*** Es una herramienta para trabajar con imágenes de contenedores. Puede inspeccionar imagenes de contenedores remotos, copiar imagenes entre registros, firmar con claves OPENPGP
convertir formato de imagen, de docker a OCI.

Puede inspeccionar una imagen sin descargarla en local, usa el formato tranposrt:image 
docker://remote_image, dir:path o oci:path:tag.

***skopeo inspect*** para leer los metadatos de una imagen.

```console
[user@host ~]$ skopeo inspect \
docker://registry.access.redhat.com/ubi9/nodejs-18
{
"Name": "registry.access.redhat.com/ubi9/nodejs-18",
"Digest": "sha256:741b...22e0",
"RepoTags": [
...output omitted..
```

Para copiar imágenes entre registros.
```console
[user@host ~]$ skopeo copy \
docker://registry.access.redhat.com/ubi9/nodejs-18 docker://quay.io/myuser/nodejs-18
Getting image source signatures
...output omitted...
```
 Tambien se puede  usar para descargar una imágen en un directorio local

```console
[user@host ~]$ skopeo copy \
docker://registry.access.redhat.com/ubi9/nodejs-18 \
dir:/var/lib/images/nodejs-18
Getting image source signatures
...output omitted...
 ```

 ***podman login nombre.deregistro*** Para autenticarse en un registro que requiera autenticación.

 ```console
[user@host ~]$ podman login registry.redhat.io
Username: YOUR_USER
Password: YOUR_PASSWORD
Login Succeeded!
[user@host ~]$ podman pull registry.redhat.io/rhel9/nginx-120
Trying to pull registry.redhat.io/rhel9/nginx-120:latest...
Getting image source signatures
...output omitted...
``` 
 
Las herramientas como skopeo estan en el paquete ***container-tools***

```console
[student@servera ~]$ sudo dnf install container-tools
[sudo] password for student: student
...output omitted...
Is this ok [y/N]: y
...output omitted...
Complete!
```

Se pueden usar formato de plantillas GO para sacar la información de inspect a parte de en formato jSON

Para buscar campos se puede sacar en formato json y luego pasarlo por grep -A nºlineas que quiro mostrar despues de la busqueda.

``` 
[user@host ~]$ podman inspect 7763097d11ab | grep -A 4 State
"State": {
"OciVersion": "1.0.2-dev",
"Status": "running",
"Running": true,

[user@host ~]$ podman inspect \
--format='{{.State.Status}}' redhat
running
```

***podman stop nombre o UUID del contenedor*** Para el contenedor. Cuando se para podman envia SIGTERM al contenedor.

***podman stop --all*** Parar todos los contenedores en marcha . Si no paran con SIGTERM podman les enviara SIGKILL, puedes cambiar el tiempo que espera
para enviar SIGKILL con la opción ***-time*** 

```console [user@host ~]$ podman stop --time=100 ```

***podman kill*** Enviar SIGKILL a un contenedor para que acabe si o si.
***podman pause*** Pausarlo
***podman unpause*** Reanudar un contenedor en pausa
***podman restart nginx*** Reiniciar un contenedor
***podman rm*** Borrar un contenedor, no puede estar en ejecución, hay que pararlo antes.
***podman rm iddelcontenedor --force*** Se puede forzar borrarlo con la opción --force
***podman rm --all*** Borrar todos los contenedores

#### Almacenamiento persistente

***podman unshare*** sirve para entrar en un entorno especial donde puedes hacer cosas como si fueras "root", pero sin tener permisos reales de administrador. Es útil para montar contenedores o limpiar datos cuando eres un usuario normal.Este comando te mete en un espacio de nombres de usuario donde tu UID (por ejemplo 1000) se convierte en UID 0 (root virtual). Así puedes ejecutar comandos que normalmente requieren permisos elevados, pero sin afectar el sistema real.

***podman exec -it*** Ejecuta un comando dentro del contenedor, la opción -it nos da la posibilidad d etener une sesion bash interactiva dentro del contenedor

```console
[user@host ~]$ podman exec -it db01 grep mysql /etc/passwd
mysql:x:27:27:MySQL Server:/var/lib/mysql:/sbin/nologin
```

Por ejemplo , para montar el directorio /home/user/db_data en el contenedor db01 para proporcionar
almacenamiento persistente en el directorio /var/lib/mysql del contenedor. A continuación,
cree el directorio /home/user/db_data y use el comando podman unshare para establecer el
UID y el GID del espacio de nombres de usuario de 27 como propietario del directorio.
El UID y el GID de 27 en el contenedor se asignan al UID y al GID de 100026 en la máquina host.
Puede verificar la asignación al ver la propiedad del directorio /home/user/db_data con el
comando ls.

```console
[user@host ~]$ mkdir /home/user/db_data
[user@host ~]$ podman unshare chown 27:27 /home/user/db_data

[user@host ~]$ ls -l /home/user/
total 0
drwxrwxr-x. 3 100026 100026 18 May
...output omitted...
```

Ahora es cuando se podría usar la opción -v para montar almacenamiento persistente en un contenedo
```
[user@host ~]$ podman run -d --name db01 \
-e MYSQL_USER=student \
-e MYSQL_PASSWORD=student \
-e MYSQL_DATABASE=dev_data \
-e MYSQL_ROOT_PASSWORD=redhat \
-v /home/user/db_data:/var/lib/mysql \
registry.lab.example.com/rhel8/mariadb-105
```

***podman container logs nombrecontenedor*** Para ver los errores en el log del contenedor

Para que los directorios se puedan montar como almacenamiento persistente tienen que tener la etiquta selinux ***container_file_t***
Tambien se puede poner al final de  la linea de montaje la letra :Z para que establezca el formato SELinux necesario

```console
[user@host ~]$ podman run -d --name db01 \
-e MYSQL_USER=student \
-e MYSQL_PASSWORD=student \
-e MYSQL_DATABASE=dev_data \
-e MYSQL_ROOT_PASSWORD=redhat \
-v /home/user/db_data:/var/lib/mysql:Z \
registry.lab.example.com/rhel8/mariadb-105
```

#### Generar un archivo de unidad de Systemd

***podman generate systemd --name*** Para generar un fichero que nos permita crear un servicio basasdo en el contenedor ejecutado  
El fichero creado se agregrara a ~/.config/systemd/user

Luego actulizaremos los ficheros con ***systemctl --user daemon-reload***

Para crear el servicio 

```console
[user@host ~]$ systemctl --user [start, stop, status, enable, disable] container-web.service

# Cuando usa la opción --user, de manera predeterminada, systemd inicia el servicio cuando inicia
# sesión y lo detiene cuando cierra sesión. Puede iniciar sus servicios habilitados en el arranque del
# sistema operativo y detenerlos en el cierre, ejecutando el comando loginctl enable-linger.

[user@host ~]$ loginctl enable-linger
```
