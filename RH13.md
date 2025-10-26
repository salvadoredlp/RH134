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
 

