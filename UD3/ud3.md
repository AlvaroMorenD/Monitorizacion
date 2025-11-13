# 🔌 UD 4: Puertos y Sockets

Volver al [README principal](../README.md)

Los puertos permiten la comunicación entre dispositivos y servicios. Estas herramientas ayudan a detectar puertos abiertos, conexiones activas o servicios escuchando.

---

## 📋 Comandos usados:
- **ss** → Muestra estadísticas y sockets activos.
- **nmap** → Escanea puertos y servicios de una red.
- **whois** → Muestra información del dominio o IP.
- **arp -n** → Lista los dispositivos conectados mostrando IP, MAC y tipo de interfaz.

---

## 📸 Análisis de Capturas

### 🖼️ `ss` (Socket Statistics)
![ss](/UD4/img/ss.png)
![ss2](/UD4/img/ss2.png)

#### Comando 1: `ss -plunt` (Foto `ss.png`)
* **`ss`**: Herramienta moderna para investigar sockets (reemplazo de `netstat`).
* **`-p`**: Muestra el **proceso** (programa) que está usando el socket.
* **`-l`**: Muestra solo los sockets que están en modo **escucha** (`LISTEN`).
* **`-u`**: Muestra sockets **UDP**.
* **`-n`**: Muestra direcciones y puertos en formato **numérico** (no resuelve DNS).
* **`-t`**: Muestra sockets **TCP**.
* **Detalles (Foto 1)**: Este comando lista todos los puertos TCP y UDP que están "a la escucha" en la máquina.
    * Vemos servicios `UDP` como `127.0.0.53:53` (un DNS local).
    * Vemos servicios `TCP` como `0.0.0.0:22` (SSH escuchando en todas las interfaces) y `127.0.0.1:631` (CUPS, servicio de impresión).
    * Destaca la línea `127.0.0.1:6463` que está siendo usada por el proceso "Discord" (pid=4183).

#### Comando 2: `ss -nntp` y `ss -tnp` (Foto `ss2.png`)
* **`ss -nntp`**:
    * **`-nn`**: Numérico para host y puerto.
    * **`-t`**: Muestra sockets **TCP**.
    * **`-p`**: Muestra el **proceso**.
* **`ss -tnp`**:
    * **`-t`**: Muestra sockets **TCP**.
    * **`-n`**: Numérico para host (pero no para puerto, aunque en la salida se ve numérico).
    * **`-p`**: Muestra el **proceso**.
* **Detalles (Foto 2)**: A diferencia del comando anterior, al no usar `-l` (listen), `ss` muestra por defecto las conexiones **establecidas** (`ESTAB`).
    * Las dos salidas son idénticas y muestran las conexiones activas salientes.
    * Vemos que los procesos "firefox" (pid 4377) y "Discord" (pid 4118) tienen varias conexiones a servidores externos (ej. `34.107.243.93:443`), la mayoría al puerto `443` (HTTPS).

### 🖼️ `nmap` (Network Mapper)
![nmap](/UD4/img/nmap.png)
![nmap2](/UD4/img/nmap2.png)

#### Comando 1: `nmap -sn 172.26.0.1/24` (Foto `nmap.png`)
* **`nmap`**: Es un potente escáner de redes y puertos.
* **`-sn`**: Es un "Ping Scan" (Scan de Ping). **Desactiva el escaneo de puertos**. Su única función es descubrir qué hosts están *vivos* (responden) en la red.
* **`172.26.0.1/24`**: Es el objetivo. Especifica la subred completa, desde `172.26.0.1` hasta `172.26.0.254`.
* **Detalles (Foto 1)**: La imagen solo muestra el comando listo para ejecutarse. El resultado de este comando sería una lista de las IPs que están encendidas en esa red.

#### Comando 2: `nmap --top-ports 100 -sV 172.26.10.99` (Foto `nmap2.png`)
* **`--top-ports 100`**: Escanea únicamente los 100 puertos TCP más comunes (en lugar de los 1000 por defecto o los 65535 totales).
* **`-sV`**: Activa la **detección de versión y servicio**. `nmap` no solo dirá si un puerto está abierto, sino que intentará averiguar *qué* software se está ejecutando en él y su versión.
* **`172.26.10.99`**: El objetivo del escaneo (un solo host).
* **Detalles (Foto 2)**: El resultado del escaneo es muy claro:
    * `Host is up`: La máquina está encendida.
    * `Not shown: 98 closed tcp ports`: De los 100 puertos escaneados, 98 estaban cerrados.
    * `PORT 80/tcp open http`: El puerto 80 (HTTP) está abierto.
    * `PORT 8081/tcp open http`: El puerto 8081 también está abierto.
    * Gracias a `-sV`, sabemos que en ambos puertos se está ejecutando un servidor web **Apache httpd 2.4.58 ((Ubuntu))**.

### 🖼️ `whois`
![whois](/UD4/img/whois.png)

#### Comando: `whois 34.107.243.93`
* **`whois`**: Es una herramienta de consulta que obtiene información de registro sobre un nombre de dominio o una dirección IP a partir de bases de datos públicas.
* **`34.107.243.93`**: La dirección IP que queremos investigar.

#### Detalles de la foto
* El resultado proviene de ARIN (el Registro Regional de Internet para América del Norte).
* **`NetRange`**: Muestra que esta IP pertenece a un bloque enorme (`34.64.0.0 - 34.127.255.255`).
* **`OrgName`**: El propietario del bloque es **Google LLC**.
* **`Address`**: Muestra la dirección física de Google (1600 Amphitheatre Parkway).
* **`Comment`**: Aclara que estas IPs son usadas por clientes de **Google Cloud**. (Esta IP la vimos en la captura de `ss2.png`, probablemente una conexión de Firefox o Discord a un servicio alojado en Google Cloud).

### 🖼️ `arp`
![arp-n](/UD4/img/arp-n.png)

#### Comando: `arp -n`
* **`arp`**: Gestiona la **caché ARP** (Protocolo de Resolución de Direcciones) del sistema. ARP es el protocolo que traduce direcciones IP (Capa 3) a direcciones MAC (Capa 2) en una red local.
* **`-n`**: Opción **numérica**. Evita que `arp` intente resolver las direcciones IP a nombres de host (DNS), haciendo que la salida sea más rápida y limpia.

#### Detalles de la foto
* La tabla muestra la caché ARP de la máquina local. Es un mapa de IPs locales y sus correspondientes direcciones físicas (MAC).
* **`Dirección`**: La dirección IP de un dispositivo en la red local (ej. `172.26.0.12`).
* **`DirecciónHW`**: La dirección MAC (hardware) única de ese dispositivo (ej. `3c:2a:f4:01:14:0f`).
* **`Interfaz`**: La interfaz de red de *nuestra* máquina (`eno1`) que se usa para comunicarse con ese dispositivo.
* En resumen, esta tabla muestra los "vecinos" de nuestra máquina en la red local con los que ha hablado recientemente.
