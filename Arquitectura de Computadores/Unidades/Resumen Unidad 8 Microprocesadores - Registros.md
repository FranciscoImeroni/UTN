---
aliases:
  - Unidad 8 AdC
---

# Unidad 8 – Microprocesadores: Registros

> Basado en las diapositivas **"Microprocesadores – Registros"** (Dra. Claudia Litvak, Ing. Silvana Panizzo, Ing. María de la Paz Colla) y en el **TP de Unidad 8** (bibliografía: Cap. 7 *"Modelo del Pentium para el programador"* de Angulo).
>
> Fuentes: [[Assets/Unidad8_Registros.pdf|Diapositivas completas]] · [[Assets/TP_Unidad8_Microprocesadores.doc|TP de la unidad]]

## Objetivos

- Diferenciar la visión de la CPU según el **programador de sistemas** y el **programador de aplicaciones**.
- Conocer en detalle los **16 registros del Pentium** visibles para el programador de aplicaciones: propósito general, EIP, EFLAGS y de segmento.
- Entender el **registro EFLAGS** bit a bit (señalizadores de estado y de sistema).
- Comprender cómo funciona la **segmentación de memoria** en Modo Real y en Modo Protegido, y cómo se calcula la dirección física en cada caso.

## 1. Programador de sistemas vs. programador de aplicaciones

| | Programador de **aplicaciones** | Programador de **sistemas** |
|---|---|---|
| Tarea | Codifica programas para usuarios finales, en general en lenguaje de alto nivel. | Desarrolla programas y utilidades del **sistema operativo**: depuradores, compiladores, objetos del SO. |
| Visión de la CPU | **Limitada/transparente**: ve la CPU como un conjunto de registros de trabajo para manipular instrucciones, datos y direcciones de sus aplicaciones. | **Profunda**: debe conocer en detalle toda la arquitectura de la CPU para optimizar recursos (potencia, seguridad, rendimiento). |
| Conocimientos necesarios | Registros internos accesibles, repertorio básico de instrucciones y modos de direccionamiento (si usa lenguaje máquina). | Memoria virtual, mecanismos de protección, conmutación de tareas, tratamiento de interrupciones y excepciones. |
| Herramienta Intel | **BINDER** (sencilla, no accede a los mecanismos del sistema). | **BUILDER** (controla todos los recursos de la CPU, permite describir la estructura global a implantar). |
| Nivel de privilegio | Menor cualificación relativa. | Mayor cualificación: controla y organiza a los programadores de aplicaciones, asignando niveles de privilegio y protección a cada tarea. |

## 2. Registros internos del Pentium

El Pentium tiene **32 registros internos**, de los cuales **16 son de uso del programador de aplicaciones** (los otros 16 son "del sistema", reservados al programador de sistemas). Estos 16 se agrupan en **4 grandes grupos**:

1. **Registros de propósito general** (8): EAX, EBX, ECX, EDX, EBP, ESP, ESI, EDI.
2. **Registro Puntero de Instrucciones** (EIP).
3. **Registro de Estado o de Señalizadores** (EFLAGS).
4. **Registros de Segmento** (6): CS, SS, DS, ES, FS, GS.

![[Assets/u8_fig71_registros_pentium.png]]

## 3. Registros de propósito general

- Son los mismos registros del **8086 (16 bits)**, pero **ampliados a 32 bits**. Se los nombra igual que en el 8086 pero **anteponiendo la "E" de "extendido"** (por ejemplo BX → EBX).
- Ocho registros que pueden manejar información de **32, 16 u 8 bits** (4 de ellos manejan también 8 bits).
- Se usan tanto para **almacenar datos** como **direcciones** (en este último caso, el contenido es un desplazamiento que apunta a una posición de memoria).

![[Assets/u8_fig72_formatos_registros.png]]

- Al acceder solo a los 16 bits de menos peso: **AX, BX, CX, DX, SP, BP, SI, DI**.
- De AX, BX, CX y DX también son accesibles sus dos bytes por separado: el de menos peso (**AL, BL, CL, DL**) y el de más peso (**AH, BH, CH, DH**).

### Detalle de cada registro de propósito general

| Registro | Nombre | Uso principal |
|---|---|---|
| **EAX** (AX, AL, AH) | **Acumulador** | Se emplea en **todas las operaciones lógico-aritméticas**: contiene un operando y recibe el resultado de la ALU. Uso intensivo por parte de Intel. |
| **EBX** (BX, BL, BH) | **Base** | Contiene una dirección que apunta a la **base de un conjunto de datos**. EBX (32 bits) direcciona la memoria del Pentium; BX (16 bits) direcciona la memoria del 8086. |
| **ECX** (CX, CL, CH) | **Contador** | Se carga con el **número de veces que se ejecuta una instrucción** (iteraciones, bucles). |
| **EDX** (DX, DL, DH) | **Datos** | Contiene las **direcciones de los puertos de E/S** en las instrucciones que manejan el mapa de E/S. |
| **ESP** (SP) | **Puntero de pila** | Contiene el **desplazamiento de la cima de la pila** en el segmento de pila actual. Lo usan PUSH/POP, llamadas a subrutinas, retornos, excepciones e interrupciones. Al **meter** un elemento se **decrementa** ESP; al **sacarlo**, se **incrementa**. |
| **EBP** (BP) | **Puntero de base (de pila)** | Se usa para **acceder a estructuras de datos pasadas por la pila**. Cuando direcciona memoria, referencia el segmento de pila en curso; apunta a la base de la pila (sirve para no tener que modificar ESP). |
| **ESI** (SI) | **Índice fuente** | Puntero que direcciona la **cadena fuente** en instrucciones de manejo de cadenas ("strings"). |
| **EDI** (DI) | **Índice destino** | Puntero que direcciona la **cadena destino**. ESI y EDI tienen **autoincremento/autodecremento**: en cada instrucción de cadena avanzan o retroceden según el tamaño (en bytes) de cada elemento. |

![[Assets/u8_fig73_pila_esp.png]]

![[Assets/u8_fig74_esi_edi.png]]

#### Ejemplo 7.1 (resuelto en la diapositiva)

```
MOV AH, AL
```

**Solución:** el byte de **menos peso** del Acumulador (AX) — es decir **AL** — se mueve al byte **AH**, que es el de **más peso**.

## 4. Registro Puntero de Instrucciones (EIP)

- Es el **contador de programa**: apunta a la siguiente instrucción a ejecutar. **No es accesible directamente por el programador**; lo gobiernan implícitamente el flujo de control, las interrupciones y las excepciones.
- Almacena el **desplazamiento** que hay que sumar a la **base del segmento de código** (dada por CS) para obtener la dirección donde está la próxima instrucción.
- Trabaja en dos modos:
  - **Modo Nativo/Protegido:** 32 bits completos → **EIP**. Desplazamiento máximo: **4 GB**.
  - **Modo Real:** direccionamiento reducido (compatible 8086/80286), solo usa los **16 bits de menos peso** de EIP, llamados **IP**. Desplazamiento máximo: **64 KB**.

![[Assets/u8_fig75_eip.png]]

## 5. Registro de Estado (EFLAGS)

- Registro de **32 bits**. La mayoría de sus bits son **señalizadores de estado** controlados por la ALU (acarreo, paridad, acarreo auxiliar, cero, signo, overflow); el resto son **señalizadores del sistema**, ligados a protección y otros recursos.

![[Assets/u8_fig77_eflags.png]]

### Señalizadores de estado (los controla la ALU)

| Flag | Significado | 1 = | 0 = |
|---|---|---|---|
| **CF** | Carry (acarreo) | Hubo acarreo en el bit de más peso de una suma. | No hubo acarreo (o hubo "llevada" en una resta). |
| **PF** | Parity (paridad) | Se genera paridad **impar** con los bits del resultado. | Paridad par. |
| **AF** | Acarreo auxiliar | Hubo acarreo en el **bit 3** del resultado (usado en operaciones con **BCD**). | No hubo acarreo en el bit 3. |
| **ZF** | Zero (cero) | Todos los bits del resultado son 0. | El resultado no es 0. |
| **SF** | Signo | El bit más significativo del resultado es 1 (negativo). | Es 0 (positivo). |
| **OF** | Overflow (desbordamiento) | En operaciones con signo, el resultado es demasiado grande o pequeño → resultado erróneo. | No hay desbordamiento. |

### Señalizadores del sistema

| Flag | Significado |
|---|---|
| **TF** | Trap. Si es 1, provoca una excepción al terminar cada instrucción → permite depurar **paso a paso**. |
| **IF** | Habilitación de interrupciones mascarables (1 = las reconoce; 0 = las ignora). |
| **DF** | Dirección de exploración de cadenas: 1 = post**decremento** automático de ESI/EDI; 0 = post**incremento**. |
| **IOPL** | Nivel de privilegio de E/S (2 bits, Modo Protegido): 11=nivel 3 (todos), 10=nivel 2, 01=nivel 1, 00=nivel 0 (solo el SO). |
| **NT** | Tarea anidada. 1 = la tarea en curso está anidada con la anterior (debe retornar obligatoriamente a ella). |
| **RF** | Reanudación. Si es 1, se ignoran los puntos de depuración/parada. |
| **VM** | Modo Virtual. 1 = desde Modo Protegido se pasa a Modo Virtual 8086. |
| **AC** | Chequeo de alineamiento. 1 = excepción si hay un dato desalineado en memoria (relevante para buses de 64 bits). |
| **VIF** | Interrupción virtual (equivalente a IF, pero en Modo Virtual). |
| **VIP** | Interrupción (mascarable) pendiente en Modo Virtual. |
| **ID** | Identificación: 1 = el Pentium soporta la instrucción **CPUID** (muestra las características del procesador). |

## 6. Registros de Segmento

- Intel usa la **segmentación** como sistema principal de organización de la memoria en la arquitectura IA-32.
- Un **segmento** es un trozo de memoria de tamaño variable que contiene un mismo tipo de información. Hay 3 tipos en los programas de aplicación: **de código, de datos y de pila**.
- El Pentium controla, en cada instante, **6 segmentos** a través de **6 registros de segmento de 16 bits**: **CS, SS, DS, ES, FS, GS**.

![[Assets/u8_fig78_registros_segmento.png]]

| Registro | Segmento que referencia |
|---|---|
| **CS** | **Código**: segmento de instrucciones que está ejecutando la CPU. El desplazamiento a sumar a su base lo da **EIP**. |
| **SS** | **Pila**: el desplazamiento a sumar a su base lo da **ESP**. |
| **DS** | **Datos** (por defecto, cuando no se especifica otro segmento). |
| **ES, FS, GS** | Segmentos **extra de datos**: permiten tener activos hasta 3 segmentos de datos adicionales al de DS. |

### Estructura del registro de segmento (Figura 7.9)

![[Assets/u8_fig79_selector_rpl.png]]

- **Selector** (14 bits de más peso): referencia el segmento (su base, límite y atributos).
- **RPL** (2 bits de menos peso): indica el **nivel de privilegio** del segmento peticionario.

### Ejemplo 7.2 (resuelto en la diapositiva)

```
MOV EAX, (5555)
```

**Solución:** se carga EAX con los 32 bits obtenidos en la posición de memoria del segmento **DS** (por defecto, si no se explicita otro) que comienza en la dirección `base + 5555`. Si el segmento fuera GS, la instrucción debería escribirse `MOV EAX, GS:5555`.

Con los 6 registros de segmento la CPU mantiene **6 segmentos activos simultáneamente**, aunque puede haber más segmentos definidos en el programa (basta con recargar el registro correspondiente para "activar" otro).

![[Assets/u8_fig711_seis_segmentos.png]]

## 7. Modo Real vs. Modo Protegido

| | **Modo Real** | **Modo Protegido** |
|---|---|---|
| Espacio de direcciones | 20 bits segmentados → **2²⁰ = 1 MB** máximo. | Memoria Principal hasta **4 GB**; Memoria Virtual hasta **64 TB** (desde el 80386). |
| Protección/multitarea | **No** hay protección de memoria ni multitarea por hardware. | **Sí**: protección de programas y multitareas. |
| Palabra | 16 bits. | — |
| Tamaño máx. de segmento | 2¹⁶ = **64 KB**. | Segmentada, con o sin paginación. |
| Direcciones de un segmento | `0000` a `FFFF` (hex); cada dirección identifica 8 bits. | — |
| Memoria caché | No contemplada. | Posible conexión a memoria caché. |
| Compatibilidad | Compatible con 8086/80286 (monotarea). | — |

### Segmentación en Modo Real

- Un segmento queda definido por: **base** (dirección de comienzo, **20 bits**) + **desplazamiento** (**16 bits**, máx. 64 KB).
- **Selector:** referencia la base del segmento. Como el 8086 solo direcciona 1 MB (2²⁰), la base se obtiene multiplicando el valor del registro de segmento **por 16** (agregando 4 ceros).
- **Desplazamiento:** basta con 16 bits. Según el tipo de segmento, lo aporta un registro distinto:

| Tipo de segmento | Base | Desplazamiento |
|---|---|---|
| Código | CS × 16 | **IP** |
| Pila | SS × 16 | **SP** |
| Datos | DS/ES/FS/GS × 16 | Desplazamiento (según modo de direccionamiento de la instrucción) |

**Fórmula general:**
```
Dirección efectiva/física = Registro de Segmento × 16 + Desplazamiento
```

![[Assets/u8_fig713_segmentacion_modo_real.png]]

### Segmentación en Modo Protegido

En Modo Protegido (multitarea), cada segmento queda caracterizado por **3 parámetros**, verificados automáticamente por el sistema de protección cada vez que se usa el segmento:

1. **Base:** dirección lineal de comienzo, de **32 bits** (hasta 2³² = 4 GB).
2. **Límite:** **20 bits** que determinan el tamaño del segmento. Si se expresa en bytes: máx. 2²⁰ = 1 MB; si se expresa en páginas de 4 KB: puede llegar a 4 GB.
3. **Atributos:** **12 bits** con las características del segmento (tipo —legible, escribible, ejecutable o combinación—, nivel de privilegio 0-3, indicadores de memoria virtual, etc.).

- Al conjunto **base + límite + atributos** de un segmento se lo llama **descriptor de segmento**.
- Los descriptores se guardan en la **tabla de descriptores**, que reside en la Memoria Principal y es creada/gestionada por el **sistema operativo**.
- En Modo Protegido, el registro de segmento actúa como **selector visible**: sus 13 bits de índice ubican el descriptor correspondiente en la tabla. La CPU carga automáticamente base, límite y atributos en **registros caché ocultos** (invisibles tanto para el programador de aplicaciones como para el de sistemas).

![[Assets/u8_fig714_descriptor_segmento.png]]

![[Assets/u8_fig715_seg_modo_protegido.png]]

## 8. TP de la unidad — resuelto paso a paso

> Enunciado completo: [[Assets/TP_Unidad8_Microprocesadores.doc]]. Bibliografía indicada: Cap. 7 de Angulo. Estas son las respuestas armadas con el contenido de la diapositiva (así se ve el criterio de corrección/nivel de detalle esperado).

**1. ¿Cuál es la diferencia entre el programador de sistemas y el de aplicaciones?**

El **programador de aplicaciones** codifica programas para usuarios finales (aplicaciones comerciales de alto nivel); tiene una visión **limitada y transparente** de la CPU, viéndola solo como un conjunto de registros de trabajo. El **programador de sistemas** desarrolla el software del sistema operativo (compiladores, depuradores, utilidades) y necesita **conocer en profundidad** toda la arquitectura de la CPU (memoria virtual, protección, conmutación de tareas, interrupciones) porque además **organiza y controla** a los programadores de aplicaciones, asignándoles niveles de privilegio. → Ver sección 1.

**2. Dibuje un esquema que muestre los 32 registros internos del Pentium indicando cuáles usa el programador de aplicaciones.**

El Pentium tiene 32 registros internos; **16 son "de aplicaciones"** (EAX, EBX, ECX, EDX, EBP, ESP, ESI, EDI, EIP, EFLAGS y los 6 de segmento CS/SS/DS/ES/FS/GS) y los otros 16 son **"del sistema"** (de uso exclusivo del programador de sistemas, no cubiertos en este resumen de aplicaciones). Esquema de referencia: ver Figura 7.1 en la sección 2 (![[Assets/u8_fig71_registros_pentium.png]]).

**3. Esquema e indicación de para qué se usa cada registro de propósito general.**

Ver la tabla completa de la sección 3: **EAX** (acumulador, operaciones ALU), **EBX** (base, apunta a direcciones), **ECX** (contador de iteraciones), **EDX** (direcciones de puertos de E/S), **ESP** (puntero de pila — cima de pila), **EBP** (puntero de base — accede a estructuras en la pila), **ESI**/**EDI** (índices fuente/destino para cadenas, con auto-incremento/decremento).

**4. ¿Cuál es la función y para qué se utiliza el registro EIP?**

Es el **puntero de instrucciones** (contador de programa): almacena el desplazamiento que hay que sumar a la base del segmento de código (CS) para obtener la dirección de la próxima instrucción a ejecutar. No es accesible directamente por el programador; se actualiza implícitamente con cada instrucción, salto, interrupción o excepción. En Modo Real usa solo sus 16 bits bajos (**IP**, máx. 64 KB); en Modo Protegido usa sus 32 bits completos (**EIP**, máx. 4 GB). → Sección 4.

**5. Ídem para el registro EFLAGS.**

Es el **registro de estado/señalizadores**, de 32 bits. Sus bits se dividen en **señalizadores de estado** (controlados por la ALU tras cada operación: CF, PF, AF, ZF, SF, OF) que reflejan el resultado de la última operación aritmético-lógica, y **señalizadores del sistema** (TF, IF, DF, IOPL, NT, RF, VM, AC, VIF, VIP, ID) que controlan comportamientos como la depuración paso a paso, la habilitación de interrupciones o el nivel de privilegio de E/S. → Sección 5 (tabla completa flag por flag).

**6. Ídem para los registros de segmento.**

Son **6 registros de 16 bits** (CS, SS, DS, ES, FS, GS) que en cada instante determinan los 6 segmentos (código, pila y hasta 4 de datos) que la CPU puede identificar y manipular. Cada uno se compone de un **selector** (14 bits, referencia el segmento) y un campo **RPL** (2 bits, nivel de privilegio del peticionario). → Sección 6.

**7. Explique cómo funciona la segmentación en Modo Real.**

Un segmento se define por una **base** de 20 bits y un **desplazamiento** de 16 bits (máx. 64 KB, compatibilidad con el 8086). La base se obtiene multiplicando por 16 el valor del registro de segmento correspondiente (CS, SS, DS...); el desplazamiento lo aporta IP (código), SP (pila) o el modo de direccionamiento de la instrucción (datos). La dirección física resulta:
```
Dirección física = Registro de segmento × 16 + Desplazamiento
```
Con esto se direcciona un máximo de 1 MB de memoria (2²⁰). → Sección 7 (con tabla y figura 7.13).

**8. Explique cómo funciona la segmentación en Modo Protegido.**

Cada segmento se caracteriza por 3 parámetros comprobados automáticamente por el sistema de protección: **base** (32 bits, dirección lineal), **límite** (20 bits, tamaño del segmento) y **atributos** (12 bits: tipo, nivel de privilegio, indicadores de memoria virtual). Este conjunto forma el **descriptor de segmento**, almacenado en la **tabla de descriptores** (en Memoria Principal, gestionada por el SO). El registro de segmento actúa como **selector**: sus 13 bits de índice localizan el descriptor en la tabla, y la CPU carga automáticamente base/límite/atributos en registros caché ocultos, invisibles al programador. Esto permite hasta 4 GB de Memoria Principal y hasta 64 TB de Memoria Virtual, con protección de programas y multitarea real. → Sección 7 (con figuras 7.14 y 7.15).
