



| Directorio | Uso |
|------------|-----|
|  `/etc/sudoers.d`  | Donde se colocan configuraciones personalizadas de usuario que pueden hacer sudo y como hacerlo<br><br>```console  %consultants ALL=(ALL) ALL```  |
| `/etc/login.defs` | Las caracteristicas por defecto al crear un usuario , expiración de contraseñas, rangos de uid , gids.<br><br>PASS_MAX_DAYS<br>PASS_MIN_DAYS<br>PASS_WARN_AGE |
| `/etc/dnf/dnf.conf ` | Donde se configuran repositorios adicionales |
| `/run/media/USERNAME/LABEL.USERNAME` | Donde se montan  dispositivos de almacenamiento extraíble . USERNAME es el nombre del usuario que ha iniciado sesión en el entorno gráfico. LABEL es un identificador, que suele ser la etiqueta en el medio de almacenamiento. |
| `/etc/ssh/sshd_config` | Para configurar el servidor sshd se configura el acceso por medio de ssh <br><br> PermitRootLogin <br>  PasswordAuthentication |
| `/var/log/messages`      | La mayoría de los mensajes de syslog. Excluye autenticación, correo, cron y depuración.     |
| `/var/log/secure`        | Mensajes de syslog relacionados con eventos de seguridad y autenticación.                   |
| `/var/log/maillog`       | Mensajes de syslog relacionados con el servidor de correo.                                  |
| `/var/log/cron`          | Mensajes de syslog relacionados con la ejecución de trabajos programados.                   |
| `/var/log/boot.log`      | Mensajes de la consola (no syslog) relacionados con el inicio del sistema.                  |
| `/run/log/ `              | Es donde se guarda el diario del sistema después de un renicio. Se puede cambiar su ubicación dentro de /etc/systemd/journald.conf  |
| `/etc/systemd/journald.conf` | Configuracióndel diario del sistema |
| `/etc/selinux/conifg`    | Directorio para configurar el modo de SELINUX = enforcing|permissive del sistema |
| `/etc/selinux/targeted/contexts/files` | Base de datos de etiquetado de archivos |
| `/var/log/audit/audit.log`   | Problemas que con SELinux |
| `/etc/fstab` | Para montar particiones de forma persistente , consta de seis campos <br> Dispositivo, directorio, sistema de ficheros, opciones, dump, fsck |
| `/etc/containers/registries.conf`   | Es donde configuraremos los registros donde buscara imagenes podman |
| `/etc/selinux/config` | Configuración de modo selinux permissive, enforcing de forma persistente |






