### EJERCICIOS EX200 ### 

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
</details>
   
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
</details>

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

13. <details>
    <summary> Enable the dbadmin1 user to use the sudo command to run any command as the superuser. </summary>
    <summary> &nbsp;&nbsp;&nbsp;&nbsp;Use the vim /etc/sudoers.d/dbadmin1 command to create the file and add the following content: </summary>
    <br>

    ```console
    [root@serverb ~]# vim /etc/sudoers.d/dbadmin1
    dbadmin1 ALL=(ALL) ALL
    ```
</details>

14. <details>
    <summary> Añadir mascara 007 al usuario dbadmin1 </summary>
    <br>
   
    ```console
    [root@serverb ~]# su - dbadmin1
    [dbadmin1@serverb ~]$ echo "umask 007" >> .bashrc
    # Para cargar la configuación de .bashrc se usa el comando source
    [dbadmin1@serverb ~]$ source ~/.bashrc
    ```   
</details>

15. <details>
    <summary> Establezca el usuario dbadmin1 y grupo database de manera recursiva en usuario propietario dentro del directorio de forma recursiva y los permisos de grupo en ejecución. </summary>
    <br>
   
    ```console
    [dbadmin1@serverb ~]$ chown -R dbadmin1:database /home/dbadmin1/
    [dbadmin1@serverb ~]$ chmod -R g+x /home/dbadmin1
    ```  
</details>

16. <details>
    <summary> Configurar el directorio /home/dbadmin1/grading/review2 para permitir que los miembros del grupo database puedan crear contenido en él. Todos los demas usuarios deben poder leer y ejecutar. </summary>
    <br>
   
    ```console
      [dbadmin1@serverb ~]$ chmod g+s /home/dbadmin1/grading/review2
      [dbadmin1@serverb ~]$ chmod 775 /home/dbadmin1/grading/review2
    ```
</details>

17. <details>
    <summary> Asegurarse de que los usuarios en ese directorio /home/dbadmin1/grading/review2 puedan borrar ficheros de los que son propietarios. </summary>
    <br>
   
    ```console
    [dbadmin1@serverb ~]$ chmod o+t /home/dbadmin1/grading/review2
    ``` 
</details>

18. <details>
    <summary> Generar claves para SSH-KEYS para un usuario determinado en un directorio determinado </summary>

    ```console
    [student@serverb ~]$ ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/student/.ssh/id_rsa): /home/student/.ssh/review3_key
    ```
</details>

19. <details>
    <summary> Configurar el usuario student de servera para aceptar logins autenticandose con la llave ***pública*** y sin necesitar password. </summary>
    <br>
       
    ```console
          [student@serverb ~]$ ssh-copy-id -i .ssh/review3_key.pub student@servera
          /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ".ssh/review3.pub"
          /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
         /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
         student@servera's password: student
         Number of key(s) added: 1
         Now try logging into the machine, with:
         "ssh 'student@servera'"
         and check to make sure that only the key(s) you wanted were added.
    ```
    <br>
    Para iniciar sesión con esa clave tendremos que indicarlo con la opción -i de ssh
    <br>

    ```console
         [student@serverb ~]$ ssh -i .ssh/review3_key student@servera
         ...output omitted...
         [student@servera ~]$
    ```
</details>

20. <details>
    <summary>Configurar serverb para prevenir que los usuarios usen sus passwords para loguear y solo usen clave de pares SSH-KEYS y para evitar que los root se puedan loguear</summary>
    <br>

    ```console
    [student@serverb ~]$ vim /etc/sshd/sshd_config
    PermitRootLogin        no
    PasswordAuthentication no
    # Recargar el servicio sshd para que los cambios tengan efecto
    [student@serverb ~]$ sudo systemctl reload sshd.service
    ```
</details>

21. <details> 
    <summary> Determine the Ethernet interface name and the connection profile name that it uses.</summary>
    <br>
    
    ```console
    [root@serverb ~]# nmcli device status
    DEVICE    TYPE    STATE                CONNECTION
    eth0   ethernet connected              System eth0
    eth1   ethernet connected              System eth1
    lo     loopback connected (externally) lo
    ```
</details>

22. <details> 
    <summary> Create the static connection profile for the ethX interface. Set the network settings statically so that it does not use DHCP. When done, activate that connection profile. <br>  Base the settings IPv4 address 172.25.250.111, Netmask 255.255.255.0 ,Gateway 172.25.250.254, DNS server 172.25.250.254 </summary>
    <br>
    
    ```console
    nmcli connection add con-name static type ethernet ifname ethX ipv4.addresses '172.25.250.111/24' \
    ipv4.gateway '172.25.250.254' ipv4.dns '172.25.250.254' ipv4.method manual
    Connection 'static' (ac8620e6-b77e-499f-9931-118b8b015807) successfully added.
    # Activar la conexión creada
    [root@serverb ~]# nmcli connection up static
    ```
</details>

23. <details> 
    <summary> On serverb, set client-review4 as the canonical hostname for the servera 172.25.250.10 IPv4 address. </summary>
    <br>
   
    ```console
    hostnamectl hostname server-review4.lab4.example.com
    [root@serverb ~]# hostname
    server-review4.lab4.example.com
    ```
    
    Añadir la dirección y el nombre del host a  /etc/hosts

    ```console
    [root@serverb ~]# vim /etc/hosts
    172.25.250.10 client-review4
    ```

24. <details>
    <summary> Modify the static connection profile to configure the additional 172.25.250.211 IPv4 address with the 255.255.255.0 netmask. Do not remove the existing IPv4 address. Verify that serverb responds to all addresses when the static connection profile is active.On serverb, restore the original settings by activating the original network profile. </summary>
    <br>

    ```console
    [root@serverb ~]# nmcli connection modify static +ipv4.addresses '172.25.250.211/24'
    # Activarla de nuevo para que tenga efecto la nueva ip adicional
    [root@serverb ~]# nmcli connection up static
    ```
    <br>

    Usar ping para comprobar si las nuevas ips estan activas
    <br>
    ```console
    student@workstation ~]$ ping -c2 172.25.250.211
    PING 172.25.250.211 (172.25.250.211) 56(84) bytes of data.
    64 bytes from 172.25.250.211: icmp_seq=1 ttl=64 time=0.246 ms
    64 bytes from 172.25.250.211: icmp_seq=2 ttl=64 time=0.296 ms
    ```
    <br>
   
    Restore the original settings by activating the original network profile.
    <br>

    ```console
    [root@serverb ~]# nmcli connection up "System eth0"
    ...output omitted...
    ```
     
</details>

25. <details>
    <summary>  • Identify the unmounted block device that contains an XFS file system on the serverb machine. Mount the block device on the /review5-disk directory.<br>  • Find the review5-path file. Create the /review5-disk/review5-path.txt file that contains a single line with the absolute path to the review5-path file.<br>  • Find all the files that the contractor1 user and the contractor group own. The files must also have the 640 octal permissions. Save the list of these files in the /review5-disk/review5-perms.txt file. <br> • Find all files with a size of 100 bytes. Save the absolute paths of these files in the /review5-disk/review5-size.txt file.</summary>
    <br>
   
    ```console
    [root@serverb ~]# lsblk -fs
    [root@serverb ~]# mkdir /review5-disk
    [root@serverb ~]# mount /dev/vdb1 /review5-disk
    [root@serverb ~]# df -Th
    [root@serverb ~]# find / -iname review5-path 2>/dev/null # Al no tener permisos para acceder a todos los directorios dara error en muchos, redirigimos la salida de error a nulo para que no salgan esos mensajes de error.
    /var/tmp/review5-path
    root@serverb ~]# find / -iname review5-path 2>/dev/null 1>/review5-disk/review5-path.txt
     [root@serverb ~]# find / -user contractor1 -group contractor -perm 640 2>/dev/null
     [root@serverb ~]# cat /review5-disk/review5-perms.txt
     /usr/share/review5-perms
     [root@serverb ~]# find / -type f -size 100c 2>/dev/null 1>/review5-disk/review5-size.txt
    ```
</details>

---

26. <details>
    <summary> Cree un scrit que nos diga cuantos usuarios ay conectados al sistema y que se ejecute diariamente y ponga un mensaje de cuantos usuarios hay conectados en el fichero /var/log </summary>
    <br>

    Crear el fichero /etc/cron.daily/ussercount
    
    ```console
    #!/bin/bash
      USERCOUNT=$(w -h | wc -l)
      logger "There are currently ${USERCOUNT} active users"
    ```
    
</details>

27.<details>
   <summary> El directorio "/test" debe ser creado en el boot del sistema serverX con permisos totales y el sticky bit activo.
El owner de dicho directorio debe ser "root" y el grupo "root". Cualquier fichero dentro de "/test" que no haya sido ni accedido ni modificado ni cambiado en más de 20 días, debe ser borrado</summary>.
   <br>
   ```console 
    vim /etc/tmpfiles.d/test.conf
   Type Path Mode UID  GID  Age Argument
      d /test 1777  root root 20d -

    Para ver que funciona, rebotar el sistema
  
    NOTA: El man de tmpfiles.d explica los detalles de la configuración y contiene ejemplos.

	man tmpfiles.d
   ```  </details>
