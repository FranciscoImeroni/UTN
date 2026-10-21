### [[Assets/U11_Memorias_Segmentacion.pdf|Diapositivas: Memorias - Segmentación]]

# Unidad 11 – Memorias y Segmentación

> Basado en las diapositivas **"Memorias – Segmentación"**.

## 1. ¿Qué son las Memorias?

Los dispositivos que contienen, almacenan, guardan y memorizan datos.

Tienen tres características:

| Ubicación | Velocidad | Capacidad |
|---|---|---|
| Internas | Rápidas | Baja |
| Externas | Lentas | Alta |

## 2. Gestión de la Memoria (IA-32)

**Introducción:**
- El procesador **8086** fue el primero de **16 bits** de ancho de palabra (sus registros tienen ese tamaño), pero su bus de direcciones es de **20 líneas**, que permiten armar direcciones de 20 bits para disponer de un espacio de memoria de **1 MByte**.
- A partir del **80286** (16 bits) y del **80386** (32 bits) se incluyó un nuevo modo de trabajo denominado **Modo Protegido**.
- Los procesadores de 32 bits tienen bus de datos de 32 líneas y bus de direcciones de 32 líneas, que permiten direccionar hasta 2³² (**4 GBytes**).
- El **Pentium** utiliza direcciones virtuales de **46 bits**, pudiendo direccionar hasta **64 TB** como si fuera un espacio único de memoria principal.

**Función dentro de la arquitectura:**
- Almacena datos e instrucciones de programas.
- Interconecta la CPU y los dispositivos de E/S.

![[Assets/u11_arquitectura_memoria_es.png]]

### Espacio físico vs. Espacio lógico

| Espacio Físico | Espacio Lógico |
|---|---|
| La memoria está organizada como una secuencia de bytes, direccionadas por el **Bus de Direcciones**. | El espacio de direccionamiento está organizado en **segmentos**. |
| Se denomina **memoria física** a la memoria conectada a este bus, y **direcciones físicas** a las direcciones que van sobre él. | Se necesitan 2 valores: la **base** del segmento (dónde están las instrucciones o los datos) y el **desplazamiento** a partir del inicio de ese segmento. A la dirección formada por estos 2 valores la denominamos **dirección lógica**. |

Un segmento se divide lógicamente, por ejemplo, en **DATA**, **CODE** y **STACK**; a partir de la base del segmento se suma el desplazamiento para obtener la dirección efectiva.

### ¿Cómo pasar de la dirección lógica a la dirección física?

Estando en **Modo Protegido**, se consigue con un proceso en dos niveles:
- **Traslación** de una dirección lógica.
- **Paginación** del espacio lineal.

Para esta traducción existe una unidad denominada **MMU (Memory Management Unit)**, que internamente tiene 2 unidades:
- La **Unidad de Segmentación**, que traslada la **dirección lógica** a una **dirección lineal**.
- La **Unidad de Paginación**, que traduce la **dirección lineal** en una **dirección física**, que es la que se envía a la memoria física por el bus de direcciones.

**Técnicas de direccionamiento a memoria:**

```
direcciones lógicas  →  MAPEO (traducción)  →  direcciones físicas
```

## 3. Espacio lógico de la memoria (Memoria Virtual)

Método de organización y gestión de la memoria que proporciona un espacio mucho mayor del que se dispone físicamente. Es una "partición" del disco que anticipa las direcciones que necesita la CPU y, con un buen algoritmo, se accede a ellas con mayor velocidad.

### Dimensión de la Memoria

- **Bits, bytes, etc.** son las unidades que maneja la memoria.
- La capacidad se expresa en Bits, Bytes, Kbytes/KB, GB, TB, PetaBytes, ExaByte, ZettaBytes, YottaBytes. En ese orden aumenta cada 2¹⁰ veces.
- No tiene que ver con los múltiplos físicos, porque:

```
2¹⁰ = 1.K = 1K = 1024 ≠ 1000 = 10³ = 1 Kilo (físico)
```

### Capacidad del Módulo (Banco de memoria)

- Es fácil calcular la capacidad de un módulo de memoria si se conocen las capacidades de los chips que hay en él. Ej: si hay 8 chips de 64 Mbits, es un módulo de 512 Mbits.
- Como la capacidad de un módulo se describe en **megabytes** y no en megabits, hay que convertir bits a bytes dividiendo el número de bits entre 8.

```
512 Mbits / 8 bits por byte = 64 MB
```

- Los módulos estándar de la industria se describen como **"4M x 32"** (4 Meg por 32 bits) o **"16M x 64"** (16 Meg por 64 bits). En estos casos se calcula la capacidad exactamente igual que un chip:

```
4M x 32 = 128 Mbits  →  128 Mbits / 8 = módulo de 16 MB
16M x 64 = 1024 Mbits →  1024 Mbits / 8 = módulo de 128 MB
```

### Tipos de almacenamiento de bytes en memoria

| LITTLE ENDIAN | BIG ENDIAN |
|---|---|
| El byte menos significativo se almacena en la dirección más baja y cada octeto se almacena en memoria en forma invertida. | El byte menos significativo se almacena en la dirección numéricamente más baja y el más significativo en la más alta. |

![[Assets/u11_little_big_endian.png]]

## 4. Jerarquía de memorias

De mayor a menor velocidad/costo (y de menor a mayor capacidad):

```
Registros  →  Memoria Caché  →  Memoria RAM  →  Disco Duro  →  Copias de Seguridad
```

A medida que se desciende en la pirámide **aumenta la capacidad** y **disminuyen la velocidad y el costo**.

## 5. Clasificación de las memorias

### Según modo de acceso

- **Acceso aleatorio:** un componente de selección habilita una posición e inhabilita las demás; recibe "el identificador" único de la unidad de información implicada. Tiempo de acceso independiente de la posición.
- **Acceso secuencial:** para acceder a una unidad se establece una posición de referencia, a partir de la cual comienza el rastreo (lectura hasta encontrar). El tiempo de acceso depende de esas posiciones.
- **Acceso asociativo:** la búsqueda implica comparar un grupo de bits de la unidad de información con el contenido de una posición. No se identifican por su dirección; se envía un grupo de bits ("rótulo") para comparación.

### Según la duración de la información

- **Volátiles:** pierden la información con el corte del suministro eléctrico.
- **No volátiles / perennes / permanentes:** no pierden la información tras un corte de suministro eléctrico.

### Tipos de memorias semiconductoras

| Tipo de memoria | Clase | Borrado | Mecanismo de escritura | Volatilidad |
|---|---|---|---|---|
| RAM (acceso aleatorio) | Lectura/escritura | Eléctricamente, por bytes | Eléctricamente | Volátil |
| ROM (solo lectura) | Solo lectura | No posible | Mediante máscaras | No volátil |
| PROM | Solo lectura | No posible | Mediante máscaras | No volátil |
| EPROM | Sobre-todo-lectura | Luz ultravioleta, chip completo | Eléctricamente | No volátil |
| FLASH | Sobre-todo-lectura | Eléctricamente, por bloques | Eléctricamente | No volátil |
| EEPROM | Sobre-todo-lectura | Eléctricamente, por bytes | Eléctricamente | No volátil |

## 6. Tipos de Memorias

- **RAM (Random Access Memory):** memoria de trabajo del sistema operativo, los programas y la mayor parte del software. En ella se cargan todas las instrucciones que ejecuta la CPU. Se denomina "de acceso aleatorio" porque se puede leer o escribir en cualquier posición con un tiempo de espera igual, sin necesidad de seguir un orden (a diferencia del acceso secuencial).
  - **Características de las RAM:** las densidades de chips DRAM se describen por su organización de celdas, con la notación *profundidad x ancho* (ej. `4Mx16`). Multiplicando profundidad por ancho se obtiene la **densidad del chip**.
- **ROM (Read Only Memory):** medio de almacenamiento que permite solo la lectura de la información y no su escritura, independientemente de si hay o no fuente de energía.
- **PROM:** ROM programable. No viene programada de fábrica; es el usuario quien graba la información, y una vez grabados los datos no se pueden borrar.
- **EPROM:** memorias de lectura reprogramables. Se programan mediante impulsos eléctricos y su contenido se borra exponiéndolas a la **luz ultravioleta**, para poder volver a ser grabadas.
- **EEPROM (E²PROM):** ROM programable y borrable eléctricamente. Puede ser programada, borrada y reprogramada eléctricamente (a diferencia de la EPROM, que requiere luz UV). Son memorias no volátiles.
- **Memoria FLASH:** derivada de la EEPROM. Permite lectura y escritura de múltiples posiciones de memoria en la misma operación, lo que le da velocidades muy superiores a la EEPROM primigenia (que solo actuaba sobre una única celda por operación). Es la tecnología empleada en las memorias USB.

### Memorias RAM estáticas y dinámicas

- **DRAM:** contiene celdas con cargas eléctricas (condensadores); la presencia o ausencia de carga representa un 0 o un 1 binario. Requieren **refrescos periódicos**. Ej: memoria principal.
- **SRAM:** a diferencia de la DRAM, guarda los datos en compuertas biestables y los mantiene siempre que se la mantenga alimentada. **No** requiere refrescos periódicos. Ej: memoria caché.

### Memoria magnética

Usa diferentes patrones de magnetización sobre una superficie cubierta con una capa magnetizada para almacenar información. Es **no volátil**. Se accede mediante uno o más cabezales de lectura/escritura; como el cabezal solo cubre una parte de la superficie, el almacenamiento magnético es de **acceso secuencial** (debe buscar, dar vueltas, o ambas cosas). Tipos: **disco magnético**, **disco duro**, **cinta magnética**.

- **Unidad de disco:** dispositivo que emplea un sistema de grabación magnética para almacenar archivos digitales. Se compone de uno o más platos/discos rígidos unidos por un mismo eje que gira a gran velocidad dentro de una caja metálica sellada. Sobre cada plato, en cada cara, hay un cabezal de lectura/escritura que flota sobre una delgada lámina de aire generada por la rotación de los discos.

### Almacenamiento óptico

Dispositivos capaces de guardar datos por medio de rayo láser; se almacenan por medio de ranuras microscópicas y la información queda grabada físicamente en la superficie. Solo el calor y las ralladuras pueden producir pérdida de datos.

| Formato | Descripción |
|---|---|
| **CD** | Disco compacto no borrable, almacena audio digitalizado. Discos de 12 cm, más de 60 minutos de reproducción ininterrumpida. |
| **CD-ROM** | Disco compacto no borrable usado como memoria de datos. Discos de 12 cm, más de 650 MB. |
| **DVD** | Disco versátil digital, para vídeo digitalizado/comprimido y grandes cantidades de datos. Formatos de 8 y 12 cm, hasta 17 GB (doble cara). El DVD básico es de solo lectura (DVD-ROM). |
| **DVD-R** | Grabable una sola vez, similar al DVD-ROM. Solo discos de una cara. |
| **DVD-RW** | Regrabable varias veces, similar al DVD-ROM. Solo discos de una cara. |

## 7. Modos de Direccionamiento u operación de los procesadores

```
MODO REAL       →  modo de trabajo desprotegido
MODO PROTEGIDO  →  implica protección de la memoria
                    (Espacio virtual o lógico, Espacio lineal, Espacio físico)
```

> **No pueden estar activados en forma simultánea.**

### Modo Real

- Modo de operación del **80286** y CPUs compatibles con la arquitectura x86 posteriores.
- Máxima cantidad de memoria direccionable: **1 MB** (2²⁰, 20 bits).
- **No** tiene conceptos de protección de memoria ni multitarea a nivel de hardware.
- Es **unitarea**.
- No puede utilizar todos los recursos del CPU.

Cálculo de la dirección física a partir de la dirección segmentada:

```
Dirección segmentada:  3A2B:1301
Base del segmento * 10H:  3A2B · 10 = 3A2B0
+ Desplazamiento:              + 1301
Dirección física o absoluta: = 3B5B1
```

### Modo Protegido

- Modo operacional de los CPUs compatibles x86 de la serie **80286** y posteriores.
- Incorpora nuevas características para mejorar la multitarea y la estabilidad del sistema: **protección de memoria** y soporte de hardware para **memoria virtual** y **conmutación de tareas**.
- Trabaja con **tablas de descriptores de segmentos**.

### Diferencias entre los modos

1. El modo protegido permite al sistema operativo utilizar memoria virtual, paginación y multitarea.
2. Todas las CPUs de la serie 80286 y posteriores empiezan en **modo real** al encenderse el computador.
3. La arquitectura 286 introdujo el modo protegido, permitiendo la protección de memoria a nivel de hardware.
4. El modo protegido requirió instrucciones de software adicionales no necesarias previamente.
5. Por compatibilidad hacia atrás (especificación primaria de diseño de los x86), el chip 286 fue hecho para iniciarse en modo real.
6. Los sistemas operativos DOS (MS-DOS, DR-DOS, etc.) trabajan en modo real.

## 8. Direccionamiento: virtual, lineal y físico

Relación entre los 3 tipos de direccionamiento:

```
Dirección Virtual (46 bits) → Unidad de Segmentación → Dirección Lineal (32 bits) → Unidad de Paginación → Dirección Física (32 bits)
```

| VIRTUAL | LINEAL | FÍSICO |
|---|---|---|
| Abarca toda la dimensión de la memoria virtual; la maneja el programador de aplicaciones. Las direcciones se componen de un **segmento (segment)** y un **desplazamiento (offset)** → Segmentación de Memoria. | Permite referenciar hasta 2³² = **4 GB** de memoria. Tiene dispuestas todas sus posiciones en orden consecutivo/lineal → Segmentación y Paginación. | Utilizado para acceder a las celdas de memoria RAM del sistema, ligado directamente a la CPU (ej. RAM). |

### Memoria en modo protegido: espacios

```
MEMORIA VIRTUAL (DISCO) 64 TB
        ↕ (46 bits)
   DIRECCIÓN LÓGICA
        ↕ (46 bits)
   UNIDAD DE SEGMENTACIÓN → DIRECCIÓN LINEAL (32 bits)
        ↓
   UNIDAD DE PAGINACIÓN → DIRECCIÓN FÍSICA (32 bits)
        ↓
   MEMORIA FÍSICA (RAM-ROM) — 4 GB máx.
```

> Si la **Unidad de Paginación no está activada**, la Dirección Lineal es igual a la Dirección Física.

El mecanismo de direccionamiento (**MMU**) traduce la dirección virtual a física; en caso de no hallarse el elemento en memoria, se detecta su "**ausencia**" y se genera una excepción que hace que el Sistema Operativo realice la transferencia oportuna (desde memoria virtual/disco hacia memoria física).

## 9. Memoria Virtual

- Método de **organización y gestión** de la memoria que permite asignar más memoria de la que hay físicamente disponible, utilizando el almacenamiento en disco (el dispositivo de acceso directo más rápido); así se simula disponer de una memoria mucho más amplia que la real.
- Proporciona al programador de aplicaciones un **espacio mucho mayor** del que se dispone físicamente, dando a la CPU la **ilusión** de manejar mucha más memoria de la que la DRAM tiene a su disposición.
- **Dirección virtual:** dirección lógica perteneciente al espacio de direccionamiento virtual; debe traducirse ("mapeo") a dirección física.

### Dirección virtual o lógica

Se caracteriza por dos partes:

| Selector | Desplazamiento |
|---|---|

- **Selector:** selecciona un determinado segmento de la memoria virtual.
- **Desplazamiento:** determina la posición dentro del segmento.

### Funcionamiento de la memoria virtual

```
Se genera la dirección del objeto que se necesita
        ↓
       MMU  →  comprueba si el objeto se encuentra en memoria principal
        ├── SI → se accede a él normalmente
        └── NO → se comunica al S.O., que localiza el objeto en memoria
                 virtual (disco) y lo transfiere a la memoria principal
```

### Formas de organizar la Memoria Virtual

| SEGMENTACIÓN | PAGINACIÓN |
|---|---|
| División de la memoria virtual en "trozos" (**segmentos**) de **tamaños variables**. | División del espacio de direcciones de cada proceso en bloques de **tamaño uniforme**, llamados **páginas**. |

**Tipos de M.V.:** M.V. segmentada · M.V. paginada · M.V. con segmentos paginados.

### Criterios para fraccionar/reemplazar memoria

1. **FIFO** (First In – First Out): se sustituye la fracción que más tiempo lleva en memoria principal.
2. **LRU** (Last Recently Used): se sustituye la porción que lleva más tiempo sin haber sido usada.
3. **LIFO** (Last In – First Out): se sustituye la fracción que menos tiempo lleva en memoria principal.
4. **LFU** (Least Frecuently Used): se sustituye la porción a la que se accedió menos veces desde que se inició el proceso.
5. **RAND** (Random): se elige una porción al azar.
6. **CLOCK**: se coloca un bit de uso en cada entrada de una cola FIFO y se establece un puntero que se convierte en circular.

## 10. Segmentación de memoria

Es un esquema de manejo de memoria mediante el cual la estructura del programa refleja su división lógica, llevándose a cabo una agrupación lógica de la información en bloques de **tamaño variable** denominados **segmentos**, cada uno con una **longitud variable** definida por el tamaño del segmento del programa.

Cada segmento contiene información lógica del programa (subrutina, arreglo, etc.). Cada espacio de direcciones de un programa consiste en una colección de segmentos, que generalmente reflejan la división lógica del mismo.

**Ejemplo (algoritmo de cálculo de la MMU en modo real):**

```
Registro de segmento → Dirección de segmento ┐
                                              ├→ CPU
       Dirección lógica → Dirección física ───┘

SEGMENTO (en memoria, de 0 a FFFFF):
  Dirección de segmento (base)
  + Desplazamiento
  = Dirección física
```

### Segmentos de memoria (IA-32)

| Segmento | Registro base | Descripción |
|---|---|---|
| **Código** | CS | Contiene las instrucciones del programa. Se accede usando el registro **IP** como desplazamiento. |
| **Datos** | DS | Contiene los datos que utiliza el programa (variables, etc.). Se suelen usar los registros **SI** y **DI** como desplazamiento. |
| **Pila (Stack)** | SS | Almacén temporal de datos, llamadas a funciones, etc. Debe estar presente obligatoriamente en todos los programas EXE. Se usa el registro **SP** para acceder a sus datos. |
| **Extra** | ES | Uso opcional; segmento definido por el usuario, generalmente con datos adicionales. Se suelen usar los registros **SI** y **DI**. |

## 11. Unidad de Segmentación

- Su **activación es obligada**.
- Traduce las direcciones **virtuales** a **lineales**.
- El direccionamiento en el espacio lógico se realiza a través de un **selector de segmento** y un **desplazamiento**.
- Las tablas pueden contener hasta **8192** (2¹³) entradas de descriptores.
- Hay dos registros que indican el comienzo de las tablas: **GDTR** y **LDTR**.

### Selectores y descriptores

Hay dos tablas de descriptores que se utilizan con los registros de segmento:
- **Descriptores globales** (tabla **GDT**): definiciones de segmentos aplicables a todos los programas. También llamado *descriptor de sistema*.
- **Descriptores locales** (tabla **LDT**): por lo general únicos para una aplicación. También llamado *descriptor de aplicación*.

Cada tabla contiene **8192 descriptores**, por lo que hay un total de **16.384 descriptores** disponibles para una aplicación en un momento dado. Como cada descriptor describe un segmento de memoria de hasta **4 GB**, una aplicación podría tener acceso a 4G × 16.384 bytes = **64 TB**.

### Campo Selector (16 bits) y Desplazamiento (32 bits)

| Índice (13 bits) | TI (1 bit) | RPL (2 bits) |
|---|---|---|
| Número de entrada en la tabla de descriptores de segmentos. | Indica si la tabla es global (**GDT**, TI=0) o local (**LDT**, TI=1). | **Nivel de privilegio solicitado** (Requestor Privilege Level): 00 es el mayor privilegio, 11 el menor. |

```
SELECTOR (15..0): ÍNDICE (15-3) | TI (2) | RPL (1-0)
DESPLAZAMIENTO (31..0)
```

### Tabla de Descriptores de Segmentos (GDT/LDT)

- En el sistema hay dos tipos de tablas: **GDT** y **LDT**.
- La **GDT** es única para todo el sistema (Intel). Su dirección base está almacenada en el registro **GDTR** (Dirección Lineal Base + Límite).
- Puede haber más de una **LDT** en el sistema, pero solo una activa. Las LDT están descriptas dentro de la GDT.
- La dirección base de la LDT activa está almacenada en el registro **LDTR**, que es un índice a la GDT.

## 12. Descriptores de Segmentos

Contienen la dirección base y el límite del segmento en el espacio lógico. Tipos de segmentos:
- **Datos (DSD – Data Segment Descriptor)**, en GDT o LDT.
- **Código (CSD – Code Segment Descriptor)**, en GDT o LDT.
- **Estado de la tarea (TSSD – Task State Segment Descriptor)**, en GDT.

### Parámetros que determinan un segmento

1. **Base:** campo de 32 bits que contiene la dirección lineal donde comienza el segmento.
2. **Límite:** campo de 20 bits que expresa el tamaño del segmento. Como con 20 bits el tamaño máximo es 1 MB, hay un bit complementario de **granularidad (G)** que indica si el límite se expresa en bytes (G=0) o en páginas de 4 KB (G=1, hasta 4 GB).
3. **Atributo o derechos de acceso:** campo de 12 bits con las características relevantes del segmento.

### Campos del Descriptor de Segmento

| Campo | Significado |
|---|---|
| **P** (Presencia) | Indica si el segmento referenciado está cargado/presente en memoria principal (P=1) o ausente (P=0). |
| **DPL** (Nivel de privilegio, 2 bits) | Indica el privilegio del segmento (0 = mayor privilegio … 3 = menor). |
| **S** (Clase de segmento) | S=1: segmento "normal" (código, datos o pila). S=0: segmento del sistema. |
| **Tipo** | Distingue si el segmento normal es de código, dato o pila, y determina el acceso permitido (lectura/escritura/ejecución). |
| **A** (Accedido) | Se pone en 1 automáticamente cada vez que el procesador accede al segmento; el SO lo usa para el algoritmo LRU. |
| **G** (Granularidad) | Indica si el campo Límite se expresa en bytes (G=0, tamaño máx. 1 MB) o en páginas de 4 KB (G=1, tamaño máx. 4 GB). |
| **D/B** (Defecto/grande) | Distingue segmentos nativos de 32 bits (Pentium) de los de 16 bits (8086/80286), para compatibilidad. Si D=1, direcciones efectivas y operandos son de 32 bits; si D=0, de 16 bits. |
| **AVL** (Disponible) | Bit a disposición del programador/SO para diferenciar segmentos con un tipo particular de información. |

Detalle adicional de **E** (Execute, dentro de Tipo):
- **E=0** (segmento de datos): **ED**=0 se expande hacia arriba, ED=1 hacia abajo (pila); **W**=0 no puede escribirse, W=1 sí.
- **E=1** (segmento de código): **C**=0 se ignora el nivel de privilegio, C=1 se obedece; **R**=0 no puede leerse el código, R=1 sí.

### Formato del descriptor de segmento (8 bytes)

El descriptor ocupa 8 bytes, en dos posiciones de memoria de 32 bits cada una, compuestas por: **Base** (32 bits, dividida en tres tramos), **Límite** (20 bits) y **Atributos** (G, D/B, AVL, P, DPL, S, Tipo, A).

## 13. Traducción de direcciones lógicas a lineales

- El **selector** determina el tipo de tabla (GDT o LDT) mediante el bit **TI**.
- Se selecciona el descriptor correspondiente al **índice**.
- Cálculo de la posición del descriptor de segmento:

```
Dirección = (LDTR o GDTR) + ÍNDICE * 8
```

- El **8** surge porque al índice se le agregan 3 ceros: 1 bit para indicar a qué tabla corresponde (GDT o LDT) y 2 bits para el nivel de privilegio.
- Se selecciona el descriptor correspondiente con esa dirección.
- A partir del descriptor se toma la **dirección base** y se le suma el **desplazamiento**.
- Este procedimiento requiere **2 accesos a memoria**.

**Optimización (Intel):** se agregan registros **no programables** (cargados por la arquitectura) que contienen los descriptores de segmentos ya resueltos (asociados al *Segment Selector* de cada registro de segmento). Esto permite que la traducción se realice en un **solo acceso**, evitando repetir la búsqueda en la tabla de descriptores cada vez que se usa el mismo segmento.


---

## 14. Cómo resolver los ejercicios de la unidad

Esta unidad se toma con **dos tipos de ejercicio** además de la teoría. Los dos son mecánicos, así que conviene practicarlos.

### 14.1 Ejercicio tipo A: dirección física en modo real

```
Dirección física = (Registro de segmento × 10h) + Desplazamiento
```

El "× 10h" es **agregar un 0 hexa** a la derecha (equivale a 4 ceros en binario).

| Dirección segmentada | Segmento × 10h | + Desplazamiento | Física |
|---|---|---|---|
| `3A2B:1301` | `3A2B0` | `1301` | **`3B5B1`** |
| `0CD7:0105` | `0CD70` | `0105` | **`0CE75`** |
| `F000:FFF0` | `F0000` | `FFF0` | **`FFFF0`** (dirección de arranque del procesador) |

Datos útiles para preguntas conceptuales:
- La física tiene 20 bits (1 MB) y el desplazamiento tiene 16 bits, o sea que un segmento tiene como máximo 64 KB.
- **Muchas** direcciones segmentadas distintas pueden dar **la misma** física. Por ejemplo, `0000:0010` y `0001:0000` dan `00010`. Los segmentos se **superponen**.

### 14.2 Ejercicio tipo B: decodificar un selector (modo protegido)

El selector tiene **16 bits**: `ÍNDICE (13) | TI (1) | RPL (2)`.

**Ejemplo: selector `001Fh`**
```
001F = 0000 0000 0001 1111
        └─ índice ─┘     │└┘
        0000 0000 0001 1 │ 11
                        TI=1 → LDT      RPL = 3 (nivel de privilegio más bajo)
        Índice = 0000000000011 = 3
```
Entonces se usa la entrada **3** de la **LDT**. La dirección del descriptor es `LDTR + 3 × 8`.

**Ejemplo: selector `002Bh`** = `0000 0000 0010 1011` → índice 5, TI = 0 (GDT), RPL = 3. El descriptor está en `GDTR + 5 × 8`.

**Atajo:** el índice es el selector **desplazado 3 bits a la derecha** (selector ÷ 8). TI es el bit 2 y RPL son los bits 1–0.

**Después, la traducción completa:**
1. Se busca el descriptor en la tabla (GDT o LDT) con `base de la tabla + índice × 8`.
2. Se comprueba **P** (¿está presente?) y que el **desplazamiento ≤ límite**.
3. Se controlan los **privilegios** (DPL contra RPL y CPL).
4. `Dirección lineal = Base del descriptor + Desplazamiento`.
5. Si la paginación está activa, esa lineal pasa por la unidad de paginación (unidad 12).

### 14.3 Cuentas que se piden seguido

| Dato | Cuenta |
|---|---|
| Descriptores por tabla | 2¹³ = **8192** |
| Descriptores totales por tarea | GDT + LDT = **16384** |
| Memoria virtual máxima | 16384 × 4 GB = **64 TB** (46 bits: 14 de selector + 32 de desplazamiento) |
| Tamaño de un descriptor | **8 bytes** |
| Tamaño máximo con G = 0 | 2²⁰ = 1 MB |
| Tamaño máximo con G = 1 | 2²⁰ × 4 KB = **4 GB** |

---

## 15. TP de la unidad: resuelto paso a paso

> Enunciado: *U11 – Segmentación – Paginación (TP estudiantes)*. Bibliografía indicada: Cap. 8 de Angulo ("Memoria Segmentada"). Entre paréntesis queda el apartado del libro que cita cada pregunta.
> Algunas preguntas piden **dibujos**. Los esquemas de abajo están en texto. Si podés, copiá además las figuras 8.1, 8.2, 8.3, 8.5, 8.6, 8.7, 8.8, 8.9, 8.10, 8.11 y 8.17 del libro.

**1. (8.1) Organización de la memoria y su relación con el bus de datos.**
El Pentium tiene un **bus de datos de 64 bits**. Por eso la memoria física se organiza en **8 bancos de 8 bits** (un byte cada uno), que se acceden **en paralelo** y entregan 64 bits por acceso. Las líneas de dirección **A31–A3** eligen la posición dentro de los bancos, y las señales de habilitación de byte (**BE0#–BE7#**) eligen qué bancos intervienen (un byte, una palabra, una doble palabra o los 8 bytes). Esto es lo que explica el **alineamiento**: un dato de 8 bytes debe empezar en una dirección múltiplo de 8 para leerse en un solo ciclo.

**2. (8.1) Estructuras de segmento y de paginación.**
- **Segmento:** bloque de memoria de **tamaño variable** (hasta 4 GB) con información de un mismo tipo (código, datos o pila). Refleja la **estructura lógica** del programa y es visible para el programador. Su unidad de gestión es **obligatoria**.
- **Página:** bloque de **tamaño fijo** (4 KB, o 4 MB con PSE). Es **transparente** al programador y la maneja el sistema operativo. Su unidad de gestión es **optativa** (se activa con el bit PG de CR0).
- Se pueden **combinar**: los segmentos se dividen a su vez en páginas.

**3. (8.2) Modo real y características.**
Es el modo de arranque del Pentium, **compatible con el 8086**. Sus características: memoria máxima de **1 MB** (20 bits de dirección), segmentos de hasta **64 KB**, palabra de 16 bits, **sin protección** de memoria, **monotarea** y acceso directo al hardware y al BIOS.

**4. (8.2) Cómo se determina la memoria física.**
```
Física = Segmento × 16 + Desplazamiento
```
Ejemplo: `3A2B:1301` → `3A2B0 + 1301 = 3B5B1`. El segmento se toma del registro de segmento (CS para el código, SS para la pila, DS/ES/FS/GS para los datos) y el desplazamiento de IP, SP o del modo de direccionamiento del operando.

**5. (8.2) Bloque de memoria en modo real: contenidos y punteros.**
```
  FFFFF ┬──────────────────────────┐
        │                          │
        ├── Segmento de datos ─────┤ ← DS (base) + desplazamiento del operando
        ├── Segmento de pila ──────┤ ← SS (base) + SP (cima)
        ├── Segmento de código ────┤ ← CS (base) + IP (próxima instrucción)
  00000 ┴──────────────────────────┘
        (cada segmento ≤ 64 KB; base = registro × 16)
```
Los **contenidos** son código, datos y pila. Los **punteros** son los registros de segmento (bases) y los desplazamientos (IP, SP, direccionamiento de operandos).

**6. (8.2) ¿Qué es la relocalización?**
Es la posibilidad de **mover un programa a otra zona de memoria** cambiando solo el valor del **registro de segmento** (la base), sin tocar el programa. Funciona porque las direcciones internas son **desplazamientos relativos al segmento**.

**7. (8.3) Para qué se creó el modo protegido y qué espacios distingue.**
Se creó para soportar **multitarea, protección de memoria y memoria virtual**. Distingue **tres espacios**:
- **Virtual (lógico):** 46 bits (selector + desplazamiento), hasta **64 TB**. Es el que ve el programador.
- **Lineal:** 32 bits, **4 GB** consecutivos. Es el resultado de la unidad de segmentación.
- **Físico:** 32 bits, la RAM real, hasta **4 GB**.

**8. (8.3) Espacio virtual contra físico, y su problemática.**
El virtual (64 TB) es **mucho más grande** que el físico (4 GB), así que no todo lo que el programa direcciona cabe en RAM: el resto está en **disco**. La problemática es la **ausencia** de lo que no está en RAM, que el hardware debe detectar y el SO resolver.
```
 Memoria virtual (disco, 64 TB) ──46 bits──► Segmentación ──32 bits──► Paginación ──32 bits──► RAM (4 GB)
```

**9. (8.3) Unidad de segmentación y variación de la dirección lineal según la paginación.**
Traduce la dirección **virtual** de 46 bits en una dirección **lineal** de 32 bits (`base + desplazamiento`).
- **Paginación desactivada (PG = 0):** la lineal **es** la física.
- **Paginación activada (PG = 1):** la lineal se descompone en `Directorio | Tabla | Desplazamiento` y la unidad de paginación la convierte en física.
```
Virtual ─► [Seg.] ─► Lineal ─┬─ PG=0 ─────────────────► Física
                             └─ PG=1 ─► [Paginación] ──► Física
```

**10. (8.3) Quién, cómo y por qué se detecta la ausencia de memoria física.**
- **Quién:** la **MMU**, o sea el hardware (unidad de segmentación y de paginación).
- **Cómo:** consulta el bit **P** (presencia) del descriptor de segmento o de la entrada de página. Si P = 0, genera una **excepción** (#NP "segmento no presente" o #PF "fallo de página", vector 14).
- **Por qué:** el espacio virtual es mayor que la RAM, entonces parte del contenido está en disco. La excepción activa una rutina del **SO**, que trae el contenido a RAM y reintenta la instrucción.
```
Dir. virtual ─► MMU ─► ¿P = 1? ─ sí ─► acceso normal
                          └ no ─► excepción ─► SO trae de disco ─► reintenta
```

**11. (8.4) Partes de una dirección lógica.**
Es un puntero de **46 bits** (16 de selector + 32 de desplazamiento, de los cuales **14** bits del selector sirven para direccionar):
```
 ┌──────────── Selector (16) ───────────┐ ┌── Desplazamiento (32) ──┐
 │ Índice (13) │ TI (1) │ RPL (2)       │ │ posición dentro del seg. │
 └──────────────────────────────────────┘ └──────────────────────────┘
```
El **selector** elige el segmento. El **desplazamiento** elige el byte dentro del segmento.

**12. (8.4) Selector y subcampos Índice, TI y RPL.**
Es un valor de 16 bits, guardado en un registro de segmento, que **identifica** un descriptor.
- **Índice (13 bits):** número de entrada en la tabla de descriptores (de 0 a 8191).
- **TI:** 0 = tabla **GDT**, 1 = tabla **LDT**.
- **RPL:** nivel de privilegio solicitado (00 = máximo, 11 = mínimo).

**13. (8.4) Campo desplazamiento (Fig. 8.7).**
El **desplazamiento** se **suma a la base** del segmento (tomada del descriptor) para obtener la dirección lineal. Antes se comprueba que el desplazamiento **no supere el límite**; si lo supera, se genera una excepción de protección. Como son 32 bits, puede recorrer hasta 4 GB.

**14. (8.5) ¿Por qué se llama dirección lineal?**
Porque el resultado es una dirección dentro de un espacio de 32 bits en el que **todas las posiciones están ordenadas consecutivamente** (de `0` a `4 GB − 1`), sin estructura de segmentos. La unidad de segmentación **coloca** los segmentos que están presentes dentro de ese espacio lineal, cada uno a partir de su base.
```
Memoria virtual (segmentos en disco)
   seg A   seg B   seg C        Unidad de segmentación
     │       │       │     ───────────────────────────►  Espacio lineal (4 GB):
     └───────┴───────┴─►  se cargan según su Base          [ seg A ][ seg C ]...[ seg B ]
```

**15. (8.5) Ausencia de un segmento en memoria.**
El descriptor tiene **P = 0**. La unidad de segmentación genera la excepción **segmento no presente** (#NP, vector 11). El SO carga el segmento desde el disco en RAM, **actualiza el descriptor** (base nueva y P = 1) y **reinicia la instrucción**.

**16. (8.5) Descriptor en modo protegido y su formato (Fig. 8.9).**
Es una estructura de **8 bytes (64 bits)** que describe un segmento: **Base** (32 bits, partida en tres tramos), **Límite** (20 bits, partido en dos tramos) y **Atributos** (12 bits).
```
 Palabra alta (bytes 7 a 4)
 ┌───────────┬───┬─────┬───┬─────┬───────────┬───┬─────┬───┬──────┬───┬───────────┐
 │ Base 31–24 │ G │ D/B │ 0 │ AVL │ Lím 19–16 │ P │ DPL │ S │ TIPO │ A?│ Base 23–16 │
 └───────────┴───┴─────┴───┴─────┴───────────┴───┴─────┴───┴──────┴───┴───────────┘
 Palabra baja (bytes 3 a 0)
 ┌─────────────────────────────┬─────────────────────────────┐
 │        Base 15–0            │         Límite 15–0         │
 └─────────────────────────────┴─────────────────────────────┘
```
(El bit A es el último bit del campo TIPO.)

(Para el examen alcanza con listar los campos Base, Límite, G, D/B, AVL, P, DPL, S, TIPO y A, tal como aparecen en la figura de tu libro.)

**17. (8.5) Subcampos: Base, Límite, G, P, A, D/B, AVL.**
- **Base:** dirección lineal (32 bits) donde empieza el segmento.
- **Límite:** tamaño del segmento (20 bits). Se interpreta según G.
- **G (granularidad):** 0 → límite en bytes (máximo 1 MB). 1 → límite en páginas de 4 KB (máximo 4 GB).
- **P (presencia):** 1 → el segmento está en RAM. 0 → ausente (genera excepción).
- **A (accedido):** el procesador lo pone en 1 al usar el segmento. El SO lo usa para el algoritmo LRU.
- **D/B:** 1 → segmento de 32 bits (operandos y direcciones de 32 bits). 0 → de 16 bits (compatibilidad con 8086/286).
- **AVL:** bit libre para el sistema operativo.

**18. (8.5) Bit S y campo TIPO: cómo interactúan.**
- **S = 1:** segmento **normal** (código, datos o pila). TIPO se interpreta con: **E** (ejecutable), **ED/C** (expansión hacia abajo en datos, o *conforming* en código), **W/R** (escribible o legible) y **A** (accedido).
- **S = 0:** segmento **del sistema** (tareas TSS, LDT, puertas de llamada, de interrupción, de trampa). TIPO se interpreta de otra forma.
```
      S ─┬─ 1 ─► TIPO = [E | ED/C | W/R | A]   → segmento de código/datos/pila
         └─ 0 ─► TIPO = tipo de objeto del sistema (TSS, LDT, puertas)
```
Es decir: **S decide cómo se lee TIPO**.

**19. (8.7) Ambiente multitarea y áreas GDT y LDT.**
En un entorno multitarea varias tareas comparten el procesador y cada una debe estar **protegida de las demás**. Para eso hay dos áreas de descriptores. La **GDT** (global) es **única** y contiene los segmentos del sistema operativo y los comunes a todas las tareas (incluyendo los descriptores de las LDT y de los TSS); su dirección está en **GDTR**. Cada tarea tiene su propia **LDT** (local) con **sus segmentos privados**; la LDT activa se indica con **LDTR**, y al **conmutar de tarea** el procesador cambia la LDT. Una tarea no puede nombrar segmentos de otra porque no están en su tabla. Cada tabla admite hasta 8192 descriptores, y el bit **TI** del selector indica cuál se consulta.

**20. (8.8) Modo plano.**
- **Cómo se obtiene:** se cargan los descriptores de código, datos y pila con **base = 0** y **límite = 4 GB** (G = 1, límite = FFFFF). Así todos los segmentos coinciden con el espacio lineal completo, y el desplazamiento **es** la dirección lineal.
- **Ventaja:** simplicidad. Es un único espacio de 4 GB y se puede ignorar la segmentación (es lo que usan Windows y Linux).
- **Dificultad:** se pierde la **protección entre segmentos** (código, datos y pila se solapan). La protección debe hacerla la **paginación**.
```
 Segmento de código ┐
 Segmento de datos  ├─ todos: Base = 0, Límite = 4 GB ─►  [ 0 ................ 4 GB ]
 Segmento de pila   ┘                                       (mismo espacio lineal)
```
