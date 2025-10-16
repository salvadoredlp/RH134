
1. <details>
   <summary> Loguearte en un host como un usuario determinado</summary>  
   <br>
  
    ```console
      [student@workstation ~]$ ssh student@serverb
    ```
   </details>

2. <details>
   <summary> Crear un directorio</summary>
   <br>
  
    ```console
      [student@serverb ~]$ mkdir grading
    ```
   </details>

3. <details>
   <summary> Crear tres ficheros vacios grade1,grade2 y grade3 </summary>
    <br>
  
    ```console
      [student@serverb ~]$ touch grading/grade{1,2,3}
    ```
</details>

4. <details>
   <summary> Visualizar el contenido de /bin/manage y redirigirlo a un fichero /grading/review.txt </summary>
   <br>

   ```console
      [student@serverb ~]$ head -5 bin/manage > grading/review.txt
   ```
  </details>

5. <details>
   <summary> Visualizar las últimas tres lineas del fichero manage y añadelo a review.txt </summary>
   <br>

    ```console
      [student@serverb ~]$ tail -3 bin/manage >> grading/review.txt
    ```
   </details>

6. <details> 
   <summary> Copiar /home/student/grading/review.txt a /home/student/grading/review-copy.txt</summary>
   <br>
  
    ```console
      [student@serverb grading]$ cp review.txt review-copy.txt</details>
    ```
    </details>

7. <details>
   <summary> Crear un hard link de /home/student/hardcopy a /home/student/grading/grade1 </summary>
   <br>

   ```console
     [student@serverb ~]$ ln grading/grade1 hardcopy
     [student@serverb ~]$ ls -l grading/grade1
     -rw-r--r--. 2 student student 0 Mar 6 16:45 grading/grade1
   ```

8. <details>
   <summary> Crear un soft link o enlace simbolico de /home/student/softcopy /home/student/gading/grade2 </summary>
   <br>

   ```console
     [student@serverb ~]$ ln -s grading/grade2 softcopy
     [student@serverb ~]$ ls -l softcopy
     lrwxrwxrwx. 1 student student 14 Mar 6 17:58 softcopy -> grading/grade2
   ```
   
</details>

9. <details>
   <summary> Identificar el proceso que mas CPU consume</summary>
   <br>

   ```console
      [student@serverb ~]$ top
      [student@serverb ~]$ top -o %CPU #Se puede ordenar por Memoría %MEM
   ```
   Teclas mayusculas para ordenar en top
   
      P -> Ordenar por CPU
      T -> Ordenar tiempo de ejecución
      M -> Ordenar por Memoría 
      
</details>

10. <details>
    <summary> Cerrar el proceso que mas consume </summary>
    <br>
       Pulsar k y escribir el pid del proceso que mas consume o si aparece entre corchetes pulsar simplemente enter.
       La señal a usar por defecto aparecera entre corchetes [15 SIGTERM] si no se puede elegir la señal que queramos para enviar al proceso.<br><br>

      Para listar todas las señales ***$kill -l***<br><br>
   
      | Señal | Nombre   | Definición       |
      |-------|-----------|------------------|
      | 1     | HUP (Hangup)     | Se usa para informar la finalización del proceso de control de un terminal. Además, solicita que se reinicie el proceso (volver a cargar la configuración) sin finalización. |
      | 2     | INT (Keyboard interrupt) | Provoca la finalización del programa. Puede bloquearse o manipularse. Enviado al presionar la secuencia de teclas INTR (Ctrl+c).                        |
      | 3     | QUIT (Keyboard quit)     | Similar a SIGINT, pero añade el volcado de un proceso en la finalización. Se envía al presionar la secuencia de teclas QUIT (Ctrl+\).                   |
      | 9     | KILL (Kill, unblockable) | Provoca la finalización abrupta del programa. No se puede bloquear, ignorar ni manipular; sistemáticamente es grave.                                     |
      | 15    | TERM (Terminate)         | Provoca la finalización del programa. A diferencia de SIGKILL, puede bloquearse, ignorarse o manipularse. Permite que el programa complete operaciones esenciales y autolimpieza. |
      | 18    | CONT (Continue)          | Se envía a un proceso para que se reinicie, en caso de que esté detenido. No puede bloquearse. Aún si se manipula, reinicia siempre el proceso.         |
      | 19    | STOP (Stop, unblockable) | Suspende el proceso. No puede bloquearse o manipularse.                   |
      | 20    | TSTP (Keyboard stop)     | A diferencia de SIGSTOP, puede bloquearse, ignorarse o manipularse. Enviado al presionar una secuencia de teclas de suspensión (Ctrl+z).              |

      ***Se recomienda enviar primero SIGTERM (15), a continuación intentar con SIGINT(2) y, solo si falla en ambos casos, volver a intentar con SIGKILL (9)***

11. <details>
    <summary> Crear un grupo con nombre *database* y un gid 50000 y añadirle un usuario *dbadmin1* y ponerle la password redhat</summary>
    <br>
   
       ```console
          [root@serverb ~]# groupadd -g 50000 database
          # Crear el usuario dbadmin1 y se incluye en el grupo database
          [root@serverb ~]# useradd -G database dbadmin1
          [root@serverb ~]# passwd dbadmin1
          # Changing password for user dbadmin1.
          New password: redhat
          BAD PASSWORD: The password is shorter than 8 characters
          Retype new password: redhat
          passwd: all authentication tokens updated successfully.
       ``` 
    </details>

12. <details>
    <summary>  
      Configurar para que el usuario dbadmin1 cambie el login cuando haga login.<br> 
       &nbsp;&nbsp;&nbsp;&nbsp;Configurar para que la vigencia mínima de la contraseña en 10 días.<br>
       &nbsp;&nbsp;&nbsp;&nbsp;Configurar para que la vigencia máxima de la contraseña en 30 días.<br>
    </summary>
    <br>
   
       ```console
          [root@serverb ~]# chage -d 0 dbadmin1
          [root@serverb ~]# chage -m 10 dbadmin1
          [root@serverb ~]# chage -M 30 dbadmin1
       ```
       
      Opciones de ***chage***
   
             -d, --lastday ÚLTIMO_DÍA      establece el día del último cambio de la
                                contraseña a ÚLTIMO_DÍA
             -E, --expiredate FECHA_CAD    establece la fecha de caducidad a FECHA_CAD
             -h, --help                    muestra este mensaje de ayuda y termina
             -i, --iso8601                 use YYYY-MM-DD when printing dates
             -I, --inactive INACTIVA       deshabilita la cuenta después de INACTIVA días de la fecha de caducidad
             -l, --list                    muestra la información de la edad de la cuenta
             -m, --mindays DÍAS_MIN        establece el número mínimo de días antes de cambiar la contraseña a DÍAS_MIN
             -M, --maxdays MAX_DAYS        set maximum number of days before password change to MAX_DAYS
             -R, --root CHROOT_DIR         directory to chroot into
             -W, --warndays DÍAS_AVISO     establece los días de aviso de expiración a DÍAS_AVISO

      </details>

11. <details>
    <summary> Pulsar</summary>
</details>
