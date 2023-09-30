# Semana 0
Pendiente de actualizar...

# Semana 1
## Target
Un `target` en un sistema Linux es un estado o nivel de ejecución que define qué servicios y unidades se inician durante el proceso de arranque del sistema. Representa un conjunto específico de funciones y servicios necesarios para un propósito determinado, como el inicio en modo multiusuario o de emergencia.

![Esquema de targets](https://soloconlinux.org.es/content/images/2022/12/niveles-ejecucion-002.png)

Para más información se puede visitar la [pagina siguiente](https://www.baeldung.com/linux/systemd-target-multi-user#:~:text=Targets%20are%20groupings%20of%20resources,poweroff) y para mas información sobre los comandos la [pagina siguiente](https://documentation.suse.com/smart/systems-management/html/reference-managing-systemd-targets-systemctl/index.html#:~:text=systemd%20targets%20are%20different%20states,network%20and%20a%20graphical%20environment.).

## Services
*`accounts-daemon`*: The `AccountService` project provides a set of D-Bus interfaces for querying and manipulating user account information and an implementation of these interfaces, based on the `useradd`, `usermod` and `userdel` commands, `accounts-daemon.service` is a potential security risk. It is part of `AccountsService`, which allows programs to get and manipulate user account information. I can't think of a good reason to allow this kind of behind-my-back operations, so I mask it.
As a general rule, if something is DBus based (and accounts-daemon is), it's safe to turn off automatic startup of it, as it will just get started by DBus whenever something actually needs it.

For this particular case, the `accounts-daemon` is the executable component of the `AccountsService`, which handles non-priveleged listing of account information (because apparently using `libc` routines for this like you should is too hard for GNOME developers to do). It may or may not be used by the display manager (login screen), the screensaver, and the account management tools in your desktop environment. As mentioned above, DBus starts requested services on-demand, so this is something that you can definitely disable automatic startup of, but it probably will be started by other components of your system (especially if you're using GNOME or KDE for your desktop).

*`apparmor`*: `AppArmor` is a Mandatory Access Control framework. When enabled, AppArmor confines programs according to a set of rules that specify what files a given program can access. This proactive approach helps protect the system against both known and unknown vulnerabilities.

AppArmor is a great tool to secure and protect your Ubuntu and Debian systems. It could, however, be a little bit restrictive and cause unnecessary problems in some situations.

*`avahi-daemon`*: Permite detectar automáticamente los recursos de una red local y conectarse a ella, para ello abre los puertos UDP 32768 y 5353, servicio de autodiscovery que no necesitamos en nuestra máquina. Se ocupa de:
1. Asignar automáticamente una dirección IP incluso sin presencia de un servidor DHCP.
2. Hacer la función de DNS (cada nodo es accesible como: nombre-nodo.local).
3. Hacer una lista de los servicios y acceder a ellos fácilmente (las máquinas de la red local son informadas de la llegada o salida de un servicio).

- `systemctl disable avahi-daemon.service`
- `systemctl mask avahi-daemon.service`

*`NetworkManager`*: NetworkManager attempts to keep an active network connection available at all times.

The point of `NetworkManager` is to make networking configuration and setup as painless and automatic as possible. If using DHCP, `NetworkManager` is intended to replace default routes, obtain IP addresses from a DHCP server and change nameservers whenever it sees fit. In effect, the goal of NetworkManager is to make networking Just Work.

> No necesario porque usamos networking.service, este se encarga de gestionar automáticamente si no lo tenemos en networking.

NetworkManager still does not properly support bridging, so if you are going to run virtual machines, turn it off.
The only time I have found NetworkManager useful was when I was using wireless.

Avoid NM like the plague. It's another effort from the developers to Fisher-Price the UI for the lowest common denominator (in otherwords, the devs think you're too dumb to manage networking via a config file).

*`cups`*: The Debian printing system has undergone many significant changes over the past few years, with much of the printer management taking advantage of the advances in modern printer technology and the proliferation of IPP printers.
> No se añade el `.service`, el comando es `systemctl disable cups && systemctl mask cups`.

*`cups-browsed`*: A daemon for browsing the Bonjour broadcasts of shared, remote CUPS printers

*`ModemManager`*: ModemManager es un demonio activado por DBus que controla dispositivos y conexiones de banda ancha móvil (2G/3G/4G). Tanto si se trata de dispositivos integrados, llaves USB, teléfonos Bluetooth o dispositivos profesionales RS232/USB con fuentes de alimentación externas, ModemManager es capaz de preparar y configurar los módems y establecer conexiones con ellos.

*`bluetooth`*: la máquina no usa Bluetooth, pero tiene ciertas dependencies.
> El comando es `systemctl disable bluetooth && systemctl mask bluetooth`.

*`switcheroo-control`*: For systems that have both an integrated GPU and a dedicated GPU, this package by default will force the integrated GPU to be used to save power.
> Servicio para comprobar disponibilidad de dual-GPU (tarjeta gráfica dual).

# Semana 2
Aun pendiente de revisar, pero [esta pagina](http://trajano.us.es/~fjfj/shell/shellscript.htm) tiene buena pinta para prender sobre porgramación shell.
