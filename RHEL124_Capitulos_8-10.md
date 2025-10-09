## Procesoso ##

### Estados de procesos ###

| Nombre             | Indicador         | Descripción                                                                 |
|--------------------|-------------------|------------------------------------------------------------------------------|
| En ejecución       | `R` / `TASK_RUNNING` | El proceso se está ejecutando en una CPU o está listo para ejecutarse. Puede estar ejecutando rutinas de usuario o del kernel. |
| En espera          | `S` / `TASK_INTERRUPTIBLE` | El proceso espera una condición (hardware, recurso o señal). Al cumplirse, vuelve al estado de ejecución. |
| En espera (bloqueado) | `D` / `TASK_UNINTERRUPTIBLE` | Similar al estado S, pero no responde a señales. Se usa cuando interrumpirlo podría causar errores en el dispositivo. |
| Anulable           | `K` / `TASK_KILLABLE` | Variante del estado D que permite responder a señales de anulación (kill). Se muestra como D en herramientas comunes. |
| Idle reportable    | `I` / `TASK_REPORT_IDLE` | Subestado de D. No se cuenta en el promedio de carga. Usado por subprocesos del kernel. Acepta señales fatales. |
| Detenido           | `T` / `TASK_STOPPED` | El proceso fue detenido por una señal. Puede reanudarse con otra señal. |
| Depurado           | `T` / `TASK_TRACED` | El proceso está siendo depurado. Comparte el mismo indicador T que el estado detenido. |
| Zombi              | `Z` / `EXIT_ZOMBIE` | El proceso ha terminado, pero su PID sigue presente hasta que el proceso padre lo recoja. |
| Muerto             | `X` / `EXIT_DEAD` | El proceso ha sido completamente liberado. No aparece en herramientas de monitoreo. |

***ps*** Se usa para mostrar una lista con los procesos actuales

***ps aux**** muestra todos los  procesos sin un terminal de control
***ps lax*** muestra una lista extensa de todos los procesos
***ps aux --forest*** Para mostrar los procesos dependientes de otros en forma de arbol
***ps j*** Muestra solo los rabajos en marcha

#### Control de los procesos ####

El comando ps muestra el nombre del dispositivo del terminal de control en la columna TTY.
Algunos procesos, como daemons del sistema, son iniciados por el sistema y no desde un terminal de control. Estos procesos no son miembros de un trabajo y no pueden colocarse en primer plano.
El comando ps muestra un signo de pregunta (?) en la columna TTY para estos procesos.

Cualquier comando o tubería puede iniciarse en segundo plano si se anexa el signo &

```console
[user@host ~]$ sleep 10000 &
[1] 5947
[user@host ~]$

[user@host ~]$ example_command | sort | mail -s "Sort output" &
[1] 5998

```

***jobs*** Muestra una lista de trabajos de la shell

```console
[user@host ~]$ jobs
[1]+ Running
sleep 10000 &
[user@host ~]$
```

**fg*** Para traer un trabajo desde segundo plano a primer plano

```console
[user@host ~]$ fg %1
sleep 10000
```

***bg*** Para poner un trabajo en segundo plano

```
user@host ~]$ bg %1
[1]+ sleep 10000 &
```

#### Control de procesos con señales ####


| Señal | Nombre           | Definición                                                                                                                                                     |
|-------|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | HUP (Hangup)     | Se usa para informar la finalización del proceso de control de un terminal. Además, solicita que se reinicie el proceso (volver a cargar la configuración) sin finalización. |
| 2     | INT (Keyboard interrupt) | Provoca la finalización del programa. Puede bloquearse o manipularse. Enviado al presionar la secuencia de teclas INTR (Ctrl+c).                        |
| 3     | QUIT (Keyboard quit)     | Similar a SIGINT, pero añade el volcado de un proceso en la finalización. Se envía al presionar la secuencia de teclas QUIT (Ctrl+\).                   |
| 9     | KILL (Kill, unblockable) | Provoca la finalización abrupta del programa. No se puede bloquear, ignorar ni manipular; sistemáticamente es grave.                                     |
| 15    | TERM (Terminate)         | Provoca la finalización del programa. A diferencia de SIGKILL, puede bloquearse, ignorarse o manipularse. Permite que el programa complete operaciones esenciales y autolimpieza. |
| 18    | CONT (Continue)          | Se envía a un proceso para que se reinicie, en caso de que esté detenido. No puede bloquearse. Aún si se manipula, reinicia siempre el proceso.         |
| 19    | STOP (Stop, unblockable) | Suspende el proceso. No puede bloquearse o manipularse.                                                                                                 |
| 20    | TSTP (Keyboard stop)     | A diferencia de SIGSTOP, puede bloquearse, ignorarse o manipularse. Enviado al presionar una secuencia de teclas de suspensión (Ctrl+z).              |

***(Ctrl+z)*** Suspender
***(Ctrl+c)*** Finalizar
***(Ctrl+\)*** Volcado

***kill -l*** Muestra un listado de las señales que existen

*** Se recomienda enviar primero SIGTERM (15), a continuación intentar con SIGINT(2) y, solo si falla en ambos casos, volver a intentar con SIGKILL (9)***

Enviar una señal a un proceso 

```console
[user@host ~]$ kill -l
[user@host ~]$ ps aux | grep job
[user@host ~]$ kill 5194
[user@host ~]$ ps aux | grep job
[user@host ~]$ kill -9 5199
[user@host ~]$ ps aux | grep job
[user@host ~]$ kill -SIGTERM 5205
```

***pkill*** Para enviar una señal a varios procesos que siguen un criterio de selección
***pgrep*** Enumera los procesos según un criterio, no envia señales
  ***-l*** Enumera los procesos y sus ids
  ***-u*** Enumerar los procesos según un ID de usuario
  ***-t*** Todos los procesos de un terminal determinado

```console
[root@host ~]# pgrep -l -u bob
6964 bash
6998 sleep
6999 sleep
7000 sleep
// Elimina todos los procesos de ese usuario
[root@host ~]# pkill -SIGKILL -u bob
[root@host ~]# pgrep -l -u bob
// Todos los procesos del terminal3
[root@host ~]# pkill -SIGKILL -t tty3
[root@host ~]# pgrep -l -u bob
```

***w*** Nos dice que usuarios estan conectados y a que terminal
  ***-f*** Nos muestra desde donde estan conectados

***pstree*** Nos muestra los procesos en forma de arbol diciendo de quien depende cada proceso

***killall*** Puede señalar varios procesos basados en el nombre del comando que lo genero

```console
[user@host ~]$ ps aux | grep job
5194 0.0 0.1 222448 2980 pts/1 S 16:39 control job1
5199 0.0 0.1 222448 3132 pts/1 S 16:39 control job2
5205 0.0 0.1 222448 3124 pts/1 S 16:39 control job3
5430 0.0 0.0 221860 1096 pts/1 S+ 16:41 
[user@host ~]$ killall control
[1]Terminated control job1
[2]- Terminated control job2
[3]+ Terminated control job3
```

Para terminar trabajos ***kill -SIGTERM %Número de trabajo mostrado con el comando jobs***

***uptime*** Nos muestra una promedio de la carga actual , tiempo de funcionamiento y sesiones de usuario. Los tres últimos valores de carga son los calculados hace 1 min 5 min y 15 min.

***lscpu*** Nos muestra las cpus disponibles, si dividimos los últimos tres numeros de uptime entrre el número de cpus nos dara la carga del sistema. 
            Por encima de 1 indica saturación de recursos
```console
# From lscpu, the system has four logical CPUs, so divide by 4:
# load average:                   2.92, 4.48, 5.20
# divide by number of logical CPUs: 4     4     4
#                                  ---- ---- ----
# per-CPU load average:            0.73 1.12 1.30
#
# This system's load average appears to be decreasing.
# With a load average of 2.92 on four CPUs, all CPUs were in use ~73% of the time.
# During the last 5 minutes, the system was overloaded by ~12%.
# During the last 15 minutes, the system was overloaded by ~30%
```

***top*** Permite el monitoreo en tiempo real de los procesos

***Teclas para el manejo de top*** 

##### Atajos interactivos de `top` #####

| Tecla        | Acción                                                                 |
|--------------|------------------------------------------------------------------------|
| `l`, `t`, `m`| Alternar carga, subprocesos y líneas de encabezado de memoria         |
| `1`          | Mostrar CPUs individuales o resumen total en el encabezado            |
| `s`          | Cambiar tasa de actualización (ej. 0.5, 1, 5 segundos)                |
| `b`          | Alternar resaltado reverso para procesos en ejecución (negrita por defecto) |
| `Shift+b`    | Activar negrita en encabezado y procesos en ejecución                 |
| `Shift+h`    | Alternar entre resumen de proceso y subprocesos individuales          |
| `u`, `Shift+u`| Filtrar por nombre de usuario (eficaz o real)                        |
| `Shift+m`    | Ordenar por uso de memoria (descendente)                              |
| `Shift+p`    | Ordenar por uso de CPU (descendente)                                  |
| `k`          | Eliminar proceso (ingresar PID y signal)                              |
| `r`          | Renice proceso (ingresar PID y nice_value)                            |
| `Shift+w`    | Guardar configuración actual para el próximo reinicio de `top`        |
| `q`          | Salir                                                                  |
| `f`          | Gestionar columnas y configurar campo de ordenamiento                 |

### Daemons del sistema ###

Por convención su nombre termina en d. El primer proceso del sistema es el systemd y tiene PID 1.

El daemon systemd usa unidades para gestionar diferentes tipos de objetos:

• Las unidades de servicio tienen una extensión .service y representan servicios del sistema.
Puede usar unidades de servicio para iniciar los daemons usados con más frecuencia, como un
servidor web.

• Las unidades de socket tienen una extensión .socket y representan sockets de comunicación entre procesos (IPC) que systemd debe monitorear.
Si un cliente se conecta al socket, el gerente systemd iniciará un daemon y le pasará la conexión. Puede usar unidades socket para demorar el inicio de un servicio en el momento del arranque y para iniciar servicios usados con menos frecuencia a pedido.

• Las unidades de ruta tienen una extensión .path y se usan para demorar la activación de un servicio hasta que ocurra un cambio en el sistema de archivos específico. Puede usar unidades de ruta para servicios que usan directorios de cola, como un sistema de impresión.

Para gestionar unidades, use el comando systemctl. Por ejemplo, el comando ***systemctl -t help*** permite visualizar los tipos de unidad disponibles. El comando systemctl puede abreviar los nombres de las unidades, las entradas de árbol de proceso y las descripciones de unidad.

***systemctl list-units --type=service*** enumera y ordena las páginas de todas las unidades de servicio cargadas actualmente.
***systemctl list-units --type=service --all --state=*** enumera todos los servicos independientemente de su estado , sin --all muestra solo los cargados. state= puedes poner LOAD ACTIVE O SUB

***systemctl status nombre.service*** Para ver el estado en el que se encuentra un servico

***Información de la unidad de servicio***

| Campo       | Descripción                                                                 |
|-------------|------------------------------------------------------------------------------|
| Loaded      | Indica si la unidad de servicio está cargada en la memoria                  |
| Active      | Muestra si el servicio está activo y desde cuándo                           |
| Docs        | Enlaces o rutas donde encontrar documentación del servicio                  |
| Main PID    | ID del proceso principal del servicio y el comando asociado                 |
| Status      | Información adicional sobre el estado del servicio                          |
| Process     | Detalles sobre procesos secundarios relacionados                            |
| CGroup      | Información sobre el grupo de control (cgroup) al que pertenece el servicio |

***Estados de servicio en la salida de systemctl***

| Estado             | Descripción                                                                 |
|--------------------|------------------------------------------------------------------------------|
| loaded             | El archivo de configuración de la unidad fue procesado correctamente        |
| active (running)   | El servicio está en ejecución con procesos activos                          |
| active (exited)    | El servicio se ejecutó una vez y terminó correctamente                      |
| active (waiting)   | El servicio está activo pero esperando un evento                            |
| inactive           | El servicio no está en ejecución                                             |
| enabled            | El servicio está configurado para iniciarse al arrancar el sistema          |
| disabled           | El servicio no se inicia automáticamente al arrancar                        |
| static             | El servicio no puede habilitarse directamente, pero puede iniciarse por dependencia |

Para comprobar los demonios  se pueden usar 

***systemctl is-active nombre.service***
***systemctl is-enabled nombre.service***
***systemctl is-failed nombre.service***

***systemctl start nombre.service*** Iniciar demonio, se ejecuta como root
***systemctl stop nombre.service*** Parar demonio , se ejecuta como root
***systemctl restart nombre.service*** Reiniciar demonio
***systemctl reload nombre.service*** Recargar la configuración del demonio para que los cambios surtan efecto
***systemctl list-dependencies sshd.service*** Visualizar una lista de dependencias para iniciar la unidad de servicio indicada
***systemctl --reverse list-dependencies*** Visualiza de que serivicos depende cada uno para iniciarse

***systemctl mask nombre.service*** Enmascara el servicio para evitar que se inicie por error. Dara error si se intenta iniciar un servicio enmascarado
***systemctl umask nombre.servico*** Para quitar el enmascaramiento


***systemctl reload-or-restart nombre.service*** Si no estamos seguros si el demonio necesita hacer reload antes de reiniciar.

***systemctl enable nombre.service*** Para indicar que se inicie ese servicio en cada inicio del sistema
***systemctl enable --now nombre.service*** Para iniciar el servicio e indicar que se inicie cada vez que se reinicia el sistema
    Crea un fichero en /systemd/system/ y su configuración estara en /etc/systemd/system/TARGETNAME.target.wants
***systemctl disable nombre.servicio*** Para desahabilitar el inicio en cada reinicio del sistema.

***systemctl is-enabled nombre.service*** Para comprobar si un servicio esta habitlitado el reinicio automatico 

Para enumerar todas los demonios que han fallado ***systemctl --failed --type=service***

### SSH ### 

***ssh USER@NOMBREHOST*** Si no se pone usuario y solo se pone HOST se asume el usuario que se esta usando

***w -f*** No muestra una lista de los usuarios conectados por ssh

Las claves públicas del host donde nos conectamos o se guardan en ***~/.ssh/known_hosts*** o en ***/etc/ssh/ssh_know_hosts***

El parámetro *StrictHostKeyChecking* se establece en el archivo *~/.ssh/config* específico del usuario, en el archivo /etc/ssh/ssh_config de todo el sistema o especificando la opción - o StrictHostKeyChecking= del comando ssh. 

• Si el parámetro StrictHostKeyChecking se establece en yes, el comando ssh siempre interrumpe la conexión SSH cuando las claves públicas no coinciden.  

• Si el parámetro StrictHostKeyChecking se establece en no, el comando ssh habilita la conexión y agrega la clave del host de destino al archivo ~/.ssh/known_hosts.

Cada entrada de clave de host conocida consta de una línea que contiene tres campos:

• El primer campo es una lista de nombres de host y direcciones IP que comparten la clave
pública.
• El segundo campo es el algoritmo de cifrado que se usa para la clave.
• El último campo es la clave en sí.

```console
[developer1@host ~]$ cat ~/.ssh/known_hosts
server1 ssh-ed25519
AAAAC3NzaC1lZDI1NTE5AAAAIOmiLKMExRnsS1g7OTxMsOmgHuUSGQBUxHhuUGcv19uT
server1 ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC8WDOooY+rh6NPa9yhLsNQXBqcQknTL/
WSd3zPvHLLd7KaC4IiEUxnwbfLBit8tRcirbQFxO20Am
...output omitted...
```

Un host puede haber cambiado su clave pública, en ese caso sera necesario quitar la llave de ese host de nuestro ~/.ssh/known_hosts para que vuelva a cogerla
En caso de que la haya cambiado nos saldra este mensaje

```console
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
@
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:hxttxb/qVi1/ycUU2wXF6mfGH++Ya7WYZv0r+tIkg4I.
Please contact your system administrator.
Add correct host key in /home/user/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/user/.ssh/known_hosts:12
ECDSA host key for server1.example.com has changed and you have requested strict
checking.
Host key verification failed.
```

No indica el número de linea en el fichero donde teniamos la clave , ese sera lo que tenomos que borrar.

Tambien lo podremos borrar con el comando ssh-keygen

```console
[developer1@host ~]$ ssh-keygen -R remotesystemname -f ~/.ssh/known_hosts
# Host remotesystemname found: line 12
/home/user/.ssh/known_hosts updated.
Original contents retained as /home/user/.ssh/known_hosts.old
``` 

#### Generación de claves SSH ####

El comando ssh-keygen  se usa para crear un par de claves. De manera predeterminada, el comando ssh-keygen guarda sus claves privadas y públicas en los archivos ~/.ssh/id_rsa y ~/.ssh/id_rsa.pub, pero puede especificar un nombre diferente. Se puede elegir una frase para generar la clave o no poner ninguna.

```console
[user@host ~]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa): Enter
Created directory '/home/user/.ssh'.
Enter passphrase (empty for no passphrase): Enter
Enter same passphrase again: Enter
Your identification has been saved in /home/user/.ssh/id_rsa.
Your public key has been saved in /home/user/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vxutUNPio3QDCyvkYm1 user@host.lab.example.com
The key's randomart image is:
+---[RSA 2048]----+|
|                  |
| .      .         |
| o o o            |
| . = o o .        |
| o + = S E .      |
| ..O o + * +      |
|.+% O . + B .     |
|=*oO . . + *      |
|++. . +.          |
+----[SHA256]-----+
```
Para elegir el nombre donde se guardara la clave privada se usa la opción -f

```
[user@host ~]$ ssh-keygen -f .ssh/key-with-pass
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): your_passphrase
Enter same passphrase again: your_passphrase
Your identification has been saved in .ssh/key-with-pass.
Your public key has been saved in .ssh/key-with-pass.pub.
The key fingerprint is:
SHA256:w3GGB7EyHUry4aOcNPKmhNKS7dl1YsMVLvFZJ77VxAo user@host.lab.example.com
The key's randomart image is:
+---[RSA 2048]----
```
La clave privada se guarda con permisos 600 y la pública con permisos 644 , se suelen guardar en el subdirectorio .ssh del home del usuario que las crea

Para compartir nuestra llave públiica a un host usaremos ***ssh-copy-id user@host*** , usaremos -i en caso de querer indicar donde estan ubicadas .

```console
[user@host ~]$ ssh-copy-id -i .ssh/key-with-pass.pub user@remotehost
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/user/.ssh/
id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter
out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted
now it is to install the new keys
user@remotehost's password: redhat
Number of key(s) added: 1
Now try logging into the machine, with:
"ssh 'user@remotehost'"
and check to make sure that only the key(s) you wanted were added.
```
#### Admistrado de claves ####

Para inicar el admistrado de claves ***ssh-agent*** 

```console
[user@host ~]$ eval $(ssh-agent)
Agent pid 10155
``` 

Para añadir claves en el gestor de claves se usa el comando ssh-add si no le indicamos de donde lo cogeraa del sitio por defecto ~/.ssh/id_rsa

```console
[user@host ~]$ ssh-add
Identity added: /home/user/.ssh/id_rsa (user@host.lab.example.com)
[user@host ~]$ ssh-add .ssh/key-with-pass
Enter passphrase for .ssh/key-with-pass: your_passphrase
Identity added: .ssh/key-with-pass (user@host.lab.example.com)
```

Cuando se cierra sessión las claves almacenadas en caché por ssh-agent se borran de la memoria.

***sh -v user@host*** Se puede usar la opción -v -vv -vvv para ver los problemas al realizar una conexión ssh

La configuración de ssh se guarda en ***~/.ssh/config*** , ahi se configuran parametros para la conexión con cada host

```console
[user@host ~]$ cat ~/.ssh/config
host         servera
HostName       servera.example.com
User           usera
IdentityFiles   ~/.ssh/id_rsa_servera

host         serverb
HostName     serverb.example.com
User         userb
IdentityFiles ~/.ssh/id_rsa_serverb
```

página 325 y 342
