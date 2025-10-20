
### SELinux 

Modos de SELinux

***Enforcing***: SELinux hace cumplir las políticas cargadas.

***Permissive***: SELinux carga las políticas y está activo, pero en lugar de aplicar las reglas de control de acceso, registra las infracciones de acceso. Este modo es útil para probar y solucionar
problemas de aplicaciones y reglas.

***Disabled***: SELinux está desactivado. Las infracciones de SELinux no se rechazan ni se registran. Se desaconseja deshabilitar SELinux.

Las politicas SELinux son reglas de seguridad que definen como los procesos acceden a los ficheros.

Muchos comandos que enumeran reccursos tienen la opción -Z para gestionar los contextos

***ps axZ*** Muestra todos los procesos con su etiqueta SELinux correspondiente
***ls -Z*** Lista ficheros imprimiendo la etiquete SELinux de cada uno

PÁGINA 176
