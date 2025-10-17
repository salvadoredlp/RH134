
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



