# 💾 UD 2: Memoria

Volver al [README principal](../README.md)

La memoria muestra cómo se usan los recursos RAM, disco y CPU en el sistema.

---

## 📋 Comandos usados:
- **free** → Indica la memoria total, usada y libre.
- **df** → Muestra el uso del disco por particiones.
- **du** → Indica el espacio ocupado por archivos o directorios.
- **iostat** → Mide el rendimiento de CPU y dispositivos de entrada/saida.
- **atop** → Monitoriza el rendimiento del sistema (memoria, CPU, disco, red).

---

## 📸 Análisis de Capturas

### 🖼️ `free` (Uso de Memoria)
![free](/UD2/img/free.png)

#### Comando 1: `free`
* **`free`**: Muestra la cantidad de memoria RAM (física) y de intercambio (swap) usada y libre en el sistema. Por defecto, la salida se muestra en Kibibytes (KiB).
* **Detalles**:
    * `Mem`: Fila de la memoria RAM física.
    * `Inter`: Fila de la memoria de Intercambio (Swap).
    * `total`: Memoria total instalada.
    * `usado`: Memoria actualmente en uso por procesos.
    * `libre`: Memoria que no está siendo utilizada para nada.
    * `compartido`: Memoria compartida entre varios procesos.
    * `búf/caché`: Memoria usada por el kernel para búferes y caché de disco (mejora el rendimiento).
    * `disponible`: La estimación real de cuánta memoria está disponible para iniciar nuevas aplicaciones (considera la `libre` + la `caché` recuperable).

#### Comando 2: `free -h`
* **`-h`**: (Human-readable). Muestra la salida en un formato legible para humanos, usando GiB (Gibibytes) o MiB (Mebibytes).
* **Detalles**: Es mucho más fácil de leer. Vemos que el sistema tiene `15Gi` de RAM total, `3,0Gi` están en uso y `10Gi` están libres.

#### Comando 3: `free -c 3`
* **`-c 3`**: (Count 3). Repite la ejecución del comando `free` 3 veces, con un intervalo de 1 segundo entre cada una. Esto permite ver cómo cambia el uso de memoria en un corto período.

### 🖼️ `df` (Uso de Disco por Particiones)
![df](/UD2/img/df.png)

#### Comando 1: `df -h`
* **`df`**: (Disk Free). Reporta el espacio en disco utilizado y disponible en los **sistemas de archivos** (particiones) montados.
* **`-h`**: (Human-readable). Muestra los tamaños en formato legible (G, M, K).
* **Detalles**: La salida muestra una tabla:
    * `S.ficheros`: El nombre del dispositivo o sistema de archivos (ej. `/dev/nvme0n1p2`).
    * `Tamaño`: Tamaño total de la partición.
    * `Usados`: Espacio usado.
    * `Disp`: Espacio disponible.
    * `Uso%`: Porcentaje de espacio usado.
    * `Montado en`: El directorio donde está montada la partición (ej. `/` para la raíz, `/boot/efi` para el arranque).
    * Vemos que la partición principal (`/dev/nvme0n1p2`) tiene 457G y está al 39% de uso.

#### Comando 2: `df -hT`
* **`-T`**: (Type). Añade una columna `Tipo` que muestra el tipo de sistema de archivos (ej. `ext4`, `vfat`, `tmpfs`).

#### Comando 3: `df -hT /`
* **`/`**: Al añadir un directorio como argumento, `df` filtra la salida para mostrar únicamente el sistema de archivos que contiene ese directorio. En este caso, solo muestra la información de la partición raíz (`/`).

### 🖼️ `du` (Uso de Disco por Directorios)
![du](/UD2/img/du.png)

#### Comando 1: `sudo du -hs /`
* **`du`**: (Disk Usage). Estima el espacio en disco **ocupado por archivos y directorios**. A diferencia de `df` (que mira particiones), `du` mira el contenido.
* **`sudo`**: Se usa para tener permisos de administrador y poder leer el tamaño de todos los directorios.
* **`-h`**: Formato legible.
* **`-s`**: (Summarize). Muestra solo un total para el directorio especificado, en lugar de listar cada subdirectorio.
* **`/`**: El directorio raíz.
* **Detalles**: La salida `172G /` indica que el tamaño total de todos los archivos en el directorio raíz es de 172 GiB. Los errores ("Permiso denegado", "No existe...") son normales al escanear la raíz de un sistema en funcionamiento.

#### Comando 2: `sudo du -hs /home/`
* **Detalles**: Muestra el tamaño total del directorio `/home` (138G).

#### Comando 3: `sudo du -hs /home/*`
* **`/home/*`**: El asterisco (`*`) actúa como comodín. El comando calcula el resumen (`-s`) del tamaño de **cada** elemento dentro de `/home/`.
* **Detalles**: Es la salida más útil de la imagen. Desglosa el tamaño de cada carpeta de usuario, permitiendo ver quién ocupa más espacio: `keibron` (64G), `2asirn` (74G), `manolo` (237M), etc.

### 🖼️ `iostat` (Estadísticas de E/S)
![iostat](/UD2/img/iostat.png)

#### Comando: `iostat` (probablemente ejecutado con un intervalo, ej. `iostat 2`)
* **`iostat`**: (Input/Output Statistics). Es una herramienta que monitoriza el rendimiento de la CPU y, lo más importante, las estadísticas de **Entrada/Salida (I/O)** de los dispositivos de almacenamiento (discos).
* **Detalles**: La imagen muestra varias "instantáneas" repetidas cada pocos segundos.
    * **`avg-cpu`**: Muestra el uso medio de la CPU. Lo más relevante aquí es `%iowait` (CPU esperando por E/S) y `%idle` (CPU inactiva). Vemos que `%idle` es casi 100% y `%iowait` es 0,00, lo que significa que la CPU no está esperando al disco (el disco no es un cuello de botella).
    * **`Device`**: Lista las estadísticas por dispositivo. `nvme0n1` es el disco SSD principal.
    * `r/s` y `w/s`: Lecturas y escrituras por segundo.
    * `rkB/s` y `wkB/s`: KiloBytes leídos y escritos por segundo. Vemos picos de escritura (`wkB/s` de 84,80 o 85,71), lo que indica que algo está escribiendo datos en el disco. (Hemos descargado una imagen iso de debian de prueba)

### 🖼️ `atop` (Monitor de Rendimiento Avanzado)
![atop1](/UD2/img/atop1.png)

#### Comando: `atop`
* **`atop`**: Es un monitor de rendimiento avanzado, similar a `top` pero mucho más completo. Muestra el uso de **todos** los recursos críticos (CPU, Memoria, Disco y Red) en una sola pantalla.
* **Detalles**:
    * **Cabecera (Resumen):**
        * `PRC`: Actividad de Procesos (sistema/usuario).
        * `CPU`: Uso **por cada núcleo**. Destaca que el núcleo `cpu007` está muy ocupado (`sys 57%`, `user 33%`).
        * `MEM`: Uso de memoria RAM (total, libre, caché, sucia).
        * `SWP`: Uso de la memoria de Intercambio (Swap).
        * `DSK`: Actividad de los discos (`busy` = % de tiempo ocupado).
        * `NET`: Actividad de Red (paquetes/kb por segundo).
    * **Lista de Procesos (Abajo):**
        * `atop` resalta en rojo los recursos que están siendo un cuello de botella.
        * Vemos un proceso (PID 10000) `syscups` que está consumiendo un `100%` de CPU.
        * A la derecha de la lista de procesos (no visible en `top`), `atop` muestra el uso de disco (`RDDSK`, `WRDSK`) y red de cada proceso. Es una herramienta excelente para diagnósticos profundos.
