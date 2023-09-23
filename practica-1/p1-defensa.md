# PRÁCTICA 1 - CONFIGURACIÓN BÁSICA
## PARTE 1
### Apartado A
Configure su máquina virtual de laboratorio con los datos proporcionados por el profesor. Analice los ficheros básicos de configuración (interfaces, hosts, resolv.conf, nsswitch.conf, sources.list, etc.)
1. Modificar el fichero /etc/network/interfaces:
> Mis IPs son 10.11.49.54 y 10.11.51.54. La configuración de este fichero dependerá de tus IPs.
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).
#source /etc/network/interfaces.d/*
# The loopback network interface
auto lo ens33 ens34
iface lo inet loopback

iface ens33 inet static
	address 10.11.48.50
	netmask 255.255.254.0
	broadcast 10.11.49.255
	network 10.11.48.0
	gateway 10.11.48.1

iface ens34 inet static
	address 10.11.50.50
	netmask 255.255.254.0
	broadcast 10.11.51.255
	network 10.11.50.0
```
