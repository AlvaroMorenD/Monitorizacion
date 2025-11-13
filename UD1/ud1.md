# 🧠 UD 1: Procesos

Volver al [README principal](../README.md)

Los procesos son programas o tareas en ejecución. Podemos monitorizarlos con herramientas como `ps`, `top` o `htop`.

---

## 📋 Comandos usados:
- **ps** → Muestra los procesos activos del sistema.
- **top** → Lista en tiempo real los procesos con más uso de CPU o memoria.
- **htop** → Versión mejorada e interactiva de `top`.

---

## 📸 Análisis de Capturas

### 🖼️ `ps` (Process Status)
Aquí vemos tres variantes del comando `ps` para filtrar procesos.

#### 1. `ps -u alumno`
![ps u alumno](/img/psualumno.png)
* **`ps`**: Comando base para ver procesos.
* **`-u alumno`**: Filtra la lista para mostrar únicamente los procesos que pertenecen al **usuario** "alumno".
* **Detalles de la foto**: La salida muestra todos los procesos propiedad del usuario `alumno`. Vemos que `alumno` está ejecutando un `systemd` de usuario, servicios como `pipewire`, un `bash` (terminal) en `tty3` y el editor de texto `nano`. Es una forma rápida de ver qué está haciendo un usuario específico.

#### 2. `ps au`
![ps au](Procesos/img/ps%20au.png)
* **`a`**: Muestra los procesos de **todos los usuarios** que están asociados a una **terminal** (TTY).
* **`u`**: Muestra la salida en formato **orientado al usuario** (user-oriented), que incluye columnas útiles como `USER`, `%CPU`, `%MEM`, `VSZ` (memoria virtual), `RSS` (memoria física), `STAT` (estado) y `COMMAND`.
* **Detalles de la foto**: Esta vista es un resumen de las sesiones de usuario activas. Vemos procesos de `keibron` (el usuario principal), `root` y `alumno`. No muestra los servicios del sistema o demonios que no tienen terminal.

#### 3. `ps aux`
![ps aux](Procesos/img/ps%20aux.png)
* **`a`**: Procesos de todos los usuarios con TTY.
* **`u`**: Formato orientado al usuario.
* **`x`**: Incluye procesos que **no** están asociados a una terminal (demonios, servicios de fondo, hilos del kernel).
* **Detalles de la foto**: `ps aux` es uno de los comandos más comunes para obtener una **instantánea completa de todo lo que se está ejecutando en el sistema**. La imagen muestra una larga lista de procesos, la mayoría propiedad de `root` y con un `?` en la columna `TTY`, lo que indica que son procesos del sistema (como `kthreadd`, `kworker`, etc.) que se ejecutan en segundo plano.

---

### 🖼️ `top` (Monitor de procesos)
![top1](Procesos/img/top1.png)

#### Comando: `top`
* **`top`**: Es un monitor de sistema interactivo que se ejecuta en la terminal. Proporciona una vista en tiempo real de los procesos y el consumo de recursos del sistema.

#### Detalles de la foto
La salida de `top` se divide en dos partes:

1.  **Resumen del Sistema (Cabecera):**
    * `top - 10:55:46 up 2:34`: Muestra la hora actual, el tiempo que lleva el sistema encendido (`uptime`) y los 2 usuarios conectados.
    * `load average: 0,14, 0,19, 0,26`: La carga media del sistema en los últimos 1, 5 y 15 minutos. Valores bajos indican un sistema con poca carga.
    * `Tareas: 336 total, 2 ejecutar`: Hay 336 procesos en total, 2 de ellos en estado de ejecución (`running`).
    * `%Cpu(s)`: Muestra el uso de la CPU dividido por tipo. `us` (usuario), `sy` (sistema), `id` (inactivo).
    * `Mib Mem` y `Mib Intercambio`: Muestra el uso de la memoria RAM (`Mem`) y la memoria de intercambio (`Swap`).

2.  **Lista de Procesos:**
    * Es una tabla con los procesos ordenados por defecto por `%CPU`.
    * `PID`: ID del Proceso.
    * `USUARIO`: El propietario del proceso.
    * `%CPU` y `%MEM`: Porcentaje de CPU y Memoria que consume el proceso.
    * `ORDEN`: El nombre del comando.
    * En la captura, vemos que `firefox` (PID 3850) y `gnome-shell` (PID 2982) son los procesos que más recursos consumen en ese momento.

### 🖼️ `top` (Modo Batch)
![TopInfo](Procesos/img/TopInfo.png)

#### Comandos: `top -b -n 1 > top.info` y `cat top.info`
* **`top -b -n 1 > top.info`**:
    * `top`: Inicia el monitor de procesos.
    * `-b`: (Modo *Batch*). Ejecuta `top` en modo no interactivo, para que su salida pueda ser redirigida a un archivo o a otro comando.
    * `-n 1`: (Número de iteraciones). Indica a `top` que se ejecute solo una vez y luego termine.
    * `> top.info`: Redirige la salida estándar (el resultado) al archivo `top.info`.
* **`cat top.info`**:
    * `cat`: Comando para concatenar y mostrar el contenido de archivos.
    * `top.info`: Muestra en la terminal el contenido del archivo que acabamos de crear.

#### Detalles de la foto
* La imagen muestra el resultado de capturar una "foto" instantánea del estado del sistema usando `top` y guardarla en un archivo. Es muy útil para *scripts* o para registrar el estado del sistema en un momento concreto sin tener que interactuar con `top`.

### 🖼️ `htop` (Monitor de procesos interactivo)
![htop](Procesos/img/htop.jpg)

#### Comando: `htop`
* **`htop`**: Es un visor de procesos interactivo y un monitor de sistema. Se considera una alternativa moderna y más visual a `top`.

#### Detalles de la foto
* **Cabecera (Gráficos):** La parte superior muestra gráficos de barras para cada núcleo de la CPU (indicando el porcentaje de uso de cada uno), así como el uso de la Memoria (`Mem`) y la memoria de Intercambio (`Swp`).
* **Lista de Procesos:** Similar a `top`, pero con colores y una disposición más clara. Permite desplazarse (scroll) vertical y horizontalmente.
* **Menú Inferior:** Muestra las acciones rápidas disponibles mediante las teclas de función (F1 a F10), como `F9 Kill` (matar un proceso), `F7 Nice -` (reducir prioridad) o `F4 Filter` (filtrar procesos por nombre).

### 🖼️ `ps` (Ejercicio 1)
![ejercicio1](Procesos/img/ejercicio1.png)

#### Comando: `ps -eo user,pid,%cpu,comm --sort=-%cpu | head -n 6`
Este es un comando compuesto por varias partes:

* **`ps`**: (Process Status) El comando base para mostrar información sobre los procesos.
* **`-e`**: Muestra **todos** los procesos del sistema.
* **`-o user,pid,%cpu,comm`**: Define un **formato de salida personalizado**. Le dice a `ps` que muestre solo las columnas: `user` (Usuario), `pid` (ID del Proceso), `%cpu` (Porcentaje de CPU) y `comm` (Nombre del Comando).
* **`--sort=-%cpu`**: **Ordena** la salida. El `--sort` indica la columna por la que ordenar (`%cpu`). El signo menos (`-`) al principio indica un orden **descendente** (de mayor a menor).
* **`|` (Pipe)**: Toma la salida del comando `ps` y la "entuba" como entrada para el siguiente comando.
* **`head -n 6`**: Lee la entrada que recibe y muestra solo las primeras `6` líneas (`-n 6`).

#### Detalles de la foto
* El resultado es una lista limpia de los 5 procesos que más CPU están consumiendo en ese instante (más la línea de cabecera `USER PID...`).
* Vemos que `ps` (el propio comando, PID 63178) aparece primero con un 100% de CPU, lo cual es normal ya que se está ejecutando en ese preciso instante.
* Le siguen `firefox` (6.1%), `gnome-shell` (3.7%) y dos procesos de `Isolated Web Co` (2.8% y 2.3%), que son subprocesos de Firefox.
