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


