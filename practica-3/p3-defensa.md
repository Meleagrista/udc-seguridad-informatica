
# Apartado 1 - C

Configure su cliente y servidor para permitir conexiones basadas en un esquema de autenticación de usuario de clave pública.

>[!Warning]
>Como usuario lsi!

*Deja en blanco todas las opciones*

>`ssh-keygen -t rsa`
>`ssh-keygen -t ed25519`
>`ssh-keygen -t ecdsa`

Ahora en nuestro compañero B:


> `cd /home/lsi/.ssh`

>`scp lsi@<ip compañero A>:/home/lsi/.ssh/*.pub ../keys_compañeroA`
>`nano authorized_keys # Dejamos vacio`
>`cat ../keys_compañeroA/id_rsa.pub >> authorized_keys `
>`cat ../keys_compañeroA/id_ed25519.pub >> authorized_keys `
>`cat ../keys_compañeroA/id_ecdsa.pub >> authorized_keys`


Ahora podemos conectarnos a la maquina de nuestro compañero B con ssh sin que nos pida contraseña

# Apartado 1 - D

Mediante túneles SSH securice algún servicio no seguro.

En la maquina en la que ejecuto esto, redireciono el puerto 10080 de mi localhost al 80 de mi compañero a traves de una conexión ssh.

Ejecuto en mi maquina:

> `ssh -L 10080:<tu ip> lsi@<ip compañero>`

Ahora este comando devuelve el 80 de la máquina de mi compañero, normalmente es no seguro, pero ahora lo mete por el tunel ssh seguro

Ahora cuando este dentro de mi compañero:

 >`wget localhost:10080`


