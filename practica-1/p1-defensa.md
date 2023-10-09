# PRÁCTICA 1 - CONFIGURACIÓN BÁSICA: PRIMERA PARTE
## Apartado A
Configure su máquina virtual de laboratorio con los datos proporcionados por el profesor. Analice los ficheros básicos de configuración (interfaces, hosts, resolv.conf, nsswitch.conf, sources.list, etc.)
1. Modificar el fichero `/etc/network/interfaces`:
> Mis IPs son `10.11.49.54` y `10.11.51.54`. La configuración de este fichero dependerá de tus IPs.
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).
#source /etc/network/interfaces.d/*
# The loopback network interface
auto lo ens33 ens34
iface lo inet loopback
iface ens33 inet static
        address 10.11.49.54/23
#       network 10.11.48.0
#       netmask 255.255.254.0
        gateway 10.11.48.1
        dns-names 10.8.12.47 10.8.12.49 10.8.12.50
iface ens34 inet static
        address 10.11.51.54/23
#       network 10.11.50.0
#       netmask 255.255.254.0
```
> La red es la `10.11.48.0/23` esto quiere decir todo el segmento comienza en `10.11.48.0` y finaliza en `10.11.49.255`.

> No confundir la dirección de broadcast con la ip address de ens34.
`/etc/hosts`:

```
127.0.0.1	localhost
10.11.49.54	debian

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

`/etc/resolv.conf`:

```
domain udc.pri
search udc.pri
nameserver 10.8.12.49
nameserver 10.8.12.50
nameserver 10.8.12.47
```

`/etc/nsswitch.conf`:

```
# /etc/nsswitch.conf
#
# Example configuration of GNU Name Service Switch functionality.
# If you have the `glibc-doc-reference' and `info' packages installed, try:
# `info libc "Name Service Switch"' for information about this file.

passwd:         files systemd
group:          files systemd
shadow:         files
gshadow:        files

hosts:          files mdns4_minimal [NOTFOUND=return] dns myhostname
networks:       files

protocols:      db files
services:       db files
ethers:         db files
rpc:            db files

netgroup:       nis
```

`/etc/apt/sources.list`:

```
deb http://deb.debian.org/debian/ buster main
deb-src http://deb.debian.org/debian/ buster main

deb https://deb.debian.org/debian-security buster-security main contrib
deb-src https://deb.debian.org/debian-security buster-security main contrib

deb http://security.debian.org/debian-security buster/updates main
deb-src http://security.debian.org/debian-security buster/updates main
```
## Apartado B
¿Qué distro y versión tiene la máquina inicialmente entregada?. Actualice su máquina a la última versión estable disponible.

Mostramos la distro actual y su versión con `lsb_release -a`.
```
root@debian:/home/lsi# lsb_release -a
No LSB modules are available.
Distributor ID:	Debian
Description:	Debian GNU/Linux 10 (buster)
Release:	10
Codename:	buster
```
Seguimos la guía sobre como actualizar el sistema de esta [esta pagina](https://www.debian.org/releases/bullseye/amd64/release-notes/ch-upgrading.es.html).

Podemos mostrar nuestra versión ejecutando `cat /etc/debian_version`.

Para actualizar desde el usuario root:

1. `apt update -y && sudo apt upgrade -y`
> La opción -y se usa para aceptar todos los prompts.
2. `apt dist-upgrade`
3. Reemplazar la lista de fuentes `/etc/apt/sources.list` con:
```
deb http://deb.debian.org/debian/ bullseye main
deb-src http://deb.debian.org/debian/ bullseye main

deb https://deb.debian.org/debian-security bullseye-security main contrib
deb-src https://deb.debian.org/debian-security bullseye-security main contrib

# deb http://deb.debian.org/debian/ bullseye-updates main contrib
# deb-src http://deb.debian.org/debian/ bullseye-updates main contrib
```
> Mi instalación dio problemas por culpa de `bullseye-updates`, el profesor me recomendó comentarlo porque no es esencial.
4. `apt update`
5. `apt upgrade --without-new-pkgs`
6. `apt full-upgrade`
7. `reboot`
8. Comprobamos que la máquina se actualizó correctamente:
```
lsi@debian:~$ lsb_release -a
No LSB modules are available.
Distributor ID: Debian
Description: Debian GNU/Linux 11 (bullseye)
Release: 11
Codename: bullseye
```
9. Repetimos el proceso para actualizarlo a la ultima versión disponible, en caso de duda consultamos [esta pagina](https://www.debian.org/releases/stable/i386/release-notes/ch-upgrading.es.html#backup).

## Apartado C
Identifique la secuencia completa de arranque de una máquina basada en la distribución de referencia (desde la pulsación del botón de arranque hasta la pantalla de login). ¿Qué target por defecto tiene su máquina? ¿Cómo podría cambiar el target de arranque? ¿Qué targets tiene su sistema y en qué estado se encuentran? ¿Y los services? Obtenga la relación de servicios de su sistema y su estado. ¿Qué otro tipo de unidades existen?

1. Ver el target predeterminado con `systemctl get-default`.
2. Ver la lista de dependencias del target actual con `systemctl list-dependencies default.target`.
> El target por defecto es `graphical.target`.
3. Cambiar el target por defecto con `systemctl set-default <TARGET>`.
4. Ver targets del sistema y su estado con `systemctl list-unit-files --type=target`
```
root@debian:/home/lsi# systemctl list-unit-files --type=target
UNIT FILE                     STATE    VENDOR PRESET
basic.target                  static   -
blockdev@.target              static   -
bluetooth.target              static   -
...
...
...
umount.target                 static   -
usb-gadget.target             static   -

67 unit files listed.
```
5. Ver servicios con `systemctl list-unit-files --type=service`
```
root@debian:/home/lsi# systemctl list-unit-files --type=service
UNIT FILE                              STATE           VENDOR PRESET
accounts-daemon.service                masked          enabled
alsa-restore.service                   static          -
alsa-state.service                     static          -
alsa-utils.service                     masked          enabled
anacron.service                        enabled         enabled
apparmor.service                       enabled         enabled
apt-daily-upgrade.service              static          -
apt-daily.service                      static          -
autovt@.service                        alias           -
...
...
...
wacom-inputattach@.service             static          -
wpa_supplicant-nl80211@.service        disabled        enabled
wpa_supplicant-wired@.service          disabled        enabled
wpa_supplicant.service                 disabled        enabled
wpa_supplicant@.service                disabled        enabled
x11-common.service                     masked          enabled

173 unit files listed.
```
7. Ver otro tipo de unidades con `systemctl list-units -t help`
```
root@debian:/home/lsi# systemctl list-units -t help
Available unit types:
service
mount
swap
socket
target
device
automount
timer
path
slice
scope
```
## Apartado D
Determine los tiempos aproximados de botado de su _kernel_ y del _userspace_. Obtenga la relación de los tiempos de ejecución de los services de su sistema.
1. Ver tiempos aproximados de botado de kernel y userspace con `systemd-analyze`.
```
lsi@debian:~$ systemd-analyze
Startup finished in 3.557s (kernel) + 1min 31.686s (userspace) = 1min 35.244s
multi-user.target reached after 1min 31.646s in userspace.
```
> Algunos servicios me dan error, por eso el tiempo de carga tan lento.
2. Ver relación de tiempos de ejecución y services con `systemd-analyze blame`.
```
lsi@debian:~$ systemd-analyze blame
13.800s apparmor.service
 5.416s e2scrub_reap.service
 2.847s dev-sda1.device
 2.704s polkit.service
 2.688s ifupdown-pre.service
 2.641s fwupd-refresh.service
 1.998s systemd-logind.service
 1.841s avahi-daemon.service
 1.814s networking.service
 1.783s dbus.service
 1.620s NetworkManager.service
 1.117s systemd-journal-flush.service
 1.045s wpa_supplicant.service
  955ms systemd-udevd.service
  876ms udisks2.service
  828ms systemd-udev-trigger.service
  822ms systemd-timesyncd.service
  736ms systemd-journald.service
  677ms keyboard-setup.service
  585ms ModemManager.service
  474ms NetworkManager-wait-online.service
  471ms apt-daily.service
  462ms apt-daily-upgrade.service
  403ms ssh.service
  396ms systemd-random-seed.service
  358ms systemd-modules-load.service
  325ms systemd-tmpfiles-setup.service
  270ms man-db.service
  239ms rsyslog.service
  225ms plymouth-start.service
  203ms systemd-sysusers.service
  192ms user@1000.service
  183ms systemd-update-utmp.service
  171ms logrotate.service
  159ms systemd-tmpfiles-setup-dev.service
  147ms modprobe@dm_mod.service
  139ms run-vmblock\x2dfuse.mount
  130ms upower.service
  122ms dev-mqueue.mount
  122ms sys-kernel-debug.mount
  121ms sys-kernel-tracing.mount
```
3. Ver cadena de ejcución de servicios en el mometo de inicio con `systemd-analyze critical-chain`.
```
lsi@debian:~$ systemd-analyze critical-chain
The time when unit became active or started is printed after the "@" character.
The time the unit took to start is printed after the "+" character.

multi-user.target @1min 31.646s
└─ssh.service @19.618s +403ms
  └─network.target @19.576s
    └─NetworkManager.service @17.955s +1.620s
      └─dbus.service @16.150s +1.783s
        └─basic.target @16.103s
          └─sockets.target @16.095s
            └─dbus.socket @16.094s
              └─sysinit.target @16.071s
                └─apparmor.service @2.270s +13.800s
                  └─local-fs.target @2.261s
                    └─run-credentials-systemd\x2dtmpfiles\x2dsetup.service.mount @3.532s
                      └─local-fs-pre.target @2.261s
                        └─keyboard-setup.service @1.583s +677ms
                          └─systemd-journald.socket @1.574s
                            └─-.mount @1.557s
                              └─-.slice @1.557s
``` 
## Apartado E
Investigue si alguno de los servicios del sistema falla. Pruebe algunas de las opciones del sistema de registro journald. Obtenga toda la información journald referente al proceso de botado de la máquina. ¿Qué hace el `systemd-timesyncd`?
1. Comprobar si algún servicio ha fallado con `systemctl list-unit-files --type=service --failed`.
2. Ver el log de un servicio con `journalctl -u <SERVICE>`.
> Solo se puede activar con permisos de adminitrador.
3. Ver el log del boot actual con `journalctl -b`.

`systemd-timesyncd` es un servicio que facilita la sincronización del reloj del sistema con servidores NTP externos de manera automática y eficiente. Cuando este servicio está habilitado y en funcionamiento, se encarga de solicitar información de tiempo a servidores NTP remotos y ajustar el reloj local del sistema en consecuencia.

## Apartado F
Identifique y cambie los principales parámetros de su segundo interface de red (ens34). Configure un segundo interface lógico. Al terminar, déjelo como estaba.
1. Ver estado inicial de `ens34`  con `ifconfig ens34`:
```
lsi@debian:~$ ifconfig ens34
ens34: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.11.51.54  netmask 255.255.254.0  broadcast 10.11.51.255
        inet6 fe80::250:56ff:fe97:b5d6  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:97:b5:d6  txqueuelen 1000  (Ethernet)
        RX packets 200  bytes 47180 (46.0 KiB)
        RX errors 0  dropped 66  overruns 0  frame 0
        TX packets 11  bytes 866 (866.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 16  base 0x2080
```
2. Configue la intrfacez de red `ens34`:
```
root@debian:/home/lsi# ifconfig ens34 down
root@debian:/home/lsi# ifconfig ens34 mtu 1200
root@debian:/home/lsi# ifconfig ens34 hw ether 00:1e:2e:b5:18:07
root@debian:/home/lsi# ifconfig ens34 10.11.50.51 netmask 255.255.254.0
root@debian:/home/lsi# ifconfig ens34 up
```
3. Configuración de una nueva interfaz lógica:
```
root@debian:/home/lsi# ifconfig ens34:1 192.168.1.1 netmask 255.255.255.0
root@debian:/home/lsi# ifconfig ens34:1 up
```
> Para persistir los cambios debemos hacerlos en `/etc/network/interfaces`, si no un `reboot` debería reestablecer los cambios.

## Apartado G
¿Qué rutas (routing) están definidas en su sistema?. Incluya una nueva ruta estática a una determinada red.
1. Mostrar rutas establecidas en el sistema con `ip route show` y `route`:
```
lsi@debian:~$ ip route show
default via 10.11.48.1 dev ens33 onlink
10.11.48.0/23 dev ens33 proto kernel scope link src 10.11.49.54
10.11.50.0/23 dev ens34 proto kernel scope link src 10.11.51.54
169.254.0.0/16 dev ens33 scope link metric 1000
```
```
lsi@debian:~$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         10.11.48.1      0.0.0.0         UG    0      0        0 ens33
10.11.48.0      0.0.0.0         255.255.254.0   U     0      0        0 ens33
10.11.50.0      0.0.0.0         255.255.254.0   U     0      0        0 ens34
link-local      0.0.0.0         255.255.0.0     U     1000   0        0 ens33
```
> `_gateway` en mi consola se muestra como la ip `10.11.48.1`.
2. Añadir una nueva ruta con `ip route add 10.11.53.0/24 via 10.11.48.1`.

## Apartado H
En el apartado d) se ha familiarizado con los services que corren en su sistema. ¿Son necesarios todos ellos?. Si identifica servicios no necesarios, proceda adecuadamente. Una limpieza no le vendrá mal a su equipo, tanto desde el punto de vista de la seguridad, como del rendimiento.

- `systemctl disable apparmor`
- `systemctl mask avahi-daemon`
- `systemctl disable NetworkManager`
- `systemctl disable cups`
- `systemctl disable cups-browsed`
- `systemctl disable ModemManager`
- `systemctl disable bluetooth`
- `systemctl mask configure-printer@`
- `systemctl mask display-manager`
- `systemctl mask nm-priv-helper`
- `systemctl mask saned@.service`
- `systemctl mask usb_modeswitch`
- `systemctl mask usbmuxd`

Antes de borrar ningún servicio es recomendable ver si este posee alguna dependencia que puedo afectar a la maquina con `systemctl list-dependencies <SERVICIO> [--all] [--reverse]`.

## Apartado I
Diseñe y configure un pequeño “script” y defina la correspondiente unidad de tipo service para que se ejecute en el proceso de botado de su máquina.

1. He creado el script `/usr/local/bin/notify` que se conecta con un bot de Telegram:
```
#!/bin/bash
API_KEY=XXXXXXXXXXXX
CHAT_ID=XXXXXXXXXXXX

if [ "$1" == "--boot" ]; then MESSAGE="`/bin/date +'%d/%m/%Y %R:%S '`-> Iniciada máquina de Álvaro."; else MESSAGE=$1; fi

MESSAGE=${MESSAGE:-"Vaya, no hay mensaje."}

wget -qO- "https://api.telegram.org/bot$API_KEY/sendMessage?chat_id=$CHAT_ID&text=$MESSAGE" &> /dev/null
```
2. Y un servicio `/etc/systemd/system/notify-boot.service` que me avisa de que la máquina ha sido encendida:
```
[Unit]
Description=Custom service that notifies through Telegram on startup.
After=network.target

[Service]
Type=simple
Restart=on-failure
RestartSec=5s
User=lsi
ExecStart=notify --boot

[Install]
WantedBy=multi-user.target
```
3. Y se activa con el comando `systemctl enable notify-boot.service`.

## Apartado J - Pending...
Identifique las conexiones de red abiertas a y desde su equipo.
- Para leer las conexiones abiertas se ejecuta el comando `netstat -netua`.

## Apartado K - Pending...
Nuestro sistema es el encargado de gestionar la CPU, memoria, red, etc., como soporte a los datos y procesos. Monitorice en “tiempo real” la información relevante de los procesos del sistema y los recursos consumidos. Monitorice en “tiempo real” las conexiones de su sistema.

1. Procesos del sistema en *tiempo real*: `top`
2. Conexiones del sistema en *tiempo real*: `netstat -netuac`

## Apartado L - Pending...
Un primer nivel de filtrado de servicios los constituyen los tcp-wrappers. Configure el tcp-
wrapper de su sistema (basado en los ficheros hosts.allow y hosts.deny) para permitir
conexiones SSH a un determinado conjunto de IPs y denegar al resto. ¿Qué política general de
filtrado ha aplicado?. ¿Es lo mismo el tcp-wrapper que un firewall?. Procure en este proceso no
perder conectividad con su máquina. No se olvide que trabaja contra ella en remoto por ssh.

1. `/etc/hosts.allow`: El sistema comprueba primero este archivo para las conexiones tcp.
2. `/etc/hosts.deny`: Si la IP desde la que se están intentando conectar a nuestra máquina no coincide con ninguna en `/etc/hosts.allow`, se comprueba si está denegada aquí.

## Apartado M
Existen múltiples paquetes para la gestión de logs (syslog, syslog-ng, rsyslog). Utilizando el rsyslog pruebe su sistema de log local.
```
lsi@debian:~$ logger "logger de util-linux 2.38.1"
root@debian:/home/lsi# tail -2 /var/log/syslog
2023-10-02 T18:12:22.339138+02:00 debian systemd-timesyncd[571]: Timed out waiting for reply from 178.215.228.24:123 (3.debian.pool.ntp.org).
2023-10-02 T18:15:40.957900+02:00 debian lsi: logger de util-linux 2.38.1
```
> `tail -2` devuelve las dos últimas líneas de un fichero.

## Apatado N
Configure IPv6 6to4 y pruebe ping6 y ssh sobre dicho protocolo. ¿Qué hace su tcp-wrapper en las conexiones ssh en IPv6? Modifique su tcp-wapper siguiendo el criterio del apartado h). ¿Necesita IPv6?. ¿Cómo se deshabilita IPv6 en su equipo?

1. Añadir a `/etc/network/interfaces` la configuración de la nueva interfaz:
```
auto 6to4
iface 6to4 inet6 v4tunnel
	pre-up modprobe ipv6
	address 2002:a0b:3036::1
	netmask 16
	gateway ::10.11.48.1
	endpoint any
	local 10.11.48.54
```
2. Activar la interfaz con `ifup 6to4`.
3. Probamos a hacer un `ping6`.
4. Añadimos a `/etc/hosts.allow`:
```
sshd: [2002:a0b:3136::1]/48, [2002:a0b:3137::1]/48
```
> Añadimos nuestra ip propia y la de nuestro compañero.
5. Probamos a hacer un `ssh`.
> No es necesario en nuestra red interna el uso de IPv6 gracias a nuestra interfaz 6to4.
- Se dehabilita añadiendo estas líneas en el fichero `/etc/sysctl.conf`:
```
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```
6. Aplicar los cambios con `sysctl -p`.

# PRÁCTICA 1 - CONFIGURACIÓN BÁSICA: SEGUNDA PARTE
## APARTADO A
En colaboración con otro alumno de prácticas, configure un servidor y un cliente NTP.
> Para la demostración usamos 10.11.49.54 como servidor y 10.11.49.55 como cliente.
### Maquina del servidor
1. Configurar el fichero `/etc/ntpsec/ntp.conf` con las siguiente lineas:
```
# pool.ntp.org maps to about 1000 low-stratum NTP servers.  Your server will
# pick a different set every time it starts up.  Please consider joining the
# pool: <http://www.pool.ntp.org/join.html>
#pool 0.debian.pool.ntp.org iburst
#pool 1.debian.pool.ntp.org iburst
#pool 2.debian.pool.ntp.org iburst
#pool 3.debian.pool.ntp.org iburst

# Comentar el primero para modo cliente o comenta el segundo (tercera linea) para modo servidor.
server 127.127.1.0
fudge 127.127.1.0 stratum 10
#server 10.11.49.54

# Nuestros restricts, no estoy seguro si funcionan correctamente, usadlos a vuestra discreción.
#restrict 10.11.49.54 mask 255.255.255.255 notrap modify
#restrict ::1
#restrict 127.0.0.1

# By default, exchange time with everybody, but don't allow configuration.
restrict default kod nomodify nopeer noquery limited

# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1
```
> La configuración de otros años fue la siguiente, pero el resto de la configuración está obsoleta.
```
# Configuración servidor
server 127.127.1.0 minpoll 4
fudge 127.127.1.0 stratum 0
```
### Maquina del cliente
1. Configurar el fichero `/etc/ntpsec/ntp.conf` con las siguiente lineas:
```
# pool.ntp.org maps to about 1000 low-stratum NTP servers.  Your server will
# pick a different set every time it starts up.  Please consider joining the
# pool: <http://www.pool.ntp.org/join.html>
#pool 0.debian.pool.ntp.org iburst
#pool 1.debian.pool.ntp.org iburst
#pool 2.debian.pool.ntp.org iburst
#pool 3.debian.pool.ntp.org iburst

# Comentar el primero para modo cliente o comenta el segundo (tercera linea) para modo servidor.
#server 127.127.1.0
fudge 127.127.1.0 stratum 10
server 10.11.49.54

# Nuestros restricts, no estoy seguro si funcionan correctamente, usadlos a vuestra discreción.
#restrict 10.11.49.54 mask 255.255.255.255 notrap modify
#restrict ::1
#restrict 127.0.0.1

# By default, exchange time with everybody, but don't allow configuration.
restrict default kod nomodify nopeer noquery limited

# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1
```
> La configuración de otros años fue la siguiente, pero el resto de la configuración está obsoleta.
```
### Configuración cliente
server 10.11.49.106 minpoll 4
fudge 127.127.1.0 stratum 1
```

2. Para actualizar cambios en `ntp.conf` usar `systemctl restart ntp`.
3. Para comprobar si ambas maquinas están funcionando correctamente podemos usar `ntpq -p`.
El comando nos indicará el estado de tanto cliente como servidor:
- Para confirmar su correcto funcionamiento debemos comprobar que el mensaje la columna `redif` es `LOCAL(0)` en vez de `.INIT.`.
- Después para comprobar si las maquinas pueden conectarse de forma segura debemos comprobar que la columna `reach` es 377.
```
lyra@ws07475:~$ ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 europium.canoni .INIT.          16 u    - 1024    0    0.000    0.000   0.000
```
> Sometimes internet routers have problems passing through NTP traffic. The reason is that UDP is a bit more trickier to forward than
TCP and sometimes the port is even used on the device itself for an NTP daemon.

Toda la información sobre le output del comando se encuentra en [esta pagina](https://paulroberts69.wordpress.com/2022/08/02/interpreting-ntpq-output/).
5. Para realizar la comprobación podemos hacer lo siguiente:
```
root@debian:~# date +%T -s 1
01:00:00
root@debian:~# ntpdate 10.11.49.106
28 Sep 23:43:46 ntpdate[1017]: step time server 10.11.49.106 offset +81815.937779 sec
```
> Cambiar la hora en la maquina cliente y luego pedir una actualización al servidor.

Para la comprobación es necesario instalar con `apt install` el paquete `nptdate`.

## Apartado B
Cruzando los dos equipos anteriores, configure con rsyslog un servidor y un cliente de logs.
> Para la demostración usamos 10.11.49.54 como servidor y 10.11.49.55 como cliente.
### Maquina del servidor
1. Configurar el fichero `/etc/rsyslog.conf` con las siguiente lineas:
```
#################
#### MODULES ####
#################

module(load="imuxsock") # provides support for local system logging
module(load="imklog")   # provides kernel logging support
#module(load="immark")  # provides --MARK-- message capability

# provides UDP syslog reception
#module(load="imudp")
#input(type="imudp" port="514")

# provides TCP syslog reception
module(load="imtcp")
input(type="imtcp" port="514")

###########################
#### GLOBAL DIRECTIVES ####
###########################


# Con esto servidor sólo aceptará mensajes del compañero
$AllowedSender TCP 127.0.0.1, 10.11.49.106

# Template para guardar los registros de log
$template remote, "/var/log/%fromhost-ip%/%programname%.log"
*.* ?remote
& stop
```
> Descomentar los modulos que se quieran utilizar para la conexión.
### Maquina del cliente
1. Configurar el fichero `/etc/rsyslog.conf` con las siguiente lineas:
```
*.* action(
       type="omfwd" 
       target="10.11.49.54" 
       port="514" 
       protocol="tcp" 
       action.resumeRetryCount="-1"
       queue.type="linkedlist"
       queue.filename="rsyslog-queue"
       queue.saveOnShutdown="on"
)
```
2. Para actualizar cambios en `rsyslog.conf` utilizar `systemctl restart rsyslog.service`.
- Para probar si funciona podemos hacer lo siguiente:
1. Ejecutar `root@debian:/home/lsi# logger "Hello world"` en el cliente.
2. Ejecutar `root@debian:~# cat /var/log/10.11.49.55/lsi.log` en el servidor.
3. En el servidor, abrir el archivo `/etc/rsyslog.conf y comentar la siguiente línea en el servidor:
```
# Provides TCP syslog reception
module(load="imtcp")
#input(type="imtcp" port="514")
```
> Esto para que deje de escucharse en el puerto 514/TCP.

4. Tras esto, ejecutar `systemctl restart rsyslog` en el servidor.
5. Mandar un log al servidor desde el cliente.
6. Comprobar que no se ha guardado el mensaje generado con `tail /var/log/10.11.49.55/lsi.log` en el servidor.
7. Descomentar la línea comentada en el paso `3` y volver a hacer el paso `4`.
8. Tras unos segundos, volver a hacer `tail /var/log/10.11.49.55/lsi.log` en el servidor.

## APARTADO C
Haga todo tipo de propuestas sobre los siguientes aspectos: ¿Qué problemas de seguridad identifica en los dos apartados anteriores? ¿Cómo podría solucionar los problemas identificados?

### En `rsyslog`
1. Si no se configura bien puede suceder cuando los logs ajenos se guardan en el mismo lugar que los propios con todos los problemas que eso conlleva.
[2](https://www.incibe.es/incibe-cert/alerta-temprana/vulnerabilidades/cve-2018-16881). Cualquiera podría enviar logs al servidor y llenar el disco, lo que supone que podrían hacer al servidor un ataque [D]DOS.
[3](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html). Además, los logs van sin cifrar por la red; cualquiera podría ver o modificar sus contenidos con un ataque MitM (_Man in the Middle_).

- *Network vulnerabilities:* Rsyslog has a feature that allows remote logging over the network, which can be useful for centralized log management. However, if this feature is not properly configured, it can open the system to network attacks such as man-in-the-middle attacks or malicious log message injection.
- *Privilege Elevation:* Rsyslog can be configured to run as a privileged user, which can pose a security risk if the software contains vulnerabilities that can be exploited to gain elevated privileges.
- *Injection Attacks:* Rsyslog can be configured to run arbitrary scripts based on specific log messages using RainerScript language.

### En `ntp`
Una busqueda resulta en bastantes vulnerabilidades encontradas, para mas información ver [esta pagina](https://www.cvedetails.com/vulnerability-list/vendor_id-2153/NTP.html).
> Typically, time synchronization is achieved by implementing Network Time Protocol (NTP), which runs on UDP port 123.

> NTP is one of the internet’s oldest protocols and is not secure by default, leaving it susceptible to distributed denial-of-service (DDoS) and man-in-the-middle (MitM) attacks.

_NTP Amplification_ is a type of reflective DDoS attack in which an attacker targets publicly-accessible NTP servers and repeatedly sends requests to the server using a spoofed IP address in order to send the targeted system a large response from the NTP server.
For example, an NTP Amplification attack could prevent internet users from reaching an organization's websites and web resources.

Additionally, NTP is vulnerable to MitM attacks. These attacks allow unauthorized users to intercept, read, and modify traffic sent between clients and servers. NTP is particularly susceptible to MitM attacks due to the reliance on a small set of servers and the algorithm used to choose a server with which to sync.

### For `rsyslog`:
1. **Network Vulnerabilities**:
   - **Solution**: Ensure that remote logging is configured securely to prevent unauthorized access and message injection. Follow these steps:
     - Configure firewall rules to limit access to the `rsyslog` port (usually UDP/514 or TCP/514) to trusted sources only.
     - Implement TLS (Transport Layer Security) encryption for remote logging to protect log messages from interception.
     - Use authentication mechanisms such as username/password or client certificates for remote log sources.
     - Regularly review and rotate TLS certificates and keys.

2. **Privilege Elevation**:
   - **Solution**: Run `rsyslog` with the least privilege necessary. Follow these steps:
     - Create a dedicated user and group for `rsyslog` to run as.
     - Configure the `rsyslog` service to run with these limited privileges.
     - Avoid running `rsyslog` as the root user, as this minimizes the potential impact of vulnerabilities.

3. **Injection Attacks**:
   - **Solution**: Be cautious when configuring RainerScript rules that execute arbitrary scripts. Follow these steps:
     - Only allow trusted users to configure custom RainerScript rules.
     - Sanitize and validate input to prevent any malicious script injection.
     - Regularly review and audit RainerScript configurations for security issues.

### For `ntp`:
1. **NTP Amplification**:
   - **Solution**: Mitigate NTP Amplification attacks by implementing the following measures:
     - Update your NTP software to the latest version to address known vulnerabilities.
     - Configure NTP servers to restrict access from unauthorized or spoofed IP addresses.
     - Implement rate limiting on NTP responses to prevent amplification attacks.
     - Monitor NTP traffic and set up alerts for unusual activity or traffic spikes that may indicate an ongoing attack.

2. **MitM Attacks**:
   - **Solution**: Protect against Man-in-the-Middle (MitM) attacks by following these guidelines:
     - Implement NTP authentication mechanisms to ensure that NTP clients can trust the server they are synchronizing with.
     - Use a diverse set of NTP servers from trusted sources to reduce the risk of relying on a single compromised server.
     - Encrypt NTP traffic using NTS (Network Time Security) or other secure methods to prevent eavesdropping and tampering.

3. **General NTP Security**:
   - **Solution**: Enhance the overall security of your NTP infrastructure:
     - Periodically review and patch your NTP software to address newly discovered vulnerabilities.
     - Restrict access to NTP servers and services to trusted networks and clients.
     - Implement network segmentation to isolate NTP servers from critical infrastructure.
     - Monitor NTP traffic and logs for suspicious activity.

# APARTADO D
En la plataforma de virtualización corren, entre otrs equipos, más de 200 máquinas virtuales para LSI. Como los recursos son limitados, y el disco duro también, identifique todas aquellas acciones que pueda hacer para reducir el espacio de disco ocupado.

- `df -H`: Muestra información sobre el almacenamiento de los sistemas de ficheros montados en la máquina.
- `apt autoclean`: Elimina de la caché los paquetes de versiones antiguas e innecesarias.
- `apt clean`: Elimina todos los paquetes de la caché.
- `apt autoremove`: Elimina aquellos paquetes generalmente instalados como dependencias de otras instalaciones, que ya no son necesarios.
- `apt --purge autoremove`: La opción `--purge` permite otras llamadas de `apt` para borrar también archivos de configuración y demás.
- `apt remove --purge man-db`: Borrar `man`.

También se puede borrar imágenes kernel antiguas:
1. `uname -r`: Muestra kernel actual.
2. `dpkg --list | grep linux-image`: Muestra los kernels que tenemos en el sistema.
```console
	root@debian:/home/lsi# dpkg --list | grep linux-image
	ii  linux-image-5.10.0-18-amd64           5.10.140-1                       amd64        Linux 5.10 for 64-bit PCs (signed)
	ii  linux-image-amd64                     5.10.140-1                       amd64        Linux for 64-bit PCs (meta-package)
```
3. `apt-get --purge remove linux-image-4...`: Elimina un kernel en específico.
   
Lo ideal es dejar solo las imagenes mas actuales.

## APARTADO E
Instale el SIEM Splunk en su máquina. Sobre dicha plataforma haga los siguientes puntos:
1. Genere una query que visualice los logs internos del splunk.
2. Cargue el fichero `/var/log/apache2/access.log` y el `journal` del sistema y visualicelos.
3. Obtenga las IPs de los equipos que se han conectado a su servidor web (pruebe a generar algún tipo de grafico de visualización), así como las IPs que se han conectado un determinado día de un determinado mes.
4. Trate de obtener el país y región origen de las IPs que se han conectado a su servidor web y y si es posible sus coordenadas geográficas.
5. Obtenga los host origen, sources y sourcestypes.
6. ¿Como podría hacer que splunk haga de servidor de log de su cliente?

Como _Splunk_ está alojado dentro de una carpeta no accesible desde el _PATH_ lo mejor es usar la siguiente linea al comienzo de la sesion: `export PATH=$PATH/opt/splunk/bin`.

1. Iniciar _Splunk_:

Abre una terminal o línea de comandos en tu sistema operativo.
Ejecuta el comando splunk start para iniciar Splunk. Dependiendo de cómo esté configurado en tu sistema, es posible que necesites privilegios de administrador o sudo para ejecutar este comando.

2. Iniciar sesión en _Splunk Web_:

Abre tu navegador web y accede a la interfaz web de Splunk.
Por lo general, puedes hacerlo ingresando la dirección http://localhost:8000 en la barra de direcciones. Esto te llevará a la página de inicio de sesión de Splunk.

3. Iniciar sesión en Splunk:

Inicia sesión en Splunk con tus credenciales de usuario.

4. Ir a la Búsqueda y Reportes:

Una vez que hayas iniciado sesión, serás redirigido al panel de control de Splunk.
Haz clic en "Búsqueda y Reportes" en el menú de navegación superior. Esto te llevará a la interfaz de búsqueda de Splunk.

5. Ejecutar la Consulta SPL:

En la interfaz de búsqueda, verás un cuadro de búsqueda de texto.
Puedes ingresar tu consulta SPL directamente en este cuadro. Por ejemplo, como mencionamos anteriormente, puedes usar index="internal" sourcetype="splunkd" para buscar los logs internos de Splunk.

6. Ver los Resultados:

Splunk mostrará los resultados de la consulta en la parte inferior de la página. Puedes explorar los registros y los datos resultantes de acuerdo con tus necesidades.

- Como debido al cambio de temario este apartado vino antes que la instalación de _Apache_ hemos generado y documento de logs falso con IPs de diferentes lugares:
```
2023-10-01T10:00:55.571035+02:00 debian sshd[1001]: Failed password for user admin from 203.0.113.1 port 12345 ssh2
2023-10-01T10:02:46.973354+02:00 debian sshd[1002]: Failed password for user root from 192.168.1.100 port 54321 ssh2
2023-10-01T10:05:12.865123+02:00 debian sshd[1003]: Accepted publickey for user user1 from 103.45.67.89 port 23456 ssh2
2023-10-01T10:10:05.791202+02:00 debian sshd[1004]: Failed password for invalid user testuser from 45.67.89.101 port 9876 ssh2
2023-10-02T14:15:33.124578+02:00 debian sshd[1005]: Accepted password for user admin from 185.23.45.67 port 34567 ssh2
2023-10-02T14:20:21.512345+02:00 debian sshd[1006]: Failed password for user user2 from 112.130.140.150 port 2222 ssh2
2023-10-03T18:30:15.981234+02:00 debian sshd[1007]: Accepted publickey for user admin from 203.45.56.78 port 1111 ssh2
2023-10-03T18:35:29.785643+02:00 debian sshd[1008]: Failed password for user root from 210.123.45.67 port 7777 ssh2
2023-10-03T18:40:10.445566+02:00 debian sshd[1009]: Failed password for invalid user guest from 123.45.67.89 port 8888 ssh2
2023-10-04T22:55:44.990011+02:00 debian sshd[1010]: Accepted password for user admin from 67.89.90.101 port 2222 ssh2
2023-10-04T22:58:57.654321+02:00 debian sshd[1011]: Failed password for user user3 from 34.56.78.90 port 3333 ssh2
2023-10-04T23:05:08.112233+02:00 debian sshd[1012]: Failed password for user testuser from 78.90.101.112 port 4444 ssh2
2023-10-05T03:20:17.776655+02:00 debian sshd[1013]: Accepted publickey for user admin from 101.112.113.114 port 5555 ssh2
2023-10-05T03:22:45.334455+02:00 debian sshd[1014]: Failed password for user root from 115.116.117.118 port 6666 ssh2
2023-10-05T03:30:12.998877+02:00 debian sshd[1015]: Failed password for invalid user guest from 119.120.121.122 port 7777 ssh2
2023-10-06T08:45:30.445566+02:00 debian sshd[1016]: Accepted password for user admin from 123.124.125.126 port 8888 ssh2
2023-10-06T08:48:22.998877+02:00 debian sshd[1017]: Failed password for user user4 from 127.128.129.130 port 9999 ssh2
2023-10-06T08:55:10.667788+02:00 debian sshd[1018]: Failed password for user testuser from 131.132.133.134 port 1111 ssh2
2023-10-07T12:10:59.223344+02:00 debian sshd[1019]: Accepted publickey for user admin from 135.136.137.138 port 1234 ssh2
2023-10-07T12:15:42.556677+02:00 debian sshd[1020]: Failed password for user root from 139.140.141.142 port 2345 ssh2
2023-10-07T12:20:18.112233+02:00 debian sshd[1021]: Failed password for invalid user guest from 143.144.145.146 port 3456 ssh2
2023-10-08T16:30:11.776655+02:00 debian sshd[1022]: Accepted password for user admin from 147.148.149.150 port 4567 ssh2
2023-10-08T16:35:59.998877+02:00 debian sshd[1023]: Failed password for user user5 from 151.152.153.154 port 5678 ssh2
2023-10-08T16:40:29.667788+02:00 debian sshd[1024]: Failed password for user testuser from 155.156.157.158 port 6789 ssh2
2023-10-09T20:55:45.334455+02:00 debian sshd[1025]: Accepted publickey for user admin from 159.160.161.162 port 7890 ssh2
2023-10-09T20:58:22.556677+02:00 debian sshd[1026]: Failed password for user root from 163.164.165.166 port 8901 ssh2
2023-10-09T21:05:10.112233+02:00 debian sshd[1027]: Failed password for invalid user guest from 167.168.169.170 port 9012 ssh2
2023-10-10T01:15:30.445566+02:00 debian sshd[1028]: Accepted password for user admin from 171.172.173.174 port 1234 ssh2
2023-10-10T01:18:45.998877+02:00 debian sshd[1029]: Failed password for user user6 from 175.176.177.178 port 2345 ssh2
2023-10-10T01:20:59.667788+02:00 debian sshd[1030]: Failed password for user testuser from 179.180.181.182 port 3456 ssh2
2023-10-11T05:30:20.556677+02:00 debian sshd[1031]: Accepted publickey for user admin from 183.184.185.186 port 4567 ssh2
2023-10-11T05:35:41.112233+02:00 debian sshd[1032]: Failed password for user root from 187.188.189.190 port 5678 ssh2
2023-10-11T05:40:59.998877+02:00 debian sshd[1033]: Failed password for invalid user guest from 191.192.193.194 port 6789 ssh2
2023-10-12T09:50:30.445566+02:00 debian sshd[1034]: Accepted password for user admin from 195.196.197.198 port 7890 ssh2
2023-10-12T09:52:45.998877+02:00 debian sshd[1035]: Failed password for user user7 from 199.200.201.202 port 8901 ssh2
2023-10-12T09:55:10.667788+02:00 debian sshd[1036]: Failed password for user testuser from 203.204.205.206 port 9012 ssh2
2023-10-13T14:05:22.556677+02:00 debian sshd[1037]: Accepted publickey for user admin from 207.208.209.210 port 1234 ssh2
2023-10-13T14:10:37.112233+02:00 debian sshd[1038]: Failed password for user root from 211.212.213.214 port 2345 ssh2
2023-10-13T14:15:49.998877+02:00 debian sshd[1039]: Failed password for invalid user guest from 215.216.217.218 port 3456 ssh2
2023-10-14T18:20:55.334455+02:00 debian sshd[1040]: Accepted password for user admin from 219.220.221.222 port 4567 ssh2
2023-10-14T18:25:41.556677+02:00 debian sshd[1041]: Failed password for user user8 from 223.224.225.226 port 5678 ssh2
2023-10-14T18:30:59.998877+02:00 debian sshd[1042]: Failed password for user testuser from 227.228.229.230 port 6789 ssh2
2023-10-15T22:35:10.667788+02:00 debian sshd[1043]: Accepted publickey for user admin from 231.232.233.234 port 7890 ssh2
2023-10-15T22:40:45.334455+02:00 debian sshd[1044]: Failed password for user root from 235.236.237.238 port 8901 ssh2
2023-10-15T22:45:59.998877+02:00 debian sshd[1045]: Failed password for invalid user guest from 239.240.241.242 port 9012 ssh2
2023-10-16T02:50:30.445566+02:00 debian sshd[1046]: Accepted password for user admin from 243.244.245.246 port 1234 ssh2
2023-10-16T02:52:45.998877+02:00 debian sshd[1047]: Failed password for user user9 from 247.248.249.250 port 2345 ssh2
2023-10-16T02:55:10.667788+02:00 debian sshd[1048]: Failed password for user testuser from 251.252.253.254 port 3456 ssh2
```
