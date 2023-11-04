# PRÁCTICA 2 - EJEMPLOS DE CATEGORÍAS DE ATAQUE ET AL
## APARTADO A
Instale el ettercap y pruebe sus opciones básicas en línea de comando.
> `apt install ettercap-text-only`
## APARTADO B
Capture paquetería variada de su compañero de prácticas que incluya varias sesiones HTTP. Sobre esta paquetería (puede utilizar el wireshark para los siguientes subapartados).
1. Hacemos un MITM a la máquina de nuestro compañero:
> `ettercap -T -q -i ens33 -M arp:remote //10.11.49.51/ //10.11.48.1/`
2. En otra terminal, hacemos un tcpdump para guardar el tráfico capturado en un fichero `pcap``:
> `tcpdump -i ens33 -s 65535 -w compa.pcap`
3. Desde nuestra máquina local, hacemos un `scp`` para obtener el fichero:
> `scp lsi@10.11.48.50:/home/lsi/compa.pcap .`
4. Ejecutamos Wireshark y abrimos el archivo compa.pcap para comenzar a analizar.

### APARTADOS COMPLEMENTARIOS
- Filtre la captura para obtener el tráfico HTTP.
> Escribir en la barra de filtrado.

- Visualice la paquetería TCP de una determinada sesión.
> Ir a `Analyze > Follow > TCP Stream`.

- Sobre el total de la paquetería obtenga estadísticas del tráfico por protocolo como fuente de información para un análisis básico sobre el tráfico.
> Ir a `Statistics > Protocol Hierarchy`.

- Obtenga información del tráfico de las distintas "conversaciones" mantenidas.
> Ir a `Statistics > Conversations`.

- Obtenga direcciones finales del tráfico de los distintos protocolos como mecanismo para determinar qué circula por nuestras redes.
> Ir a `Statistics > Endpoints`.

## APARTADO C
Obtenga la relación de las direcciones MAC de los equipos de su segmento.
> `nmap -sP 10.11.48.0/23`

## APARTADO D
> *Apartado incompleto.*

Obtenga la relación de las direcciones IPv6 de su segmento.
> `ping6 -c2 -I ens33 ff02::1`

## APARTADO E
Obtenga el tráfico de entrada y salida legítimo de su interface de red `ens33` e investigue los servicios, conexiones y protocolos involucrados.

## APARTADO F
Mediante `arpspoofing` entre una máquina objeto (víctima) y el router del laboratorio obtenga todas las URL HTTP visitadas por la víctima.
- Utilizamos el archivo `pcap` obtenido de la máquina de nuestro compañero.

Se puede ver en *Statistics > HTTP > Requests*

## APARTADO G
> *Apartado incompleto.*

Instale metasploit. Haga un ejecutable que incluya Reverse TCP meterpreter payload para plataformas linux. Inclúyalo en un filtro ettercap y aplique toda su sabiduría en ingeniería social para que una víctima u objetivo lo ejecute.

## APARTADO H
> *Apartado incompleto.*

Haga un MITM en IPv6 y visualice la paquetería.

## APARTADO I
Pruebe alguna herramienta y técnica de detección del sniffing (preferiblemente arpon).
> `apt install arpon`
- Para vaciar la tabla arp: `ip -s -s neigh flush all`
> `nano /etc/arpon.conf`
```
#
# ArpON configuration file.
#
# See the arpon(8) man page for details.
#
#
# Static entries matching the ens33 network interface:
#
10.11.48.1	    dc:08:56:10:84:b9
10.11.49.51	    00:50:56:97:24:d0
```
- Para saber la dirección mac: `ip addr show`
> `arpon -d -i ens33 -H`
- Para cerrar los procesos de arpon usar `ps -A | grep arpon` y luego `kill <process>`.

### *Advertencias*
1. Tras instalar `arpon` es necesario desahbilitarlo para que no se inicie en boot.
2. Como medida de seguridad es recomendable usar `shutdown -r +10` para programar un reinicio mientras se trabaja con `arpon` para evitar quedarte fuera.
> Si no está desahabilitado esto es inutil.
3. Usar el comando `kill` para cerrarlo, y despues siempre maskearlo, si no desinstalarlo, cuando dejes de usarlo.

## APARTADO J
Pruebe distintas técnicas de host discovery, port scanning y OS fingerprinting sobre la máquinas del laboratorio de prácticas en IPv4.
> `nmap -A 10.11.48.0/23 > nmap_full.txt`
- Puede ser increiblemente lento, es recomendable usarlo en ips individuales.

### CONTINUACIÓN DEL APARTADO
### *Apartado incompleto.*
1. Realice alguna de las pruebas de port scanning sobre IPv6.
> `nmap -A -6 2002::a0b:3136::1`

2. ¿Coinciden los servicios prestados por un sistema con los de IPv4?
> ...

## APARTADO K
Obtenga información "en tiempo real" sobre las conexiones de su máquina, así como del ancho de banda consumido en cada una de ellas.
> `apt-get install iftop`

> `iftop -i ens33`

> `apt-get install vnstat`

> `vnstat -l -i ens33`

## APARTADO L
> *Apartado incompleto.*

Monitoremos nuestra infraestructura:
### APARTADO M
¿Cómo podría hacer un DoS de tipo direct attack contra un equipo de la red de prácticas?
¿Y mediante un DoS de tipo reflective flooding attack?
### APARTADO N
Ataque un servidor apache instalado en algunas de las máquinas del laboratorio de prácticas para tratar de provocarle una DoS. Utilice herramientas DoS que trabajen a nivel de aplicación (capa 7).
> `apt install apache2`

> `curl https://www.alvarofreire.es/ > /var/www/html/index.html`

> `slowhttptest -c 1000 -H -g -o slowhttp -i 10 -r 200 -t GET -u http://10.11.49.54 -x 24 -p 3`
1. ¿Cómo podría proteger dicho servicio ante este tipo de ataque?
> Con un firewall de aplicaciones web como modsecurity.
2. ¿Y si se produjese desde fuera de su segmento de red?
> ...
3. ¿Cómo podría tratar de saltarse dicha protección?
> ...
### APARTADO O
Instale y configure modsecurity. Vuelva a proceder con el ataque del apartado anterior. ¿Qué acontece ahora?
> `apt install libapache2-mod-security2`

> `a2enmod headers`

> `systemctl restart apache2`

> `cp /etc/modsecurity/modsecurity.conf-recommended app`

> `cat app`

> `nano /etc/modsecurity/modsecurity.conf`
```
# Enable ModSecurity, attaching it to every transaction. Use detection
# only to start with, because that minimises the chances of post-installation
# disruption.
#
SecRuleEngine On
```
- Change `SecRuleEngine` from `DetectionOnly` to `On`
> `apt install libapache2-mod-evasive`

> `a2enmod evasive`

> `nano /etc/apache2/mods-enabled/evasive.conf`
```
<IfModule mod_evasive20.c>
    #DOSHashTableSize    3097
    DOSPageCount        1
    DOSSiteCount        1
    
    DOSPageInterval     1
    DOSSiteInterval     2
    
    DOSBlockingPeriod   300

    #DOSEmailNotify      you@yourdomain.com
    #DOSSystemCommand    "su - someuser -c '/sbin/... %s ...'"
    #DOSLogDir           "/var/log/mod_evasive"
</IfModule>
```

## APARTADO P
1. Obtenga de forma pasiva el direccionamiento público IPv4 e IPv6 asignado a la Universidade da Coruña.
> `whois -h whois.ripe.net UDC`
- Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.

2. Obtenga información sobre el direccionamiento de los servidores DNS y MX de la Universidade da Coruña.
> `apt install bind-utils`

> `dig udc.es MX`
- Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.

3. ¿Puede hacer una transferencia de zona sobre los servidores DNS de la UDC?
- No, tiene la transferencia de zona deshabilitada:
> `dig axfr @zipi.udc.es udc.es.`
- Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.

4. En caso negativo, obtenga todos los nombres.dominio posibles de la UDC.
> `nmap --script hostmap-crtsh.nse udc.es`

5. ¿Qué gestor de contenidos se utiliza en www.usc.es?
> `apt-get install whatweb`

> `whatweb www.usc.es`

## APARTADO Q
> *Apartado repetido*
Trate de sacar un perfil de los principales sistemas que conviven en su red de prácticas, puertos accesibles, fingerprinting, etc.

## APARTADO R
Realice algún ataque de “password guessing” contra su servidor ssh y compruebe que el analizador de logs reporta las correspondientes alarmas.
- El archivo exacto se puede encontrar [aquí](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10k-most-common.txt).
> `scp .\10k-most-common.txt lsi@10.11.49.54:/home/lsi`

Después de añadir la contraseña correcta continuamos...
> `medusa -h 10.11.49.106 -u lsi -P 10k-most-common.txt -M ssh`
