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

#### top #### 

Permite el monitoreo en tiempo real de los procesos

***Teclas para su manejo*** 

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


