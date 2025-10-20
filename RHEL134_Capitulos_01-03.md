
## Bash Scripts ##

Al comienzo de los scripts hay que poner la directiva :

```console
#!/usr/bin/bash
```

El script tendra que tener permiso de ejecución . Si el directorio donde colocamos el directorio en la variable de entorno PATH entonces no hara falta especificar el directorio para ejecutarlo. Si queremos ejecutarlo y estamos en el directoiro donde se encuentra y no esta en la variable PATH habra que ejecutarlos usando ./scriptname

El caracter \ anula el significado especial al caracter que le sigue.

```console
[user@host ~]$ echo # not a comment
[user@host ~]$ echo \# not a comment
# not a comment
```

Los caracteres encerrados en comillas simple ' ' se interpretan de forma literal

```console
[user@host ~]$ echo # not a comment #
[user@host ~]$ echo \# not a comment #
# not a comment
[user@host ~]$ echo \# not a comment \#
# not a comment #
[user@host ~]$ echo '# not a comment #'
# not a comment #
```

Las comillas dobles evitan el *globbing* que es el expandir para ejecutar comandos tras $

```console
[user@host ~]$ var=$(hostname -s); echo $var
host
[user@host ~]$ echo "***** hostname is ${var} *****"
***** hostname is host *****
[user@host ~]$ echo Your username variable is \$USER.
Your username variable is $USER.
[user@host ~]$ echo "Will variable $var evaluate to $(hostname -s)?"
Will variable host evaluate to host?
[user@host ~]$ echo 'Will variable $var evaluate to $(hostname -s)?'
Will variable $var evaluate to $(hostname -s)?
[user@host ~]$ echo "\"Hello, world\""
"Hello, world"
[user@host ~]$ echo '"Hello, world"'
"Hello, world"
```

***echo*** muestra el texto que pongas como argumento . Por defecto lo envia a la pantalla STDOUT

```console
[user@host ~]$ cat ~/bin/hello
#!/usr/bin/bash
echo "Hello, world"
[user@host ~]$ hello
Hello, world
```

Echo se puede usar para mostrar mensajes , es buena práctica el redirigir los mensajes de error redirigirlos a STDERR separado de la salida normal de mensajes

```console
user@host ~]$ cat ~/bin/hello
#!/usr/bin/bash
echo "Hello, world"
echo "ERROR: Houston, we have a problem." >&2
[user@host ~]$ hello 2> hello.log
Hello, world
[user@host ~]$ cat hello.log
ERROR: Houston, we have a problem.
```

### Bucles ###

***bucle for***

```
for VARIABLE in LIST; do
COMMAND VARIABLE
done
```

Tres ejemplos de uso de ***for***

```console
[user@host ~]$ for HOST in host1 host2 host3; do echo $HOST; done
host1
host2
host3
[user@host ~]$ for HOST in host{1,2,3}; do echo $HOST; done
host1
host2
host3
[user@host ~]$ for HOST in host{1..3}; do echo $HOST; done
host1
host2
host3
[user@host ~]$ for FILE in file{a..c}; do ls $FILE; done
filea
fileb
filec
```

Tambien se puede usar con la salida de otros comandos 

```console
[user@host ~]$ for PACKAGE in $(rpm -qa | grep kernel); \
do echo "$PACKAGE was installed on \
$(date -d @$(rpm -q --qf "%{INSTALLTIME}\n" $PACKAGE))"; done
kernel-tools-libs-5.14.0-70.2.1.el9_0.x86_64 was installed on Thu Mar 24 10:52:40
PM EDT 2022
kernel-tools-5.14.0-70.2.1.el9_0.x86_64 was installed on Thu Mar 24 10:52:40 PM
EDT 2022
kernel-core-5.14.0-70.2.1.el9_0.x86_64 was installed on Thu Mar 24 10:52:46 PM EDT
2022
kernel-modules-5.14.0-70.2.1.el9_0.x86_64 was installed on Thu Mar 24 10:52:47 PM
EDT 2022
kernel-5.14.0-70.2.1.el9_0.x86_64 was installed on Thu Mar 24 10:53:04 PM EDT 2022
[user@host ~]$ for EVEN in $(seq 2 2 10); do echo "$EVEN"; done
2
4
6
8
10
``` 

***seq*** crea secuencias de números con la forma seq [OPCIÓN]... PRIMERO INCREMENTO ÚLTIMO

***exit nº*** Para dar salida a un código al script, 0 se usa para salida con exito.
***echo $?*** El último código de salida 

***test*** se usa para evaluar variables se recomineda usarlo para evaluar el estado que tendra la salida del script ($?)
Se usan los siguientes parametros para :
un número es mayor que (gt), mayor o igual que (ge), menor que (lt), menor o igual que (le), o
igual (eq) que otro número.

Tambien se puede usar operadores para probar si una cadena de texto es igual (= o ==) o no es lo mismo (!=) que
otra cadena de texto, o si la cadena tiene una longitud cero (z) o un valor distinto de cero longitud
(n). También puede probar si existe un archivo regular (-f) o directorio (-d) y algunos atributos
especiales, como si el archivo es un enlace simbólico (-L) o si el usuario tiene permisos de lectura
(-r).
<br>

| Tipo de prueba     | Operador       | Descripción                                                                 |
|--------------------|----------------|-----------------------------------------------------------------------------|
| 📁 Archivos        | `-e`           | El archivo existe                                                           |
|                    | `-f`           | Es un archivo regular                                                       |
|                    | `-d`           | Es un directorio                                                            |
|                    | `-s`           | El archivo existe y no está vacío                                          |
|                    | `-r`           | El archivo tiene permisos de lectura                                       |
|                    | `-w`           | El archivo tiene permisos de escritura                                     |
|                    | `-x`           | El archivo tiene permisos de ejecución                                     |
|                    | `-L`           | Es un enlace simbólico                                                      |
|                    | `-b`           | Es un archivo de bloque especial                                            |
|                    | `-c`           | Es un archivo de carácter especial                                          |
|                    | `-p`           | Es un pipe (FIFO)                                                           |
|                    | `-t`           | El descriptor de archivo (FD) está asociado a un terminal                   |
| 🔤 Cadenas         | `=`            | Las cadenas son iguales                                                    |
|                    | `!=`           | Las cadenas son distintas                                                  |
|                    | `-z`           | La cadena está vacía                                                       |
|                    | `-n`           | La cadena no está vacía                                                    |
| 🔢 Números         | `-eq`          | Igualdad numérica                                                           |
|                    | `-ne`          | Desigualdad numérica                                                       |
|                    | `-gt`          | Mayor que                                                                  |
|                    | `-ge`          | Mayor o igual que                                                          |
|                    | `-lt`          | Menor que                                                                  |
|                    | `-le`          | Menor o igual que                                                          |
| 🔗 Lógicos         | `!`            | Negación                                                                   |
|                    | `-a`           | AND lógico (obsoleto, usar `&&` mejor)                                     |
|                    | `-o`           | OR lógico (obsoleto, usar `||` mejor)                                      |

<br>
```console
[user@host ~]$ test 1 -gt 0 ; echo $?
0
[user@host ~]$ test 0 -gt 1 ; echo $?
1
```

Los [] o la nueva extension [[]] se puede usar tambien para ejecutar expresiones.

```console
[user@host ~]$ [[ 1 -eq 1 ]]; echo $?
0
[user@host ~]$ [[ 1 -ne 1 ]]; echo $?
1
[user@host ~]$ [[ 8 -gt 2 ]]; echo $?
0
[user@host ~]$ [[ 2 -ge 2 ]]; echo $?
0
[user@host ~]$ [[ 2 -lt 2 ]]; echo $?
1
[user@host ~]$ [[ 1 -lt 2 ]]; echo $?
0
[user@host ~]$ [[ abc = abc ]]; echo $?
0
[user@host ~]$ [[ abc == def ]]; echo $?
1
[user@host ~]$ [[ abc != def ]]; echo $?
0
[user@host ~]$ STRING=''; [[ -z "$STRING" ]]; echo $?
0
[user@host ~]$ STRING='abc'; [[ -n "$STRING" ]]; echo $?
0
```
***Los espacios dentro  de los [[]] es obligatorio 

***if/then*** Es una escructura condicional

```
if <CONDITION>; then
<STATEMENT>
...
<STATEMENT>
fi
```

Ejemplo uso de if 

```console
[user@host ~]$ systemctl is-active psacct > /dev/null 2>&1
[user@host ~]$ if [[ $? -ne 0 ]]; then sudo systemctl start psacct; fi
```

***if/then/Else***

```
if <CONDITION>; then
<STATEMENT>
...
<STATEMENT>
else
<STATEMENT>
...
<STATEMENT>
fi
```

Ejemplo de uso 

```console
[user@host ~]$ systemctl is-active psacct > /dev/null 2>&1
[user@host ~]$ if [[ $? -ne 0 ]]; then \
sudo systemctl start psacct; \
else \
sudo systemctl stop psacct; \
fi
```

***If/Then/Elif/Then/Else***

```
if <CONDITION>; then
<STATEMENT>
...
<STATEMENT>
elif <CONDITION>; then
<STATEMENT>
...
<STATEMENT>
else
<STATEMENT>
...
<STATEMENT>
fi
```

Ejemplo de uso

```console
[user@host ~]$ systemctl is-active mariadb > /dev/null 2>&1
[user@host ~]$ MARIADB_ACTIVE=$?
[user@host ~]$ sudo systemctl is-active postgresql > /dev/null 2>&1
[user@host ~]$ POSTGRESQL_ACTIVE=$?
[user@host ~]$ if [[ "$MARIADB_ACTIVE" -eq 0 ]]; then \
mysql; \
elif [[ "$POSTGRESQL_ACTIVE" -eq 0 ]]; then \
psql; \
else \
sqlite3; \
fi
```

### Expresiones regulares y expresiones extendidas ###

| Basic Syntax | Extended Syntax | Descripción                                                                 |
|--------------|-----------------|------------------------------------------------------------------------------|
| `.`          | `.`             | Coincide con cualquier carácter individual                                  |
|              | `?`             | El elemento anterior es opcional y aparece como máximo una vez              |
| `*`          | `*`             | El elemento anterior aparece cero o más veces                               |
|              | `+`             | El elemento anterior aparece una o más veces                                |
| `\{n\}`      | `{n}`           | El elemento anterior aparece exactamente n veces                            |
| `\{n,\}`     | `{n,}`          | El elemento anterior aparece n o más veces                                  |
| `\{,m\}`     | `{,m}`          | El elemento anterior aparece como máximo m veces                            |
| `\{n,m\}`    | `{n,m}`         | El elemento anterior aparece al menos n veces, pero no más de m veces       |
| `[:alnum:]`  | `[:alnum:]`     | Caracteres alfanuméricos: letras y dígitos (`[0-9A-Za-z]`)                   |
| `[:alpha:]`  | `[:alpha:]`     | Caracteres alfabéticos (`[A-Za-z]`)                                         |
| `[:blank:]`  | `[:blank:]`     | Espacio y tabulador                                                         |
| `[:cntrl:]`  | `[:cntrl:]`     | Caracteres de control (ASCII 000–037 y 177)                                 |
| `[:digit:]`  | `[:digit:]`     | Dígitos (`0–9`)                                                             |
| `[:graph:]`  | `[:graph:]`     | Caracteres gráficos: alfanuméricos y puntuación                            |
| `[:lower:]`  | `[:lower:]`     | Letras minúsculas (`a–z`)                                                   |
| `[:print:]`  | `[:print:]`     | Caracteres imprimibles: alfanuméricos, puntuación y espacio                 |
| `[:punct:]`  | `[:punct:]`     | Caracteres de puntuación (`!"#$%&'()*+,-./:;<=>?@[\]^_{|}~`)                |
| `[:space:]`  | `[:space:]`     | Caracteres de espacio: tab, newline, vertical tab, form feed, carriage return, espacio |
| `[:upper:]`  | `[:upper:]`     | Letras mayúsculas (`A–Z`)                                                   |
| `[:xdigit:]` | `[:xdigit:]`    | Dígitos hexadecimales (`0–9`, `A–F`, `a–f`)                                  |
| `\b`         | `\b`            | Coincide con el borde de una palabra                                        |
| `\B`         | `\B`            | Coincide si no está en el borde de una palabra                              |
| `\<`         | `\<`            | Coincide con el inicio de una palabra                                       |
| `\>`         | `\>`            | Coincide con el final de una palabra                                        |
| `\w`         | `\w`            | Coincide con un carácter de palabra (`[_[:alnum:]]`)                        |
| `\W`         | `\W`            | Coincide con un carácter no de palabra (`[^_[:alnum:]]`)                    |
| `\s`         | `\s`            | Coincide con espacio en blanco (`[[:space:]]`)                              |
|              |                 | Coincide con no espacio en blanco (`[^[:space:]]`)                          |

***grep*** puede procesar la salida de otros comandos mediante el uso de un carácter (|)

```console
[root@host ~]# ps aux | grep chrony
chrony 662 0.0 0.1 29440 2468 ? S 10:560:00 /usr/sbin/chronyd
```
***opciones del comando grep***

***Tabla de opciones de grep comunes***

| Opción     | Función                                                                                      |
|------------|-----------------------------------------------------------------------------------------------|
| `-i`       | Usa la expresión regular sin distinguir entre mayúsculas y minúsculas                        |
| `-v`       | Muestra solo las líneas que **no** contienen coincidencias con la expresión regular          |
| `-r`       | Busca coincidencias de forma recursiva en archivos y directorios                             |
| `-A NUMBER`| Muestra `NUMBER` líneas **después** de la coincidencia                                       |
| `-B NUMBER`| Muestra `NUMBER` líneas **antes** de la coincidencia                                         |
| `-e`       | Permite usar varias expresiones regulares con lógica OR (`-e expr1 -e expr2`)                |
| `-E`       | Usa sintaxis de expresión regular **extendida** en lugar de la básica                        |

Ejemplo 

```console
[user@host ~]$ grep -i serverroot /etc/httpd/conf/httpd.conf
# with "/", the value of ServerRoot is prepended -- so 'log/access_log'
# with ServerRoot set to '/www' will be interpreted by the
# ServerRoot: The top of the directory tree under which the server's
# ServerRoot at a non-local disk, be sure to specify a local disk on the
# same ServerRoot for multiple httpd daemons, you will need to change at
ServerRoot "/etc/httpd
```

Ejemplos , elimina las lineas que comienzan con # o ;
La opción -v muestra las lineas que no coinciden con la expreisión.

```console
[user@host ~]$ grep -v '^[#;]' /etc/systemd/system/multi-user.target.wants/rsyslog.service
```

Se pueden usar varias expresiones regulares con la opción -e , sera como si se pusiera un OR entre ellas
Para usar un AND habría que pasar la salida de uno de los greps con una expresiones por una tuberia a otro grep con otra expresión.

```console
[root@host ~]# cat /var/log/secure | grep -e 'pam_unix' -e 'user root' -e 'Accepted publickey' | less
Mar 4 03:31:41 localhost passwd[6639]: pam_unix(passwd:chauthtok): password
changed for root
Mar 4 03:32:34 localhost sshd[15556]: Accepted publickey for devops from
10.30.0.167 port 56472 ssh2: RSA SHA256:M8ikhcEDm2tQ95Z0o7ZvufqEixCFCt
+wowZLNzNlBT0
Mar 4 03:32:34 localhost systemd[15560]: pam_unix(systemd-user:session): session
opened for user devops(uid=1001) by (uid=0)
``` 

Las expresiones se pueden usar dentro de ***vim** y ***less*** despues de pulsar /
Luego se pulsa N para encontrar la siguiente coincidencia.

### Programación de un trabajo en diferido ###

***at TIMESPEC***

Si solo pones eso luego tendremos que escribir las tareas que queremos que ejecute a la hora indicada para terminar la entrada de tareas pulsar CTRL+D.

Si no lo podremos redirigir con < , podemos intrudicr todas las tareas en un script y luego redirigirlo a at.

```console
[user@host ~]$ date
Wed May 18 21:01:18 CDT 2022
[user@host ~]$ at 21:03 < myscript
job 3 at Wed May 18 21:03:00 2022
[user@host ~]$ at 21:00 < myscript
job 4 at Thu May 19 21:00:00 2022
```

at admite horas de tiempo natural como 

• now +5min
• teatime tomorrow (la hora del té es 16:00)
• noon +4 days
• 5pm august 3 2021

***atq*** o ***at -l***  Para ver los trabajos pendientes.

***at -c JOBNUMBER*** Para inspeccionar un trabajo especifico.

***atrm JOBNUMBER*** Elimina un trabajo programado

Ejemplo de uso

```console
[student@servera ~]$ echo "date >> /home/student/myjob.txt" | at now +2min
warning: commands will be executed using /bin/sh
job 1 at Thu Feb 16 18:51:16 2023
[student@servera ~]$ atq
1
Thu Feb 16 18:51:16 2023 a student
[student@servera ~]$ watch atq
Every 2.0s: atq
servera.lab.example.com: Thu Feb 16 17:58:50 2023
1
Thu Feb 16 18:51:16 2023 a student
```

***at -q LETRACOLA*** permite varias colas de trabajos, hasta 26.

Ejemplo , cola g.

```console
[student@servera ~]$ at -q g teatime
warning: commands will be executed using /bin/sh
at> echo "It's teatime" >> /home/student/tea.txt
at> Ctrl+d
job 2 at Fri Feb 17 16:00:00 2023
```

***crontab*** Sirve para programar trabajos recurrentes . Cada usiario tiene su propio crontab que se accede mediante la opcion -e de crontab

***crontab -l*** Detallar los trabajos del usuario actual
***crontab -r*** Elimina todos los trabajos del usuario actual
***crontab -e*** Edita los trabajos del usuario actual
***crontab filename*** Eliminar todos los trabajos y reemplazarlos con los leídos de nombre de archivo. Este comando sa la entrada stdin cuando no se especifica ningún archivo.

***crontab -u*** Un usuario con privilegios puede gestionar los trabajos de otro usuario.

***No se recomienda usar crontab para gestionar los trabajos del root***

Al invocar crontab -e se abrira el comando que indicque en la variable de entorno EDITOR

Se usaran las variables 
SHELL para indicar la shell que ejecutara el trabajo y la variable MAILTO para indicar quien recibira la salida enviada por correo.

los campos del crontab son 

• Minutes (Minutos)
• Hours (Horas)
• Day of month (Día del mes)
• Month (Mes)
• Day of week (Día de la semana)
• Comando

Se usa el carácter * para ejecutar en todas las instancias posibles del campo.

• Un número para especificar la cantidad de minutos u horas, una fecha o un día de la semana.
Para los días de semana, 0 equivale a domingo, 1 equivale a lunes, 2 equivale a martes, etc. 7
también equivale a domingo.
• x-y para un rango, que incluye los valores x y y.
• x,y para listas. Las listas también pueden incluir rangos, por ejemplo, 5,10-13,17 en la columna Minutes, para indicar que un trabajo debe ejecutarse a los 5 minutos, a los 10 minutos, a los 11 minutos, a los 12 minutos, a los 13 minutos y a los 17 minutos de la hora.
• */x indica un intervalo de x, por ejemplo, */7 en la columna Minutes ejecuta un trabajo exactamente cada siete minutos.

Si se usan abreviaturas en inglés de tres letras para los meses o los días de la semana, por ejemplo, Jan (enero), Feb (febrero) y Mon (lunes), Tue (martes).

```
*/5 9-16 * Jul 5 echo "Chime"
```

El rango de horas 9-16 precedente significa que el temporizador de trabajo comienza a la novena
hora (09:00) y continúa hasta el final de la decimosexta hora (16:59). El trabajo comienza a
ejecutarse a las 09:00 con la última ejecución a las 16:55 porque a cinco minutos de las 16:55
es 17:00 que está más allá del alcance de las horas dadas.

Los trabajos en cron se ejecutan en segundo plano y la salida se envia por mail si se tiene configurado.

El siguiente trabajo ejecuta el comando mutt para enviar el mensaje de correo Checking in al
destinatario developer@example.com en cada día de trabajo (lunes a viernes), a las 9:00 a. m. El caracter % en crontab significa salto de linea.

```
0 9 * * 1-5 mutt -s "Checking in" developer@example.com % Hi there, just checking in.
```

***date -d "last day" +%a*** último día
***date -d "next day" +%a*** Siguiente día

/etc/crontab.d/ Es donde se colocan los trabajos recurrentes del sistema para evitar que alguna actulizacion sobreescriba /etc/crontab

Se colocan aquí los scripts agrupados relacionados en un solo archivo.

Tambien existen directorios que incluyen para incluir  scripts para ejecutarse cada día, semana y mes.

/etc/cron.hourly
/etc/cron.daily
/etc/cron.weeekly
/etc/cron.monthly

*/etc/anacrontab* tambien se usa para configuar trabajos diarios, semanales y mensuales.

Los trabajos programados en /etc/anacrontab se ejecutaran aunque en el momento en el que se tendrian que ejecutar el sistema no este encendido.

Tienen cuatro campos 

*Period in days* que define el intervalo en días para el trabajo que se ejecuta en una programación recurrente. Este campo acepta un valor entero o macro. Por ejemplo, la macro @daily es equivalente al entero 1, lo que ejecuta el trabajo diariamente. De manera similar, la macro @weekly es equivalente
al entero 7, lo que ejecuta el trabajo semanalmente.
*Delay in minutes* Define el tiempo que el daemon crond debe esperar antes de iniciar el trabajo.
*Job identifier* Identifica el nombre único del trabajo en los mensajes de registro.
*Command* El comando que se ejecutará.

#### Temporizador systemd #### 

El paquete sysstat proporciona la unidad de temporizador systemd, llamada el servicio
sysstat-collect.timer, para recopilar estadísticas del sistema cada 10 minutos. En la
siguiente salida, se muestra el contenido del archivo de configuración /usr/lib/systemd/
system/sysstat-collect.timer.

```
...output omitted...
[Unit]
Description=Run system activity accounting tool every 10 minutes
[Timer]
OnCalendar=*:00/10
[Install]
WantedBy=sysstat.service
```

La opción OnCalendar=*:00/10 significa que esta unidad de temporizador activa la unidad
sysstat-collect.service correspondiente cada 10 minutos. Puede especificar intervalos de
tiempo más complejo

Para configurar el temporzador systemd  que llama al servicio sysstat-collector.timer se hace en el fichero ubicado en /usr/lib/systemd/system/sysstat-collect.timer
Se crea una copia del archivo de configuración  en el directorio /etc/systemd/system y se modifican esos ya que los otros systemd los volvera a su contenido original al iniciarse.

Para activar la unidad temporizador primero cambiar el archivo de configuración de la unidad del temporizador, use el comando systemctl daemon-reload para asegurar que la unidad del temporizador systemd cargue los cambios.

```
[root@host ~]# systemctl daemon-reload
```

Después de recargar la configuración del daemon systemd, use el comando systemctl para activar la unidad del temporizador.

```
[root@host ~]# systemctl enable --now <unitname>.timer
```



## 🧹 Gestión de Archivos Temporales en RHEL

Red Hat Enterprise Linux utiliza `systemd-tmpfiles` para gestionar archivos y directorios temporales de forma estructurada y automatizada.

Para editar la configuración de la unidad systemd-tmpfiles-clean.timer

```console
[user@host ~]$ systemctl cat systemd-tmpfiles-clean.timer
# /usr/lib/systemd/system/systemd-tmpfiles-clean.timer
# SPDX-License-Identifier: LGPL-2.1-or-later
#
# This file is part of systemd.
#
#
#
systemd is free software; you can redistribute it and/or modify it
under the terms of the GNU Lesser General Public License as published by
the Free Software Foundation; either version 2.1 of the License, or
(at your option) any later version.
[Unit]
Description=Daily Cleanup of Temporary Directories
Documentation=man:tmpfiles.d(5) man:systemd-tmpfiles(8)
ConditionPathExists=!/etc/initrd-release
[Timer]
OnBootSec=15min
OnUnitActiveSec=1d
```
Cuando se cambia un archivo de configuración de una unidad  hay que recargarlos con 

```console
[root@host ~]# systemctl daemon-reload
```
---

## 🗂️ Ubicaciones comunes de archivos temporales

- `/tmp`: Datos transitorios de usuarios y aplicaciones.
- `/run`: Directorios volátiles en memoria (daemon, usuario).
- Se limpian automáticamente al reiniciar o perder energía.

---

## ⚙️ systemd-tmpfiles

### Función

- Crea, elimina o ajusta permisos de archivos temporales según configuración.

### Archivos de configuración

| Ruta                          | Propósito                                      |
|------------------------------|------------------------------------------------|
| `/usr/lib/tmpfiles.d/`       | Configuración por defecto de paquetes RPM      |
| `/run/tmpfiles.d/`           | Archivos volátiles usados por daemons          |
| `/etc/tmpfiles.d/`           | Configuración personalizada del administrador  |

📌 **Precedencia:** `/etc/` > `/run/` > `/usr/lib/`

---

## 🚀 Servicios systemd relacionados

### `systemd-tmpfiles-setup.service`

- Se ejecuta al inicio del sistema.
- Comando: `systemd-tmpfiles --create --remove`

### `systemd-tmpfiles-clean.timer`

- Limpieza periódica de archivos temporales.
- Configuración:

```ini
[Timer]
OnBootSec=15min         # 15 min después del arranque
OnUnitActiveSec=1d      # Cada 24h tras la última ejecución
``` 
Sintaxis de configuración tmpfiles.d

|Columna	| Descripción |
Tipo	Acción (d, D, L, Z, etc.)
Ruta	Ubicación del archivo/directorio
Modo	Permisos (ej. 0755)
UID/GID	Propietario y grupo
Antigüedad	Tiempo máximo antes de purgar (1d, 10d, etc.)
Argumento	Extra (ej. destino de enlace simbólico)

d /run/systemd/seats 0755 root root -
D /home/student 0700 student student 1d
L /run/fstablink - root root - /etc/fstab

Copiaremos las configuraciones de /usr/lib/tmpfiles.d/*.conf a /etc/tmpfiles.d/*.conf y ahí las modificaremos 

Por ejemplop 
/etc/tmpfiles.d/tmp.conf file should appear as follows:
q /tmp 1777 root root 5d

Para verificar que funciona y el fichero esta bien escrito 

```console
[root@servera ~]# systemd-tmpfiles --clean /etc/tmpfiles.d/tmp.conf
[root@servera ~]# echo $?
```

### Registro del sistema

El servicio systemd-journald reestructura los registros en un formato estándar y los escribe en un diario de sistema indexado y estructurado. De forma predeterminada, este diario se almacena en un sistema de archivos que no persiste en los reinicios.

El servicio rsyslog lee los mensajes de syslog recibidos por el servicio systemd-journald desde el diario (journal) a medida que llegan. El servicio rsyslog procesa los eventos de syslog, los registra en sus archivos de registro o los reenvía a otros servicios de acuerdo con su propia configuración.

El servicio rsyslog ordena y escribe mensajes de syslog en los archivos de registro que no persisten en los reinicios en el directorio ***/var/log***.

# 📋 Archivos de registro del sistema seleccionados

| Archivo de registro      | Tipo de mensajes almacenados                                                                 |
|--------------------------|----------------------------------------------------------------------------------------------|
| `/var/log/messages`      | La mayoría de los mensajes de syslog. Excluye autenticación, correo, cron y depuración.     |
| `/var/log/secure`        | Mensajes de syslog relacionados con eventos de seguridad y autenticación.                   |
| `/var/log/maillog`       | Mensajes de syslog relacionados con el servidor de correo.                                  |
| `/var/log/cron`          | Mensajes de syslog relacionados con la ejecución de trabajos programados.                   |
| `/var/log/boot.log`      | Mensajes de la consola (no syslog) relacionados con el inicio del sistema.                  |

📌 Algunas aplicaciones no usan `syslog`.  
Por ejemplo, **Apache** guarda sus registros en subdirectorios dentro de `/var/log`.

***syslog*** Sirve para registrar eventos en el sistema

### 🧾 Descripción general de las instalaciones de syslog
### Facilities

| Código   | Instalación   | Descripción de la instalación                          |
|----------|---------------|--------------------------------------------------------|
| 0        | kern          | Mensajes del kernel                                    |
| 1        | user          | Mensajes de nivel de usuario                           |
| 2        | mail          | Mensajes del sistema de correo                         |
| 3        | daemon        | Mensajes de daemons del sistema                        |
| 4        | auth          | Mensajes de autenticación y seguridad                  |
| 5        | syslog        | Mensajes de syslog internos                            |
| 6        | lpr           | Mensajes de la impresora                               |
| 7        | news          | Mensajes de noticias de la red                         |
| 8        | uucp          | Mensajes del protocolo UUCP                            |
| 9        | cron          | Mensajes del daemon de reloj                           |
| 10       | authpriv      | Mensajes de autorización ajenos al sistema             |
| 11       | ftp           | Mensajes de protocolo FTP                              |
| 16–23    | local0–local7 | Mensajes locales personalizados                        |

### 📊 Descripción general de las prioridades de syslog
### Priorities

| Código   | Prioridad | Descripción de la prioridad                        |
|----------|-----------|----------------------------------------------------|
| 0        | emerg     | El sistema no se puede usar                        |
| 1        | alert     | Se debe implementar una acción de inmediato        |
| 2        | crit      | Condición crítica                                  |
| 3        | err       | Condición de error no crítica                      |
| 4        | warning   | Condición de advertencia                           |
| 5        | notice    | Evento normal, pero importante                     |
| 6        | info      | Evento informativo                                 |
| 7        | debug     | Mensaje de nivel de depuración                     |

Para ver las prioridades facilities  ***#man 2 syslog***

Se configuran en /etc/rsyslog.conf

Enviara todos los mensajes con facilitie authriv a /var/log/secure
```
authpriv.* /var/log/secure
```
En lugar de registrarse en un archivo, también pueden imprimirse en las terminales de todos los usuarios que hayan iniciado sesión. El archivo rsyslog.conf tiene una configuración para imprimir todos los mensajes de syslog con la prioridad emerg en las terminales de todos los usuarios que hayan iniciado sesión.

Ejemplo 

```console
#### RULES ####
# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.* /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none      /var/log/messages
# The authpriv file has restricted access.
authpriv.*                                    /var/log/secure
# Log all the mail messages in one place.
# El guion no fuerza la escritura en disco y se espera para escribirlo hasta que este disponible. Lo guarda en buffer mientras.
mail.*                                        -/var/log/maillog
# Log cron stuff
cron.*                                        /var/log/cron
# Everybody gets emergency messages
*.emerg                                        :omusrmsg:*
# Save news errors of level crit and higher in a special file.
uucp,news.crit                                 /var/log/spooler
# Save boot messages also to boot.log
local7.*                                        /var/log/boot.log
```

***logrotate*** El comando logrotate rota los archivos de registro para evitar que ocupen demasiado espacio en el directorio /var/log. Cuando se rota un archivo de registro, se le cambia el nombre con una extensión que indica la fecha de rotación. Por ejemplo, el antiguo archivo /var/log/messages se renombra al archivo /var/log/messages-20220320 cuando se rota el 20-03-2022. 

Después de que el archivo de registro anterior rota, crea un archivo de registro y notifica al servicio
que escribió el archivo de registro.

### Analisis de entradas de syslog ###

```console
Mar 20 20:11:48 localhost sshd[1433]: Failed password for student from 172.25.0.10
port 59344 ssh2
```

• Mar 20 20:11:48 : registra la marca de tiempo de la entrada de registro.
• localhost : el host que envía el mensaje de registro.
• sshd[1433] : el nombre del programa o el proceso y el número de PID que envió el mensaje de
registro.
• Failed password for … : el mensaje que se envió.

#### Monitorear logs en tiempo real 

```console
tail -f /var/log/secure
```

#### Envio manual de mensajes de syslog 

Con la opción ***-p*** se indica la facilitie (prioridad)

```console
[root@host ~]# logger -p local7.notice "Log entry created on host"
```


### Buscar eventos en el diario del sistema 

***journalctl*** Para recuperar ficheros del registro del diario. Los usuarios tambien pueden usarlo pero restringe ciertos mensajes que solo pueden accerder root. Pulsar q para salir.

***journalctl -n nºde lineas*** Muestra las últimas lineas del diario de registro del sistema.
***journalctl -f*** Muestra los cambios en tiempo real, Ctrl+C para salir.
***journalctl -p prioridad*** Muestra las entradas con una prioridad determinada.
***journalctl -u nombre de una unidad***
***journalctl -b 1*** No muestra las entradas del primer reinicio nada mas , si ponemos un 2 del segundo reinicio ..
***journalctl --list-booots*** Muestra los eventos de arranque del sistema que ha habido
***journalctl -b*** Muestra los registros del reiniicio actual


    ```
      $ journalctl -u sshd.service
    ``` 
Ejemplos , se pueden mostrar de unas fechas a otras determinadas.

```console
[root@host ~]# journalctl --since today
...output omitted...
Mar 15 05:04:20 host.lab.example.com systemd[1]: Started Session 8 of User
student.
Mar 15 05:04:20 host.lab.example.com sshd[2255]: pam_unix(sshd:session): session
opened for user student(uid=1000) by (uid=0)
Mar 15 05:04:20 host.lab.example.com systemd[1]: Starting Hostname Service...
Mar 15 05:04:20 host.lab.example.com systemd[1]: Started Hostname Service.
Mar 15 05:04:50 host.lab.example.com systemd[1]: systemd-hostnamed.service:
Deactivated successfully.
Mar 15 05:06:33 host.lab.example.com systemd[2261]: Starting Mark boot as
successful...
Mar 15 05:06:33 host.lab.example.com systemd[2261]: Finished Mark boot as
successful.
lines 1996-2043/2043 (END) q

[root@host ~]# journalctl --since "2022-03-11 20:30" --until "2022-03-14 10:00"
...output omitted...
[root@host ~]# journalctl --since "-1 hour"
...output omitted...
```

***journalctl -o verbose*** Salida mas extensa. Muy util para corregir errores.

```console
[root@host ~]# journalctl -o verbose
Tue 2022-03-15 05:10:32.625470 EDT [s=e7623387430b4c14b2c71917db58e0ee;i...]
_BOOT_ID=beaadd6e5c5448e393ce716cd76229d4
_MACHINE_ID=4ec03abd2f7b40118b1b357f479b3112
PRIORITY=6
SYSLOG_FACILITY=3
SYSLOG_IDENTIFIER=systemd
...

```

Se puede refinar mas la busqueda  dentro de journactl

```console
[root@host ~]# journalctl _SYSTEMD_UNIT=sshd.service _PID=2110
Mar 15 04:42:16 host.lab.example.com sshd[2110]: Accepted
publickey for root from 172.25.250.254 port 46224 ssh2: RSA
SHA256:1UGybTe52L2jzEJa1HLVKn9QUCKrTv3ZzxnMJol1Fro
Mar 15 04:42:16 host.lab.example.com sshd[2110]: pam_unix(sshd:session): session
opened for user root(uid=0) by (uid=0)
```
Los diarios del sistema se guardan en /run/log y se borra al reninicio.

Eso se configura en /etc/systemd/journald.conf en la sección Storage

Los valores que puede tomar 

• persistent: almacena los diarios en el directorio /var/log/journal, que no se borra en los reinicios. Si el directorio /var/log/journal no existe, el servicio systemd-journald lo
crea.
• volatile: almacena los diarios en el directorio /run/log/journal volátil. Puesto que el sistema de archivos /run es temporal y solo existe en la memoria de tiempo de ejecución, los
datos almacenados en él, incluidos los diarios (journals) del sistema, se borran en el reinicio.  
• auto: si el directorio /var/log/journal existe, el servicio systemd-journald usa el almacenamiento persistente; de lo contrario, usa el almacenamiento volátil. Esta acción es la
predeterminada si no establece el parámetro Storage.  
• none: no usa ningún almacenamiento. El sistema descarta todos los registros, pero aún puede reenviarlos  

Los límites actuales en cuanto al tamaño del diario se registran cuando inicia el proceso systemd-journald. En el siguiente resultado de comando, se muestran las entradas del diario que reflejan los límites de tamaño actuales:

```console
[user@host ~]$ journalctl | grep -E 'Runtime Journal|System Journal'
Mar 15 04:21:14 localhost systemd-journald[226]: Runtime Journal (/run/log/journal/4ec03abd2f7b40118b1b357f479b3112) is 8.0M, max 113.3M, 105.3M free.
Mar 15 04:21:19 host.lab.example.com systemd-journald[719]: Runtime Journal (/run/log/journal/4ec03abd2f7b40118b1b357f479b3112) is 8.0M, max 113.3M, 105.3M free.
Mar 15 04:21:19 host.lab.example.com systemd-journald[719]: System Journal (/run/log/journal/4ec03abd2f7b40118b1b357f479b3112) is 8.0M, max 4.0G, 4.0G free.
```

### Configuración de diarios del sistema persistentes 

Se crea el directorio /var/log/journal 

Se establece en /etc/systemd/journald.conf y se edita y cambia el conteniido de la variable Storage= por ***persistent***

Despues habra que reniciar el servicio 

```console
[root@host ~]# systemctl restart systemd-journald
```

### Admistración de zonas horarias

***timedatectl*** Muestra la hora y su configuración y si esta sincronizado mediante NTP

***timedatectl list-timezones*** Muestra una lista con las zonas horarias

***tzselect*** Comando interactivo que sierve para identificar nuestrra zona horaria correcta

***timedatectl set-timezone Europe/Madrid*** Para poner la zona horaria que nos corresponde

***timedatectl set-time 9:00*** Para poner la hora.  Formato YYYY-MM-DD hh:mm:ss

***timedatectl set-ntp yes/no*** Para configurar  configuracion de la hora con servidores mediante ntp.

***/etc/chrony.conf*** Donde se configura los servidores de ntp y los saltos hacia esos servidores stratum level 

Ejemplo 

``` 
# Use public servers from the pool.ntp.org project.
...output omitted...
server classroom.example.com iburst
...output omitted...
```

Luego habra que reiniciar el servicio 

```console
[root@host ~]# systemctl restart chronyd
```

Para ver la configuración de los servidores que usa el servicio chronycd 

***chronyc sources -v**** Para ver los servidores horarios en /etc/chrony.conf


### Creación de archivos

***tar*** Se usa para comprimir ficheros
    -c --create Crea un archivo de almacenamiento
    -t --list Enumera el contenido de un archivo
    -x --extract extrae un archivo
    -v --verbose muestra los archivos que se estan archivando o estrayendo durante la operación con tar
    -f --file Se indica el nombre del fichero para abrir o crear
    -p o --preserve-permissions : conserva los permisos del archivo original al extraer.
    --xattrs : habilita el soporte de atributos extendidos y almacena atributos de archivos extendidos.
    --selinux : habilita el soporte de contexto de SELinux y almacena contextos de archivos de SELinux.
Las siguientes opciones de compresión del comando tar se usan para seleccionar un algoritmo:
    -a o --auto-compress : use el sufijo del archivo para determinar el algoritmo que se usará.
    -z o --gzip : use el algoritmo de compresión gzip, lo que genera un sufijo .tar.gz.
    -j o --bzip2 : use el algoritmo de compresión bzip2, lo que genera un sufijo .tar.bz2.
    -J o --xz : use el algoritmo de compresión xz, lo que genera un sufijo .tar.xz.

Crear un fichero . Para incluir los ficheros de un directorio el usuario tiene que tener permiso de lectura sobre esos ficheros, si no los excluye.

```console
[user@host ~]$ tar -cf mybackup.tar myapp1.log myapp2.log myapp3.log
[user@host ~]$ ls mybackup.tar
mybackup.tar
```

Listar el contenido de un fichero 

```console
[root@host ~]# tar -tf /root/etc.tar
etc/
etc/fstab
etc/crypttab
etc/mtab
...output omitted.
```

Extraer un ficheros 

```console
[root@host etcbackup]# tar -xf /root/etc.tar
#Con la opción -p conservaran los permisos de los ficheros originales en lugar de aplicar la mascara umask del usuario que ejecuta tar
[user@host scripts]# tar -xpf /home/user/myscripts.tar
```

Los algoritmos gzip, bzip2 y xz también se implementan como comandos independientes para comprimir archivos individuales sin crear un archivo.

```console
[user@host ~]$ gzip -l file.tar.gz

[user@host ~]$ xz -l file.xz

```

### Transferencia de archivos 

***sftp user@remotehost*** 

Tiene un help al entrar en el prompt de sftp. Los comandos de sftp precedidos de l se ejecutaran en nuestro host lpwd,lcd,lls,...
Los comandos put sirve para enviar ficheros al remotehost, get para descargarlos en nuestro host

```console
ftp> mkdir hostbackup
sftp> cd hostbackup
sftp> put /etc/hosts
Uploading /etc/hosts to /home/remoteuser/hostbackup/hosts
/etc/hosts
100% 227
0.2KB/s
00:00
To copy a whole directory tree recursively, use the sftp command -r option. The following
example recursively copies the /home/user/directory local directory to the remotehost
machine.
sftp> put -r directory
Uploading directory/ to /home/remoteuser/directory
Entering directory/
file1
100%
sftp> ls -l
drwxr-xr-x
2 student student
32 Mar 21 07:51 directory
00:00
sftp> get /etc/yum.conf
Fetching /etc/yum.conf to yum.conf
/etc/yum.conf
sftp> exit
```

Se puede descargar un fichero con sftp sin entrar en el prompt en una sola linea

```console
[user@host ~]$ sftp remoteuser@remotehost:/home/remoteuser/remotefile
Connected to remotehost.
Fetching /home/remoteuser/remotefile to remotefile
remotefile
```

***scp user@host*** Tambien sirve para copiar archivos remotos pero se recomienda usar sftp

#### Sincronizar ficheros y directorios remotos 

***rsync es otra manera de copiar ficheros entre un sistema y otro de forma segura
-n Hace una simulación de los cambios que hariá rsync al ejecutarlo
-r recursive
-l --links Sincroniza enlaces simbolicos
-p --perms Preserva los permisos
-t --preserva las marcas de tiempo
-D --devices Preserva los ficheros de dispositivo 
-a --archive Modo archivo Es lo mismo que habilitar las opciones anteriores

Tambien se puede sincronizar carpetas donde una de ellas este en otro host

```console
[root@host ~]# rsync -av /var/log hosta:/tmp
root@hosta's password: password
receiving incremental file list
log/
log/README
log/boot.log
...output omitted...
sent 9,783 bytes received 290,576 bytes 85,816.86 bytes/sec
total size is 11,585,690 speedup is 38.57
```

### Ajuste del rendimiento del sistema 

***tuned*** Sirve para la optimización del sistema . Es un demonio del sistema.
Por defectoo esta deshabilitado /etc/tuned/tuned-main.conf

```console
[root@host ~]$ cat /etc/tuned/tuned-main.conf
...output omitted...
# Dynamicaly tune devices, if disabled only static tuning will be used.
dynamic_tuning = 1
...output omitted...
# Update interval for dynamic tunings (in seconds).
# It must be multiply of the sleep_interval.
update_interval = 10
...output omitted...
```

Para activar tuned 

```console
[root@host ~]$ dnf install tuned
...output omitted...
[root@host ~]$ systemctl enable --now tuned
Created symlink /etc/systemd/system/multi-user.target.wants/tuned.service → /usr/
lib/systemd/system/tuned.service.
```

Tuned tiene varios perfiles de ajuste

## Perfiles de ajuste distribuidos con tuned

| Perfil de ajuste            | Propósito                                                                 |
|----------------------------|---------------------------------------------------------------------------|
| `balanced`                 | Ideal para los sistemas que requieren un equilibrio entre ahorro de energía y rendimiento. |
| `powersave`                | Ajusta el sistema para maximizar el ahorro de energía.                    |
| `throughput-performance`   | Ajusta el sistema para maximizar el rendimiento.                          |
| `accelerator-performance`  | Ajusta lo mismo que throughput-performance y también reduce la latencia a menos de 100 μs. |
| `latency-performance`      | Ideal para los sistemas de servidores que requieren baja latencia a expensas del consumo de energía. |
| `network-throughput`       | Derivado de throughput-performance. Aplica ajustes adicionales para maximizar el rendimiento de red. |
| `network-latency`          | Derivado de latency-performance. Aplica ajustes adicionales para baja latencia de red. |
| `desktop`                  | Derivado de balanced. Mejora la respuesta de aplicaciones interactivas.   |
| `hpc-compute`              | Derivado de latency-performance. Ideal para computación de alto rendimiento. |
| `virtual-guest`            | Optimiza el rendimiento en máquinas virtuales.                            |
| `virtual-host`             | Optimiza el rendimiento como host de máquinas virtuales.                  |
| `intel-sst`                | Optimizado para sistemas con Intel Speed Select. Úselo como superposición en otros perfiles. |
| `optimize-serial-console`  | Mejora la respuesta de la consola serial. Úselo como superposición en otros perfiles. |


***tuned-adm list*** Lista los perfiles disponibles
***tuned-adm active*** Para ver el perfil activo

Los perfiles estan en ***/usr/lib/tuned*** no se tocan, se copian a /etc/tuned y ahí se modifican si lo necesitamos.
Cada perfil tiene un directorio separado y dentro del directorio el archivo de configuración principal tuned.conf

***tuned-adm profile perfil*** Para cambiar de perfil, si no se especifica ningún perfil mostrara info del activo.

***tuned.-adm recommend*** Recomienda el perfil para nuestro sistema
***tuned-adm off*** Para desactivar el servicio tuned

### Prioridad de los procesos 

