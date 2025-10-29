# Arsenal del Analista: Herramientas de Identificación de Activos y Mapeo de Superficie

> **Introducción**
Vamos a explicar algunas herramientas de identificación de activos y mapeo de superficie. La mecánica será primero otorgar un resumen teórico respecto de las bondades y características de cada una para luego ir a la parte práctica (instalación y ejemplos de ejecución), otorgando finalmente un mapa conceptual.

## Sn1per

**Descripción**
Es un framework automatizado de reconnaissance y escaneo de vulnerabilidades, que integra múltiples herramientas (como Nmap, Amass, Metasploit, Nikto, etc.) en un solo flujo. No es solo un escáner; orquesta un ataque completo: recopila OSINT (información pública), escanea puertos, detecta servicios, busca vulnerabilidades web (ej. SQLi, XSS) y genera reportes HTML detallados.

**Qué hace en detalle**
Ejecuta fases secuenciales: reconnaissance (dominios/subdominios), escaneo de puertos, brute-force, explotación básica y post-explotación. Por ejemplo, contra un Windows 10, detecta SMB/RDP abiertos y chequea shares expuestos; en Debian, identifica SSH/HTTP y busca misconfigs. Para web apps, integra escaneos de directorios y vulns comunes.

**Fortalezas**
Automatización total (ahorra tiempo en pentests grandes), reportes visuales profesionales. Velocidad media-alta en redes locales.

**Limitaciones**
Muy ruidoso (genera mucho tráfico, detectable por IDS/firewalls); consume recursos (CPU/RAM); no es sigiloso para entornos stealth.

## Amass

**Descripción**
Herramienta especializada en enumeración de activos y subdominios mediante OSINT (Open Source Intelligence). Usa fuentes como DNS, certificados SSL, APIs públicas (Shodan, Censys) y brute-force para mapear la superficie de ataque de un dominio o IP. En el manual (p.35), se menciona para identificación inicial de activos expuestos.

**Qué hace en detalle**
Realiza enumeración pasiva (sin tocar el target, solo consultas públicas) o activa (brute-force DNS). Por ej., contra un dominio web, encuentra subdominios ocultos; para IPs (como tus VMs), hace reverse lookups para ASNs y hosts relacionados. En Windows, útil para dominios AD; en Linux/Debian, para servidores web expuestos.

**Fortalezas**
Sigiloso en modo pasivo (no detectable); excelente para web apps (encuentra endpoints ocultos); outputs estructurados (JSON/CSV) para integración con otras tools.

**Limitaciones**
No escanea puertos o servicios directamente (necesita complementos como Nmap); lento en brute-force grandes; depende de fuentes externas (puede fallar si APIs cambian).

## Nmap

**Descripción**
El escáner de puertos y servicios más versátil y estándar en ciberseguridad. Descubre hosts vivos, puertos abiertos, versiones de servicios, OS fingerprinting y vulns básicas via scripts NSE (Nmap Scripting Engine). En el manual (p.36), se describe como esencial para mapeo detallado.

**Qué hace en detalle**
Envía paquetes para probar respuestas (SYN/ACK, etc.). Con -sV detecta versiones (ej. Apache en Debian); -O identifica OS (Windows vs Linux); -A agrega scripts para vulns (ej. heartbleed). Contra Windows, detecta RDP/SMB y firewalls; en Debian, SSH/FTP; para web apps, scripts como http-enum buscan directorios.

**Fortalezas**
Altamente personalizable (miles de scripts); preciso en detección de OS/servicios; soporta IPv6 y decoys para sigilo.

**Limitaciones**
Lento en rangos grandes (sin optimizaciones); puede ser bloqueado por firewalls; no tan rápido como Masscan.

## Masscan

**Descripción**
Escáner de puertos ultra-rápido, diseñado para escanear Internet entero (inspirado en Nmap pero optimizado). Enfocado en detección masiva de puertos abiertos, sin detalles profundos. En el manual (p.37), se destaca por velocidad en superficies grandes.

**Qué hace en detalle**
Usa transmisión asíncrona para enviar millones de paquetes/seg. Escanea rangos IP/puertos; outputs simples (lista de abiertos). Contra Windows, rápido para puertos comunes (80/445); en Debian, ideal para redes grandes; para web apps, detecta 80/443 rápidamente.

**Fortalezas**
Velocidad extrema (10x Nmap en rangos grandes); bajo overhead; soporta banners básicos.

**Limitaciones**
No detecta versiones/OS por default (necesita flags o integración); menos features (no scripts); outputs crudos.

## Rustscan

**Descripción**
Wrapper moderno de Nmap escrito en Rust, enfocado en velocidad y scripting. Escanea puertos rápidamente y pasa results a Nmap para análisis detallado. En el manual (p.37), se menciona para escaneos eficientes.

**Qué hace en detalle**
Usa algoritmos paralelos para puertos (más rápido que Nmap base); integra Nmap automáticamente. Ej. detecta puertos en segs, luego corre Nmap -sV. Contra Windows/Debian, similar a Nmap pero más rápido; para web apps, bueno para puertos HTTP iniciales.

**Fortalezas**
Ultra-rápido (escanea 65k puertos en ~3s); fácil scripting (outputs pipeables); moderno y seguro (Rust evita bugs).

**Limitaciones**
Dependiente de Nmap (no standalone para todo); menos maduro que Nmap; puede fallar en entornos complejos.

# Instalación Inicial

Vamos a instalar **Ubuntu 24.04* para luego instalar las herramientas mencionadas previamente. Luego de instalar Ubuntu por defecto, ejecutamos el comando “ifconfig” para ver nuestra ip pero no funciona. Por ello, instalamos el paquete de “net-tools” para tener éste y otros comandos de red.

<img width="567" height="187" alt="1" src="https://github.com/user-attachments/assets/1a34470f-f3fb-44ee-a0f5-61d3bceab310" />

Obtenemos la IP para conectarnos por SSH.

<img width="567" height="154" alt="2" src="https://github.com/user-attachments/assets/1be88e12-25a7-4530-bcba-cdcb00be7037" />

Al querer conectarnos no funciona. 

<img width="435" height="271" alt="3" src="https://github.com/user-attachments/assets/a37a48ea-57f8-46c1-946f-8db88b500851" />

Levantamos el servicio del servidor de ssh pero vemos que no está instalado, así que procedemos a instalarlo.

<img width="567" height="118" alt="4" src="https://github.com/user-attachments/assets/2ed3c9de-e400-440f-8162-b531e14bad3b" />

Luego de eso, probamos de nuevo desde Putty y ya nos conectamos. Esto es bastante importante porque a la hora de copiar y pegar comandos se hace tedioso hacerlo contra vmware o cualquier otro orquestador virtual.

<img width="567" height="371" alt="5" src="https://github.com/user-attachments/assets/8736c53a-3b8f-42ef-b5d9-5b8ed13e91be" />

Antes de proceder a instalar las herramientas de visualización de activos, ejecutaremos otro comando para actualizar repositorios, dependencias, etc:

•	sudo apt update && sudo apt upgrade -y

Luego, bajaremos git, curl, Python para utilizarlo en breve:

•	sudo apt install git curl python3-pip -y 

INSTALACION DE HERRAMIENTAS

SN1PER
https://github.com/1N3/Sn1per
Tal como dice la url:

<img width="567" height="98" alt="6" src="https://github.com/user-attachments/assets/ce94b718-ed50-4358-8e18-80ef76ed8007" />

Al hacerlo no funciona porque el script final debe correrse con root:

<img width="567" height="231" alt="7" src="https://github.com/user-attachments/assets/52ce00fd-e260-4a58-9c5e-b2731d4f41bc" />

Al hacerlo con sudo ahora sí:

<img width="567" height="136" alt="8" src="https://github.com/user-attachments/assets/506f9e4c-281b-48cb-a34d-a04f0911bc94" />
(tarda bastante porque descarga varias dependencias)

Terminado:

<img width="567" height="500" alt="9" src="https://github.com/user-attachments/assets/10688367-dcd2-47c6-9297-03a9d56d4287" />

Para las próximas instalaciones las haremos con root directamente con “sudo su –“
Para la siguiente ejecución y demás herramientas utilizaremos los siguientes “targets” de destino:

•	Windows 10: IP 192.168.94.130
•	Ubuntu 12: IP 192.168.94.136

Ejecución de Sn1per en ambiente de laboratorio:

-	sniper -t 192.168.94.130 -m stealth
-	sniper -t 192.168.94.136 -m normal -w miworkspace
  
Flags:

-	-t: Especifica el target (IP o dominio) para escanear; inicia el reconnaissance completo en esa dirección.
-	-m stealth: Selecciona el modo de escaneo sigiloso (stealth), que reduce ruido (menos paquetes, timings lentos) para evitar detección por firewalls (útil en Windows con Defender activo); alternativas: "normal" (estándar) o "aggressive" (rápido pero ruidoso).
-	-w: Guarda loot en /usr/share/sniper/loot/workspace/miworkspace. Esto es útil si queremos guardar diferentes tipos de escaneos y tener un historial, todo ordenado.

Capturas de pantalla y algunas aclaraciones:
Windows 10:

<img width="567" height="321" alt="10" src="https://github.com/user-attachments/assets/06b749e3-eea8-44a5-a717-05159bdb8bad" />
<img width="567" height="368" alt="11" src="https://github.com/user-attachments/assets/46b19334-2e93-4732-a453-6b0e1c1a392a" />

Acá vemos el escaneo en pantalla, pero el mismo al ser silencioso no hace un escaneo en profundidad. También lo que sale, lo guarda en la siguiente carpeta:

<img width="567" height="197" alt="12" src="https://github.com/user-attachments/assets/758b5acc-572f-4983-b853-0cbc182c0940" />

Pongo debajo un ejemplo con el modo “normal”; queda luego en cada uno lanzar algo más agresivo dependiendo del caso y del ambiente que estemos analizando.

<img width="567" height="180" alt="13" src="https://github.com/user-attachments/assets/50d1ef08-ebf4-4874-a527-24eda364f3e8" />
<img width="567" height="192" alt="14" src="https://github.com/user-attachments/assets/7e8af733-89fa-4b2b-81cd-b0a3d7f3b7a7" />
(continua la lista)
<img width="567" height="339" alt="15" src="https://github.com/user-attachments/assets/8b764527-952c-4a6b-b038-eeb2ee1c3d76" />

Debian 12

<img width="567" height="397" alt="16" src="https://github.com/user-attachments/assets/6fc0c044-8302-4a33-a4c7-584349542bf0" />
<img width="567" height="138" alt="17" src="https://github.com/user-attachments/assets/f30ff31f-8f7b-47ce-a271-a06d84d1d8dc" />
<img width="567" height="337" alt="18" src="https://github.com/user-attachments/assets/f96f922b-eb2a-46a6-8cb1-d3a5f5bb20f1" />

Vemos que nos generó el reporte en “miworkspace” tal como le indicamos con el flag “-w”.

<img width="567" height="183" alt="19" src="https://github.com/user-attachments/assets/982799db-6eee-49ec-b7d8-0c76fe76495f" />


AMASS

Hay varias formas de instalarlo pero la recomendada y más fácil sería con Snap y esto instalaría las dependencias:

•	snap install amass

<img width="567" height="45" alt="20" src="https://github.com/user-attachments/assets/b7323dc1-6560-4723-b74d-474228f05205" />

Para mayor información: https://owasp-amass.github.io/docs/

Ejecución de AMASS en ambiente de laboratorio:

-	amass intel -addr 192.168.94.130 
-	amass intel -addr 192.168.94.136 -o output.txt

Flags:

-	-intel: Modo para recopilar inteligencia básica (ASNs, nombres de host) via OSINT pasivo.
-	-addr: Especifica la IP como dirección de inicio para reverse lookups (busca nombres/DNS asociados).
-	-o: Output a archivo TXT (estructurado para parseo).

Capturas de pantalla y algunas explicaciones:

Aquí viene una parte “jugosa” de las pruebas. Corrí AMASS y se quedaba colgado, durante 1 hora. Al chequear la versión que instalé antes con SNAP vi que era antigua y no habían más actualizaciones. Así que procedo ahora a desinstalarlo e instalarlo con “Go”:

<img width="554" height="98" alt="21" src="https://github.com/user-attachments/assets/87d4c6dd-d9a6-49a9-9b7f-385405bc6bf4" />

Instalarlo:

<img width="567" height="212" alt="22" src="https://github.com/user-attachments/assets/3b6d2b95-4450-4507-8f16-20b73ec4be42" />

Aclaración: si al correr el comando nos da error, ejecutamos “go clean -modcache” y luego volvemos a correr el comando anterior.

Al terminar de instalarlo ponemos el PATH y vemos la versión:

<img width="567" height="47" alt="23" src="https://github.com/user-attachments/assets/f647019b-501c-4ced-a75a-c9851d6803e2" />

Mostramos alguna ejecución:

<img width="567" height="64" alt="24" src="https://github.com/user-attachments/assets/b5c49894-0365-4251-b4a6-d0b9c40268f0" />

NMAP

Instalamos con:
•	apt install nmap -y

<img width="567" height="120" alt="25" src="https://github.com/user-attachments/assets/64179f82-c475-4400-a495-3cb2291e96ca" />

En el caso que esté instalado, figurará como en la imagen de arriba.
Para mayor información: https://nmap.org/book/install.html

Ejecución de NMAP en ambiente de laboratorio:

-	nmap -sV -O 192.168.94.130
-	nmap -A 192.168.94.136

Flags:

-	-sV: Detección de versiones de servicios (probea puertos abiertos para banners/versión, ej. RDP en Windows).
-	-O: Fingerprinting de OS (analiza respuestas TCP/IP para identificar "Microsoft Windows").
-	-A: Escaneo agresivo (incluye -sV + -O + -sC para scripts NSE de vulns/scripts; detecta "Linux" y servicios como SSH).

Capturas de pantalla y algunas explicaciones:

<img width="567" height="144" alt="26" src="https://github.com/user-attachments/assets/19c76888-0b8e-4a00-b3d5-f40782c038e1" />

Vemos como nos detectó arriba que es un equipo virtual (vmware)

<img width="567" height="119" alt="27" src="https://github.com/user-attachments/assets/ceb6138d-76a2-4e64-9d5e-6153cf6d0bd1" />

MASSCAN

Instalamos con:

•	apt install masscan -y

Para mayor información: https://github.com/robertdavidgraham/masscan#building-and-testing

<img width="567" height="184" alt="28" src="https://github.com/user-attachments/assets/194b773a-9889-4edd-98d6-2e45183bd87f" />

Me da error, vamos a correr el update y sino un fix para ver si funciona:

<img width="567" height="81" alt="29" src="https://github.com/user-attachments/assets/442e1729-04ac-485e-b655-0cca1f1186c1" />
<img width="567" height="110" alt="30" src="https://github.com/user-attachments/assets/01fcd348-57a0-4a9c-90de-e86c95e573fe" />

Luego de hacer esto da error, lo mismo agregando “--fix-missing”

Vamos a actualizar el “source.list” para ver si en otro repo podemos encontrar lo necesario que no nos de problemas. Esto lo podríamos hacer editando con algún editor o directamente con este comando lo modificamos:

•	sed -i 's/ar\.archive\.ubuntu\.com/archive.ubuntu.com/g' /etc/apt/sources.list

Luego de esto corremos el update y la instalación de nuevo con los siguientes comandos:

•	apt update
•	apt install masscan -y

Solucionado!!

<img width="567" height="337" alt="31" src="https://github.com/user-attachments/assets/5379979f-5e40-4106-9e4f-01b623569f12" />

Ejecución de MASSCAN en ambiente de laboratorio:

-	masscan 192.168.94.130 -p1-65535 --rate=1000
-	masscan 192.168.94.136 -p1-65535 -oL output.txt

Flags:

•	-p1-65535: Especifica rango de puertos a escanear (1 a 65535, full scan).
•	--rate=1000: Limita velocidad a 1000 paquetes/seg (ajusta para no saturar red; default alto, pero bajo para labs).
•	-oL: Output en formato lista (grep-friendly, para pipe a otras tools).

Capturas de pantalla y algunas explicaciones:

<img width="567" height="79" alt="32" src="https://github.com/user-attachments/assets/be055400-df0b-4a8a-83fd-69b8568e62b1" />

Vemos un puerto abierto (7680).

<img width="567" height="78" alt="33" src="https://github.com/user-attachments/assets/11163f60-cc49-4be3-a386-9796cdff57c3" />

RUSTSCAN

Instalamos con:

•	curl -sSf https://sh.rustup.rs | sh -s -- -y  
•	source ~/.cargo/env
•	cargo install rustscan

<img width="567" height="249" alt="34" src="https://github.com/user-attachments/assets/0d475433-a0e2-4e52-8b5f-08d0c51bb375" />
<img width="567" height="337" alt="35" src="https://github.com/user-attachments/assets/06d621d6-25e6-44eb-8b6a-c2182e86bde4" />

Para mayor información: https://github.com/bee-san/RustScan#installation-guide

Ejecución de RUSTSCAN en ambiente de laboratorio:

-	rustscan -a 192.168.94.130 --ulimit 5000
-	rustscan -a 192.168.94.136 --scripts

Flags:

•	-a: Especifica la address (IP) para escanear.
•	--ulimit 5000: Aumenta límite de files abiertos (ulimit) a 5000 para scans intensivos (evita crashes en full ports).
•	--scripts default: Ejecuta scripts Nmap por default (ej. -sC en Nmap, como http, ssh checks).

Capturas de pantalla y algunas explicaciones:

<img width="567" height="449" alt="36" src="https://github.com/user-attachments/assets/73aede6e-6f60-44d1-b79f-ad1eefab3e47" />
<img width="567" height="182" alt="37" src="https://github.com/user-attachments/assets/c0030227-3584-429b-907d-f2721bb5fb69" />

Aquí abajo ejecutamos el script por default y si bien no encuentra nada, nos proporciona posibles causas (ver signos “!” en rojo).
Vamos a configurar los siguientes parámetros:

-	Límite: elevar el límite de archivos abiertos para que RustScan maneje más sockets sin fallar (5000).
-	Bajar el batch size: Reduce el número de puertos escaneados por lote para evitar sobrecarga y mejorar detección en redes lentas o sensibles (1000 o 2000 se puede usar).
-	Aumentar el timeout (-t): da más tiempo para respuestas si el ping a la VM es alto, ej. por latencia en VMware.

Comando a usar: rustscan -a 192.168.94.136 --scripts default --ulimit 5000 -b 2000 -t 2000

<img width="567" height="176" alt="38" src="https://github.com/user-attachments/assets/8a50122c-2332-4c78-b800-c32125736289" />
