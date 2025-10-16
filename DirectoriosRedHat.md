



| Directorio | Uso |
|------------|-----|
|  /etc/sudoers.d | Donde se colocan configuraciones personalizadas de usuario que pueden hacer sudo y como hacerlo<br><br>```console  %consultants ALL=(ALL) ALL```  |
| /etc/login.defs | Las caracteristicas por defecto al crear un usuario , expiración de contraseñas, rangos de uid , gids.<br><br>PASS_MAX_DAYS<br>PASS_MIN_DAYS<br>PASS_WARN_AGE |
| /etc/dnf/dnf.conf  | Donde se configuran repositorios adicionales |
| /run/media/USERNAME/LABEL. USERNAME | Donde se montan  dispositivos de almacenamiento extraíble . USERNAME es el nombre del usuario que ha iniciado sesión en el entorno gráfico. LABEL es un identificador, que suele ser la etiqueta en el medio de almacenamiento. |
| /etc/ssh/sshd_config | Para configurar el servidor sshd se configura el acceso por medio de ssh <br><br> PermitRootLogin <br>  PasswordAuthentication |
                        
