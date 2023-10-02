# PRÁCTICA 1 - CONFIGURACIÓN BÁSICA
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

## Apatado N - Pending...
Configure IPv6 6to4 y pruebe ping6 y ssh sobre dicho protocolo. ¿Qué hace su tcp-wrapper en las conexiones ssh en IPv6? Modifique su tcp-wapper siguiendo el criterio del apartado h). ¿Necesita IPv6?. ¿Cómo se deshabilita IPv6 en su equipo?

1. Añadir a `/etc/network/interfaces` la configuración de la nueva interfaz:
```
auto 6to4
iface 6to4 inet6 v4tunnel
	pre-up modprobe ipv6
	address 2002:a0b:3032::1
	netmask 16
	gateway ::10.11.48.1
	endpoint any
	local 10.11.48.50
```
2. Activar la interfaz con `ifup 6to4`.
3. Probamos a hacer un `ping6`.
4. Añadimos a `/etc/hosts.allow`:
```
sshd: [2002:a0b:3032::1]/48, [2002:a0b:316a::1]/48
```
> IP propia (ens33) + IP compañero (ens33).
5. Probamos `ssh`:
> No es necesario en nuestra red interna el uso de IPv6 gracias a nuestra interfaz 6to4.
6. Se dehabilita añadiendo estas líneas en el fichero `/etc/sysctl.conf`:
```
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```
7. Aplicar los cambios con `sysctl -p`.