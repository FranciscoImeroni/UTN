### [[Assets/U12_Direccionamiento_Paginacion.pdf|Diapositivas: Direccionamiento Modo Protegido - Paginación]]

# Unidad 12 – Direccionamiento en Modo Protegido: Paginación

> Basado en las diapositivas **"Direccionamiento Modo Protegido - Paginación"**.

## 1. Paginación de la Memoria: ¿por qué existe?

- La **Segmentación** es más flexible para la programación de aplicaciones, pero justamente por esa flexibilidad hace **más complicada** la administración de la memoria por parte del sistema operativo (los segmentos tienen tamaño variable).
- La **Paginación** no es tan flexible como esquema de programación porque usa **bloques del mismo tamaño**, pero a cambio hace **más sencillo** el desarrollo del algoritmo de memoria virtual.
- **UNIX** es un ejemplo de sistema operativo diseñado administrando la memoria con bloques de tamaño uniforme; la unidad de paginación fue lo que permitió ejecutar UNIX sobre procesadores **IA-32**.

## 2. Paginación: definición y características

Es una **técnica de manejo de memoria** en la cual el espacio de memoria se divide en secciones físicas de igual tamaño, denominadas **páginas**. Es la técnica utilizada en ambientes multitarea y en el uso de memoria virtual.

Los programas se dividen en unidades lógicas:
- **Páginas:** tienen el mismo tamaño que los **marcos de página** (divisiones de la memoria física).
- La paginación permite que las **direcciones lineales** sean reubicadas en **direcciones físicas** específicas utilizando bloques de tamaño fijo (páginas físicas).

Características:
- Estrategia de organización de la **memoria física**.
- Bloques de tamaño fijo: **páginas**.
- Permite que la memoria "resida" en un dispositivo de almacenamiento secundario.
- Es **manejada por el sistema operativo**.

Esta reubicación se hace por medio de **tablas**. Por razones de eficiencia, no se trata de una única tabla sino de una **estructura jerárquica de n tablas o niveles de paginación**.

> Una vez que se dispone de la estructura de tablas, se enciende el bit **PG** de **CR0** para activar la unidad de paginación, ya que la misma es **optativa** y debe ser activada explícitamente.

## 3. Paginación IA-32

- Por compatibilidad, se mantuvo el tamaño de página definido para el **80386**: **4 KBytes**. Los procesadores posteriores (ej. Pentium) también pueden trabajar con páginas de **4 MBytes**; si no se aclara lo contrario, la unidad de paginación trabaja con páginas de 4 KB.
- La elección del tipo de página se realiza mediante el bit **PSE** del registro **CR4** (propio del programador de sistemas):
  - **PSE = 1** → páginas de **4 MB**.
  - **PSE = 0** → páginas de **4 KB**.
- El espacio lineal es de **4 GBytes**, que se puede dividir completamente en **2²⁰ páginas de 4 KB**.
- Esto implicaría una tabla de descriptores de páginas enorme: 2²⁰ descriptores × 4 bytes cada uno = tabla de **4 MBytes** de memoria física.
- Para reducir ese tamaño, se arman **tablas de descriptores de paginación en 2 niveles de acceso**: **Directorios** y **Páginas**.

## 4. Unidad de Paginación

- Traduce las **direcciones lineales** de 32 bits en **direcciones físicas**, también de 32 bits.
- **Por defecto no está habilitada**; si el sistema operativo lo desea, debe setear la bandera **PG** en el registro **CR0**.

```
PG = 0 (No activada)  →  la dirección lineal coincide con la dirección física
                         (acceso directo a la memoria principal ligada a la CPU)
PG = 1 (Activada)     →  cada segmento se descompone en un número variable de páginas
                         del mismo tamaño (4KB ó 4MB), y la unidad deposita esas páginas
                         sobre la memoria física en los huecos que encuentra libres
```

## 5. Descriptor de Página

Las páginas en un procesador de 32 bits quedan definidas por 2 parámetros:
- **Base**
- **Atributos**

A diferencia del **descriptor de segmento**, el descriptor de página **no tiene campo límite**, porque el tamaño de las páginas es fijo.

- La base debe tener 32 bits, de los cuales los **12 últimos deben ser cero**, puesto que las páginas son de 4 KB y sus direcciones de inicio son múltiplos de 4 KB.
- Por lo tanto, para definir la base solo se necesitan **20 bits** (los más significativos del descriptor); los **12 bits** menos significativos corresponden a los **derechos de acceso o atributos**.
- Cada descriptor de página ocupa **32 bits (4 bytes)**, es decir, 4 posiciones de memoria en la tabla correspondiente.

```
Descriptor de Página (32 bits):
31                              12 11                    0
| NÚMERO DE PÁGINA (20 bits)      | DERECHOS DE ACCESO (12 bits) |
```

## 6. Traducción de direcciones lineales a físicas (páginas de 4 KB)

La traducción se lleva a cabo en **dos etapas**:
- **Page Directory** (Directorio de páginas): **1024 entradas (1K)**.
- **Page Table** (Tabla de páginas): **1024 entradas (1K)**.

En total se puede direccionar: `1K × 1K × 2¹² (tamaño de cada página) = 1M × 4K = 4 GB`.

- El registro **base de la tabla de Directorio de páginas** está en **CR3**.
- Los dos niveles permiten **disminuir el espacio de memoria** ocupado por las tablas.
- Las tablas deben ser **mantenidas por el sistema operativo**.
- Si hay problemas con la paginación, el impacto es mayor trabajando con **memoria virtual** (el tiempo de acceso a disco es mayor que a la RAM).

### Estructura de la dirección lineal (páginas de 4 KB)

```
DIRECCIÓN LINEAL (32 bits):
31        22 21        12 11         0
| ÍNDICE DIRECTORIO | ÍNDICE TABLA | DESPLAZAMIENTO |
|     (10 bits)      |  (10 bits)   |   (12 bits)    |
```

- **Directorio de páginas** (10 bits) → indexa **CR3** para hallar el descriptor del directorio.
- **Tabla de páginas** (10 bits) → indexa la tabla de páginas señalada por el directorio.
- **Desplazamiento** (12 bits) → posición dentro de la página de 4 KB.

Proceso:
1. **CR3** contiene la dirección base de la tabla de **Directorio de Páginas**.
2. Como cada índice tiene 10 bits, se dispone de **1024 entradas** al Directorio de Páginas.
3. Cada descriptor se obtiene leyendo 4 posiciones de memoria (4 bytes), por lo que la tabla de Directorio tiene un tamaño de **4 KB**; lo mismo ocurre con la Tabla de Páginas.
4. El espacio de RAM que se ocupa para guardar los descriptores de páginas es de **8 KB**, en **dos accesos a memoria**: uno al Directorio y otro a la Tabla de Páginas.

## 7. Traducción con páginas de 4 MB (bit PSE activado)

Cuando se activa el bit **PSE** del registro **CR4**, la dirección lineal se divide en solo **dos campos**:

```
DIRECCIÓN LINEAL (32 bits):
31        22 21                      0
| ÍNDICE DIRECTORIO | DESPLAZAMIENTO DE PÁGINA |
|    (10 bits)        |        (22 bits)         |
```

- **Índice de Directorio de Página** (10 bits más significativos).
- **Desplazamiento de página** (22 bits menos significativos) → permite moverse dentro de los 4 MB de la página.

En este caso la dirección física se obtiene con **un solo acceso a memoria**, y la única tabla (Directorio de Páginas) tiene un tamaño de **4 KB**.

## 8. Tablas de Páginas Inversa

Solución alternativa para reducir el tamaño de la tabla de páginas: en lugar de tener una entrada por cada **página virtual**, existe una entrada por cada **marco de página** de la memoria física asignada al proceso.

- La tabla inversa solo da información de las páginas guardadas en los marcos.
- Hay tantas entradas como marcos de página asignados al proceso por el S.O.
- Se utiliza en equipos **AS/400 de IBM** y en procesadores **RISC Power PC**.
- **Ventaja principal:** el tamaño de la tabla es **constante**, independiente del tamaño del proceso.

## 9. Formato de las entradas del Directorio y de las Tablas de Páginas

```
31                              12 11                                        0
| DIRECCIÓN FÍSICA DE LA PÁGINA (20) | DEFINIBLES | 0 | SIZ | D | A | PCD | PWT | U/S | R/W | P |
```

- La **base de la dirección física** de cada página queda determinada por los **20 bits más significativos**.
- Los **12 bits** menos significativos siempre son cero en la página en sí, pero en el **formato de la entrada** llevan los derechos de acceso/atributos.
- De los 12 bits de atributos, los **3 más significativos** están disponibles para el programador de sistema (información auxiliar sobre la página).
- Cualquier fallo de protección detectado por la Unidad de Paginación genera una **excepción**, que además guarda la dirección lineal que lo provocó en el registro de control **CR2**.

### Atributos de las entradas (8 bits menos significativos, de mayor a menor)

| Bit | Significado |
|---|---|
| **SIZ** | Válido solo en CR4 (se llama **PSE**); si vale 1, el Pentium trabaja con páginas de 4 MB. En las entradas de Directorio/Tabla de páginas vale 0 (no tiene valor ahí). Procesadores anteriores al Pentium solo manejan páginas de 4 KB. |
| **D** (bit sucio / *Dirty*) | Si vale 1, la página fue escrita; para eliminarla de la RAM primero debe salvarse en memoria virtual. |
| **A** (Accedido) | Se pone en 1 cada vez que se accede a la página; lo maneja el SO para contar accesos. |
| **PCD** (aceptación de caché) | Indica si la página es cacheable. |
| **PWT** (escritura obligada) | Además de cacheable, funciona en modo de escritura obligada (*write-through*). |
| **U/S** (Usuario/Supervisor) | Indica el nivel de privilegio de la página (0 = Usuario). |
| **R/W** (Lectura/Escritura) | R/W=1 → accesible en lectura y escritura. R/W=0 → solo lectura. |
| **P** (Presencia) | P=1 → página cargada en memoria física. P=0 → al acceder, la CPU genera una **excepción de fallo de página**, que activa una rutina del SO que trae la página desde disco a la RAM; una vez cargada, el SO pone P=1, escribe los 20 bits de la dirección base y actualiza el resto de los atributos. |

## 10. Fallo de Páginas (Page Fault)

- Si el flag de **presencia (P)** no está encendido, la unidad de paginación carga la dirección lineal en el registro **CR2** y genera la **excepción número 14**.

```
Formato del registro de error (bits bajos):
6  5  4  3  2  1  0
D  A  PCD PWT U W P
      ↓
   Present · Writable · User defined · Write-through · Cache disable · Accessed · Dirty
```

**Page fault:** excepción arrojada cuando se requiere una dirección que no se encuentra en memoria en ese momento. Secuencia:
1. Emite la excepción.
2. Guarda el **IP** y la **pila**.
3. Determina que es de tipo fallo de página y llama a la rutina correspondiente.
4. Averigua qué dirección virtual se estaba buscando.
5. Chequea que sea una dirección **válida**.
6. Selecciona un **marco** libre; si no hay, se libera uno mediante el algoritmo de reemplazo.

## 11. Paginación por demanda

- Los procesos residen en **disco** y se traen a memoria principal solo **cuando se necesitan** (*carga perezosa* / "cargador perezoso").
- **Algoritmos de reemplazo** de páginas: **FIFO**, **Second Chance**, **NRU** (Not Recently Used).

## 12. Trashing y predicción anticipada

- **Trashing (hiperpaginación):** situación en la que se utiliza una cantidad de recursos cada vez mayor para hacer una cantidad de trabajo cada vez menor. El procesador pasa el tiempo llevando y trayendo páginas → **reduce el rendimiento**.
- **Predicción anticipada:** algoritmos que intentan adivinar qué páginas van a usarse próximamente, basados en la **historia reciente** y el **principio de cercanía de referencias** (localidad).

## 13. Paginación y Segmentación combinada

Esquema completo usado en modo protegido IA-32: primero se traduce la **dirección virtual** (Segmento + Desplazamiento) a **dirección lineal** mediante la **Unidad de Segmentación** (usando la Tabla de Descriptores/Segmentos); luego la dirección lineal se traduce a **dirección física** mediante la **Unidad de Paginación** (Directorio de Páginas + Tabla de Páginas).

```
Dirección Virtual (Segmento | Desplazamiento)
        ↓  Unidad de Segmentación (Tabla de Descriptores/Segmentos)
Dirección Lineal (Directorio | Tabla | Desplazamiento)
        ↓  Unidad de Paginación (Directorio de Páginas → Tabla de Páginas)
Dirección Física
        ↓
Memoria Principal
```

- El registro **CR3** apunta al **Directorio de Tablas de Páginas**.
- Cada entrada del Directorio (**PDE**, Page Directory Entry) apunta a una **Tabla de Páginas**.
- Cada entrada de la Tabla de Páginas (**PTE**, Page Table Entry) apunta a una **página física** dentro del **espacio físico de direcciones**.

## 14. TLB (Translation Lookaside Buffer)

- Es una **pequeña memoria caché** que almacena traducciones de direcciones lineales a físicas ya resueltas.
- Sin TLB, cada traducción requiere **2 accesos a memoria**: uno al **Directorio** y otro a la **Tabla de páginas**.
- Funcionamiento: cada vez que la **MMU** debe hacer una traducción:
  - La busca primero en la **TLB**. Si ya está resuelta → se ahorran los dos accesos a memoria.
  - Si no estaba resuelta, la resuelve accediendo a las tablas y **la guarda en la TLB** para el futuro.
- Una TLB de **32 entradas** proporciona un **97% de aciertos** (*hits*).
- El **Pentium** tiene **2 TLBs de 32 entradas**: una para la **caché de datos** y otra para la **caché de instrucciones**.

### Esquema completo: de la dirección lógica a la dirección física con TLB

```
Dirección Lógica IA-32 (Selector | Offset)
        ↓  Unidad de Segmentación
Dirección Lineal (Directorio | Tabla | Offset)
        ↓
   ¿Está resuelta en la TLB? ──► SÍ → usa Dir.FísicaBase + Offset = Dirección Física
        │
        NO
        ↓
   Resuelve vía Directorio + Tabla de Páginas, guarda el resultado en la TLB
        ↓
Dirección Física (Dir.FísicaBase | Offset)
```


---

## 15. Cómo resolver los ejercicios de la unidad

### 15.1 Ejercicio tipo A: traducir una dirección lineal (páginas de 4 KB)

La dirección lineal de 32 bits se parte así: `Directorio (10) | Tabla (10) | Desplazamiento (12)`.

**Ejemplo: dirección lineal `00C0A123h`**

1. **A binario**, de a 4 bits: `0000 0000 1100 0000 1010 0001 0010 0011`.
2. **Cortar** en 10 + 10 + 12 bits:
   - Directorio: `0000 0000 11` = `3`.
   - Tabla: `00 0000 1010` = `0Ah` (10).
   - Desplazamiento: `0001 0010 0011` = `123h`.
3. **Primer acceso a memoria:** se lee la entrada 3 del directorio en `CR3 + 3 × 4`. Ahí está la base de la tabla de páginas.
4. **Segundo acceso:** se lee la entrada `0Ah` de esa tabla en `base de tabla + 0Ah × 4`. Ahí está la base del marco (los 20 bits altos).
5. **Dirección física** = `base del marco (20 bits) + desplazamiento (12 bits)`.
   - Si la entrada dijera `00555h`, la física sería `00555000h + 123h` = **`00555123h`**.

**Atajos:**
- Directorio = lineal **>> 22**.
- Tabla = (lineal **>> 12**) AND `3FFh`.
- Desplazamiento = lineal AND `FFFh`.
- Se multiplica por **4** porque cada entrada ocupa 4 bytes.

**Páginas de 4 MB (PSE = 1):** se parte `Directorio (10) | Desplazamiento (22)`. Hay **un solo acceso**, y el desplazamiento es de 22 bits.

### 15.2 Cuentas típicas

| Dato | Cuenta |
|---|---|
| Páginas de 4 KB en 4 GB | 2³² / 2¹² = 2²⁰ = **1 M páginas** |
| Entradas por tabla | 2¹⁰ = **1024** |
| Tamaño de directorio o de tabla | 1024 × 4 B = **4 KB** |
| Memoria que ocupan las tablas (mínimo, un proceso) | 4 KB + 4 KB = **8 KB** (en lugar de 4 MB con una sola tabla plana) |
| Tabla plana para 4 GB | 2²⁰ × 4 B = **4 MB** (por eso se usan dos niveles) |
| Accesos a memoria por referencia **sin TLB** | **3** (directorio + tabla + el dato) |

### 15.3 Ejercicio tipo B: tiempo promedio con TLB

La cuenta sigue la misma lógica que el tiempo medio de la caché (unidad 13).

**Ejemplo:** acierto en TLB del 97%, `t_TLB` = 1 ns, `t_memoria` = 50 ns.

- **Sin TLB:** 3 accesos × 50 ns = **150 ns**.
- **Con TLB:**
  - Acierto: `1 + 50` = 51 ns.
  - Fallo: `1 + 2 × 50 + 50` = 151 ns (consulta la TLB, lee directorio y tabla, y luego el dato).
  - Promedio: `0,97 × 51 + 0,03 × 151` = 49,47 + 4,53 = **54 ns**.

---

## 16. TP de la unidad: resuelto paso a paso

> Enunciado: *U12 – Paginación (TP estudiantes)*. Bibliografía: Cap. 9 de Angulo ("Mecanismo de Paginación").

**1. (9.1) ¿Qué es la paginación? Diferencia con la segmentación, ventajas e inconvenientes.**
Es una técnica de manejo de memoria que divide el espacio en bloques **de igual tamaño** (páginas de 4 KB, o de 4 MB con PSE) y los ubica en la memoria física mediante tablas. La **segmentación** usa bloques de **tamaño variable** y refleja la estructura lógica del programa.

| | Segmentación | Paginación |
|---|---|---|
| Tamaño | Variable | **Fijo** |
| Visible para el programador | **Sí** | No (transparente) |
| Activación | Obligatoria | **Optativa** (bit PG de CR0) |
| Fragmentación | Externa | Interna (última página a medio llenar) |

- **Ventajas de la paginación:** no hay fragmentación externa y el SO la gestiona con un algoritmo simple. Facilita la **memoria virtual**, porque se mueven páginas iguales entre RAM y disco.
- **Inconvenientes:** hace falta mantener **tablas** (ocupan memoria) y cada traducción necesita **accesos extra** a memoria (lo resuelve la TLB). No refleja la división lógica del programa. Si hay muchos fallos de página se cae el rendimiento (*trashing*).

**2. (9.2) Resuma el mecanismo de paginación.**
1. La unidad de segmentación entrega una **dirección lineal** de 32 bits.
2. Se la divide en `Directorio (10) | Tabla (10) | Desplazamiento (12)`.
3. **CR3** apunta al directorio. El índice de directorio elige una **entrada de directorio**, que apunta a una **tabla de páginas**.
4. El índice de tabla elige una **entrada de tabla**, que contiene la **base del marco** (20 bits).
5. `Dirección física = base del marco + desplazamiento`.
Son **dos accesos a memoria** por traducción (uno de ellos desaparece si la TLB acierta). Con PSE = 1 (páginas de 4 MB) hay un solo acceso.

**3. (9.2) ¿Qué es el fallo de página y cómo lo resuelve la unidad de paginación?**
Ocurre cuando la página pedida **no está en RAM** (bit **P = 0**) o no se puede acceder. La unidad de paginación guarda la dirección lineal en **CR2**, deja un código de error y genera la **excepción 14**. La rutina del SO:
1. Comprueba que la dirección sea **válida**.
2. Busca un **marco libre** (si no hay, desaloja una página con un algoritmo de reemplazo; si estaba modificada, **bit D = 1**, antes la graba en disco).
3. **Trae la página** desde el disco.
4. Actualiza la entrada (P = 1, base y atributos).
5. **Reinicia la instrucción** que falló.

**4. (9.2) ¿Cómo se resuelve la protección a nivel de página?**
Con los atributos de cada entrada, que se controlan en **cada acceso**:
- **U/S** (usuario o supervisor): define quién puede usar la página.
- **R/W:** `0` solo lectura, `1` lectura y escritura.
- **P:** si vale 0 la página no está accesible (fallo de página).
Si un acceso viola alguno, se genera una **excepción 14** (con el código de error indicando la causa) y la dirección en CR2. A diferencia de la segmentación (4 niveles de privilegio), en paginación hay **dos**: supervisor y usuario.

> Ojo: en la figura de las diapositivas el bit U/S aparece como "0 = Usuario". En el manual de Intel es al revés: **U/S = 1 es usuario** y 0 es supervisor. Si te lo preguntan, contestá lo que dice tu libro de cátedra.

**5. (9.3) Estructura de una entrada de directorio o de tabla de páginas.**
Tiene **32 bits**:
```
31                          12 11  9  8   7    6  5   4    3    2    1   0
┌────────────────────────────┬──────┬───┬────┬──┬───┬────┬────┬────┬────┬───┐
│ Dirección base de la página│ DISP │ 0 │SIZ │ D│ A │PCD │PWT │U/S │R/W │ P │
│          (20 bits)         │ (3)  │   │    │  │   │    │    │    │    │   │
└────────────────────────────┴──────┴───┴────┴──┴───┴────┴────┴────┴────┴───┘
```
| Bit | Significado |
|---|---|
| **P** | Presente en memoria (1) o no (0, genera fallo de página). |
| **R/W** | Lectura y escritura (1) o solo lectura (0). |
| **U/S** | Nivel: usuario o supervisor. |
| **PWT** | Escritura obligada (*write-through*) en la caché. |
| **PCD** | Cacheabilidad: desactiva la caché para esa página. |
| **A** | Accedido: se pone en 1 en cada uso. Sirve al SO para el reemplazo. |
| **D** | *Dirty* (sucia): la página fue escrita, así que antes de sacarla de RAM hay que guardarla en disco. |
| **SIZ** | Tamaño (4 MB si vale 1, solo con PSE). |
| **3 bits libres** | Disponibles para el SO. |

**6. (9.4) Función de la TLB y por qué se usa.**
La **TLB** (*Translation Lookaside Buffer*) es una **pequeña caché de traducciones** de dirección lineal a física. Se usa porque sin ella **cada acceso** a memoria necesitaría **2 accesos extra** (directorio y tabla), o sea 3 accesos en total, lo que haría el sistema mucho más lento. Con una TLB de **32 entradas** se logra cerca de un **97% de aciertos**.

**7. (9.5) ¿Cómo funciona la TLB?**
1. La MMU recibe una dirección lineal y **busca** en la TLB si ya tiene resuelto ese número de página (`Directorio + Tabla`).
2. **Acierto:** toma la base del marco guardada y le suma el desplazamiento, **sin acceder** a las tablas.
3. **Fallo:** recorre las tablas (CR3 → directorio → tabla), obtiene la base del marco, **guarda** la traducción en la TLB (reemplazando una entrada si está llena) y sigue con el acceso.
4. Si en las tablas **P = 0**, se produce el fallo de página.

**8. (9.5) Diagrama de flujo de las dos TLB (instrucciones y datos).**
El Pentium tiene **dos cachés L1 independientes** (instrucciones y datos), y cada una tiene su **TLB propia** (32 entradas cada una), lo que permite **traducir en paralelo** una búsqueda de instrucción y un acceso a datos.
```
CPU genera dirección lineal
          │
   ┌──────┴───────┐
   │ ¿Instrucción │
   │   o dato?    │
   └──┬────────┬──┘
 instr│        │dato
      ▼        ▼
 TLB de      TLB de
 instruc.     datos
      │        │
   ¿Acierto?  ¿Acierto?
   sí│  no     sí│  no
     │   ▼       │   ▼
     │  Recorre tablas (CR3 → Directorio → Tabla)
     │   │  ¿P = 1? ── no ──► Fallo de página (excepción 14) → SO
     │   ▼ sí
     │  Guarda la traducción en la TLB correspondiente
     ▼   ▼
 Dirección física ──► Caché L1 de instrucciones / de datos
```
