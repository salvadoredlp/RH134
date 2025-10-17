
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
