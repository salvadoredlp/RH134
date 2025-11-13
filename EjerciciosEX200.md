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

27. <details>
    <summary> El directorio "/test" debe ser creado en el boot del sistema serverX con permisos totales y el sticky bit activo. El owner de dicho directorio debe ser "root" y el grupo "root". Cualquier fichero dentro de "/test" que no haya sido ni accedido ni modificado ni cambiado en más de 20 días, debe ser borrado. </summary>
    <br>
   
    ```console 
    vim /etc/tmpfiles.d/test.conf
    Type Path Mode UID  GID  Age Argument
      d /test 1777  root root 20d -
    ```

    Para ver que funciona, rebotar el sistema
  
    ***El man de tmpfiles.d explica los detalles de la configuración y contiene ejemplos.***
	`man tmpfiles.d`
   
  </details>

28. <details>
	<summary> Crea un servidor apache contenerizado con el nombre miweb y con el tag mas bajo de versión de las imagenes de rhel8/httpd-24. Usar registry.redhat.io como registry
	Crea y monta ~/storage/ como almacenamiento persistente en el contenedor. Y un index.html con el mensaje "Vamos a morir todos. Welcome to the container server" 
	Los puertos mapeados sera el 8080 y tendras que declarar las variables HTTPD_USER Y HTTPD_PASSWORD con admin como valor
	Conviertelo en servicio usando systemd y hazlo persistente a los reinicios </summary>
	<br>
          
    ```console
    # dnf install container-tools

	$ podman login registry.redhat.io

	$ skopeo inspect docker://registry.redhat.io/rhel8/httpd-24 | grep -A10 Tags
	#Tambien se puede ejecutar 
    $ skopeo list-tags docker://registry.redhat.io/rhel8/httpd-24/ | head o tail o less // Por si son demasiadas versiones
    // Descargamos la imagen de la versión mas baja que vemos 
	$ podman pull registry.redhat.io/rhel8/httpd-24:1-104
    // comprobamos
    $ podman images
	#Creamos el almacenamieto persistente
    $ mkdir ~/storage
    #Creamos el index.html
    $ touch ~/storage/index.html; echo "Vamos a morir todos. Welcome to the container server" > ~/storage/index.html
    # Descargamos la imagen de contenedor que queremos levantar
    $ podman run -d -v ~/storage:/var/www/html:Z -p 8080:8080 --name miweb -e HTTPD_USER=admin -e \
        HTTPD_PASSWORD=admin registry.redhat.io/rhel8/httpd-24:1-104
    #Abrimos los puertos del firewall
    # firewall-cmd --add-port=8080/tcp --permanent
    # firewall-cmd --reload
    #Crear directorio de usuario para guardar el servicio
    $ mkdir ~/.config/systemd/user
    $ podman generate systemd  --name miweb --new --files
    #Copiar fichero creado a ~/.config/systemd/user
    $ cp .config/container-miweb.service ~/.config/systemd/user
	#Activar que los servicios se caarguen aunque el usuario no se loguee
    $ loginctl enable-linger
    #Comprobar
    $ loginctl show-user nombredeusuario
    #Cargar servicio
    $ systemct	--user daemon-reload
    $ systemctl --user enable container-miweb.service
    #Reiniciar el sistema y comprobar
    $ curl http:/192.168.200.203:8080
    ```
29. <details> 
	<summary>Ejecuta un root container para MariaDb , se tiene que llamar mariadb, debe ser accesible por el puerto 3206, el DB USER sera muser y su password mpassword, el nombre de l abase de datos sera mydb , el directorio /opt/mariadb en el host sera el almacenamiento persistente de mariadb en el contenedor en /var/lib/mariadb.  Se tiene que configurar para que se inicie en cada reinicio del sistema </summary>

 	```console
    $ sudo -i
    # podman login registry.redhat.io
    # podman search mariad
    # skopeo list-tags docker://registry.redhat.io/rhel8/mariadb-1011
    // Descargamos la imagen
    # podman pull registry.redhat.io/rhel8/mariadb-1011
    // Comprobar
    # podman images
    // Vemos sus variables de entorno
    // Con skopeo no hace falta hacer pull para inspeccionarla
    # skopeo inspect docker://registry.redhat.io/rhel8/mariadb-1011 --format '{{ .Env }}'
        o
    # podman inspect  mariadb  --format '{{ .Config.Env }}'
   
    #podman run -d --name mariadb -p 3206:3206 -e MYSQL_USER=muser -e MYSQL_PASSWORD=mpassword -e MYSQL_DATABASE=mydb       -e MYSQL_ROOT_PASSWORD=password -v /opt/mariadb:/var/lib/mariabd:Z mariadb-1011:latest
    // Comprobamos
    # podman ps
    # podman logs mariadb
    # loginctl show-user root
    # loginctl enable-linger root
    // Comprobamos
    # loginctl show-user root

	// Directorio donde van los servicios de root, por eso nos colocamos ahí
    # cd /etc/systemd/system/

    // Generamos el fichero para crear el servicio a partir del contenedor
    # podman generate systemd --name mariadb --files
    // recargamos
    # systemctl daemon-reload
    // Activamos servicio
    # systemctl enable container-mariab.service
    // Reiniciamos sistema para comprobar si lo ejecuta
    # reboot
    # podman ps
    # systemctl status container-mariadb.service
  	```
  </details>

30. <details>
	<summary>En "serverb", ejecutar un contenedor rootless llamado "wordled" que contendrá una aplicación Javascript básica. Su almacenamiento persistente estará en /home/student/wordled donde se pondrán los ficheros contenidos en el tar "wordled.tar". Dicho contenedor se ejecutará como servicio con el nombre "wordled" en el arranque de serverb y enlazará a su puerto 8080 permitiendo su uso desde workstation.
	- Usar la imagen registry.access.redhat.com/rhscl/httpd-24-rhel7
	- DocumentRoot de Apache en /var/www/html
	- Los ficheros contenidos en el fichero wordled.tar también se pueden obtener de:<br> https://github.com/MMGInstructor/wordled</summary><br>

    ```console
       $ ssh student@serverb
       $ sudo -i 	
	   # sudo dnf install podman container-tools -y
       # exit // volvemos a usuario normal
       $ podman login registry.redhat.com // Nos logueamos en el registry para poder bajar imagenes
	   $ podman pull registry.access.redhat.com/rhscl/httpd-24-rhel7 
	   $ podman images // vemos si se descargo y la tenemos en el registro local
	   $ podman inspect registry.access.redhat.com/rhscl/httpd-24-rhel7 | grep -A5 'Config' -> veo usuario 1001 y puertos 8080 y 8443

       // Crear el almacenamiento del contenedor y lo preparo para que lo pueda usar:

	   $ mkdir  ~/wordled 
	   $ cp wordled.tar ; cd ~/wordled
	   $ tar xvf wordled.tar; rm wordled.tar
       // Ponemos como propiertario del directorio al usuario del contenedor. Importante hacerlo desde el namespace del contenedor con podman unshare
	   $ podman unshare chown -R 1001:1001 /home/student/wordled

	   $ podman run -d --name wordled -v /home/student/wordled:/var/www/html:Z -p 8080:8080 registry.access.redhat.com/rhscl/httpd-24-rhel7 
	   $ podman logs wordled  // Comprobamos que no hay errores
	
	   $ curl -k http://localhost:8080    

       // Crear el directorio donde guardar los servicios de usuario si no existe

	   $ mkdir -p ~/.config/systemd/user/
	   $ cd ~/.config/systemd/user
       // Generamos el fichero de configuración del servicio
	   $ podman generate systemd --new --files --name wordled
	   $ podman stop wordled && podman rm wordled
	   $ systemctl --user daemon-reload
	   $ systemctl --user enable --now container-wordled
	   $ loginctl enable-linger
	   $ loginctl show-user student	// Buscar linger=yes
	   $ sudo reboot

       // Abrir puerto 8080 en el firewall de serverb:

	   $ ssh student@serverb
	   $ systemctl --user status container-wordled			
	   $ podman ps							
	   $ sudo firewall-cmd --add-port=8080/tcp --permanent
	   $ sudo firewall-cmd --reload

       // Comprobar
       $  curl http://serverb.lab.example.com:8080 
  	```
</details>

31. <details> 
	<summary> Crear un pod, conjunto de contenedores, que permita instalar  Wordpress, para eso es necesario levantar     los contenedores pause,mysql y wordpress. </summary>
	<br>
	
	```console
	$ podman login -u <usuario> -p <password> registry.redhat.io

	$ podman pull registry.redhat.io/rhscl/mysql-57-rhel7:latest

	$ podman pull docker.io/wordpress
	
	$ podman pull k8s.gcr.io/pause:3.1

	$ podman pod create -n wordpresspod -p 8080:80

	$ podman pod list

	$ podman ps -ap

	$ podman run --name persistent-db -d -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 --pod wordpresspod rhscl/mysql-57-rhel7

	$ podman run --name my-wordpress -e WORDPRESS_DB_HOST=127.0.0.1 -e WORDPRESS_DB_USER=user1 -e WORDPRESS_DB_PASSWORD=mypa55 -e WORDPRESS_DB_NAME=items -d --pod wordpresspod docker.io/wordpress

	$ podman ps -ap

    $ curl http://localhost:8080 o desde un navegador configurar wordpress
    ```
    </details>


32. <details>
	<summary>En servera tengo unos recursos NFS exportados y quiero montarlos con automonter en workstation.</summary>
	<br>
	
	```console
	- Creo contenido en recursos exportados:

	mkdir -p /ofertas/{viajes,productos,servicios}
	mkdir /info
	
	touch /ofertas/viajes/{caribe,islas,costa}.pdf
	touch /ofertas/servicios/{hbo,netflix,limpieza}.pdf
	touch /ofertas/productos/{thermomix,rumba,bq_m5}.pdf
	echo "Enhorabuena, puedes ver el contenido del fichero" > /info/mensaje.txt
	
	- Añado los recursos a exportar
	
	echo "/ofertas/viajes  172.25.0.0/16(rw,sync)" >> /etc/exports
	echo "/ofertas/productos  172.25.0.0/16(rw,sync)" >> /etc/exports
	echo "/ofertas/servicios  172.25.0.0/16(rw,sync)" >> /etc/exports
	echo "/info  172.25.0.0/16(rw,sync)" >> /etc/exports
	
	- Arranco el servicio nfs-server y veo que exporta los recursos:
	
	systemctl start nfs-server
	exportfs -s  -> deben salir las 4 líneas
	
	- Abro firewall para el NFS:
	
	firewall-cmd --permament --add-service=nfs 
	firewall-cmd --reload
	
	
	En workstation, hago:
	
	  dnf install -y tree 
	
	1) Veo los recursos que exporta por NFS servera:
	
	   mount -t nfs servera:/ /mnt; tree /mnt
	
	  veo:
	 
	/mnt
	├── info
	│   └── mensaje.txt
	└── ofertas
	    ├── productos
	    │   ├── bq_m5.pdf
	    │   ├── rumba.pdf
	    │   └── thermomix.pdf
	    ├── servicios
	    │   ├── hbo.pdf
	    │   ├── limpieza.pdf
	    │   └── netflix.pdf
	    └── viajes
	        ├── caribe.pdf
	        ├── costa.pdf
	        └── islas.pdf
	
	  Decido montar con montaje directo /info en /servera-info y con montaje indirecto /ofertas en /servera-ofertas.
	
	 umount /mnt
	
	2) Instalo paquete autofs
	   dnf install -y autofs
	
	3) Creo puntos de montaje:
	   mkdir /servera-{info,ofertas}
	
	4) Creo montaje directo:
	
	   echo "/- /etc/auto.info" >> /etc/auto.master.d/info.autofs   
	
	   echo "/servera-info -rw,sync servera:/info" >> /etc/auto.info
	
	5) Arranco servicio:
	  
	   systemctl enable --now autofs
	
	6) Accedo al directorio: ls /servera  -info -> mensaje.txt y puedo ver contenido
	
	7) Creo montaje indirecto:
	
	   echo "/servera-ofertas /etc/auto.ofertas" > /etc/auto.master.d/ofertas.autofs
	
	   echo "* -rw,sync servera:/ofertas/&" > /etc/auto.ofertas
	
	8) Reinicio servicio autofs:
	
	   systemctl restart autofs
	
	9) Accedo al contenido de ofertas, p.e. viajes: 
	   tree /servera-ofertas/viajes
	
	/servera-ofertas/
	└── viajes
	    ├── caribe.pdf
	    ├── costa.pdf
	    └── islas.pdf
	
	NOTA: Si hago un tree /servera-ofertas no veo los subdirectorios, tengo que acceder explícitamente a ellos 		primero cd /servera-ofertas/viajes y luego ya se verán. Al rato desaparecen
	```
	</details>

32. <details>
	<summary> Usar automount como systemd </summary>
	<br>
	
	```console
	Para que se automonten unidades con systemd (no es necesario el autofs) se modifica el fichero /etc/fstab con la      línea:

    <server>:<recurso>  <punto_montaje>  nfs noauto,x-systemd.automount  0   0 

    Reboot del sistema o systemctl daemon-reload y systemctl restart remote-fs.target 

    Después basta acceder para que se automonte.

    Documentación sobre opciones
 
    man systemd.mount

    La opción noauto hara que tengamos que acceder al punto de montaje para que se monte , no se montara al inicio
    ```
    </details>
	
33. <details>
	<summary> Adjust the context of port 2332 to xen_port_t using the tcp protocol. Adjust SELinux so that it runs in permissive mode persistently. </summary>
	<br>
	
	```console
		// Vemos que etiqueta tiene el puerto
		$ sudo semanage port -l | grep 2332
		// Ajustamos la etiqueta al puerto
		$ sudo semanage port -a -t xen_port_t -p 2332 tcp
		// Comprobamos si la etiqueta esta ajustada 
		$ sudo semanage port -l | grep 2332
		// Ajustamos el modo permissivo de forma persistente editando el fichero /etc/selinux/config
		$ sudo /etc/selinux/config
	  	SELINUX=permissive
		$ setenforce permissive
	```
 
    </details>

34. <details> 
    <summary> Create a 2GB partition using /dev/sdb make it as ext4 file system and mounted automatically under /mnt/data at boot-start</summary>
	<br>
	
	```console
	# parted /dev/sdb mklabel gpt 
	# parted /dev/sdb print
	// 2GB son (2000000000 /1024)/1024 MiB como en la primera partición tenemos que dejar 1MiB=2048sectores de  
	// de espacio para no borrar la tabla de partición al inicio. 2GB = 1907.34MiB
	// se puede hacer ejecutando parted y de forma interactiva o mediante una orden de la consola
	# parted /dev/sdb mkpart particion2g ext4 1MiB 1908MiB
	// Comprobamos 
	# parted /dev/vdc unit GB print
	// Formateamos la partición 
	# mkfs.ext4 /dev/sdb1
	# Actualizamos
	# udevadm settle
	// Creamos la carpeta de montaje
	# mkdir -p /mnt/data
	// Buscamos el UUID de la partición creada y la añadimos a fstab para no tener que buscarla
	# echo $(lsbl -nfs /dev/sdb1) >> /etc/fstab
	// Editamos /etc/fstab para montarlo de forma permanente añadiendo la linea
	# vim /etc/fstab
	UUID=ASJDOJD3009j0jf /mnt/data ext4 defaults 0 0 
    ```
 
</details>

35. <detail>
	<summary> Encontrar todos ficheros del sistema que tengan permiso el Guid activdo y guarda la salida en un fichero </summary>
	<br>

    ```console
	 # find / -type f -perm /2000 > listado.txt
    ```
    
    </details>

36. <details>
	
	<summary>   Crea un fichero tar en los modos gzip y bzip2, comprueba su contenido y luego lo extraes. </summary>
	<br>

	```console
     // gzip
     $ sudo  tar cvfz home.tar.gz /home
     $ sudo tar -tf home.tar.gz
     $ file home.tar.gz
     home.tar.gz: gzip compressed data, from Unix
  	 $ sudo tar xvfz home.tar.gz 
      
   	 // Bzip2 
     $ sudo tar cvfj home.tar.bz2 /home
     $ sudo tar -t home.tar.dz2
     $ file home.tar.bz2
     home.tar.gz: gzip compressed data, from Unix,
     $ sudo tar xvfj home.tar.bz2
    ```

    </details> 
