
Montar cuando ha habido error al reiniciar y la unidad esta montada en solo lectura

`# mount -o rw,remount /dev/dispositivo /`

Cada vez que se cambie /etc/fstab ejecutar cualquiera de estas ordenes para comprobar que esta bien

`# systemctl daemon-reload`  
`# findmnt --verify`   
`# mount -a`   

Cambiar target en ejecución

`# systemctl isolated multi-user.target`

Ver el target por defecto 

`# systemctl get-default`

Cambiar el target por defecto en el siguiente reinicio 

`# systemctl set-default`

