# 🌐 UD 3: Tráfico de la red

Volver al [README principal](../README.md)

Permite analizar y monitorizar la actividad de red, conexiones y paquetes.

---

## 📋 Comandos usados:
- **tcpdump** → Captura y analiza paquetes de red.
- **tcptrack** → Muestra las conexiones TCP activas en tiempo real.
- **iptraf** → Interfaz gráfica en terminal para tráfico de red.
- **bmon** → Visualiza el ancho de banda usado por interfaces.

---

## 📸 Análisis de Capturas

### 🖼️ `tcpdump` (Capturador de Paquetes)
![tcpdump](/UD3/img/tcpdump.png)

#### Comando: `sudo tcpdump -i eno1 -vvv`
* **`tcpdump`**: Es un "sniffer" de red. Una potente herramienta de línea de comandos que captura y analiza el tráfico de red (paquetes) que pasa por una interfaz.
* **`sudo`**: Es necesario ejecutarlo como superusuario para poder poner la tarjeta de red en modo de captura.
* **`-i eno1`**: Especifica la **interfaz** de red que se va a escuchar (en este caso, `eno1`).
* **`-vvv`**: Aumenta el nivel de **verbosidad** (detalle) al máximo, para obtener la mayor cantidad de información posible de cada paquete.

#### Detalles de la foto
* La salida es un registro en tiempo real de los paquetes capturados. Cada línea es un paquete:
    * `09:49:23...`: La marca de tiempo (timestamp) de cuándo se capturó el paquete.
    * `IP pc204.42696 > 238.red-80-58-0.static.ri.net.domain`: Esto describe un paquete IP.
        * Origen: `pc204` (nuestra máquina) desde el puerto `42696`.
        * Destino: Un servidor (`238.red-80-58-0...`) en el puerto `.domain` (que es el puerto 53, usado para consultas DNS).
    * `IP 34.107.243.93.https > pc204.42080`: Esto es un paquete de vuelta.
        * Origen: Un servidor externo (IP `34.107...`) desde su puerto `https` (puerto 443).
        * Destino: Nuestra máquina (`pc204`) en el puerto `42080`.
    * `Flags [P.]`: Es un *flag* de TCP. `P` (Push) indica que los datos deben ser entregados a la aplicación inmediatamente.
    * **En resumen**: Estamos viendo tráfico mixto, principalmente consultas DNS (salientes al puerto 53) y tráfico web seguro (entrante desde el puerto 443).

### 🖼️ `tcptrack`
![tcptrack1](/UD3/img/tcptrack1.png)
![tcptrack2](/UD3/img/tcptrack2.png)

#### Comando: `sudo tcptrack -i eno1`
* **`sudo`**: Ejecuta el comando como superusuario (root), necesario para capturar paquetes de red.
* **`tcptrack`**: Es la herramienta para monitorizar conexiones TCP.
* **`-i eno1`**: Especifica la **interfaz** de red que se va a escuchar. En este caso, la interfaz Ethernet `eno1`.

#### Detalles de la foto
* La **primera imagen** muestra el comando justo antes de ser ejecutado.
* La **segunda imagen** muestra la salida interactiva de `tcptrack`. Lista todas las conexiones TCP activas en la interfaz `eno1`:
    * **`Client`**: La IP y puerto de origen (nuestro equipo).
    * **`Server`**: La IP y puerto de destino (el servidor remoto).
    * **`State`**: El estado de la conexión (ej. `ESTABLISHED` significa que está activa).
    * **`Idle`**: Tiempo de inactividad (cuánto tiempo ha pasado desde el último paquete).
    * **`Speed`**: Velocidad de transferencia actual.
    * En la captura, vemos tres conexiones establecidas al puerto `443` (HTTPS) a diferentes IPs de servidores remotos.

### 🖼️ `iptraf`
![iptraf](/UD3/img/iptraf.png)

#### Comando: `iptraf` (o `sudo iptraf-ng`)
* **`iptraf`**: Es una herramienta interactiva basada en ncurses (interfaz en terminal) que proporciona estadísticas de red en tiempo real. La imagen muestra el monitor de tráfico IP.

#### Detalles de la foto
* La pantalla está dividida en dos secciones principales:
    1.  **Conexiones TCP (arriba)**: Muestra un resumen de las conexiones TCP activas. Vemos la IP de origen/destino y sus puertos, el número de paquetes (`Packets`) y bytes (`Bytes`) transferidos, y la interfaz (`Iface`). Por ejemplo, hay una conexión a `104.18.39.21:443`.
    2.  **Registro de paquetes (abajo)**: La sección inferior (con texto rojo y blanco) muestra un *log* detallado de paquetes individuales a medida que se capturan. En este caso, está mostrando principalmente tráfico `UDP` desde nuestra IP (`172.26.10.227`) a un servidor DNS (`80.58.61.250`) en el puerto `53`.

### 🖼️ `bmon`
![bmon](/UD3/img/bmon.png)

#### Comando: `bmon`
* **`bmon`** (Bandwidth Monitor): Es una herramienta para monitorizar el ancho de banda y visualizar el tráfico de red en tiempo real, de forma gráfica, en la terminal.

#### Detalles de la foto
* **`Interfaces`**: Arriba a la izquierda, lista todas las interfaces de red disponibles (`eno1`, `lo`, `docker0`, etc.). La interfaz `eno1` está seleccionada.
* **`Gráficos (RX/TX)`**: La parte principal de la pantalla muestra dos gráficos:
    * **`RX Bytes/second` (Recepción)**: Muestra el tráfico de *descarga* (entrada) en Kibibytes (KiB) por segundo.
    * **`TX Bytes/second` (Transmisión)**: Muestra el tráfico de *subida* (salida) en KiB por segundo.
* En la captura, podemos ver picos de tráfico de bajada (RX) de hasta 8.57 KiB/s y de subida (TX) de hasta 9.82 KiB/s.
