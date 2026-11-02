# Guía de examen: tipos de ejercicio, mapa de la PC y fórmulas (unidades 8 a 13)

> Esta guía junta tres cosas: **(1)** los tipos de ejercicio que conviene saber resolver, **(2)** un dibujo de la PC y la CPU donde encaja cada concepto, y **(3)** todas las fórmulas con un ejemplo resuelto.
> Importante: **no conozco el parcial real**. Armé los tipos de ejercicio a partir de los TPs, de las diapositivas y de `TEMAS.md`. Son los que más se repiten en el material, no una predicción segura.
> Notas completas: [[Resumen Unidad 8 Microprocesadores - Registros]] · [[Resumen Unidad 9 Microprocesadores - Interrupciones]] · [[Resumen Unidad 10 Microprocesadores - Instrucciones]] · [[Resumen Unidad 11 Segmentación]] · [[Resumen Unidad 12 Direccionamiento en Modo Protegido - Paginación]] · [[Resumen Unidad 13 Memoria Cache y Entrada Salida]] · [[Plan de Repaso Segundo Parcial]]

---

## Parte 1: tipos de ejercicio que hay que saber hacer

### Ejercicios numéricos (los que más puntos suelen dar)

| # | Tipo de ejercicio | Unidad | Qué hay que hacer | Dificultad |
|---|---|---|---|---|
| 1 | **Dirección física en modo real** | 8, 11 | `Seg × 10h + Desplazamiento` | Fácil |
| 2 | **Decodificar un selector** | 11 | Sacar índice, TI y RPL; ubicar el descriptor en GDT o LDT | Media |
| 3 | **Dirección lineal con descriptor** | 11 | `Base + desplazamiento`, comprobando el límite y la granularidad | Media |
| 4 | **Traducir una dirección lineal con paginación** | 12 | Cortar 10 + 10 + 12 bits, calcular las direcciones de las entradas y la dirección física | Media/alta |
| 5 | **Tiempo medio con TLB** | 12 | Ponderar acierto y fallo | Media |
| 6 | **Dirección del vector de interrupción** | 9 | `n × 4` y `CS × 10h + IP` de la rutina | Fácil |
| 7 | **Flags después de una operación** | 8, 10 | Hacer `ADD`/`SUB`/`CMP`/`AND`/`ROL` y decir ZF, SF, CF, OF, PF | Media |
| 8 | **Seguir un programa en DEBUG** | 10 | Registros, memoria (little endian), pila, `MUL`/`DIV` | Media |
| 9 | **Campos de la dirección en la caché** | 13 | Etiqueta, línea o conjunto y posición, para cada organización | Media/alta |
| 10 | **Armar direcciones en hexa y sacar tamaños** | 13 | Concatenar bits, agrupar de a 4, calcular MP y tamaño de la caché | Media/alta |
| 11 | **Tiempo medio, factor de velocidad y eficacia** | 13 | Fórmulas de la caché (serie y paralelo) | Fácil/media |
| 12 | **Armar memoria con módulos** | 11, 13 | Cuántos módulos y qué bits los seleccionan | Fácil/media |

### Ejercicios teóricos (preguntas de desarrollo o de elegir)

| Tema | Qué conviene poder explicar |
|---|---|
| **Registros** (U8) | Para qué sirve cada registro general, los 6 de segmento y cada flag. Modo real contra protegido. |
| **Interrupciones** (U9) | Interrupción contra excepción, enmascarable contra no enmascarable, IDT, IDTR, las fases de atención. |
| **Instrucciones** (U10) | Los modos de direccionamiento con un ejemplo de cada uno. Qué hacen `CALL`, `RET`, `INT`, `IRET`, `LOOP`. |
| **Segmentación** (U11) | Descriptor (campos), GDT contra LDT, modo plano, memoria virtual contra física. |
| **Paginación** (U12) | Fallo de página, atributos de las entradas (P, R/W, U/S, A, D), TLB, PSE. |
| **Caché** (U13) | Las 3 organizaciones con ventajas y desventajas, reemplazo (RANDOM y LRU), escritura (inmediata, diferida, obligada), serie contra paralelo, MESI. |
| **E/S** (U13) | Las 4 modalidades, cuál usa menos CPU, pasos del DMA, registros REP, RB y RDI. |

### Orden de práctica recomendado

1. Primero los **fáciles que suman seguro**: 1, 6, 11, 12.
2. Después los **de proceso largo**: 4 (paginación) y 9 y 10 (caché). Hacé cada uno **3 veces con números distintos**.
3. Por último los de **flags y DEBUG** (7 y 8), que se aprenden leyendo volcados.

---

## Parte 2: mapa de la PC y la CPU

> Léelo de arriba hacia abajo: es el camino que sigue un dato o una instrucción. Los números entre corchetes `[U8]`, `[U11]`, etc. indican en qué unidad se estudia cada pieza.

```
┌──────────────────────────────────────────────────────────────────────────────────────┐
│                                        CPU (PENTIUM)                                 │
│                                                                                      │
│  ┌──────────────────────────────┐        ┌───────────────────────────────────────┐   │
│  │ UNIDAD DE CONTROL (UC)  [U7] │        │ UNIDAD ARITMÉTICO-LÓGICA (ALU)   [U7] │   │
│  │ ─ busca y decodifica         │        │ ─ suma, resta, AND, OR, desplaz.      │   │
│  │ ─ genera las órdenes         │        │ ─ actualiza los FLAGS                 │   │
│  │ ─ ciclo: FETCH → EXECUTE     │        └───────────────────────────────────────┘   │
│  │ ─ al final de cada instr.:   │                                                    │
│  │   ¿hay interrupción? [U9]    │        ┌───────────────────────────────────────┐   │
│  └──────────────────────────────┘        │ REGISTROS GENERALES              [U8] │   │
│                                          │ EAX acumulador   EBX base             │   │
│  ┌──────────────────────────────┐        │ ECX contador     EDX datos / puertos  │   │
│  │ EIP / IP  [U8]               │        │ ESP puntero pila EBP base de pila     │   │
│  │ próxima instrucción          │        │ ESI/EDI índices de cadenas            │   │
│  │ (el IP apunta a la SIGUIENTE)│        └───────────────────────────────────────┘   │
│  └──────────────────────────────┘                                                    │
│                                          ┌───────────────────────────────────────┐   │
│  ┌──────────────────────────────┐        │ EFLAGS  [U8]                          │   │
│  │ MAR / MDR / IR  [U7]         │        │ CF  ZF  SF  OF  PF  AF   (estado)     │   │
│  │ MAR: dirección a la memoria  │        │ IF  TF  DF               (control)    │   │
│  │ MDR: dato que viaja          │        └───────────────────────────────────────┘   │
│  │ IR : instrucción (COP+DATA)  │                                                    │
│  └──────────────────────────────┘                                                    │
│                                                                                      │
│  ┌──────────────────────────────────────────────────────────────────────────────┐    │
│  │ REGISTROS DE SEGMENTO  [U8, U11]:   CS (código)  SS (pila)  DS ES FS GS (datos)│   │
│  │ Cada uno guarda un SELECTOR:  | Índice (13) | TI (1) | RPL (2) |               │   │
│  └──────────────────────────────────────────────────────────────────────────────┘    │
│                  │ selector + desplazamiento  =  DIRECCIÓN VIRTUAL / LÓGICA (46 bits)│
│                  ▼                                                                   │
│  ┌──────────────────────────────────────────────────────────────────────────────┐    │
│  │ UNIDAD DE SEGMENTACIÓN  [U11]   (obligatoria)                                │    │
│  │  ─ busca el DESCRIPTOR en la tabla:  GDT (global, GDTR)  o  LDT (local, LDTR)│    │
│  │  ─ descriptor de 8 bytes:  Base(32) Límite(20) G  D/B  AVL  P  DPL  S  TIPO A│    │
│  │  ─ comprueba P (¿presente?), límite y privilegios                            │    │
│  │  ─ DIRECCIÓN LINEAL (32 bits) = Base + Desplazamiento                        │    │
│  │  ─ Modo REAL (sin descriptor):  FÍSICA = Segmento × 10h + Desplazamiento     │    │
│  └──────────────────────────────────────────────────────────────────────────────┘    │
│                  │ dirección lineal (32 bits, 4 GB)                                  │
│                  ▼                                                                   │
│  ┌──────────────────────────────────────────────────────────────────────────────┐    │
│  │ UNIDAD DE PAGINACIÓN  [U12]   (optativa: bit PG de CR0)                      │    │
│  │   PG = 0  →  lineal = física                                                 │    │
│  │   PG = 1  →  | Directorio (10) | Tabla (10) | Desplazamiento (12) |          │    │
│  │      CR3 → Directorio de páginas → Tabla de páginas → marco → FÍSICA         │    │
│  │      Entradas: P R/W U/S PWT PCD A D SIZ  (+3 bits libres)                   │    │
│  │      TLB (32 entradas, 97% de aciertos): evita los 2 accesos extra           │    │
│  │      Fallo de página (P = 0): excepción 14, dirección en CR2, el SO la trae  │    │
│  │      CR4.PSE = 1 → páginas de 4 MB (Directorio 10 + Desplazamiento 22)       │    │
│  └──────────────────────────────────────────────────────────────────────────────┘    │
│                  │ dirección FÍSICA (32 bits, hasta 4 GB)                            │
│                  ▼                                                                   │
│  ┌──────────────────────────────────────────────────────────────────────────────┐    │
│  │ CACHÉ L1  [U13]   (SRAM, 3 a 10 ns)   — separada: instrucciones | datos      │    │
│  │   ┌──────────────────┐   ┌────────────────────────────────────────────┐      │    │
│  │   │ Directorio       │   │ Bloque de datos (líneas de 32 bytes)       │      │    │
│  │   │ (etiquetas)      │   │ Dirección:  Etiqueta | Conjunto | Posición │      │    │
│  │   └──────────────────┘   └────────────────────────────────────────────┘      │    │
│  │   Organización: totalmente asociativa / 1 vía / N vías                       │    │
│  │   Acierto → entrega el dato.   Fallo → busca en MP (o en L2 / L3)            │    │
│  │   Reemplazo: RANDOM, LRU.   Coherencia entre CPUs: MESI                      │    │
│  └──────────────────────────────────────────────────────────────────────────────┘    │
└───────────────────────────────────────────┬──────────────────────────────────────────┘
                                            │
        ═══════════════════ BUS DEL SISTEMA  [U13] ═════════════════════════════
        │ direcciones      │ datos (64 bits)      │ control (RD, WR, IRQ, ...) │
        ═════════╤══════════════════╤════════════════════╤═══════════════════════
                 │                  │                    │
                 ▼                  ▼                    ▼
   ┌──────────────────────┐  ┌─────────────────────────────┐  ┌──────────────────────┐
   │ MEMORIA PRINCIPAL    │  │ MÓDULOS DE E/S  [U13]       │  │ CONTROLADOR DMA      │
   │ (DRAM)  [U11]        │  │  Interfaz: REP, RB, RDI     │  │  [U13]               │
   │  Dirección física     │  │  Controlador del periférico │  │  maestro del bus     │
   │  Memoria en MÓDULOS   │  │  Modalidades:               │  │  transfiere E/S ↔ MP │
   │  (decodificador       │  │   • programada (polling)    │  │  sin pasar por CPU   │
   │   selecciona módulo)  │  │   • por interrupción (IRQ)  │  │  robo de ciclo       │
   │  Mapa de memoria      │  │   • DMA   • canal (IOP)     │  │  avisa al final con  │
   └──────────────────────┘  └─────────────┬───────────────┘  │  una interrupción    │
                                           │                  └──────────────────────┘
                                           ▼
                                  PERIFÉRICOS (teclado, disco, ...)
                                           │
                                           │  IRQ#  (interrupción externa)
                                           ▼
   ┌──────────────────────────────────────────────────────────────────────────────┐
   │ INTERRUPCIONES  [U9]                                                         │
   │  Al final de cada instrucción la CPU mira si hay una pendiente:              │
   │   1. apila FLAGS, CS, IP     2. IF = 0, TF = 0                               │
   │   3. busca el vector n en la IDT (IDTR)      4. salta a la rutina (SAI)      │
   │   5. IRET restaura y vuelve                                                  │
   │  Modo real: IDT en 0000:0000, dirección del vector = n × 4 (límite 03FF)     │
   │  Tipos: externas (enmascarables / NMI), internas (INT n), excepciones        │
   │  Pila [U9, U10]: PUSH baja SP en 2 / POP sube SP en 2                        │
   └──────────────────────────────────────────────────────────────────────────────┘
```

### Cómo se conecta todo: la historia de un acceso a memoria

1. La **UC** busca una instrucción (el **EIP** dice cuál). La instrucción llega al **IR** y la **UC** la decodifica. El **EIP** pasa a apuntar a la siguiente.
2. Si la instrucción necesita un dato, se arma la **dirección lógica**: un **selector** (de un registro de segmento) más un **desplazamiento**.
3. La **unidad de segmentación** usa el selector para buscar el **descriptor** en la **GDT o LDT**. Con la base del descriptor más el desplazamiento obtiene la **dirección lineal**.
4. Si la paginación está activa, la **unidad de paginación** convierte la lineal en **física** con **CR3**, el directorio y la tabla. La **TLB** acelera esto.
5. La **caché** busca esa dirección física: compara la **etiqueta** y, si acierta, entrega el dato. Si falla, se pide a la **memoria principal** por el **bus**.
6. La **ALU** opera con el dato y actualiza los **flags**.
7. Al terminar, la CPU revisa si hay una **interrupción** pendiente. Si la hay, usa la **IDT** y la **pila**.
8. Para la **entrada y salida**, la CPU puede encargarse (programada), recibir un aviso (interrupción) o delegar al **DMA**.

---

## Parte 3: fórmulas y ejemplos

> Cada fórmula tiene un ejemplo resuelto. Hacelos de nuevo tapando la solución.

### 3.1 Dirección física en modo real (U8, U11)

```
Física = Segmento × 10h + Desplazamiento
```
**Ejemplo:** `0CD7:0105`
- `0CD7 × 10h` = `0CD70`
- `0CD70 + 0105` = **`0CE75`**

### 3.2 Números con signo y complemento a 2 (base)

```
Cambiar de signo = invertir los bits y sumar 1
Rango con n bits (con signo) = −2ⁿ⁻¹ … 2ⁿ⁻¹ − 1
```
**Ejemplo:** pasar `−100` a 8 bits.
- `100` = `64h` = `0110 0100`
- Invertir: `1001 1011` (`9Bh`); sumar 1: `1001 1100` = **`9Ch`**.

### 3.3 Selector y descriptor (U11)

```
Índice = selector >> 3          TI = bit 2          RPL = bits 1 y 0
Dirección del descriptor = (GDTR o LDTR) + Índice × 8
Dirección lineal = Base + Desplazamiento        (siempre Desplazamiento ≤ Límite)
Tamaño del segmento = (Límite + 1) × 1       si G = 0 (bytes)
                    = (Límite + 1) × 4 KB    si G = 1 (páginas)
```
**Ejemplo 1:** selector `002Bh` = `0000 0000 0010 1011`
- Índice = 5, TI = 0 (GDT), RPL = 3.
- Descriptor en `GDTR + 5 × 8` = `GDTR + 28h`.

**Ejemplo 2:** descriptor con `Base = 00400000h`, `Límite = 000FFh`, `G = 1` y desplazamiento `00001234h`.
- Tamaño = `(255 + 1) × 4 KB` = `1 MB` = `100000h`. El desplazamiento está dentro del límite.
- Lineal = `00400000h + 00001234h` = **`00401234h`**.

### 3.4 Paginación de 4 KB (U12)

```
Directorio = lineal >> 22
Tabla      = (lineal >> 12) AND 3FFh
Desplaz.   = lineal AND FFFh

Entrada de directorio en  CR3 + Directorio × 4
Entrada de tabla      en  Base de tabla + Tabla × 4
Física = Base del marco (20 bits) + Desplazamiento (12 bits)
```
**Ejemplo:** lineal `00C0A123h`
- Directorio = `3`, Tabla = `0Ah`, Desplazamiento = `123h`.
- Entrada del directorio: `CR3 + 3 × 4` = `CR3 + 0Ch`.
- Entrada de la tabla: `base de tabla + 0Ah × 4` = `base de tabla + 28h`.
- Si esa entrada tiene la base `00555h`, entonces **física = `00555123h`**.

**Con páginas de 4 MB (PSE = 1):** `Directorio (10) + Desplazamiento (22)`.
- Para `00C0A123h`: Directorio = `3`, Desplazamiento = `00A123h`. Hay un solo acceso.

```
Cantidad de páginas de 4 KB en 4 GB = 2³² / 2¹² = 2²⁰ = 1 M
Entradas por tabla = 1024 · Tamaño de cada tabla = 1024 × 4 B = 4 KB
Tabla plana para 4 GB = 2²⁰ × 4 B = 4 MB   (por eso se usan 2 niveles: 4 KB + 4 KB)
```

### 3.5 Tiempo medio con TLB (U12)

```
t medio = h × (t_TLB + t_mem) + (1 − h) × (t_TLB + 3 × t_mem)
Sin TLB = 3 × t_mem
```
**Ejemplo:** h = 97%, `t_TLB` = 1 ns, `t_mem` = 50 ns.
- Acierto: `1 + 50` = 51 ns. Fallo: `1 + 3 × 50` = 151 ns.
- `0,97 × 51 + 0,03 × 151` = 49,47 + 4,53 = **54 ns** (contra 150 ns sin TLB).

### 3.6 Interrupciones en modo real (U9)

```
Dirección del vector n = n × 4
Rutina = CS × 10h + IP    (CS e IP se leen de n × 4 y n × 4 + 2)
Tamaño de la IDT = 256 × 4 = 1024 bytes = 400h   → límite del IDTR = 03FFh
```
**Ejemplo:** `INT 21h`
- Vector en `21h × 4` = **`84h`** (`0000:0084`).
- Si ahí hay `IP = 0100h` y `CS = 0200h`, la rutina está en `0200h × 10h + 0100h` = **`02100h`**.

### 3.7 Pila (U9, U10)

```
PUSH:  SP ← SP − 2  y luego escribe      POP:  lee y luego SP ← SP + 2
```
**Ejemplo:** `SP = FFEE`, `PUSH AX` (con `AX = D5D6`).
- `SP` queda en `FFEC`. En `FFEC` se guarda `D6` y en `FFED` se guarda `D5` (little endian).

### 3.8 Flags en `ADD` y `SUB` de 16 bits (U8, U10)

```
CF = acarreo del resultado sin signo        OF = overflow con signo
ZF = resultado cero    SF = bit más alto    PF = cantidad PAR de unos del byte bajo
```
**Ejemplo:** `ADD AX,DX` con `AX = 7FFF` y `DX = 0001`.
- Resultado `8000h`.
- **CF = 0** (no se pasa de 16 bits), **OF = 1** (dos positivos dieron negativo), **SF = 1**, **ZF = 0**.

### 3.9 `MUL` y `DIV` (U10)

```
MUL r16:   DX:AX = AX × r16          MUL r8:   AX = AL × r8
DIV r8:    AL = cociente, AH = resto  (dividendo en AX)
```
**Ejemplo:** `7 ÷ 3` con `DIV BL` → **AL = 02**, **AH = 01**.
**Ejemplo:** `FFFF × FFFF` → `DX:AX = FFFE:0001`.

### 3.10 Capacidad de memoria y módulos (U11, U13)

```
Posiciones = 2ⁿ (n = bits de dirección)         Bits = log₂(capacidad en bytes)
Nº de módulos = (capacidad total / capacidad módulo) × (ancho total / ancho módulo)
Bits que seleccionan el módulo = bits del bus − bits que usa cada módulo
```
**Ejemplo:** memoria de `16K × 16` con módulos de `8K × 8`, bus de 14 bits.
- Módulos = `(16/8) × (16/8)` = **4**.
- Cada módulo usa 13 bits (2¹³ = 8K). Sobra **1 bit** que selecciona el módulo.

### 3.11 Caché: tiempos (U13)

```
Factor de velocidad = t_MP / t_caché
t medio (serie)     = α × tc + (1 − α) × (tc + tp)
t medio (paralelo)  = α × tc + (1 − α) × tp
Eficacia = tc / t medio        Rendimiento = Eficacia × 100 (%)
```
**Ejemplo:** `tc = 5 ns`, `tp = 50 ns`, `α = 90%`.
- Factor de velocidad = 50 / 5 = **10**.
- Serie: `0,9 × 5 + 0,1 × 55` = **10 ns**. Paralelo: `0,9 × 5 + 0,1 × 50` = **9,5 ns**.
- Eficacia (serie) = 5 / 10 = **0,5**, es decir un rendimiento del **50%**.

### 3.12 Caché: campos de la dirección y tamaños (U13)

```
Posición  = log₂(bytes por línea)
Línea     = log₂(cantidad de líneas)                  (1 vía)
Conjunto  = log₂(cantidad de conjuntos)               (N vías)
Etiqueta  = bits de dirección − (línea o conjunto) − posición
Conjuntos = tamaño de caché / (vías × tamaño de línea)
Tamaño de caché = conjuntos × vías × tamaño de línea
Bloques de MP = tamaño de MP / tamaño de línea
Memoria instalada = 2^(bits de dirección)
```
**Ejemplo:** MP de 4 GB (32 bits), caché de 4 KB, líneas de 32 B.

| Organización | Etiqueta | Línea o conjunto | Posición |
|---|---|---|---|
| Totalmente asociativa | 27 | – | 5 |
| 1 vía (128 líneas) | 20 | 7 | 5 |
| 2 vías (64 conjuntos) | 21 | 6 | 5 |

- Posición: 32 B → 5 bits. Líneas: 4 KB / 32 B = 128 → 7 bits.
- 2 vías: conjuntos = `4 KB / (2 × 32 B)` = 64 → 6 bits. Etiqueta = 32 − 6 − 5 = 21.
- Bloques de MP = 4 GB / 32 B = 128 M = 2²⁷.

**Armar una dirección en hexa:** se concatenan los campos y se agrupa **de a 4 bits desde la derecha**.
- Ejemplo: etiqueta `DEF10` + posición `000` → `1101 1110 1111 0001 0000 000` → `110 1111 0111 1000 1000 0000` = **`6F7880h`**.

### 3.13 Frecuencia y período (base de los tiempos)

```
Período = 1 / frecuencia
```
**Ejemplo:** 2,5 GHz → `1 / 2,5 GHz` = **0,4 ns**.

### 3.14 Bits de dirección y capacidades (base)

| Capacidad | Bits |
|---|---|
| 64 KB | 16 |
| 1 MB | 20 |
| 4 GB | 32 |
| 64 TB | 46 |

**Ejemplo:** ¿cuántos bits hacen falta para 16 MB? `16 MB = 2⁴ × 2²⁰ = 2²⁴` → **24 bits**.

---

## Resumen de una carilla (para el día antes)

```
Real:        Física = Seg×10h + Desp
Selector:    Índice = sel>>3 · TI = bit 2 · RPL = bits 1-0 · Descriptor = tabla + Índice×8
Lineal:      Base + Desp  (Desp ≤ Límite · G=1 → ×4 KB)
Paginación:  Dir = lin>>22 · Tabla = (lin>>12)&3FF · Desp = lin&FFF · entrada = base + índice×4
Interrupción: vector n → dirección n×4 · rutina = CS×10h + IP
Pila:        PUSH SP−2 · POP SP+2
Caché:       t serie = α·tc + (1−α)(tc+tp) · t paral = α·tc + (1−α)·tp · Eficacia = tc/t
Campos:      Etiqueta = bits − línea/conj − posición · tamaño = conjuntos×vías×línea
TLB:         h·(tTLB+tm) + (1−h)·(tTLB+3tm)
Módulos:     (cap/capm) × (ancho/anchom) · bits selec. = bus − bits del módulo
```
