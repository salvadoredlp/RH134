
 El comando ssh con la opción -i se usa para especificar el archivo de clave privada del
usuario, que es mylab.pem. La clave pública coincidente ya está configurada como una clave
autorizada en la cuenta remoteuser.

```bash
[user@host ~]$ ssh -i mylab.pem remoteuser@remotehost
[remoteuser@remotehost ~]$
```

>[!NOTE]
>
>- Alt + F2 en Gnome se abre ventana para  ejecutar un comando.
>
>- Super + L Bloqueo de la sesión.
>
>- $yelp #Abre ayuda de Gnome
>
>- $yelp help:gnome-help/shell-introduction  #Resumen visual de GNOME
