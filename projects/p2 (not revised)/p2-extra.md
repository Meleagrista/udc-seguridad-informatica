### A
``ettercap -text-only
``ettercap [options] [target1] [target2]

Nunca usar ///

q para salir o ctrl +x (mejor usar q)

### B

ettercap + whiteshark
HTTP

repoison → danger

### E

No trabajar sobre localhost

### F

Arp spoofing compañero

- comparñero abre web
- yo tengo que ver en mi pc su url de la web (tiempo real

xdg-open
w3m → navegador para terminal

### G

metasploit + msfvenom → 
- nuestro atacante va generar una trampa
- Con un meterpreter
- Usaremos una shell inversa, tendremos control de esa maquina
- Usad los comandos de meterpreter para saber si estamos en la maquina del compa (nos da la ip)
- Vamos a engañar al atancante poniendo un waring en la url que nos roben


Navegaador lynx
### H

IPV6
No sabremos si va 

>[!Info] Comando a usar
>ettercap -6 -T -q -i ens33 -M arp:remote //2002:a0b:3136::1/ //::10.11.48.1/


### I 

Arpon
- Como servicio o como demonio
- Nos va a tirar la maquina xd, asi que solo una cosa a la vez

### J

Trilogia de la red
- Host discobery
- Port scanning, solo con el compañero
- os firgetrepintin, solo con el compañero

netstart -putona

>[!Info] Host discovery
>  nmap -sP 10.11.48.0/23 

>[!Info] Port scanning 
> nmap -sV 10.11.49.54

>[!IMPORTANT] OS fingerprinting
> nmap  10.11.48.0/23
### K

iftop
toptrack
netlog

Mirar el trafico entrnete en un ataque

### M 

packit, aprtado teorico

### N

Los dos vitimacas y atanquentes

vitimaca hace server apache
el atanque hace DOS
- Apache benchmarking
- Slowhttptest
- slowloris
	- codigo python
	- codigo perl
 IMPORTANTE LOS CODIGOS

### O

Defenderse 

- MoDsecurity (jodido de instalar y configurar)
	 -  Configurar las 4 maneras
- Modevasive -d25
- Un tercer modulo de seguridad (no te va a poner que es de seguridad, pero esta relaccionado)

### P

Servicios DNS / Transferencia de zona
Gestor de contendio → UDC
Servicios de correo /estafeta → aparezca en pantalla las estafetas de correo usamos

### R

passeord guesslog → con medusa

Medusa
- fichero users (solo lsi)
- Un fichero de password para que pruebe, con muchas contras y la ultima valida
	- Ej 10 contras, 9 no, y 1 si


### S

OSSEC

lee los logs, los defiende (nos proteje del atcante anterior)


Imaginate que hacemos que salte al quinto intento de guess (Baneo de ips, podemos poner tiempo, hayq que saber usar -ose (o -oso no se))



> [!Info]
> df -h (porccentaje de disco usado)


### T

Grafana 10.2
Prometeus ultima (247) debian 11 o superior (no va el apt install)
dashborard 1860 → User : admin Pass : admin
USAR VPN

- Grafana puerto 3000
- Node_exporter puerto 9010
- Prometeus puerto 9000

Acceso desde VPN → http://10.11.49.55//9000 o 3000
