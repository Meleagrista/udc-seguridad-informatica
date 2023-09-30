# Información sobre los target
Un `target` en un sistema Linux es un estado o nivel de ejecución que define qué servicios y unidades se inician durante el proceso de arranque del sistema. Representa un conjunto específico de funciones y servicios necesarios para un propósito determinado, como el inicio en modo multiusuario o de emergencia.

![Esquema de targets](https://soloconlinux.org.es/content/images/2022/12/niveles-ejecucion-002.png)

Para más información se puede visitar la [pagina siguiente](https://www.baeldung.com/linux/systemd-target-multi-user#:~:text=Targets%20are%20groupings%20of%20resources,poweroff) y para mas información sobre los comandos la [pagina siguiente](https://documentation.suse.com/smart/systems-management/html/reference-managing-systemd-targets-systemctl/index.html#:~:text=systemd%20targets%20are%20different%20states,network%20and%20a%20graphical%20environment.).

### Liberar espacio
Como estamos usando `multi-user.target`, ya no es tan necesario tener una interfaz, para liberar mas espacio y acelarar el tiempo de boot podemos eliminar el DE (Desktop Enviorement), en este caso es GNOME.
1. `sudo apt-get autoremove gdm3`
2. `sudo apt-get autoremove --purge gnome*`
3. `reboot`

En mi caso ya tenía problemas previos con un programa: `tracker` y sus hijos `tracker-extract` y `tracker-miner-fs`. Entonces, además de causar que mi tiempo de boot fuese de mas de un minuto como se ve en [este apartado](https://github.com/Meleagrista/legislacion-seguridad-informatica/blob/main/practica-1/p1-defensa.md#apartado-d). También causó problemas al intentar purgar los archivos relacionados con GNOME.
- `sudo apt remove tracker tracker-extract tracker-miner-fs`

Tras eliminarlo pude seguir desintalando GNOME y como efecto secundaio corrigio los errores que causaban la inflacción de tiempo de boot.

> A DE is 'just' a set of separated programs to do specific tasks. Building blocks as it were. The window manager could be considered the floor of a house and the extra tools like file manager, panel, and notifications are like the appliances in your home. You could live in a single empty room, if you wanted. (งツ)ว

> You just have to manually do things that you are used to being automatic, such as... mounting of USB drives you insert, changing sound inputs when you plug in your heaphones... It's not hard to just use a WM, but a DE is sort of a collection of extras that everyone was adding to their wm anyway.

# Información sobre los servicios
| `sytemctl mask` | `systemctl disable` |
| -------- | ------- |
| Adding a mask to a service creates a _symlink_ from `/etc/systemd/system` to `/dev/null`. | Disabling a service removes the _symlink_ from `/lib/systemd/system`. |
| Masking a service makes it permanently unusable unless we unmask it. If we boot with a unit masked, it will not run even to satisfy dependencies. | A disabled service doesn’t automatically start at boot time. But, we can start it manually. Also, other services that need a disabled service can manually enable it. |

## `accounts-daemon` - Pending...
The `AccountService` project provides a set of D-Bus interfaces for querying and manipulating user account information and an implementation of these interfaces, based on the `useradd`, `usermod` and `userdel` commands, `accounts-daemon.service` is a potential security risk. It is part of `AccountsService`, which allows programs to get and manipulate user account information. I can't think of a good reason to allow this kind of behind-my-back operations, so I mask it.
As a general rule, if something is DBus based (and accounts-daemon is), it's safe to turn off automatic startup of it, as it will just get started by DBus whenever something actually needs it.
```
accounts-daemon.service
○ └─graphical.target
```
For this particular case, the `accounts-daemon` is the executable component of the `AccountsService`, which handles non-priveleged listing of account information (because apparently using `libc` routines for this like you should is too hard for GNOME developers to do). It may or may not be used by the display manager (login screen), the screensaver, and the account management tools in your desktop environment. As mentioned above, DBus starts requested services on-demand, so this is something that you can definitely disable automatic startup of, but it probably will be started by other components of your system (especially if you're using GNOME or KDE for your desktop).

## `apparmor` - Pending...
`AppArmor` is a Mandatory Access Control framework. When enabled, AppArmor confines programs according to a set of rules that specify what files a given program can access. This proactive approach helps protect the system against both known and unknown vulnerabilities.

AppArmor is a great tool to secure and protect your Ubuntu and Debian systems. It could, however, be a little bit restrictive and cause unnecessary problems in some situations.

## `avahi-daemon` - mask
Permite detectar automáticamente los recursos de una red local y conectarse a ella, para ello abre los puertos UDP 32768 y 5353, servicio de autodiscovery que no necesitamos en nuestra máquina. Se ocupa de:
1. Asignar automáticamente una dirección IP incluso sin presencia de un servidor DHCP.
2. Hacer la función de DNS (cada nodo es accesible como: nombre-nodo.local).
3. Hacer una lista de los servicios y acceder a ellos fácilmente (las máquinas de la red local son informadas de la llegada o salida de un servicio).
```
avahi-daemon.service
● ├─cups-browsed.service
● │ └─multi-user.target
○ │   └─graphical.target
● └─multi-user.target
○   └─graphical.target
```
## `NetworkManager` - disable
NetworkManager attempts to keep an active network connection available at all times.

> No necesario porque usamos networking.service, este se encarga de gestionar automáticamente si no lo tenemos en networking.

The point of `NetworkManager` is to make networking configuration and setup as painless and automatic as possible. If using DHCP, `NetworkManager` is intended to replace default routes, obtain IP addresses from a DHCP server and change nameservers whenever it sees fit. In effect, the goal of NetworkManager is to make networking Just Work.

> `NetworkManager` still does not properly support bridging, so if you are going to run virtual machines, turn it off.
The only time I have found `NetworkManager` useful was when I was using wireless.

> Avoid `NetworkManager` like the plague. It's another effort from the developers to Fisher-Price the UI for the lowest common denominator (in otherwords, the devs think you're too dumb to manage networking via a config file).

## `cups` - disable
The Debian printing system has undergone many significant changes over the past few years, with much of the printer management taking advantage of the advances in modern printer technology and the proliferation of IPP printers.
> No se añade el `.service`, el comando es `systemctl disable cups && systemctl mask cups`.
```
cups.service
● ├─cups-browsed.service
● │ └─multi-user.target
○ │   └─graphical.target
● └─multi-user.target
○   └─graphical.target
```

## `cups-browsed` - disable
A daemon for browsing the Bonjour broadcasts of shared, remote CUPS printers.

## `ModemManager` - disable
ModemManager es un demonio activado por DBus que controla dispositivos y conexiones de banda ancha móvil (2G/3G/4G). Tanto si se trata de dispositivos integrados, llaves USB, teléfonos Bluetooth o dispositivos profesionales RS232/USB con fuentes de alimentación externas, ModemManager es capaz de preparar y configurar los módems y establecer conexiones con ellos.
```
ModemManager.service
● └─multi-user.target
○   └─graphical.target
```

## `bluetooth` - disable
La máquina no usa Bluetooth, pero tiene ciertas dependencies.

## `switcheroo-control` - Pending...
For systems that have both an integrated GPU and a dedicated GPU, this package by default will force the integrated GPU to be used to save power.
> Servicio para comprobar disponibilidad de dual-GPU (tarjeta gráfica dual).
```
switcheroo-control.service
○ └─graphical.target
```

## `configure-printer@` - mask
`system-config-printer` is an administration tool that functions in a similar way to the CUPS web interface for the configuration of printers and print queues, but it is a native application rather than a web page.

## `display-manager` - mask
A display manager is a graphical login manager which starts a session on a server from the same or another computer. A display manager presents the user with a login screen. A session starts when a user successfully enters a valid combination of username and password.

## `nm-priv-helper` - mask
File in a list of packages for `network-manager`: `/usr/lib/NetworkManager/nm-priv-helper`

## `saned@.service` - mask
`saned` is the SANE (Scanner Access Now Easy) daemon that allows remote clients to access image acquisition devices available on the local host.

## `usb_modeswitch` - mask
Several new USB devices have their proprietary Windows drivers onboard, most of them WWAN and WLAN dongles. When plugged in for the first time, they act like a flash storage and start installing the Windows driver from there. If the driver is installed, it makes the storage device disappear and a new device, mainly composite (e.g. with modem ports), shows up.
On Linux, in most cases the drivers are available as kernel modules, such as "usbserial" or "option". However, the device initially binds to "usb-storage" by default. usb_modeswitch can then send a provided bulk message (most likely a mass storage command) to the device; this message has to be determined by analyzing the actions of the Windows driver.

In some cases, USB control commands are used for switching. These cases are handled by custom functions, and no bulk message needs to be provided.

Usually, the program is distributed with a set of configurations for many known devices, which allows a fully automatic handling of a device upon insertion, made possible by combining usb_modeswitch with the wrapper script usb_modeswitch_dispatcher which is launched by the udev daemon. This requires a Linux-flavoured system though.

Note that `usb_modeswitch` itself has no specific Linux dependencies.

## `usbmuxd` - mask
The USB multiplexor daemon, is in charge of coordinating access to iPhone and iPod Touch services over USB. Synchronization and management applications for the iPhone and iPod Touch need this daemon to communicate with such devices concurrently.

This package includes udev rules to start the daemon when a supported device is plugged in, and stop it when all devices are removed.

La máquina no usa Bluetooth, pero tiene ciertas dependencies.

# Script
Aun pendiente de revisar, pero [esta pagina](http://trajano.us.es/~fjfj/shell/shellscript.htm) tiene buena pinta para prender sobre porgramación shell.
