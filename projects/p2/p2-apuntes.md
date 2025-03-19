## **Ettercap en modo texto**  
**Descripción:** Ettercap es una herramienta para realizar ataques de tipo Man-in-the-Middle (MitM). Aquí  menciona su uso en modo texto.  

- **Comando:**  
  ```bash
  ettercap -Tq -i [interfaz] -M [modo] [target1] [target2]
  ```
- `-T`: Modo texto (sin interfaz gráfica).  
- `-q`: Silencia algunos mensajes de salida.  
- `-i [interfaz]`: Especifica la interfaz de red a usar.  
- `-M [modo]`: Especifica el tipo de ataque MitM (ej., `arp:remote`).  
- **Salir:** Presionar `q` o `Ctrl + X` (se recomienda `q`). 

> [!WARNING] 
> **Nunca usar "///" como objetivo**, podría causar problemas en la red.  

## **Ettercap y Wireshark**  
**Descripción:** Combinación de Ettercap y Wireshark para analizar tráfico de red, especialmente HTTP.  

- Wireshark se usa para inspeccionar los paquetes capturados por Ettercap.  
- **Repoison** (reinyección de paquetes) puede ser peligroso.  

## **Restricción sobre localhost**  
**Descripción:** No se recomienda trabajar sobre `localhost` en algunos ataques o análisis de tráfico, ya e esto puede limitar la visibilidad de paquetes en la red.  

## **ARP Spoofing entre compañeros**  
**Descripción:** Técnica para interceptar tráfico de red entre dos equipos.  

Pasos:  
1. El compañero abre una web.  
2. Con ARP Spoofing, visualizamos en nuestra máquina la URL en tiempo real.  

### **Herramientas útiles:**  
- `xdg-open [URL]` → Abre una URL en el navegador predeterminado.  
- `w3m [URL]` → Navegador web en terminal.  

## **Metasploit y msfvenom**  
**Descripción:** Uso de Metasploit y msfvenom para generar cargas maliciosas y obtener acceso a sistemas motos.  

Pasos:  
1. El atacante genera una trampa con **msfvenom**.  
2. Se utiliza un **meterpreter** para interactuar con la máquina comprometida.  
3. Se lanza una **shell inversa** para controlar el equipo objetivo.  
4. Se ejecutan comandos de meterpreter para verificar acceso (ver IP).  
5. Se puede engañar al atacante mostrando una advertencia en la URL interceptada.  

### **Navegador en terminal:**  
- `lynx` → Otra opción para navegar desde la terminal.  

## **Ataques con IPv6**  
**Descripción:** Uso de Ettercap con IPv6 para ataques MitM.  

**Comando a usar:**  
```bash
ettercap -6 -T -q -i ens33 -M arp:remote //2002:a0b:3136::1/ //::10.11.48.1/
```  
> [!NOTE] 
> No siempre se podrá predecir si la conexión IPv6 funcionará.  

## **Protección con Arpón**  
**Descripción:** Arpón es una herramienta que protege contra ataques ARP Spoofing, ejecutándose como rvicio o demonio.  

- Puede bloquear la conexión en la máquina, así que **usar con precaución**.  

## **Trilogía de la red**  
**Descripción:** Tres fases en un análisis de red:  

1. **Host Discovery** (Descubrimiento de dispositivos en la red).  
2. **Port Scanning** (Escaneo de puertos abiertos en un dispositivo).  
3. **OS Fingerprinting** (Identificación del sistema operativo).  

### **Comandos:**  
- **Descubrimiento de hosts:**  
  ```bash
  nmap -sP 10.11.48.0/23
  ```
- **Escaneo de puertos:**  
  ```bash
  nmap -sV 10.11.49.54
  ```
- **Fingerprinting de SO:**  
  ```bash
  nmap 10.11.48.0/23
  ```  

🔍 **Herramienta extra:** `netstat -putona` → Muestra conexiones activas.  

## **Análisis de tráfico en ataques**  
**Descripción:** Herramientas para monitorear tráfico en ataques.  

- `iftop` → Monitorea tráfico en tiempo real.  
- `toptrack` → Seguimiento de conexiones.  
- `netlog` → Registro de actividad en la red.  

## **Teoría de Packit**  
**Descripción:** Packit es una herramienta teórica y práctica para manipulación de paquetes de red.  

## **Ataque DDoS entre víctimas y atacantes**  
**Descripción:** Un atacante ejecuta un **Denial of Service (DoS)** contra un servidor Apache.  

### **Herramientas utilizadas:**  
- **Apache Benchmarking (`ab`)** → Prueba de carga.  
- **Slowhttptest** → Ataque de HTTP lento.  
- **Slowloris**  
  - Se puede ejecutar con código en **Python** o **Perl**.  

> [!IMPORTANT] 
>  Tener los scripts listos para estos ataques.  

## **Defensa contra ataques**  
**Descripción:** Módulos y herramientas para proteger servidores web.  

1. **ModSecurity** → Firewall de aplicaciones web (WAF). **Difícil de configurar.**  
   - Se debe configurar en sus **4 modos posibles**.  
2. **ModEvasive** → Protección contra ataques DoS/DDoS (`-d25` para detección).  
3. **Un tercer módulo de seguridad** (su nombre no es explícito, pero está relacionado con la protección).  

## **Servicios de red**  
**Descripción:** Aspectos clave de servicios de DNS, correo y contenido.  

- **Transferencia de zona DNS.**  
- **Gestores de contenido (UDC).**  
- **Correo (Estafetas)** → Se busca listar en pantalla los servidores de correo disponibles.  

## **Ataques de fuerza bruta con Medusa**  
**Descripción:** Uso de **Medusa** para adivinar contraseñas mediante diccionarios.  

## **OSSEC (Seguridad y monitoreo de logs)**  
**Descripción:** Herramienta para leer logs y defenderse de ataques.  

🔍 **Comando para verificar uso de disco:**  
```bash
df -h
```  

## **Monitorización con Grafana y Prometheus**  
**Descripción:** Uso de Grafana y Prometheus para visualizar métricas en sistemas Debian 11 o superior.  

> [!NOTE] 
> `apt install` no funciona para Prometheus en versiones recientes.  



### **Conclusión**  
Estas notas cubren herramientas esenciales para pruebas de seguridad ofensiva y defensiva, incluyendo ataques de red, escaneo de vulnerabilidades y técnicas de protección.  
