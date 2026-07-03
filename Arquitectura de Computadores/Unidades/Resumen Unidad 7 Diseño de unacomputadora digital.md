---
aliases:
  - Unidad 7 AdC
---

# Unidad 7 – Diseño de una computadora digital

> Basado en las diapositivas **"Diseño de una computadora digital – Primera Parte"** y **"Segunda Parte"** (Prof. Silvana Panizzo).

## Objetivos

- Comprender el funcionamiento de una computadora básica en relación con su diseño interno.
- Demostrar la importancia del Álgebra de Boole y la teoría de circuitos lógicos.
- Ver un modelo de computadora con una estructura sencilla para entender su funcionamiento.
- Usar instrucciones básicas de **Assembler** para entender el movimiento de instrucciones y datos a través de los registros internos.

## 1. Diseño de una computadora digital

El diseño de una computadora digital es **la organización de hardware relacionada por rutas de control y datos**.

- Permite el flujo de señales binarias para transformar datos de entrada en información útil al usuario.
- Es un diseño **abstracto**, ya que se ocupa de ensamblar los módulos que la componen.

## 2. Módulo de cálculo en una computadora digital

Un **módulo**:

- Está constituido por una configuración determinada de compuertas.
- Contiene circuitos **abstractos**, que actúan como "cajas negras" que cumplen una determinada función lógica.
- Cada módulo realiza una o varias operaciones sobre datos codificados en binario, que se almacenan en **registros** asociados al módulo mientras dura la operación.
- Si dentro de un módulo una operación se aplica a un registro, esta se denomina **microoperación**, y se activa en un instante de tiempo sincronizado por los pulsos del reloj.

### Ejemplo: Sumador binario paralelo

![[Assets/u7_sumador_binario.png]]

- **Función:** operar datos binarios para obtener en la salida el resultado de su suma.
- Los **datos de entrada** se almacenan temporalmente en los registros A y B y, tras la orden de comando S, el **resultado** se obtiene sobre el registro C.
- La orden de comando es la **microoperación de suma** que habilita al registro C para actuar de receptor del resultado.
- La orden puede ser una señal "1" generada por otro módulo, cuya función es "dar órdenes", en caso de que este sumador pertenezca a una computadora.

## 3. Lenguaje de alto nivel vs. lenguaje de bajo nivel

- Se necesita un **programa traductor** (puede generar un ejecutable).
- Por cada sentencia *fuente* puede haber una o *n* sentencias en lenguaje de máquina.

| Lenguaje de alto nivel | Lenguaje simbólico (Assembler) | En memoria: lenguaje de máquina (binario) |
|---|---|---|
| `C = A + B;` | 3 instrucciones equivalentes | Código de instrucción |

Ejemplo:
```
13E0:0100 mov ah,[0300]
13E0:0104 add ah,[0301]
13E0:0108 mov [0400], ah
```
equivale, en binario, a:
```
13E0:0100 8A260003
13E0:0104 02260103
13E0:0108 88260004
```
La máquina **no entiende** el lenguaje simbólico; siempre debe traducirse a lenguaje de máquina.

**Compiladores:** traducen las sentencias de alto nivel en instrucciones de bajo nivel *antes* de la ejecución, generando un ejecutable (por ejemplo, un `.exe`).

**Intérpretes:** traducen las sentencias de alto nivel *en el momento* de la ejecución (por ejemplo, un navegador interpretando HTML).

## 4. Relación entre el diseño del hardware y la ejecución de instrucciones

- Programar en lenguaje de máquina implica conocer el tipo de instrucciones en código nativo que obedece la computadora.
- **Assembler** surge para simplificar el trabajo con largas secuencias binarias. Cada computadora y sus compatibles tiene su propio Assembler.
- Se estudia Assembler para comprender mejor la **unidad de control (CU)** y las interacciones entre lo físico y lo lógico.

### Formato de datos vs. formato de instrucción

- **Formato de datos:** los datos son enteros signados de 16 bits (1 bit de signo + 15 bits de magnitud).
- **Formato de instrucción:** las instrucciones se almacenan como palabras consecutivas a partir de la palabra `000H`.

### Instrucciones

- Una instrucción es una operación expresada mediante la codificación binaria de unos y ceros: **lenguaje máquina**.
- El lenguaje máquina es distinto para cada computador (salvo compatibilidad entre familias).
- **Repertorio o set de instrucciones:** conjunto de órdenes que puede ejecutar un computador.
- **Lenguaje ensamblador:** set de instrucciones expresado con mnemónicos.
- **Código de una instrucción:** combinación de bits que la CU de la CPU interpreta para generar las microoperaciones que permiten su ejecución.
- **Formato de la instrucción:** el más simple asigna un grupo de bits para la "acción" (**OPCODE**) y otro grupo para el "dato" al que afecta esa acción.
  - "n bits" determinan "2ⁿ códigos de operación distintos".

## 5. CPU – Unidad Central de Procesamiento

La función de la CPU se separa en tres partes:

1. **Tratamiento de instrucciones** → Unidad de Control (**CU**, sincronizada por el generador de pulsos de reloj).
2. **Operación de los datos** → Unidad Aritmético-Lógica (**ALU**).
3. **Cálculo de dirección de memoria** → Unidad de Manejo de Memoria (**MMU**).

### CPU – Memoria Principal

![[Assets/u7_cpu_memoria_principal.png]]

- Los datos e instrucciones se transportan a través del **bus de datos** (bidireccional), permitiendo la transferencia de grupos de bits desde/hacia la Memoria Principal.
- La **MMU** se comunica con la memoria principal a través del **bus de direcciones** (address bus).
- A través del **bus de control** se envían órdenes de **Lectura (RD)** o **Escritura (WR)** hacia la Memoria Principal.

## 6. Registros

- **Registros de cálculo** (16 y 32 bits): en una IA-32 son **EAX** (Acumulador), **EBX** (Base), **ECX** (Contador) y **EDX** (Datos). Todos se usan en operaciones aritméticas y lógicas.
- **Registros punteros.**
- **Registro de estado** (flags) y **registro de segmento**.

## 7. Modo real vs. Modo protegido

**Modo real:**
- 20 bits de espacio de direcciones segmentado (2²⁰ = 1 MB máximo de memoria).
- Acceso directo del software a rutinas del BIOS y hardware periférico.
- No tiene protección de memoria ni multitarea a nivel de hardware.
- Palabra de 16 bits; memoria segmentada (tamaño máximo de segmento 2¹⁶ = 64 KB).
- Rango de direcciones de un segmento: `0000` a `FFFF` (hexadecimal). Cada dirección identifica 8 bits de almacenamiento.

**Modo protegido:**
- Existe Memoria Virtual.
- Memoria Principal de hasta 4 GB y Memoria Virtual de hasta 64 TB (a partir del 80386).
- Memoria segmentada, con o sin paginación.
- Protección de programas, multitareas y posible conexión a memoria caché.

### CPU – Memoria Principal – Modo real

![[Assets/u7_cpu_memoria_modo_real_segmentos.png]]

- Para identificar un dato o instrucción dentro de la memoria se debe obtener la **dirección física**.
- La dirección física se obtiene a partir de la **dirección lógica**.
- La dirección lógica se compone de **Base del segmento : Desplazamiento**.
- Cuando se asigna un segmento para crear un programa en Assembler, este se divide lógicamente en 3 partes: **Código, Datos y Pila**.

### Cálculo de la dirección física (Modo real)

Dirección lógica: `Base del segmento : Desplazamiento`, con los pares `CS:IP`, `DS:DATA`, `ES:DATA`, `SS:SP`.

```
Dirección Física = Base del segmento * 10H + Desplazamiento
```

Ejemplo en Assembler:
```
13E0:0100 mov ax,0002
13E0:0103 mov bx,0004
13E0:0106 add ax,bx
13E0:0108 int 20
```

Dirección lógica: `13E0 : 0100` → Dirección física: `13E0 * 10 + 0100 = 13F00`

## 8. Instrucciones Assembler

**MOV** — transferencia de datos entre celdas de memoria, registros y acumulador.
```
MOV destino, fuente
```
Ejemplos:
```
MOV AX,0006h
MOV BX,AX
MOV AX,4C00h
MOV AH,[0300]
```

**ADD** — adición de los operandos, guarda el resultado en el operando "destino".
```
ADD destino, fuente
```
Ejemplos:
```
ADD AX,0006h
ADD BX,AX
ADD AH,[0301]
```

## 9. Modelo de estudio: arquitectura básica de "X"

![[Assets/u7_arquitectura_basica_x.png]]

Registros clave del modelo:
- **MAR** (*Memory Address Register*): registro de dirección de memoria.
- **IP** (*Instruction Pointer*): puntero de instrucción.
- **MDR** (*Memory Data Register*): registro de datos de memoria.
- **IR** (*Instruction Register*): registro de instrucción, dividido en **COP** (código de operación) y **DATA/DIR**.

La CU (CU = CPU sin ALU) contiene el IP, el IR y el bloque de **CONTROL** que genera las microoperaciones (µop₀ … µop₁₅). La memoria principal se divide, a través de un **SELECTOR**, en memoria de instrucciones y memoria de datos. La ALU contiene el acumulador (**AC**) y el registro de estado (**SR**) con las banderas **S, Z, V, C**.

## 10. Ciclo de instrucción

![[Assets/u7_ciclo_instruccion.png]]

El proceso se divide en las siguientes etapas:

1. **Búsqueda de la instrucción en memoria** → *fase de búsqueda* o **fetch**.
2. **Interpretación (decodificación) del código de instrucción.**
3. **Búsqueda del dato afectado** por la instrucción (si corresponde) → junto con el punto 2 y 4 forman la *fase de ejecución* o **execute**.
4. **Generación de órdenes al módulo que opera sobre ese dato.**

Actividades previas a la ejecución de la primera instrucción (por ejemplo, la asignación del procesador al proceso) preceden a los *n* ciclos de ejecución de las *n* instrucciones del programa, hasta el fin de la ejecución y la reasignación del procesador.

### Detalle de las fases (Segunda Parte)

**Fase de búsqueda:**
- Cálculo de la dirección física de la instrucción.
- Dar orden de lectura (RD).
- Se carga el registro **IR**.

**Fase de ejecución:**
- Interpretar el código de la instrucción (decodificar).
- Incrementar el **IP**.
- Búsqueda del dato (RD) o guardar el dato (WR), si la instrucción lo afecta.
- Generar la orden al módulo que opera el dato.

## 11. Ejemplo completo paso a paso (Ejemplo 1.txt)

Programa Assembler utilizado como caso de estudio:
```
-a
13E0:0100 mov ax,0002
13E0:0103 mov bx,0004
13E0:0106 add ax,bx
13E0:0108 int 20
13E0:010A

-u
13E0:0100 B80200   MOV AX,0002
13E0:0103 BB0400   MOV BX,0004
13E0:0106 01D8     ADD AX,BX
13E0:0108 CD20     INT 20
```

![[Assets/u7_cpu_ejecucion_ejemplo_registros.png]]

Recorrido del ciclo de instrucción sobre este ejemplo:

- **Actividades previas:** se cargan `CS = 13E0` y `DS = 13E0`, `IP = 0100`. El segmento se divide en Código, Datos y Pila.
- **Instrucción `MOV AX,0002` (dirección `13E0:0100`, código `B80200`):**
  - *Fase de búsqueda:* dirección física = `13E0 * 10 + 0100 = 13F00`. Se carga el **IR** con `B80200`.
  - *Fase de ejecución:* se decodifica, se incrementa el IP a `0103`, y **AX** recibe el valor `0002`.
- **Instrucción `MOV BX,0004` (dirección `13E0:0103`, código `BB0400`):**
  - Dirección física = `13E0 * 10 + 0103 = 13F03`.
  - Tras la ejecución, **BX = 0004**, IP pasa a `0106`.
- **Instrucción `ADD AX,BX` (dirección `13E0:0106`, código `01D8`):**
  - Dirección física = `13E0 * 10 + 0106 = 13F06`.
  - En la fase de ejecución se realiza la suma en la **ALU** y se actualizan los **flags**: **AX = 0006**, IP pasa a `0108`.
- **Instrucción `INT 20` (dirección `13E0:0108`, código `CD20`):**
  - Dirección física = `13E0 * 10 + 0108 = 13F08`.
  - Es una **interrupción de software**, programada por el usuario, que provoca que termine el programa y se liberen/reasignen los recursos → **fin del ciclo de instrucción**. IP pasa a `010A`.

### Banderas (Flags) usadas en el ejemplo

| Bandera | Significado |
|---|---|
| Overflow | NV = no hay desbordamiento; OV = sí lo hay |
| Direction | UP = hacia adelante; DN = hacia atrás |
| Interrupts | DI = desactivadas; EI = activadas |
| Sign | PL = positivo; NG = negativo |
| Zero | NZ = no es cero; ZR = sí lo es |
| Auxiliary Carry | NA = no hay acarreo auxiliar; AC = hay acarreo auxiliar |
| Parity | PO = paridad non; PE = paridad par |
| Carry | NC = no hay acarreo; CY = sí lo hay |

Estado tras `ADD AX,BX`: `AX=0006 BX=0004 ... NV UP EI PL NZ NA PE NC` (no hubo acarreo ni overflow).

## 12. Ejemplo 2 – Acceso a datos en memoria (Ejemplo 2.txt)

```
-a0100
13E0:0100 mov ah,[0300]
13E0:0104 add ah,[0301]
13E0:0108 mov [0400], ah
13E0:010C ret
13E0:010D

-u
13E0:0100 8A260003   MOV AH,[0300]
13E0:0104 02260103   ADD AH,[0301]
13E0:0108 88260004   MOV [0400],AH
13E0:010C C3         RET
```

Se cargan los datos iniciales en memoria:
```
-e 0300
13E0:0300  00.01
-e 0301
13E0:0301  00.ff
-e 0400
13E0:0400  00.
```

> La memoria no se borra sola: solo se libera el recurso. Por eso las variables deben inicializarse con valores válidos; de lo contrario, tomarán los valores que ya existan en esa posición de memoria.

Recorrido (mismo patrón de fases búsqueda/ejecución que el Ejemplo 1, pero accediendo a **datos en memoria** en lugar de constantes inmediatas):

- **`MOV AH,[0300]`**: dirección física de la instrucción `13E0*10+0100=13F00`; dirección física del dato (`DS:0300`) = `13E0*10+0300=14100` → **AH = 01**.
- **`ADD AH,[0301]`**: dirección física del dato (`DS:0301`) = `13E0*10+0301=14101` (valor `FF`). Suma **01 + FF = 00** con acarreo:
  ```
    11111111
  +  00000001   ->   1 (10)
  =  00000000
  ```
  Resultado: `AH = 00`, flags `ZR AC PE CY` (es cero, hay acarreo auxiliar, paridad par y acarreo).
- **`MOV [0400],AH`**: dirección física del dato = `13E0*10+0400=14200` → se guarda `00` en memoria.
- **`RET`**: dirección física `13E0*10+010C=13F0C`; finaliza la ejecución.

## 13. Unidad de control y sincronización del tiempo

- ¿Cómo se controla el flujo secuencial de microoperaciones para llevar a cabo el procesamiento? Las señales de tiempo que afectan a las microoperaciones están reguladas según el tiempo de respuesta de los registros involucrados.
- En la mayoría de las computadoras, las señales de tiempo son generadas por un **sistema de reloj**: un oscilador y circuitos asociados que generan pulsos, cuyo ancho y separación se determinan con precisión.
- **Ciclo de reloj (o ciclo menor):** intervalo entre dos pulsos consecutivos de reloj (0 y 1).
- **Frecuencia (Hz):** la oscilación de la señal entre 0 y 1 se controla por un oscilador; cada ciclo de reloj se identifica como 1 hercio (Hz). Es decir: `1 Hz = 1 ciclo / 1 segundo`.
  - *Ejemplo:* si una CPU trabaja a una frecuencia de 25 MHz, ¿cuántos nanosegundos (10⁻⁹ s) tarda un ciclo de reloj? (las unidades de tiempo de la CPU están en el orden de los ns).
- Una computadora controlada por un sistema de reloj se denomina **sincrónica**.
- Se proporcionan secuencias de tiempo repetitivas (4, 8 o 16 señales: t₀, t₁, …, t₁₅). El tiempo de esa secuencia repetitiva constituye el **ciclo de la computadora** o **ciclo de máquina**, que debería ser compatible con el **ciclo de memoria**.
- **Ciclo de memoria:** igual al tiempo de acceso, si la memoria es de lectura no destructiva; o igual al tiempo de acceso más el tiempo de restauración, si es de lectura destructiva.
- **Tiempo de acceso a memoria:** tiempo que tarda la CU en buscar la información en la memoria y dejarla disponible en el **MDR**.
- Al presionar **ON**, se desencadena la búsqueda de la primera instrucción. A partir de ahí, la CU está en uno de dos estados: **búsqueda** (fase *fetch*) o **ejecución** (fase *execute*). La CU actualiza una **flag de control de fase** (biestable): `1` = fase de búsqueda, `0` = fase de ejecución. Con esta información se generan las **funciones de control** (microoperaciones) necesarias para el procesamiento.

### Esquema interno de la CU

![[Assets/u7_esquema_interno_cu.png]]

La CU de "X" está formada por dos bloques:

- **Decodificador de instrucción:** puede obtenerse a partir de un decodificador **4·16**. Las cuatro entradas (`COP`, desde el IR) varían entre `0000` y `1111` según la instrucción almacenada en el IR; las salidas (`COP₀` … `COP₁₅`) indican con un `1` cuál de las dieciséis combinaciones se dio en la entrada.
- **Secuenciador:** genera las señales de tiempo `t₀ … t₇` a partir del **reloj** (por ejemplo, 10 MHz) y de la entrada de habilitación.

Ambas salidas alimentan la **red de compuertas** (lógica de generación de microoperaciones), que produce las **microoperaciones** (`µop₀ … µop₇`, señales de control que afectan a los componentes del sistema). El **FLAG F** indica la fase actual: `0` = fase *execute*, `1` = fase *fetch*.
