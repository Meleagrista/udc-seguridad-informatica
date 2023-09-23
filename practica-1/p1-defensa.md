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
