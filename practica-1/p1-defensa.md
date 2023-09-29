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
2. Ver relación de tiempos de ejecución y services con `systemd-analyze blame`.
3. Ver cadena de ejcución de servicios en el mometo de inicio con `systemd-analyze critical-chain`.
4. 
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
2. Configue la intrfacez de red `ens34` (_pendiente de probar_):
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
1. Mostrar rutas establecidas en el sistema con `ip route show` y `route` (_pendiente de probar_):
```
root@debian:/home/lsi# ip route show
default via 10.11.48.1 dev ens33 onlink 
10.11.48.0/23 dev ens33 proto kernel scope link src 10.11.48.50 
10.11.50.0/23 dev ens34 proto kernel scope link src 10.11.50.50 
169.254.0.0/16 dev ens33 scope link metric 1000
```
```
root@debian:/home/lsi# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    0      0        0 ens33
10.11.48.0      0.0.0.0         255.255.254.0   U     0      0        0 ens33
10.11.50.0      0.0.0.0         255.255.254.0   U     0      0        0 ens34
link-local      0.0.0.0         255.255.0.0     U     1000   0        0 ens33
```
> `_gateway` en mi consola se muestra como la ip `10.11.48.1`.
2. Añadir una nueva ruta con `ip route add 10.11.52.0/24 via 10.11.48.1` (_pendiente de probar_).

## Apartado H
En el apartado d) se ha familiarizado con los services que corren en su sistema. ¿Son necesarios todos ellos?. Si identifica servicios no necesarios, proceda adecuadamente. Una limpieza no le vendrá mal a su equipo, tanto desde el punto de vista de la seguridad, como del rendimiento.

## Apartado I
Diseñe y configure un pequeño “script” y defina la correspondiente unidad de tipo service para que se ejecute en el proceso de botado de su máquina.
