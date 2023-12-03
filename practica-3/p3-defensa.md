# PRÁCTICA 3
## APARTADO 1
Tomando como base de trabajo el openVPN deberá configurar una VPN entre dos equipos virtuales del laboratorio que garanticen la confidencialidad entre sus comunicaciones.
### APARTADO 1A
Abra un shell remoto sobre SSH y analice el proceso que se realiza. Configure su fichero ssh_known_hosts para dar soporte a la clave pública del servidor
> `ssh -v lsi@10.11.49.54`

> `touch /etc/ssh/ssh_known_hosts`

> `ssh-keyscan 10.11.49.54 >> /etc/ssh/ssh_known_hosts`

> `echo "" > /home/lsi/.ssh/known_hosts`

Si no pone el mensaje de _add new fingerprint_ está bien configurado.

### APARTADO 1B
Haga una copia remota de un fichero utilizando un algoritmo de cifrado determinado. Analice el proceso que se realiza.

> `scp -c aes128-ctr archivo lsi@10.11.48.70:/home/lsi`

> [!Note]
> Para ver que cifrados estan disponibles usar `man ssh_config`.

### APARTADO 1C
Configure su cliente y servidor para permitir conexiones basadas en un esquema de autenticación de usuario de clave pública.

> [!Warning]
> Usar comandos como usuario *lsi*.

> `ssh-keygen -t rsa`

> [!Note]
> Otras opciones son `ssh-keygen -t ed25519` o `ssh-keygen -t ecdsa`.

> `ssh-copy-id -i $HOME/.ssh/id_rsa.pub lsi@10.11.48.70`

> [!Note]
> De forma manual se puede hacer de la siguiente forma.

```bash
lsi@debian:~$ cd /home/lsi/.ssh
lsi@debian:~$ scp lsi@10.11.49.54:/home/lsi/.ssh/*.pub ../keys_compañero
lsi@debian:~$ nano authorized_keys # Dejamos vacio
lsi@debian:~$ cat ../keys_compañero/id_rsa.pub >> authorized_keys
```

> [!Note]
> Si se usan otras opciones se añadiría la clave correspondiente.

```bash
lsi@debian:~$ cat ../keys_compañero/id_ecdsa.pub >> authorized_keys
```

### APARTADO 1D
Mediante túneles SSH securice algún servicio no seguro.

> `ssh -L 10080:10.11.49.54:80 lsi@10.11.49.55`

Estando en la maquina de nuestro compañero:

> `wget localhost:10080` # Devuelve mi puerto 80.

> [!Note]
> Esto redirecciona el `localhost`, si quisiera que redireccionara la ip se tiene añadir al principio.

> `ssh -L 10.11.49.55:10080:10.11.49.54:80 lsi@10.11.49.55`

> [!Warning]
> Había algunas dudas sobre que tunel está conectado a que tunel.

```bash
lsi@debian:~$ ssh -L 10080:10.11.48.70:80 lsi@10.11.48.70
# En la maquina en la que ejecuto esto, redireciono el puerto 10080 de mi localhost al 80 de mi compañero a traves de una conexión ssh.

lsi@debian:~$ wget localhost:10080 # Devuelve el 80 de la máquina de mi compañero, normalmente es no seguro, pero ahora lo mete por el tunel ssh seguro

# Esto redirecciona el localhost, si quisiera que redireccionara mi ip, se pone al prinicipio :
lsi@debian:~$ ssh -L 10.11.48.69:10080:10.11.48.70:80 lsi@10.11.48.70
```
## APARTADO 2
Tomando como base de trabajo el openVPN deberá configurar una VPN entre dos equipos virtuales del laboratorio que garanticen la confidencialidad entre sus comunicaciones.
### APARTADO 2A
Configure su Autoridad Certificadora en su equipo.

> `cd /usr/lib/ssl/misc`

> `./CA.pl -newca`

> [!Note]
> _CA certificate is in ./demoCA/cacert.pem_

### APARTADO 2B
Cree su propio certificado para ser firmado por la Autoridad Certificado. Bueno, y fírmelo.

1. Generamos certificado para nuestro propio servidor:
> `./CA.pl -newreq-nodes`

> [!Note]
> _Request is in newreq.pem, private key is in newkey.pem_

2. Firmamos el certificado:
> `./CA.pl -sign`

> [!Note]
> _Signed certificate is in newcert.pem_

### APARTADO 2C
Configure su Apache para que únicamente proporcione acceso a un determinado directorio del árbol web bajo la condición del uso de SSL. Considere que si su clave privada está cifrada en el proceso de arranque su máquina le solicitará la correspondiente frase de paso, pudiendo dejarla inalcanzable para su sesión ssh de trabajo.

> `cd /etc/apache2`

> `a2enmod ssl`

Copiamos el certificado firmado, la `CPriv` del certificado y la `CPriv` de la autoridad en `/etc/apache2/ssl/nombre`.

> `cp newkey.pem newcert.pem demoCA/cacert.pem /etc/apache2/ssl/nombre`

> `nano default-ssl.conf`

> [!Warning]
> Hay alguna diferencias entre los repositorios.

```bash
    <VirtualHost *:443>
        ServerName nombre
        DocumentRoot /var/www/html

        SSLEngine On
        SSLCertificateFile /etc/apache2/ssl/nombre/newcert.pem
        SSLCertificateKeyFile /etc/apache2/ssl/nombre/newkey.pem
        SSLCACertificateFile /etc/apache2/ssl/nombre/cacert.pem

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
```
```bash
    <IfModule mod_ssl.c>
        <VirtualHost _default_:443>
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/html
            # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
            # error, crit, alert, emerg.
            # It is also possible to configure the loglevel for particular
            # modules, e.g.
            #LogLevel info ssl:warn

            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
            
            # For most configuration files from conf-available/, which are
            # enabled or disabled at a global level, it is possible to
            # include a line for only one particular virtual host. For example the
            # following line enables the CGI configuration for this host only
            # after it has been globally disabled with "a2disconf".
            #Include conf-available/serve-cgi-bin.conf

            #   SSL Engine Switch:
            #   Enable/Disable SSL for this virtual host.
            SSLEngine on
            
            #   A self-signed (snakeoil) certificate can be created by installing
            #   the ssl-cert package. See
            #   /usr/share/doc/apache2/README.Debian.gz for more info.
            #   If both key and certificate are stored in the same file, only the
            #   SSLCertificateFile directive is needed.
            SSLCertificateFile	/etc/ssl/certs/newcert.pem
            SSLCertificateKeyFile /etc/ssl/private/newkey.pem
        </VirtualHost>
    </IfModule>
```

> [!Warning]
> Aun falta por actualizar.

En el cliente, se introduce `cacert.pem` en `/usr/local/share/ca-certificates/`

> `update-ca-certificates`

> `cat /etc/hosts`

```bash
127.0.0.1	    localhost
127.0.1.1	    debian
10.11.48.70	    nombre

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

## APARTADO 3
Tomando como base de trabajo el openVPN deberá configurar una VPN entre dos equipos virtuales del laboratorio que garanticen la confidencialidad entre sus comunicaciones.

> `apt install openvpn`

> `lsmod | grep tun`

> `modprobe tun`

> `echo tun >> /etc/modules`

> `cd /etc/openvpn`

> `openvpn --genkey --secret vpn_key.key`

> `nano tunel.conf`

```bash
local 10.11.49.54 # Ip propia
remote 10.11.48.70 # Ip compañero
dev tun1 # Interface de red asociado a mi extremo de la vpn
port 5555 # Puerto
comp-lzo # Metodo de compresión. La vpn antes de cifrar la comprime
user nobody # Servidor con permisos del usuario nobody
ping 15 # Cada 15 segundos le manda un ping al otro extremo de la vpn para ver si está ahi
ifconfig 172.160.0.1 172.160.0.2 # Servidor pone .01 de primero, el cliente el .02
secret /etc/openvpn/vpn_key.key # Path de la clave con la que se va a cifrar y descifrar toda la paqueteria.
providers legacy default
```

Le paso la `vpn_key.key` a mi compañero y hace exactamente lo mismo haciendo los cambios permitentes.

> `openvpn --config /etc/openvpn/tunel.conf`

> `ifconfig -a`

> `ping 172.160.0.2`

> [!Warning]
> Si se quiere cambiar y el comando da error hay que realizar el siguiente proceso.

> `systemctl stop openvpn`

> `modprobe -rv tun`

> `systemctl start openvpn`

> `modprobe tun`

# Apartado 6 - Firewall

_En este punto, cada máquina virtual será servidor y cliente de diversos servicios (NTP, syslog, ssh, web, etc.). Configure un “firewall stateful” de máquina adecuado a la situación actual de su máquina._


Para eso creamos tres scripts:

**setFirewallRules.sh**:

```bash
#!/bin/sh

ipCompa=10.11.49.54
ip6Compa=2002:a0b:3137::1
ipCompaVPN=172.160.0.2
ipVPN_1=10.20.32.0/21 # EDUROAM
ipVPN_2=10.30.8.0/21 # VPN FIC
servidorDNS1=10.8.12.49
servidorDNS2=10.8.12.50
servidorDNS3=10.8.12.47
debianRep1=151.101.194.132
debianRep2=151.101.130.132
ipLocalhost=127.0.0.1
ip6Localhost=::1

iptables -F # Borra reglas
iptables -X # Borra cadenas
iptables -t nat -F # Borra reglas de tabla NAT

# Se descarta todo con DROP
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP
# -------------------------
ip6tables -P INPUT DROP
ip6tables -P OUTPUT DROP
ip6tables -P FORWARD DROP

## Control de estado:
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A OUTPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
# -------------------------
ip6tables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
ip6tables -A OUTPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Loopback
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT
#---------------------------------
ip6tables -A INPUT -i lo -j ACCEPT
ip6tables -A OUTPUT -o lo -j ACCEPT

# Tunneled IPv6 - IPv4
iptables -A INPUT -s $ipCompa -p ipv6 -j ACCEPT
iptables -A OUTPUT -d $ipCompa -p ipv6 -j ACCEPT

# SSH
iptables -A INPUT -s $ipVPN_1,$ipVPN_2,$ipCompa -p TCP --dport 22 -m conntrack --ctstate NEW -j ACCEPT
iptables -A OUTPUT -d $ipCompa -p TCP --dport 22 -m conntrack --ctstate NEW -j ACCEPT
# ------------------------------------------------------------------------------------------------------
ip6tables -A INPUT -s $ip6Compa -p tcp --dport 22 -m conntrack --ctstate NEW -j ACCEPT
ip6tables -A OUTPUT -d $ip6Compa -p tcp --dport 22 -m conntrack --ctstate NEW -j ACCEPT

# DNS
iptables -A OUTPUT -d $servidorDNS1,$servidorDNS2,$servidorDNS3 -p UDP --dport 53 -m conntrack --ctstate NEW  -j ACCEPT

# Rsyslog (server)
iptables -A INPUT -s $ipCompa -p TCP --dport 514 -m conntrack --ctstate NEW -j ACCEPT

# VPN
iptables -A INPUT -s $ipCompa -p UDP --dport 5555 -m conntrack --ctstate NEW -j ACCEPT
iptables -A OUTPUT -d $ipCompa -p UDP --sport 5555 -m conntrack --ctstate NEW -j ACCEPT

# NTP (client)
iptables -A OUTPUT -d $ipCompa,$ipLocalhost -p UDP --dport 123 -m conntrack --ctstate NEW -j ACCEPT

# HTTP
iptables -A INPUT -s $ipCompa -p TCP --dport 80 -m conntrack --ctstate NEW -j ACCEPT
iptables -A OUTPUT -d $ipCompa,$debianRep1,$debianRep2 -p TCP --dport 80 -m conntrack --ctstate NEW -j ACCEPT

# HTTPS
iptables -A INPUT -s $ipCompa -p TCP --dport 443 -m conntrack --ctstate NEW -j ACCEPT
iptables -A OUTPUT -d $ipCompa -p TCP --dport 443 -m conntrack --ctstate NEW -j ACCEPT

# ICMP
iptables -A INPUT -s $ipCompa,$ipCompaVPN,$ipLocalhost -p ICMP -m conntrack --ctstate NEW -j ACCEPT
iptables -A OUTPUT -d $ipCompa,$ipCompaVPN,$ipLocalhost -p ICMP -m conntrack --ctstate NEW -j ACCEPT
# ----------------------------------------------------------------------------------------------------
ip6tables -A INPUT -s $ip6Compa,$ip6Localhost -p icmpv6 -m conntrack --ctstate NEW -j ACCEPT
ip6tables -A OUTPUT -d $ip6Compa,$ip6Localhost -p icmpv6 -m conntrack --ctstate NEW -j ACCEPT


# Rejects
iptables -A INPUT -p UDP -j REJECT --reject-with icmp-port-unreachable
iptables -A INPUT -p TCP -j REJECT --reject-with tcp-reset
iptables -A INPUT -p ICMP -j REJECT --reject-with icmp-port-unreachable
# -----------------------------------------------------------------------
ip6tables -A INPUT -p udp -j REJECT --reject-with icmp6-port-unreachable
ip6tables -A INPUT -p tcp -j REJECT --reject-with tcp-reset
ip6tables -A INPUT -p icmpv6 -j REJECT --reject-with icmp6-port-unreachable



```

**resetFirewall.sh**:

```shell
#!/bin/bash

# Reset to avoid losing connections
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -F
iptables -X
iptables -t nat -F
# --------------------------
ip6tables -P INPUT ACCEPT
ip6tables -P OUTPUT ACCEPT
ip6tables -P FORWARD ACCEPT
ip6tables -F
ip6tables -X
ip6tables -t nat -F

# Write reset log:
/bin/echo "$(date): firewall reset" >> /var/log/fw_reset.log


#CUIDADO, SI FALLA EL SCRIPT DE REINICIO NOS QUEDAMOS SIN MAQUINA
```

**testFirewall.sh**:

```shell
shutdown -r +10
./setFirewallRules.sh
echo "En 5 min el firewall se reniciara, no cierre este proceso"
sleep 300 && ./resetFirewall.sh
echo "Firewall reseteado"
```

Guardamos estos scripts en una misma carpeta y ejecutamos testFirewall.sh para probar.

# Apartado 7 - Lynis

_Ejecute la utilidad de auditoría de seguridad lynis en su sistema y trate de identificar las acciones de securización detectadas así como los consejos sobre las que se deberían contemplar._


> `apt install lynis`
> `sudo lynis audit system`


