# PRÁCTICA 1 - CONFIGURACIÓN BÁSICA
## PARTE 1
### Apartado A
Configure su máquina virtual de laboratorio con los datos proporcionados por el profesor. Analice los ficheros básicos de configuración (interfaces, hosts, resolv.conf, nsswitch.conf, sources.list, etc.)
1. Modificar el fichero `/etc/network/interfaces`:
> Mis IPs son `10.11.49.54` y `10.11.51.54`. La configuración de este fichero dependerá de tus IPs.
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).
#source /etc/network/interfaces.d/*
# The loopback network interface
auto lo ens33
iface lo inet loopback

iface ens33 inet static
        address 10.11.49.54
        network 10.11.48.0
        netmask 255.255.254.0
        gateway 10.11.48.1
	# broadcast 10.11.49.255

iface ens34 inet static
	address 10.11.51.54
	netmask 255.255.254.0
	network 10.11.50.0
	# broadcast 10.11.51.255
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
### Apartado B
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
> La opción -y acepta todos los prompts.
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

