### Ver también: [[Resumen Unidad 7 Diseño de unacomputadora digital.md]] · [[Resumen Unidad 8 Microprocesadores - Registros.md]] · [[Resumen Unidad 9 Microprocesadores - Interrupciones.md]] · [[Resumen Unidad 10 Microprocesadores - Instrucciones.md]] · [[Resumen Unidad 11 Segmentación.md]] · [[Resumen Unidad 12 Direccionamiento en Modo Protegido - Paginación.md]] · [[Resumen Unidad 13 Memoria Cache y Entrada Salida.md]] · [[Plan de Repaso Segundo Parcial.md]]

# Repaso base: lo que hay que saber ANTES de estudiar las Unidades 8 a 13

> Esta nota explica **desde cero** los conceptos de la Unidad 7 y lo que se usa de las Unidades 1 a 6 (secciones 1 a 10) que las Unidades 8 a 13 asumen que ya sabés. Está escrita como si nunca hubieras visto el tema. Al final hay **ejercicios de opción múltiple** para practicar antes de meterte con lo nuevo.

## 0. La idea más importante de todas (leé esto primero)

Una computadora, en el fondo, hace todo el tiempo **una sola cosa en bucle**:

```
1. Buscar una instrucción en memoria   (FETCH)
2. Ejecutarla                          (EXECUTE)
3. Volver al paso 1, con la siguiente instrucción
```

Este bucle se llama **ciclo de instrucción**, y absolutamente todo lo que viene después (registros, interrupciones, modos de direccionamiento, segmentación, paginación) es una forma de responder a la misma pregunta:

> **¿Cómo hace la CPU para saber en qué posición exacta de memoria está el dato o la instrucción que necesita?**

Las Unidades 8 a 12 son, en esencia, 5 capítulos de la misma historia, cada vez más sofisticados, sobre esa única pregunta. Si entendés bien este repaso, todo lo nuevo va a ser "una vuelta de tuerca más" sobre algo que ya conocés, no un tema nuevo de cero.

## 1. Cómo se guarda la información: bits, bytes, hexadecimal

- **Bit:** un 0 o un 1.
- **Byte:** 8 bits juntos. Ejemplo: `01001101`.
- Trabajar en binario puro es incómodo (es largo), así que en arquitectura casi todo se escribe en **hexadecimal (base 16)**: cada dígito hex representa exactamente 4 bits.

| Hex | Binario | Hex | Binario |
|---|---|---|---|
| 0 | 0000 | 8 | 1000 |
| 1 | 0001 | 9 | 1001 |
| 2 | 0010 | A | 1010 |
| 3 | 0011 | B | 1011 |
| 4 | 0100 | C | 1100 |
| 5 | 0101 | D | 1101 |
| 6 | 0110 | E | 1110 |
| 7 | 0111 | F | 1111 |

Un byte completo son 2 dígitos hex (ej. `4D` = `0100 1101`). Todas las direcciones de memoria que vas a ver en las diapositivas (`13E0:0100`, `3A2B:1301`, etc.) están en hexadecimal.

> **Por qué importa para lo que viene:** en la Unidad 12, vas a tener que separar una dirección de 32 bits en 3 partes (10 bits + 10 bits + 12 bits). Si no manejás bien la relación bits↔hex, ese ejercicio se vuelve mucho más difícil de lo necesario.

## 2. La memoria es como un edificio de casillas numeradas

Imaginate la memoria principal (RAM) como una calle enorme de casillas de correo, todas del mismo tamaño (1 byte cada una), numeradas desde `0`. Ese número es la **dirección**.

- **Dirección física:** el número real de la casilla en la calle, tal cual la memoria RAM lo entiende (esto es lo que viaja por el "bus de direcciones" hacia la memoria).
- La CPU **no siempre trabaja directamente con la dirección física**. Casi siempre trabaja con una forma "indirecta" de referirse a esa casilla, y hace falta un cálculo para llegar a la dirección física real.

Esa forma indirecta es la **dirección lógica**, y el cálculo para pasar de una a la otra es EXACTAMENTE lo que van a pedirte en los parciales de las Unidades 11 y 12 (con distintos niveles de complejidad).

## 3. Dirección lógica = Segmento : Desplazamiento (Modo Real)

En la CPU 8086 (y en "Modo Real"), la memoria no se ve como una calle continua, sino dividida en **segmentos**: bloques de hasta 64 KB cada uno. Un programa típico usa 3 segmentos:

| Segmento | Para qué sirve | Registro que guarda su base |
|---|---|---|
| **Código** | Guarda las instrucciones del programa | **CS** |
| **Datos** | Guarda las variables | **DS** |
| **Pila (Stack)** | Guarda datos temporales, direcciones de retorno | **SS** |

Una dirección lógica se escribe `SEGMENTO : DESPLAZAMIENTO` (ej. `13E0:0100`). El **segmento** te dice "en qué bloque de 64KB estoy" y el **desplazamiento** te dice "cuántos bytes más adelante, dentro de ese bloque".

### La fórmula que tenés que saber de memoria

```
Dirección física = (Segmento × 10H) + Desplazamiento
```

`10H` es 16 en decimal (multiplicar por 16 en hex es lo mismo que agregar un `0` al final).

**Ejemplo resuelto:**
```
Dirección lógica:  13E0 : 0100
Base del segmento:  13E0 × 10H = 13E00
+ Desplazamiento:        +  0100
= Dirección física:    =  13F00
```

> **Por qué importa:** la Unidad 11 generaliza esta misma cuenta a Modo Protegido (con selector + descriptor de segmento), y la Unidad 12 agrega un paso más arriba (traducir de "dirección lineal" —que es el resultado de este cálculo— a "dirección física" usando páginas). Si no te sale esta cuenta simple, las de las Unidades 11 y 12 te van a costar el doble.

## 4. Los registros que mueven todo esto (modelo simple de la Unidad 7)

Antes de llegar al Pentium con sus 32 registros (Unidad 8), la Unidad 7 usa un modelo mínimo con 4 registros clave. Pensalos como "cajitas" donde la CPU guarda valores mientras trabaja:

| Registro | Nombre completo | Para qué sirve |
|---|---|---|
| **IP** | Instruction Pointer | Guarda la dirección de la **próxima instrucción** a ejecutar. Se incrementa solo después de cada instrucción. |
| **MAR** | Memory Address Register | Guarda la dirección (física) que se le va a pedir a la memoria. |
| **MDR** | Memory Data Register | Guarda el dato que "viaja" entre la CPU y la memoria (en cualquiera de los dos sentidos). |
| **IR** | Instruction Register | Guarda la instrucción recién leída, ya dividida en **COP** (código de operación: qué hacer) y **DATA/DIR** (con qué dato). |

Estos 4 registros son la versión "de juguete" de lo que en el Pentium real se llaman **EIP** (Unidad 8) y de los buses internos que conectan la CPU con la memoria.

## 5. El ciclo de instrucción, paso a paso

```
FASE DE BÚSQUEDA (fetch):
  1. Se calcula la dirección física de la instrucción (usando IP, y Segmento:Desplazamiento)
  2. Se pide "Lectura" (RD) a la memoria con esa dirección
  3. El dato que vuelve se carga en el registro IR

FASE DE EJECUCIÓN (execute):
  4. Se decodifica el código de operación (COP) dentro de IR
  5. Se incrementa el IP (para que apunte a la SIGUIENTE instrucción)
  6. Si la instrucción necesita un dato: se lee (RD) o se escribe (WR) ese dato en memoria
  7. Se generan las órdenes al módulo que corresponda (ej. a la ALU si es una suma)
```

Después de la fase de ejecución, se vuelve al paso 1 con la nueva instrucción (la que ahora apunta IP). Este bucle se repite mientras el programa esté corriendo.

> **Por qué importa:** la Unidad 9 (Interrupciones) agrega un único paso nuevo a este ciclo: **al final de cada instrucción ejecutada, la CPU se pregunta "¿hay alguna interrupción pendiente?"** antes de volver al paso 1. Si no tenés clarísimo el ciclo de arriba, ese "paso extra" no tiene dónde encajar en tu cabeza.

## 6. Ejemplo completo (el mismo que usan las diapositivas de las Unidades 7 y 9)

```assembly
13E0:0100  MOV AX,0002     ; AX = 0002
13E0:0103  MOV BX,0004     ; BX = 0004
13E0:0106  ADD AX,BX       ; AX = AX + BX = 0006
13E0:0108  INT 20          ; fin del programa
```

Con `CS = DS = 13E0` e `IP` empezando en `0100`:

| Instrucción | Dirección lógica | Dirección física (Seg×10H + Desp) | Resultado |
|---|---|---|---|
| `MOV AX,0002` | 13E0:0100 | 13E00+0100 = **13F00** | AX=0002, IP→0103 |
| `MOV BX,0004` | 13E0:0103 | 13E00+0103 = **13F03** | BX=0004, IP→0106 |
| `ADD AX,BX` | 13E0:0106 | 13E00+0106 = **13F06** | AX=0006, IP→0108 |
| `INT 20` | 13E0:0108 | 13E00+0108 = **13F08** | fin del programa |

Fijate que **el IP siempre queda apuntando a la instrucción siguiente**, no a la que se acaba de ejecutar. Este detalle se usa mucho en preguntas teóricas.

## 7. Los "flags" (banderas): cómo la CPU recuerda qué pasó en la última operación

Cada vez que la ALU hace una operación (suma, resta, AND, etc.), prende o apaga ciertos "avisos" de 1 bit llamados **flags**. Los 4 más importantes para empezar:

| Flag | Nombre | Se pone en 1 cuando... |
|---|---|---|
| **Z (ZF)** | Zero | El resultado de la operación fue exactamente 0 |
| **S (SF)** | Sign | El resultado es negativo (bit más alto = 1) |
| **C (CF)** | Carry | Hubo "acarreo" que se sale del ancho del registro (ej. sumar dos números de 16 bits y el resultado necesita 17 bits) |
| **V/O (OF)** | Overflow | El resultado, interpretado **con signo**, es incorrecto (se pasó del rango representable) |

> **Por qué importa:** en la Unidad 8 estos mismos 4 flags (más PF y AF) viven dentro de un registro más grande llamado **EFLAGS**, junto con otros bits nuevos (IF, TF, DF...) que controlan cosas del sistema. En la Unidad 12, los **atributos de una página** (P, D, A, R/W) son conceptualmente lo mismo: bits de 1 sola posición que informan un estado. Si entendés la lógica de "un bit = un aviso de sí/no", te ahorrás la mitad del trabajo de memorizar los atributos de páginas y segmentos.

## 8. Modo Real vs. Modo Protegido (panorama general, sin entrar en detalle todavía)

| | **Modo Real** | **Modo Protegido** |
|---|---|---|
| Memoria máxima direccionable | 1 MB (2²⁰) | 4 GB físicos, hasta 64 TB virtuales |
| ¿Hay protección entre programas? | No | Sí |
| ¿Multitarea a nivel de hardware? | No | Sí |
| ¿Cómo se organiza la memoria? | Segmentos simples (Seg×16+Desp) | Segmentos con tabla de descriptores, + paginación (opcional) |
| CPUs | 8086 y compatibles | 80286 en adelante (80386+ para 32 bits completos) |

Todas las CPUs x86 modernas **arrancan siempre en Modo Real** (por compatibilidad) y luego el sistema operativo las pasa a Modo Protegido. Los dos modos **no pueden estar activos al mismo tiempo**.

> Este cuadro es la base de TODO lo que hacen las Unidades 8, 11 y 12: van a explicar, con cada vez más detalle, cómo funciona específicamente la columna de "Modo Protegido".

## 9. Cómo se conecta todo esto con lo nuevo (mapa mental)

```
Unidad 7 (esta nota):
  Dirección física = Segmento×10H + Desplazamiento     [Modo Real, 1 solo cálculo simple]
        │
        ▼
Unidad 8:
  Mismo concepto, pero con los 32 registros reales del Pentium
  y viendo también CÓMO se calcula en Modo Protegido (con descriptor de segmento)
        │
        ▼
Unidad 9:
  El "ciclo de instrucción" (sección 5) se interrumpe: aparece la IDT,
  una tabla para saber a qué rutina saltar ante una interrupción/excepción
        │
        ▼
Unidad 10:
  Formaliza TODAS las formas posibles de decirle a la CPU "dónde está el dato"
  (los "modos de direccionamiento"): la dirección lógica de la Unidad 7 era
  solo UNO de esos modos (el "directo con segmento")
        │
        ▼
Unidad 11:
  Modo Protegido en detalle: la dirección lógica ahora usa un SELECTOR
  (no un registro de segmento simple) que apunta a una tabla (GDT/LDT)
  con descriptores de 8 bytes → de ahí sale la "dirección LINEAL"
        │
        ▼
Unidad 12:
  La dirección LINEAL de la Unidad 11 todavía no es la física:
  falta un segundo paso (paginación) que la traduce a la dirección FÍSICA real,
  usando el mismo patrón de "tabla + índice" que ya viste en la IDT (U9) y en la GDT (U11)
        │
        ▼
Unidad 13:
  La dirección física ya está lista, pero la RAM es lenta: la CPU la busca
  primero en la CACHÉ. La dirección se parte en campos (etiqueta / línea o conjunto /
  posición) con la misma lógica de "contar bits" de la unidad 12. Además, E/S:
  cómo se comunican la CPU, la memoria y los periféricos (programada, interrupción, DMA)
```

Si podés seguir esta cadena sin trabarte, estás listo para entrar a las Unidades 8-13 con la base correcta. Antes de eso, leé la sección 10, que junta lo que se usa de las unidades 1 a 6.

## 10. Lo que se usa de las Unidades 1 a 6 en las Unidades 8 a 13

> Esta sección junta **solo** lo que las unidades nuevas dan por sabido. Si necesitás el detalle, están los resúmenes completos: [[Resumen Unidad 2 SISTEMAS NUMERICOS]] y [[Resumen Unidad 3 & 4 REPRECENTACION DE DATOSARITMETICA DEL COMPUTADOR]].

### 10.1 Mapa rápido: qué se usa y dónde

| Tema viejo | Dónde reaparece |
|---|---|
| Hexa ↔ binario ↔ decimal | Todas las direcciones, la unidad 12 (cortar bits) y la unidad 13 (armar direcciones en hexa) |
| Complemento a 2 y números con signo | Flags SF/OF/CF (U8), `SUB`/`IMUL`/`IDIV` (U10, DEBUG) |
| Little endian | DEBUG (`MOV [0200],AX`), U10 y U11 |
| Potencias de 2 y bits de dirección | U11 (memoria virtual), U12 (tablas), U13 (caché, tamaños de MP) |
| Frecuencia y período | U13 (tiempos de caché) |
| Decodificadores y multiplexores (unidad 6) | Selección de módulos de memoria (U11), selección del byte en una línea de caché (U13) |
| Paridad | Flag PF (U8), detección de errores |
| Bus y ciclos de bus | U9 (interrupciones), U13 (E/S, DMA) |

### 10.2 Números con signo y complemento a 2 (lo que más se usa)

- Con **n bits** el rango con signo es `−2ⁿ⁻¹ … 2ⁿ⁻¹ − 1`.

| Bits | Rango con signo | Sin signo |
|---|---|---|
| 8 | −128 … +127 | 0 … 255 |
| 16 | −32 768 … +32 767 | 0 … 65 535 |
| 32 | −2³¹ … 2³¹ − 1 | 0 … 2³² − 1 |

- **El bit de la izquierda es el signo** (1 = negativo).
- **Para cambiar de signo** (complemento a 2): invertir todos los bits y sumar 1.
  - `FF` → invertir `00` → +1 = `01` → **`FF = −1`**.
  - `E4` → invertir `1B` → +1 = `1C` → **`E4 = −28`**.
  - `80` → es **−128**, el más negativo de 8 bits.
- **Carry contra overflow** (se confunden mucho):
  - **CF (carry)** avisa que el resultado **sin signo** no entró en el registro.
  - **OF (overflow)** avisa que el resultado **con signo** es incorrecto: se suman dos positivos y da negativo, o dos negativos y da positivo.

| Operación (8 bits) | Resultado | CF | OF | SF |
|---|---|---|---|---|
| `7F + 01` | `80` | 0 | **1** (dos positivos dieron negativo) | 1 |
| `FF + 01` | `00` | **1** (se pasó de 8 bits) | 0 (−1 + 1 = 0 es correcto) | 0 |
| `08 + 03 − 0C` | `FF` (−1) | 1 (hubo "préstamo") | 0 | 1 |

### 10.3 Little endian

Intel guarda los datos de más de un byte con el **byte menos significativo en la dirección más baja**.

```
Palabra 1234h en la dirección 0200:        Doble palabra 12345678h en 0200:
   0200 = 34    0201 = 12                     0200 = 78  0201 = 56  0202 = 34  0203 = 12
```

Por eso en DEBUG `MOV [0203],AX` con `AX = 0003` deja `0203 = 03` y `0204 = 00`, y por eso `A3 03 02` es la instrucción "guardar en la dirección `0203`".

### 10.4 Potencias de 2 y bits de dirección

- `2¹⁰ = 1 K`, `2²⁰ = 1 M`, `2³⁰ = 1 G`, `2⁴⁰ = 1 T`.
- Con **n bits de dirección** se direccionan `2ⁿ` posiciones. Al revés: `bits = log₂(capacidad en bytes)`.

| Capacidad | Bits de dirección |
|---|---|
| 64 KB (segmento en modo real) | 16 |
| 1 MB (memoria en modo real) | 20 |
| 16 MB | 24 |
| 4 GB (memoria física y lineal del Pentium) | 32 |
| 64 TB (memoria virtual) | 46 |

- **Truco:** descomponé en potencias. `16K = 2⁴ · 2¹⁰ = 2¹⁴`. `128 M = 2⁷ · 2²⁰ = 2²⁷`.
- Cada **dígito hexa** son 4 bits, así que una dirección de 32 bits son 8 dígitos hexa.

### 10.5 Conversiones y cuentas en hexa

- **Hexa → decimal:** sumar `dígito × 16ᵏ`. Ejemplo: `2F5h = 2·256 + 15·16 + 5 = 757`.
- **Decimal → binario/hexa:** divisiones sucesivas por la base, leyendo los restos de abajo hacia arriba.
- **Hexa ↔ binario:** directo, de a 4 bits (ver la tabla de la sección 1).
- **Sumar y restar en hexa:** como en decimal, pero se "lleva" al pasar de 15 (F). Ejemplo: `3330 + 4879 = 7BA9`. Es la cuenta que aparece en el TP de la unidad 10.

### 10.6 Frecuencia y período

```
Período (T) = 1 / frecuencia
```

| Frecuencia | Período |
|---|---|
| 100 MHz | 10 ns |
| 500 MHz | 2 ns |
| 1 GHz | 1 ns |
| 2,5 GHz | 0,4 ns |

- `1 ns = 10⁻⁹ s`, `1 µs = 10⁻⁶ s`, `1 ms = 10⁻³ s`.
- Se usa en la unidad 13 para comparar el ciclo del procesador con el tiempo de acceso de la caché y de la memoria.

### 10.7 Decodificadores y multiplexores (unidad 6)

- **Decodificador de n entradas:** tiene `2ⁿ` salidas y activa **una sola** según el valor binario de entrada.
  - Se usa para **elegir un módulo de memoria** con los bits de dirección que sobran (ejemplo de la unidad 11).
- **Multiplexor:** `2ⁿ` entradas de datos, `n` líneas de selección y **1 salida**. Elige cuál de las entradas pasa.
  - Se usa en la caché (unidad 13) para elegir el **byte dentro de la línea** con los bits de POSICIÓN.
- **Demultiplexor:** lo inverso: una entrada que se reparte hacia una de varias salidas.

### 10.8 Paridad

- Se agrega un bit a cada dato para **detectar errores** de un solo bit.
- **Paridad par:** el bit se elige para que la cantidad total de unos (dato + bit) sea **par**. **Paridad impar:** que sea **impar**.
- Ejemplo: dato `0110 1100` tiene 4 unos. Con paridad par el bit vale **0**, y con paridad impar vale **1**.
- El flag **PF** del EFLAGS (unidad 8) vale 1 cuando el byte bajo del resultado tiene una **cantidad par de unos** (DEBUG lo muestra como `PE`; si es impar muestra `PO`).

### 10.9 Bus y ciclos de bus

- **Buses del sistema:** de **direcciones** (la CPU lo maneja y dice a dónde), de **datos** (el dato viaja en los dos sentidos) y de **control** (órdenes como lectura `RD` y escritura `WR`, interrupciones, reconocimientos).
- **Ciclo de bus** = una transferencia completa. En una lectura: la CPU pone la dirección, activa `RD` y recibe el dato.
- **Sincrónico** si lo controla el reloj del sistema, **asincrónico** si no.
- **Ancho del bus de datos** (64 bits en el Pentium) → cuántos bytes se leen por ciclo (y por qué importa el alineamiento).
- Se vuelve a usar en la unidad 9 (el ciclo de instrucción), en la 13 (DMA, que "toma el bus") y en la caché (qué tan ocupado está el bus del sistema).

### 10.10 Tipos de memoria (para no confundirlas)

| Tipo | Idea clave |
|---|---|
| **RAM** | Lectura y escritura. Pierde el contenido sin energía. |
| **SRAM** | Estática. Rápida y cara. Se usa en la **caché**. |
| **DRAM** | Dinámica. Lenta y barata. Necesita refresco. Es la **memoria principal**. |
| **ROM** | Solo lectura. No se borra sin energía (guarda el arranque). |

El detalle está en la unidad 11 (secciones 5 y 6).

---

---

# Ejercicios de práctica (opción múltiple)

> Resolvelos sin mirar las respuestas. Al final está la clave.

**1.** ¿Cuál es la dirección física correspondiente a la dirección lógica `2000:0050`?
A) 20050
B) 02050
C) 20500
D) 00050

**2.** En el ciclo de instrucción, ¿en qué fase se incrementa el registro IP?
A) En la fase de búsqueda, antes de leer la instrucción
B) En la fase de ejecución
C) Solo cuando hay una interrupción
D) El IP nunca se incrementa automáticamente

**3.** ¿Qué registro guarda la instrucción recién leída de memoria, ya dividida en código de operación y dato?
A) MAR
B) MDR
C) IR
D) IP

**4.** Si `AX = 00FF` y se ejecuta `ADD AX, 0001`, el resultado es `AX = 0100`. ¿Qué flag(s) se activa(n) como consecuencia directa de esta suma?
A) Carry (CF), porque el resultado superó el máximo de 16 bits
B) Ninguno, el resultado entra perfecto en 16 bits, no hay acarreo fuera del registro ni sobrepasa el rango con signo
C) Overflow (OF), porque cambió la cantidad de dígitos hexadecimales
D) Zero (ZF), porque terminó en 00

**5.** ¿Cuál de las siguientes afirmaciones sobre Modo Real y Modo Protegido es correcta?
A) Pueden estar ambos activos al mismo tiempo para mayor flexibilidad
B) El Modo Protegido no tiene protección de memoria, al contrario de lo que dice su nombre
C) Todas las CPU x86 arrancan en Modo Real y luego, si el SO lo decide, pasan a Modo Protegido
D) El Modo Real permite direccionar hasta 4 GB de memoria

**6.** ¿Cuántos bytes representa el valor hexadecimal `3F`?
A) 2 bytes
B) 1 byte (8 bits)
C) 4 bits
D) 3 bytes

**7.** Dada la dirección lógica `3A2B:1301`, ¿cuál es su dirección física?
A) 3A2B1301
B) 3B5B1
C) 4D2C
D) 3A2C301

**8.** Después de ejecutar una instrucción, el IP queda apuntando a...
A) La misma instrucción que se acaba de ejecutar, para poder revisarla
B) La instrucción anterior
C) La primera instrucción del programa
D) La instrucción siguiente a la que se acaba de ejecutar

**9.** ¿Cuál de estas es la definición correcta de "dirección física"?
A) La dirección tal como la escribe el programador en Assembler, con segmento y desplazamiento
B) El número real de la posición de memoria, tal cual lo recibe la memoria RAM por el bus de direcciones
C) Un nombre alternativo para el registro IP
D) La dirección que usa exclusivamente la pila (stack)

**10.** Un segmento en Modo Real puede tener como máximo:
A) 1 MB
B) 4 GB
C) 64 KB
D) 4 KB


**11.** En 8 bits con signo, ¿cuál es el rango de valores representables?
A) 0 a 255
B) −128 a +127
C) −127 a +128
D) −256 a +255

**12.** Se ejecuta `ADD AL,01` con `AL = 7F`. ¿Cómo quedan AL y los flags CF y OF?
A) AL = 80, CF = 1, OF = 0
B) AL = 80, CF = 0, OF = 1
C) AL = 7E, CF = 0, OF = 0
D) AL = 00, CF = 1, OF = 1

**13.** La palabra `1234h` se guarda en la dirección `0200` en un procesador Intel. ¿Qué hay en `0200` y en `0201`?
A) 0200 = 12, 0201 = 34
B) 0200 = 34, 0201 = 12
C) 0200 = 1234, 0201 = vacío
D) 0200 = 21, 0201 = 43

**14.** ¿Cuántos bits de dirección hacen falta para una memoria de 16 MB?
A) 20
B) 22
C) 24
D) 32

**15.** ¿Cuál es el período de un procesador de 500 MHz?
A) 0,5 ns
B) 2 ns
C) 5 ns
D) 20 ns

**16.** ¿Qué valor debe tener el bit de paridad **par** para el dato `0110 1100`?
A) 0
B) 1
C) Depende del procesador
D) No se puede calcular

**17.** Convertí `2F5h` a decimal.
A) 501
B) 757
C) 765
D) 1 013

**18.** ¿Cuántos módulos de 4K×8 hacen falta para armar una memoria de 16K×16?
A) 2
B) 4
C) 8
D) 16

**19.** Se arma una memoria de 16K×8 con módulos de 4K×8 (bus de 14 bits de dirección). ¿Cuántos bits de dirección sirven para **elegir el módulo**?
A) 1
B) 2
C) 4
D) 12

**20.** `AL = FF`, ¿qué valor decimal tiene si se interpreta **con signo**?
A) 255
B) −127
C) −1
D) −255

---

## Clave de respuestas

1. **A** — `2000 × 10H = 20000`, `+ 0050 = 20050`.
2. **B** — El IP se incrementa durante la fase de ejecución, después de decodificar la instrucción.
3. **C** — El IR (Instruction Register) es el que guarda la instrucción dividida en COP + DATA/DIR.
4. **B** — 00FF + 0001 = 0100, entra sin problema en 16 bits: no hay Carry ni Overflow. (ZF tampoco, porque el resultado no es cero).
5. **C** — Es la única afirmación correcta; los otros dos modos no pueden convivir, el Modo Protegido SÍ tiene protección, y el Real llega solo a 1 MB.
6. **A** — `3F` son 2 dígitos hexadecimales = 8 bits = 1 byte.
7. **B** — `3A2B × 10H = 3A2B0`, `+ 1301 = 3B5B1`.
8. **D** — El IP queda siempre apuntando a la siguiente instrucción a ejecutar.
9. **B** — Es la definición de dirección física; la lógica es la del programador (A), y las otras dos son incorrectas.
10. **C** — 64 KB (2¹⁶), por eso el desplazamiento en Modo Real usa 16 bits.
11. **B** — Con n bits el rango es −2ⁿ⁻¹ a 2ⁿ⁻¹−1. Para 8 bits: −128 a +127.
12. **B** — `7F + 01 = 80`. No hay carry (no se pasó de 8 bits), pero sí **overflow**: dos positivos dieron un resultado negativo (`80` = −128).
13. **B** — Little endian: el byte menos significativo (`34`) va en la dirección más baja.
14. **C** — 16 MB = 2⁴ · 2²⁰ = 2²⁴, o sea 24 bits.
15. **B** — T = 1 / 500 MHz = 2 ns.
16. **A** — `0110 1100` ya tiene 4 unos (cantidad par), así que el bit de paridad par vale 0.
17. **B** — `2·256 + 15·16 + 5 = 512 + 240 + 5 = 757`.
18. **C** — `(16K / 4K) × (16 bits / 8 bits) = 4 × 2 = 8` módulos.
19. **B** — Cada módulo de 4K usa 12 bits (2¹² = 4K). Sobran 14 − 12 = 2 bits, que seleccionan entre 4 módulos.
20. **C** — `FF` con signo es −1 (invertir `00`, sumar 1 = `01`).
