## Control the Boot Process

***systemctl poweroff*** y ***poweroff** Para todos los servicios y desmonta todos los sistemas de ficheros y apaga el sistema
***systemctl reboot*** y ***reboot*** Para todos los servicios, desmonta todos los sistemas de ficheros y reinicia el sistema.
***systemctl halt*** y ***halt*** Detiene el sistema, es como un botonazo, no usar a menos que lo anterior no funcione.

### Systemd Targets

| Target              | Purpose                                                                 |
|---------------------|-------------------------------------------------------------------------|
| `graphical.target`  | Supports multiple users; provides graphical and text-based logins.      |
| `multi-user.target` | Supports multiple users; provides text-based logins only.               |
| `rescue.target`     | Provides a single-user system for repairing your system.                |
| `emergency.target`  | Starts the minimal system when `rescue.target` fails to start.          |

Para ver las dependencias de cada Systemd target

```console
user@host ~]$ systemctl list-dependencies graphical.target | grep target
graphical.target
* └─multi-user.target
*
├─basic.target
*
│ ├─paths.target
*
│ ├─slices.target
*
│ ├─sockets.target
*
│ ├─sysinit.target
*
│ │ ├─cryptsetup.target
*
| | ├─integritysetup.target
*
│ │ ├─local-fs.target
...output omitted..
```
Para listar todos los Systemd target disponibles

```console
[user@host ~]$ systemctl list-units --type=target --all
UNIT LOAD ACTIVE  SUB DESCRIPTION
---------------------------------------------------------------------------
basic.target loaded active active Basic System
...output omitted...
cloud-config.target loaded active active Cloud-config availability
cloud-init.target  loaded active active Cloud-init target
cryptsetup-pre.target loaded inactive dead Local Encrypted Volumes
```

Cambiar de Systemd target en tiempo de ejecución

```console
[root@host ~]# systemctl isolate multi-user.target
```

No todos los targets permiten hacer *isolate*. Para comprobar si se puede 
```console
[user@host ~]$ systemctl cat graphical.target
# /usr/lib/systemd/system/graphical.target
...output omitted...
[Unit]
Description=Graphical Interface
Documentation=man:systemd.special(7)
Requires=multi-user.target
Wants=display-manager.service
Conflicts=rescue.service rescue.target
After=multi-user.target rescue.service rescue.target display-manager.service
AllowIsolate=yes
[user@host ~]$ systemctl cat cryptsetup.target
# /usr/lib/systemd/system/cryptsetup.target
...output omitted...
[Unit]
Description=Local Encrypted Volumes
Documentation=man:systemd.special(7)
```

***systemctl get-default*** Nos dice el target que hay seleccionado por defecto
***systemctl set-default graphical.target*** Seleccionar un nuevo target

1. Tambien se puede arrancar el sistema con un target determinado en el menu de arranque pulsando la tecla 'e' 
y añadiendo al final de la linea ***systemd.unit=rescue.target*** o el target con el que queramos arrancar
2. Nos movemos al principio de la linea que empieza con ***linux*** y pulsamos end
3. Al final de esa linea escribiremos ***systemd=rescue.target*** o el target que queramos
4. Pulsar ***Ctrl+X***
5. El target rescue nos pedira o pulsar **Ctrl+d** o escribir la contraseña de root
6. Si pulsamos ctrl+d continuara el arranque, si metemos la contraseña de root y luego pulsamos ctrl+d tambien

### Restablecimiento de la contraseña del ROOT

1. Pulsar e al inicio
2. Al final de la linea linux escribir rd.break y pulsar ctrl+x
3. Montar el directorio sysroot ***mount -o remount,rw /sysroot***
4. Hacer jail de sysroot ***chroot /sysroot***
5. Cambiar la password de root ***passwd root***
6. Crear el fichero /.autorelabel para que selinux vuelva a poner contexto a los ficheros ***touch /.autorelabel***
7. Reiniciar

### Diario de registros Persistentes

Los registros se almacenan en ***/etc/log/journal pero se borraan en cada reinicio
Si queremos hacerlo persistentes editamos /etc/systemd/journald.conf y añadimos en el apartado [Journal] Storage=persistent

```console
[root@host ~]# vim /etc/systemd/journald.conf
...output omitted...
[Journal]
Storage=persistent
...output omitted...
[root@host ~]# systemctl restart systemd-journald.service
```

Para inspeccionar los registros de un arranque anterior -b del comando journalctl

```console 
[root@host ~]# journalctl -b -1 -p err
# Para listar los renicios guardados
[root@host ~]# journalctl --list-boots 
IDX BOOT ID                          FIRST ENTRY                  LAST ENTRY                  
 -1 c81f44a5026742b6974d3084471950d1 Fri 2025-10-24 20:41:43 CEST Fri 2025-10-24 20:42:40 CEST
  0 046cb5e4700a4229ad98bd6babe0fb65 Fri 2025-10-24 20:42:48 CEST Fri 2025-10-24 20:46:20 CEST
``` 

### Reparación de problemas de arranque

Al habilitar el servicio debug-shell con el comando systemctl enable debug-
shell.service, el sistema genera una shell root en TTY9 (Ctrl+Alt+F9) al principio de la
secuencia de arranque. Esta shell inicia sesión automáticamente como root, de modo que los
administradores pueden depurar el sistema cuando el sistema operativo aún está arrancando.

>[!CAUTION]
> Deshabilite el servicio debug-shell.service cuando haya finalizado la
> depuración, ya que deja una shell root no autenticada abierta
> a cualquier usuario con acceso a la consola local.

Lo mejor es añadir al final de la linea que empieza por linux systemd.debug-shell y pulsar ctrl+x
Luego pulsaremos Ctrl+alt+F9 para entrar en la shell root para depurar 

Al agregar systemd.unit=rescue.target o systemd.unit=emergency.target delante de la línea de comandos del kernel desde el cargador de arranque, el sistema ingresa a una shell
de emergencia o recuperación en lugar de iniciarse normalmente. Estas dos shells requieren la contraseña root.

El objetivo de emergencia mantiene el sistema de archivos root montado con solo lectura;
mientras que el objetivo de recuperación espera que la unidad sysinit.target se complete,
para que una mayor parte del sistema se inicie (p. ej., servicio de inicio de sesión o sistemas de archivos).
El usuario root en este punto no puede cambiar /etc/fstab hasta que la unidad se vuelva a montar en un estado de lectura y escritura con el comando mount -o remount,rw /.
Los administradores pueden usar estas shells para corregir problemas que impiden que el sistema arranque normalmente; por ejemplo, un bucle de dependencia entre servicios o una entrada incorrecta en /etc/fstab. Cuando se sale de estas shells, continúa el proceso de arranque regular.

***systemctl list-jobs***  Sirve para la identificación de trabajos atascados durante el inicio para ver si alguno ocasiona el problema.


### Reparación del sistema de archivos en el arranque

Para reparar sistemas de archivos en el arranque tenemos el target de emergency . Si suele haber errores en el /etc/fstab es el target que saltara y montara el disco en solo lectura, para reparar algo en el fstab tendremos que volverlo a montar en escritura ya que si no no se escribiran los cambios que hagamos.

***mount -o rw,remount /***

## Seguridad de redes

Para la seguridad tendremos firewalld . Firewalld funciona por zonas predefinidas  que se pueden personalizar

| **Nombre de la zona** | **Configuración predeterminada** |
|-----------------------|----------------------------------|
| `trusted`             | Permite todo el tráfico entrante. |
| `home`                | Rechaza el tráfico entrante, a menos que esté relacionado con tráfico saliente o que relacione los servicios predefinidos `ssh`, `mdns`, `ipp-client`, `samba-client` o `dhcpv6-client`. |
| `internal`            | Igual que la zona `home`: rechaza el tráfico entrante salvo tráfico saliente o servicios predefinidos `ssh`, `mdns`, `ipp-client`, `samba-client` o `dhcpv6-client`. |
| `work`                | Rechaza el tráfico entrante, salvo tráfico saliente o servicios predefinidos `ssh`, `ipp-client` o `dhcpv6-client`. |
| `public`              | Rechaza el tráfico entrante, salvo tráfico saliente o servicios predefinidos `ssh` o `dhcpv6-client`. Es la zona predeterminada para interfaces de red recién agregadas. |
| `external`            | Rechaza el tráfico entrante salvo tráfico saliente o servicio `ssh`. El tráfico IPv4 saliente reenviado se enmascara como si se originara desde la dirección IPv4 de la interfaz saliente. |
| `dmz`                 | Rechaza el tráfico entrante salvo tráfico saliente o servicio `ssh`. |
| `block`               | Rechaza todo el tráfico entrante, salvo tráfico saliente. |
| `drop`                | Deja caer todo el tráfico entrante, salvo tráfico saliente. No responde ni con errores ICMP. |

Para conocer las zonas predefinidas se usa ***firewall-cmd --list-all-zones***

### Servicios predefinidos

Firewalld incluye servicios ya predefinidos para lugares comunies para simplificar las reglas de firewall. 
Para ver los servicios prefefinidos ***firewall-cmd --get-services***

| **Nombre del servicio** | **Configuración** |
|-------------------------|-------------------|
| `ssh`                  | Servidor SSH local. Tráfico a `22/tcp`. |
| `dhcpv6-client`        | Cliente DHCPv6 local. Tráfico a `546/udp` en la red `fe80::/64` IPv6. |
| `ipp-client`           | Impresión IPP local. Tráfico a `631/udp`. |
| `samba-client`         | Archivo Windows local y cliente de intercambio de impresión. Tráfico a `137/udp` y `138/udp`. |
| `mdns`                 | Resolución del nombre del enlace local DNS (mDNS) multidifusión. Tráfico a `5353/udp` a las direcciones de multidifusión `224.0.0.251` (IPv4) o `ff02::fb` (IPv6). |
| `cockpit`              | Interfaz web de Red Hat Enterprise Linux para gestionar y monitorear su sistema local y remoto. Tráfico al puerto `9090`. |

Para configurar el firewall 

| **Comando `firewall-cmd`** | **Explicación** |
|----------------------------|-----------------|
| `--get-default-zone` | Consulta la zona predeterminada actual. |
| `--set-default-zone=ZONE` | Establece la zona predeterminada. Cambia tanto la configuración en tiempo de ejecución como la permanente. |
| `--get-zones` | Lista todas las zonas disponibles. |
| `--get-active-zones` | Lista todas las zonas actualmente en uso (con una interfaz o fuente asociada), junto con su información. |
| `--add-source=CIDR [--zone=ZONE]` | Redirige todo el tráfico desde la IP o red especificada a la zona indicada. Si no se especifica `--zone=`, se usa la zona predeterminada. |
| `--remove-source=CIDR [--zone=ZONE]` | Elimina la regla que redirige tráfico desde la IP o red a la zona. Si no se especifica `--zone=`, se usa la zona predeterminada. |
| `--add-interface=INTERFACE [--zone=ZONE]` | Redirige todo el tráfico desde la interfaz a la zona indicada. Si no se especifica `--zone=`, se usa la zona predeterminada. |
| `--change-interface=INTERFACE [--zone=ZONE]` | Asocia la interfaz con otra zona. Si no se especifica `--zone=`, se usa la zona predeterminada. |
| `--list-all [--zone=ZONE]` | Lista todas las interfaces, fuentes, servicios y puertos configurados para la zona. Si no se especifica `--zone=`, se usa la zona predeterminada. |
| `--list-all-zones` | Recupera toda la información de todas las zonas (interfaces, fuentes, puertos y servicios). |
| `--add-service=SERVICE [--zone=ZONE]` | Permite tráfico hacia el servicio indicado. Si no se especifica `--zone=`, se usa la zona predeterminada. |
| `--add-port=PORT/PROTOCOL [--zone=ZONE]` | Permite tráfico hacia el puerto/protocolo indicado. Si no se especifica `--zone=`, se usa la zona predeterminada. |
| `--remove-service=SERVICE [--zone=ZONE]` | Elimina el servicio de la lista permitida para la zona. Si no se especifica `--zone=`, se usa la zona predeterminada. |
| `--remove-port=PORT/PROTOCOL [--zone=ZONE]` | Elimina el puerto/protocolo de la lista permitida para la zona. Si no se especifica `--zone=`, se usa la zona predeterminada. |
| `--reload` | Descarta la configuración en tiempo de ejecución y aplica la configuración persistente. |

Ejemplo 

```console
[root@host ~]# firewall-cmd --set-default-zone=dmz
[root@host ~]# firewall-cmd --permanent --zone=internal \
--add-source=192.168.0.0/24
[root@host ~]# firewall-cmd --permanent --zone=internal --add-service=mysql
[root@host ~]# firewall-cmd --reload
```
Siempre que abramos o cerremos un puerto o servicio incluiremos la zona a la que nos referimos y --permanent para que sea permanente. 
Para que sea efectivo habra que recargar la configuración con ***firewall-cmd --reload***

Para comprobar el servicio https instalado usar ***curl -k https://host*** la opción -k hace que curl no active el modo seguro de https que daria error sin certificados.

### Etiquetado SELinux de Puertos

***cat /etc/services*** Para listar los servicios disponibles y sus puertos
***grep nombreservicio /etc/service*** Si queremos filtrar los servicios y buscar uno concreto

***semanage port -l*** Para listar las etiquetas de puertos asignadas en ese momento
***semanage port -l | grep ftp** Para filtrar una en concreto
***semanage port -l | grep -w 80*** Para filtrar un puerto por su número, -w en grep busca una palabra determinada y no la cadena.

***semanage port -a -t port_label -p tcp|upd NUM_PUERTO*** Para aplicar etiquetas existentes a un nuevo puerto.

```console
[root@host~]# semanage port -a -t gopher_port_t -p tcp 71
[root@host~]# semanage port -a -t http_port_t  -p tcp 80
```

***semanage port -l -C*** Para comprobar los cambios realizados

Para instalar las páginas del manual que incluye información especifica del servicio sobre tipos de SELinux y booleanos y puertos y luego buscarlo ***man -k _selinux***

```console
[root@host ~]# dnf -y install selinux-policy-doc
[root@host ~]# man -k _selinux
```

***semanage port -d -t PORT_LABEL -p tcp|upd NUMERO_PUERTO*** Para eliminar una etiqueta de un puerto
***semanage port -m -t PORT_LABEL -p tcp|udp NUMERO_PUERTO*** Para modificar una etiqueta de un puerto

```console
root@host ~]# semanage port -d -t gopher_port_t -p tcp 71

[root@server ~]# semanage port -m -t http_port_t -p tcp 71

[root@server ~]# semanage port -l -C
SELinux Port Type Proto  Port Number
http_port_t              tcp    71
[root@server ~]# semanage port -l | grep http
http_cache_port_t tcp 8080, 8118, 8123, 10001-10010
http_cache_port_t udp 3130
http_port_t tcp 71, 80, 81, 443, 488, 8008, 8009, 8443,9000 
pegasus_http_port_t tcp 5988
pegasus_https_port_t tcp 5989
```

## Instalación de Red Hat 

***composer-cli*** Sirve para crear imagenes con Image builder. Tambien se puede hacer con la consola web.

### Kickstart

Las directivas comienzan con % y terminan con %end

***%packages*** Indica que se debe incluir en la instalación.

@ denota grupo de paquetes 
@^ dentan grupos de entorno( grupos de grupos de paquetes)

@module:stream/profile

Para excluir un paquete se indica precedido de un guión (-)

Un fichero kickstart incluye una o más secciones ***%pre*** y ***%post*** que contienen scripts que pueden ser en bash o python.
Evitar la sección %pre ya que los errores ahí son dificiles de detectar.
Puede haber tantas secciones pre y post como queramos.

***Comandos de instalación*** 
 
***url** Especifica la url donde apunta los medios de instalación

`url --url="http://classroom.example.com/content/rhel9.0/x86_64/dvd/"`

***repo*** Especifica donde encontrar paquetes adicionales para la instalación.

`repo --name="appstream" --baseurl=http://classroom.example.com/content/rhel9.0/x86_64/dvd/AppStream/`

***text*** Fuerza la instalación modo texto

***vnc*** Habilita un vnc para la instalación gráfica de forma remota a traves de VNC

`vnc --password=redhat`

***clearpart*** Elimina particiones del sistema antes de la creación de particiones

`clearpart --all --drives=vda,vdb`

***part*** Especifica el tamaño , el formato y el nombre de una partición. Obligatorio a menos que esten los comandos autopart o mount

`part /home --fstype=ext4 --label=homes --size=4096 --maxsize=8192 --grow`

***autopart*** Crea las particiones básicas de forma automatica

***ignoredisk*** Evite que Anaconda modifique los discos , es util junto con autopart

`ignoredisk --drives=sdc`

***bootloader:*** define dónde instalar el cargador de arranque. Obligatorio.

`bootloader --location=mbr --boot-drive=sda`

***volgroup, logvol*** crea grupos de volúmenes LVM y volúmenes lógicos.

`
part pv.01 --size=8192
volgroup myvg pv.01
logvol / --vgname=myvg --fstype=xfs --size=2048 --name=rootvol --grow
logvol /var --vgname=myvg --fstype=xfs --size=4096 --name=varvol
`

***zerombr*** inicializa los discos cuyo formato no se reconoce.


***network*** configura la información de la red para el sistema objetivo. Activa los dispositivos de red en el entorno del instalador.

`network --device=eth0 --bootproto=dhcp `

***firewall***  define la configuración del firewall para el sistema objetivo.

`firewall --enabled --service=ssh,http`

Comandos de ubicación y seguridad

Los siguientes comandos de Kickstart configuran los ajustes de seguridad, idioma y región:

• lang: establece el idioma que se utilizará en el sistema instalado. Obligatorio.
`lang en_US`
• keyboard: establece el tipo de teclado del sistema. Obligatorio.
`keyboard --vckeymap=us`
• timezone: define la zona horaria y si el reloj del hardware usa UTC. Obligatorio.
`timezone --utc Europe/Amsterdam`
• timesource: habilita o deshabilita NTP. Si habilita NTP, debe especificar servidores o
conjuntos de NTP.
` timesource --ntp-server classroom.example.com`
• authselect: establece opciones de autenticación. Las opciones que reconoce el comando authselect son válidas para este comando. Consulte authselect (8).
• rootpw: define la contraseña inicial del usuario root. Obligatorio.
`rootpw --plaintext redhat
o
rootpw --iscrypted $6$KUnFfrTzO8jv.PiH$YlBbOtXBkWzoMuRfb0.SpbQ....XDR1UuchoMG1`
• selinux: establece el modo de SELinux para el sistema instalado.
`selinux --enforcing`
• services: modifica el conjunto de servicios predeterminado que se ejecutan en el objetivo systemd predeterminado.
`services --disabled=network,iptables,ip6tables --enabled=NetworkManager,firewalld`
• group, user: crea un grupo o usuario locales en el sistema.
`group --name=admins --gid=10001
 user --name=jdoe --gecos="John Doe" --groups=admins`

Comandos varios

Los siguientes comandos de Kickstart configuran el registro del estado de energía del host al
finalizar:
• logging: este comando define cómo Anaconda realiza el registro durante la instalación.
`logging --host=loghost.example.com`
• firstboot: si está habilitado, el Agente de configuración se inicia la primera vez que el sistema arranca. Este comando requiere el paquete initial-setup.
`firstboot --disabled`
• reboot, poweroff, halt: especifican la medida final cuando finalice la instalación. El valor predeterminado es la opción halt.

Ejemplo de un archivo Kickstart

En el siguiente ejemplo, la primera parte del archivo Kickstart consta de los comandos de
instalación, como la partición del disco y la fuente de instalación.

```
#version=RHEL9
# Define system bootloader options
bootloader --append="console=ttyS0 console=ttyS0,115200n8 no_timer_check
net.ifnames=0 crashkernel=auto" --location=mbr --timeout=1 --boot-drive=vda
# Clear and partition disks
clearpart --all --initlabel
ignoredisk --only-use=vda
zerombr
part / --fstype="xfs" --ondisk=vda --size=10000
# Define installation options
text
repo --name="appstream" --baseurl="http://classroom.example.com/content/rhel9.0/
x86_64/dvd/AppStream/"
url --url="http://classroom.example.com/content/rhel9.0/x86_64/dvd/"
# Configure keyboard and language settings
keyboard --vckeymap=us
lang en_US
# Set a root password, authselect profile, and selinux policy
rootpw --plaintext redhat
authselect select sssd
selinux --enforcing
firstboot --disable
# Enable and disable system services
services --disabled="kdump,rhsmcertd" --enabled="sshd,rngd,chronyd"
# Configure the system timezone and NTP server
timezone America/New_York --utc
timesource --ntp-server classroom.example.com
%packages
@core
chrony
cloud-init
dracut-config-generic
dracut-norescue
firewalld
grub2
kernel
rsync
tar
-plymouth
%end
%post
echo "This system was deployed using Kickstart on $(date)" > /etc/motd
%end
También puede especificar un script de Python con la opción --interpreter.
%post --interpreter="/usr/libexec/platform-python"
print("This line of text is printed with python")
%end
```

Se pueden hacer con el editor de texto o con el sitio web https://access.redhat.com/labs/kickstartconfig

En ***/root/anaconda-ks.cfg*** existe el fichero que se uso en la instalación del sistema, se puede modificar 

***ksvalidator*** sirve para validar el fichero kickstart y ver que no tiene errores

```[user@host ~]$ ksvalidator /tmp/anaconda-ks.cfg```

***ksverdiff*** Para ver diferencia de sintaxis entre diferentes versiones.

```
[user@host ~]$ ksverdiff -f RHEL8 -t RHEL9
The following commands were removed in RHEL9:
device deviceprobe dmraid install multipath
The following commands were deprecated in RHEL9:
autostep btrfs method
The following commands were added in RHEL9:
timesource
...output omitted...
```

Para instalarlo ***dnf install pykickstart***

Una vez elegido un método de Kickstart, se le indica al instalador dónde ubicar el archivo Kickstart
al pasar el parámetro inst.ks=LOCATION al kernel de instalación.

Ejemplos 

• inst.ks=http://server/dir/file
• inst.ks=ftp://server/dir/file
• inst.ks=nfs:server:/dir/file
• inst.ks=hd:device:/dir/file
• inst.ks=cdrom:device


### Verificación de los requisitos del sistema

***dnf group install "Virtualization Host"*** Para instalar el paquete de virtualización

Para comprabar los requisitos del sistema 

```
[root@host ~]# virt-host-validate
QEMU: Checking for hardware virtualization  :PASS
QEMU: Checking if device /dev/kvm exists     :PASS
....
```
#### Instalar una maquina Virtual

***virt-install*** 

```console
[root@host ~]# virt-install --name demo --memory 4096 --vcpus 2 --disk size=40 \
--os-type linux --cdrom /root/rhel.iso
...output omitted...
```

Instalar el paqute para crear maquinas virtuales desde el cockpit

```console
[root@host ~]# dnf install cockpit-machines
[root@host ~]# systemctl enable --now cockpit.socket

```
