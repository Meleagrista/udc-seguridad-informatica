# Parte 1

## C

 - Multiuser-target


## G

sudo ip route add 10.11.55.0/24 via 10.11.48.1

## H


Servicios desactivados: 

- cups.service 
	ipp algo.service
	cups-browser.service
	
- netwrokmanager.service
- apparmor.service
- avahi-daemon.service


Cambios sistema : 
https://wiki.debian.org/BootProcessSpeedup 

- Using a faster system shell
- Using kexec for warm reboots

Aplicaciones desinstaladas : 

- fuera pulse,pipewire
- Borrar archivos .desktop para solucionar errores de arranque

Cambiar el Grub

- Grub timeout = 0 (No se si hace algo)

## J

`netstat -tuln`

## K

- `top`: El comando `top` muestra una lista de los procesos en ejecución en tu sistema, ordenados por uso de CPU por defecto. También muestra información sobre la memoria y otros recursos utilizados. Para ejecutar `top` en tiempo real, simplemente ejecuta `top` en una terminal.

- `free`: El comando `free` muestra información sobre la memoria utilizada y libre en tu sistema, incluyendo RAM y memoria de intercambio (swap).

- `df`: `df` muestra información sobre el espacio en disco utilizado y disponible en todas las particiones de almacenamiento de tu sistema.

- `netstat`: El comando `netstat` muestra información sobre las conexiones de red y las estadísticas de red. Puedes utilizar `netstat -tuln` para mostrar las conexiones TCP y UDP que están abiertas y escuchando.
    
- `ss`: `ss` es una alternativa moderna a `netstat` y proporciona información más detallada sobre las conexiones de red y los sockets.

- `iftop`: `iftop` muestra una lista en tiempo real de las conexiones de red y el uso de ancho de banda por interfaz de red.

## L

Bloquear tcp wrappers

Twists
Spawn

host.allow host.deny , cual  va primero

mensajes cuando se bloquea, un archivo que guarde los bloqueados


Conexciones
	1. Puerta trasera
	2. Ens33 compañero
	3. vpn -> Rango


## M

Rsyslog  :  

Mandar logs a los compañeros, separados sin mezlcar


Pila de logs :  guardo logs mientras este apagada la maquina de mi compañero, cuando arranque le mando los logs que no tenga

## N

IPV6 activado y segurizado, o desactivado

6to4 en fichero interfaces
pasamos nuestra ipv4 a ipv6

Comprobacion 
	ping6
	ssh -6 
Ahora segurizamos

___
# Parte 2 
___

## E

export PATH=$PATH:/opt/splunk/bin



