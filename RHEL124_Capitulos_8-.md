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

# Señales fundamentales de gestión de procesos

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

