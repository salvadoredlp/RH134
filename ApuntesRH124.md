l comando ssh con la opción -i se usa para especificar el archivo de clave privada del
usuario, que es mylab.pem. La clave pública coincidente ya está configurada como una clave
autorizada en la cuenta remoteuser.
```bash
[user@host ~]$ ssh -i mylab.pem remoteuser@remotehost
[remoteuser@remotehost ~]$
```
