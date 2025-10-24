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

Tambien se puede arrancar el sistema con un target determinado en el menu de arranque pulsando la tecla 'e' 
y añadiendo al final de la linea ***systemd.unit=rescue.target*** o el target con el que queramos arrancar
Pulsar ***Ctrl+X***


