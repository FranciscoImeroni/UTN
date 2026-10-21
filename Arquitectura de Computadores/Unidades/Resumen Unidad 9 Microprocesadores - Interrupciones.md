---
aliases:
  - Unidad 9 AdC
---

# Unidad 9 – Microprocesadores: Interrupciones

> Basado en las diapositivas **"Microprocesadores – Interrupciones"** (Prof. Silvana Panizzo) y en el **TP de Unidad 9** (bibliografía: Cap. 14 *"Interrupciones y excepciones"* de Angulo).
>
> Fuentes: [[Assets/Unidad9_Interrupciones.pdf|Diapositivas completas]] · [[Assets/TP_Unidad9_Interrupciones.pdf|TP de la unidad]]

## Objetivos

- Entender qué son las **interrupciones** y las **excepciones**, y en qué se diferencian.
- Conocer la **Tabla de Descriptores de Interrupción (IDT)** y el registro **IDTR**, tanto en Modo Real como en Modo Protegido.
- Repasar el **concepto de pila** (LIFO) y su rol en el tratamiento de interrupciones (PUSH/POP, CALL/RET, INT/IRET).
- Clasificar los **tipos de interrupciones y excepciones** (externas/internas, enmascarables/no enmascarables, faltas/trampas/abortos).
- Comprender las **4 fases de atención** de una interrupción o excepción.
- Repasar el **ciclo de instrucción** (fetch/execute) aplicado a un ejemplo concreto en Assembler, incluyendo la consulta de interrupciones externas al final de cada instrucción.

## 1. Repaso: ciclo de instrucción

Antes de entrar en interrupciones, la diapositiva repasa el ciclo de instrucción visto en la Unidad 7:

- **Fase de búsqueda (fetch):** cálculo de la dirección física de la instrucción, orden de lectura (RD), carga del registro **IR**.
- **Fase de ejecución (execute):** interpretar (decodificar) el código de instrucción, incrementar el **IP**, buscar el dato (RD) o guardarlo (WR) si la instrucción lo afecta, y generar la orden al módulo que opera sobre ese dato.

La **interrupción** se inserta como un desvío posible de este ciclo normal: al final de cada instrucción ejecutada, la CPU consulta si hay una interrupción pendiente antes de continuar con la siguiente.

## 2. Capacidad de interrupción

- Las **interrupciones** y **excepciones** son acontecimientos que provocan un **desvío en el flujo de control** de la CPU.
- **Interrupciones:** acontecimientos **externos** que desvían el flujo de control (por ejemplo, averías de hardware, un periférico pidiendo atención).
- **Excepciones:** acontecimientos **internos**, producidos como consecuencia de una **anomalía dentro de la CPU** durante la ejecución de un programa (por ejemplo, una división por cero).

## 3. IDT (Tabla de Descriptores de Interrupción) en Modo Real

- Para el manejo de interrupciones y excepciones, el modelo dispone de una **tabla o vector de interrupciones** llamada **Tabla de Descriptores de Interrupciones y Excepciones (IDT)**.
- La IDT está formada por **256 entradas**; cada tipo de interrupción está asociado a una de esas entradas.
- La IDT **ocupa un segmento**, cuya base y límite están contenidos en el **Registro de Tabla de Descriptores de Interrupciones (IDTR)**.
- Tanto en **Modo Real** como en **Modo Protegido** se utiliza la IDT (aunque la forma de operar es distinta en cada modo); el objetivo es el mismo: localizar la rutina que atiende cada interrupción/excepción.

### Particularidades en Modo Real

- En la **inicialización o reset** del modelo (Modo Real), el SO carga la **base del IDTR con el valor `0000 0000H`**, ya que solo se utiliza el primer MB de memoria.
- Se trabaja con **segmentos** (no existe paginación en Modo Real); el **tamaño máximo del registro** es de **64 KB**.
- En cada entrada de la IDT se apunta al **segmento de código** donde reside la rutina que atiende la interrupción.
- El **tamaño máximo de la IDT** es de **1 KB**, es decir **256 entradas × 4 bytes/entrada**; por lo tanto el **límite es `03FFH`**.

### IDT – Vector

- Hay tantas entradas en la IDT como **servicios definidos**; cada vector (puntero) contiene la **posición del servicio**.

![[Assets/u9_tabla_vectores_idt.png]]

| Función | Nº de vector |
|---|---|
| Error de división | 0 |
| Interrupción paso a paso | 1 |
| Interrupción NMI | 2 |
| Interrupción punto de parada (breakpoint) | 3 |
| Excepción por sobrepasamiento (overflow) | 4 |
| Sobrepasamiento de los límites (BOUND) | 5 |
| Código OP inválido | 6 |
| Coprocesador no disponible | 7 |
| Reservadas (Modo Protegido) | 8-15 |
| Error de coprocesador | 16 |
| Reservados Intel | 17-31 |
| Definidas por el usuario | 32-255 |

> Esta tabla (Figura 14.9 de Angulo) muestra los vectores **comunes a Modo Real y Modo Protegido**. En Modo Protegido, el rango 8-31 se completa con excepciones adicionales propias del Pentium (ver sección 8 del TP resuelto, más abajo).

## 4. Concepto de pila

- La **pila** es una estructura de datos en memoria de acceso **LIFO** (*Last In First Out*, "último en entrar, primero en salir"). Se puede imaginar como una **pila de platos**.
- El registro de segmento **SS** se accede con este criterio LIFO.

### La CPU utiliza la pila para

1. **Almacenar la dirección de retorno** (IP), y eventualmente el CS, cuando se llama a un procedimiento o subrutina.
2. **Almacenar el estado del procesador** cuando se produce una interrupción: se apilan obligatoriamente **CS, IP y el estado de los flags**, y eventualmente los registros de cálculo, etc.
3. **Pasar parámetros** entre dos procedimientos.

- El acceso a la pila se realiza a través de los **registros punteros SP y BP**: **SP** contiene la dirección del **próximo elemento vacío** de la pila. Es decir, se referencia como **SS:SP**.

### Instrucciones que operan sobre la pila

- La escritura o lectura de datos de la pila es un procedimiento por **software**, incrementando o decrementando el registro **SP**.
- Si la pila trabaja a **nivel palabra** (2 bytes): al **extraer** un dato se autoincrementa `SP = SP + 2`; al **cargar** un dato se autodecrementa `SP = SP – 2`.
- **PUSH:** almacena una palabra en la pila y luego **decrementa** SP.
- **POP:** extrae una palabra de la pila y **desafecta** esas posiciones de memoria, **incrementando** SP.
- Las instrucciones que, por su ejecución, afectan a la pila son **CALL, INT, RET e IRET**:
  - **CALL** y **RET**: invocan y retornan de un procedimiento o subrutina.
  - **INT** e **IRET**: cumplen la misma función cuando se invoca una **subrutina de interrupción**.

## 5. Tipos de interrupciones y excepciones

![[Assets/u9_tabla_interrupciones_externas.png]]

### Externas o hardware

Son convocadas en forma **asincrónica**; **no dependen** del programa en ejecución.

| Tipo | Cómo avisa | Descripción |
|---|---|---|
| **No enmascarables (NMI)** | Señal de control **NMI** (*Non Maskable Interrupt*) | **Siempre son atendidas**; se consideran de máxima importancia, pues las provoca el hardware ante acontecimientos que no pueden ser, por lo menos, "avisados" (p. ej., la caída de tensión de la alimentación). |
| **Enmascarables** | Otra señal, distinta de la NMI | Se consulta la señal de interrupción en **cada ciclo de ejecución de instrucción**. Como no siempre debe atenderse, también se consulta una **bandera de estado de habilitación de interrupciones (IF)**. Si la bandera lo autoriza, la CPU suspende momentáneamente el programa activo y ejecuta el servicio de interrupción; una función hardware externa determina la **prioridad** de esta interrupción respecto de otras peticiones pendientes (p. ej., recepción de un byte en un puerto). Si la bandera está desactivada, la CPU **no la tiene en cuenta** y continúa con el programa activo; de ahí el nombre "enmascarable". |

![[Assets/u9_tabla_excepciones.png]]

### Internas o software

Son **convocadas por el programa**. Se pueden producir por la ejecución de una instrucción específica dentro de un programa, para solicitar una interrupción que cumpla con alguna función determinada. La forma de convocarla es **`INT #`**, donde `#` corresponde al número que identifica la interrupción.

### Excepciones

Son provocadas como consecuencia de **anomalías** que se producen y detectan **durante la ejecución del programa y a causa de ella**. Se dividen en tres subtipos:

| Subtipo | Descripción |
|---|---|
| **Faltas o errores** | Se pueden **detectar y corregir antes** de que se produzca la ejecución de una instrucción determinada (p. ej., se produce una falla al invocar una página que no está en Memoria Principal). La atención de esta interrupción provoca que el sistema operativo **localice la página faltante y la cargue**. |
| **Trampas** | Se detectan **una vez ejecutada** la instrucción que las provoca (p. ej., las que de alguna manera incorpora el usuario en el programa, como un sobreflujo u *overflow*). |
| **Abortos** | Se detectan **sin localizar** la instrucción que las provoca, **abortando** la ejecución del programa (p. ej., un valor no válido en un registro de sistema). |

## 6. Fases de atención de una interrupción o excepción

**Fase 1:**
Se comprueba si hay interrupciones pendientes para ser atendidas. Si hay varias peticiones pendientes, se atienden **por orden de prioridad**.

**Fase 2:**
Al comenzar la interrupción se **resguarda el entorno actual de la CPU** (*context switch*) para que, al finalizar la interrupción o excepción, se retorne al mismo punto: se guarda el contenido de los registros del procesador en la **pila**. Por último, **IF = 0** prohíbe las interrupciones enmascarables, evitando que una interrupción de menor importancia interrumpa a la rutina en ejecución (por ejemplo, un periférico).

**Fase 3:**
Se busca la entrada correspondiente en la **IDT** y se cargan los registros **CS** e **IP** con el valor contenido en dicha entrada; de esta forma se obtiene el inicio de la rutina y comienza su ejecución.

**Fase 4:**
La rutina de interrupción **finaliza con la instrucción IRET**: se restaura el contexto de la CPU (*context switch*). El programa continúa su ejecución en la instrucción siguiente a la que se produjo la interrupción o excepción, y el flag **IF = 1**, es decir, se vuelven a activar las interrupciones enmascarables.

## 7. Repaso: CPU – Memoria Principal – Modo Real

- Para identificar un dato o instrucción dentro de la memoria se debe obtener la **dirección física**.
- La dirección física se obtiene a partir de la **dirección lógica** = **Base del segmento : Desplazamiento**.
- Cuando se asigna un segmento para crear un programa en Assembler, este se divide lógicamente en **3 partes: Código, Datos y Pila** (repaso de la Unidad 7).

## 8. Ejemplo completo: ciclo de instrucción y consulta de interrupciones

Programa Assembler usado como caso de estudio (el mismo de la Unidad 7):

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

**Actividades previas:** se cargan `CS = 13E0`, `DS = 13E0`, `IP = 0100`.

**`MOV AX,0002`** (dirección lógica `CS:IP = 13E0:0100`):
- *Fase de búsqueda:* dirección física = `13E0 * 10 + 0100 = 13F00`. Se carga el IR con `B80200`.
- *Fase de ejecución:* se decodifica, se incrementa el IP a `0103`, **AX = 0002**.

**`MOV BX,0004`** (dirección lógica `13E0:0103`):
- Dirección física = `13E0 * 10 + 0103 = 13F03`. Tras la ejecución, **BX = 0004**, IP pasa a `0106`.

**`ADD AX,BX`** (dirección lógica `13E0:0106`):
- Dirección física = `13E0 * 10 + 0106 = 13F06`. **AX = 0006**, IP pasa a `0108`. Flags resultantes: `NV UP EI PL NZ NA PO NC` (no hay overflow, no hay acarreo, resultado no es cero, positivo, paridad par).

**`INT 20`** (dirección lógica `13E0:0108`):
- Dirección física = `13E0 * 10 + 0108 = 13F08`. Es una **interrupción de software**, programada por el usuario, que provoca que termine el programa y se liberen/reasignen los recursos.

> **Punto clave de esta unidad:** al finalizar cada instrucción (cada "trace" `-t` del depurador), la CPU **consulta por interrupciones externas**. Si hay una interrupción pendiente, se **guarda el contexto del programa en la pila** y se ejecuta el código correspondiente a la interrupción; si no hay, se sigue con la ejecución normal del programa.

### Banderas usadas en el ejemplo

| Bandera | Significado |
|---|---|
| Overflow | NV = no hay desbordamiento; OV = sí lo hay |
| Direction | UP = hacia adelante; DN = hacia atrás |
| Interrupts (IF) | DI = desactivadas; EI = activadas |
| Sign | PL = positivo; NG = negativo |
| Zero | NZ = no es cero; ZR = sí lo es |
| Auxiliary Carry | NA = no hay acarreo auxiliar; AC = hay acarreo auxiliar |
| Parity | PO = paridad non; PE = paridad par |
| Carry | NC = no hay acarreo; CY = sí lo hay |

## 9. TP de la unidad — resuelto paso a paso

> Enunciado completo: [[Assets/TP_Unidad9_Interrupciones.pdf]]. Bibliografía indicada: Cap. 14 de Angulo, "Interrupciones y excepciones" (más las clases teóricas). El TP tiene una parte "a)" (práctica, resolución en clase/laboratorio con el depurador, no incluida en el PDF) y una parte "b)" de preguntas teóricas, que es la que se resuelve a continuación con el criterio de detalle esperado.

**1. ¿Qué es una interrupción?**

Es un acontecimiento **externo** a la CPU que provoca un **desvío en el flujo de control** normal del programa en ejecución. Es convocada de forma **asincrónica** por el hardware (por ejemplo, un periférico solicitando atención o una falla de alimentación) y **no depende** del programa que se está ejecutando en ese momento. → Secciones 2 y 5.

**2. ¿Qué es una excepción?**

Es un acontecimiento **interno** de la CPU, producido como consecuencia de una **anomalía** durante la ejecución de un programa **y a causa de él** (por ejemplo, una división por cero, un código de operación inválido o un desbordamiento). A diferencia de la interrupción, se origina y detecta dentro del propio procesamiento de la instrucción. → Secciones 2 y 5.

**3. ¿En base a qué maneja las interrupciones y excepciones un Pentium? Breve concepto.**

El Pentium maneja las interrupciones y excepciones en base a la **IDT (Tabla de Descriptores de Interrupciones y Excepciones)**: una tabla de **256 entradas**, donde cada tipo de interrupción/excepción está asociado a una entrada que apunta a la rutina que la atiende. La IDT ocupa un segmento cuya base y límite están contenidos en el registro **IDTR**. Se usa tanto en Modo Real como en Modo Protegido, aunque opera de forma distinta en cada uno. → Sección 3.

**4. ¿Qué son las interrupciones externas?**

Son las interrupciones de tipo **hardware**: se convocan de forma **asincrónica** y no dependen del programa en ejecución. Se dividen en **no enmascarables (NMI)** y **enmascarables**. → Sección 5.

**5. ¿Cuál es la diferencia entre interrupción externa enmascarable e interrupción externa no enmascarable? Dé un ejemplo de cada caso.**

- **No enmascarable (NMI):** siempre es atendida por la CPU, sin importar el estado de ninguna bandera; se reserva para eventos de máxima importancia que no pueden ser "avisados" con anticipación. *Ejemplo:* una caída de tensión en la alimentación.
- **Enmascarable:** solo se atiende si la **bandera IF** (habilitación de interrupciones) está activa; se consulta en cada ciclo de instrucción, y si IF está desactivada la CPU la ignora y sigue con el programa activo. Una función de hardware externa le asigna una **prioridad** frente a otras peticiones pendientes. *Ejemplo:* la recepción de un byte en un puerto de E/S (una impresora o un teclado pidiendo atención).

→ Sección 5.

**6. Indique y explique los tipos de excepciones.**

Las excepciones (internas, provocadas por anomalías durante la ejecución y a causa de ella) se dividen en tres tipos:

- **Faltas o errores:** se detectan y **corrigen antes** de que se ejecute la instrucción que las provoca (p. ej., una falta de página: el SO localiza la página faltante en disco y la carga en memoria).
- **Trampas:** se detectan **una vez ejecutada** la instrucción que las provoca (p. ej., un *overflow* incorporado por el propio programa).
- **Abortos:** se detectan **sin poder localizar** la instrucción que las originó, y **abortan** la ejecución del programa (p. ej., un valor inválido en un registro de sistema).

→ Sección 5.

**7. ¿Qué es una Tabla de Descriptores de Interrupción (IDT) y qué contiene?**

Es la tabla (o vector) de interrupciones que usa el Pentium para el manejo de interrupciones y excepciones. Contiene **256 entradas**; cada entrada corresponde a un tipo de interrupción/excepción distinto (identificado por su número de vector) y almacena la información necesaria para localizar la rutina que la atiende (en Modo Real, el segmento de código donde reside dicha rutina). → Sección 3.

**8. ¿Cómo está compuesta y cuál es la principal diferencia en la estructura IDT en Modo Real y en Modo Protegido?**

En **Modo Real**, la IDT ocupa como máximo **1 KB** (256 entradas × **4 bytes** cada una: 2 bytes de IP + 2 bytes de CS), por lo que su límite es `03FFH`; cada entrada apunta directamente al **par CS:IP** de la rutina de servicio, y su base se fija en `0000 0000H` al reset. En **Modo Protegido**, cada entrada de la IDT es un **descriptor de 8 bytes** (no un simple puntero CS:IP), que incluye el **selector de segmento**, el **desplazamiento (offset)** dentro de ese segmento y los **atributos** de protección (tipo de puerta —de interrupción, de trampa o de tarea— y nivel de privilegio requerido para invocarla). Por eso, en Modo Protegido la IDT puede ocupar hasta **2 KB** (256 × 8 bytes) y agrega verificación de privilegios que en Modo Real no existe. → Sección 3.

**9. ¿Para qué sirve el IDTR? Realice un esquema de cómo se vincula con la IDT.**

El **IDTR** (Registro de Tabla de Descriptores de Interrupciones) es el registro que contiene la **base** (dirección de comienzo) y el **límite** (tamaño) del segmento donde reside la IDT en memoria. Sirve para que la CPU sepa **dónde buscar** la tabla de vectores cada vez que debe atender una interrupción o excepción: al producirse un evento con número de vector `N`, la CPU calcula la entrada correspondiente indexando `Base(IDTR) + N × tamaño_de_entrada` para obtener el descriptor/puntero a la rutina de servicio.

Esquema conceptual:

```
        IDTR                              IDT (en memoria)
 ┌───────────────────┐            ┌──────────────────────────┐
 │ Base   │ Límite    │  ───────▶ │ entrada 0  (vector 0)     │
 └───────────────────┘            │ entrada 1  (vector 1)     │
                                   │ entrada 2  (vector 2)     │
      vector N  ─────────────────▶│ entrada N  → CS:IP rutina │
                                   │ ...                       │
                                   │ entrada 255               │
                                   └──────────────────────────┘
```

→ Sección 3.

**10. Describa brevemente los vectores de interrupción #0 #2 #3 #6 #7 #12 #14 #16 #19**

| Vector | Nombre | Descripción breve |
|---|---|---|
| **#0** | Error de división | Excepción (falta) que se produce cuando una instrucción de división (DIV/IDIV) intenta dividir por cero o el cociente no entra en el registro destino. |
| **#2** | Interrupción NMI | Interrupción externa **no enmascarable**; siempre se atiende, reservada para eventos críticos de hardware (p. ej., fallo de memoria o de alimentación). |
| **#3** | Punto de interrupción (*breakpoint*, `INT 3`) | Excepción de tipo **trampa** usada por los depuradores para detener la ejecución en un punto determinado del programa. |
| **#6** | Código de operación inválido | Excepción (falta) que se produce al intentar decodificar un opcode que la CPU no reconoce, o que no está permitido en el modo de ejecución actual. |
| **#7** | Dispositivo/coprocesador no disponible | Se produce al ejecutar una instrucción de punto flotante cuando no hay una unidad de coma flotante disponible (por hardware o porque el SO la deshabilitó para hacer *lazy save* del contexto). |
| **#12** | Falla de pila (*Stack Fault*) | Excepción (falta) generada por una violación de límites del segmento de pila (SS), o al intentar cargar SS con un segmento no presente en memoria. |
| **#14** | Falla de página (*Page Fault*) | Excepción (falta), propia del Modo Protegido con paginación: se produce cuando se referencia una página que no está presente en Memoria Principal; el SO la localiza en disco y la carga. |
| **#16** | Error de coma flotante (*x87 FPU error*) | Excepción de tipo trampa generada por el coprocesador matemático ante un error aritmético en una operación de punto flotante (p. ej., overflow/underflow). |
| **#19** | Excepción de punto flotante SIMD | Excepción generada por errores en instrucciones de punto flotante SIMD (extensiones tipo SSE) del Pentium. |

> Nota: los vectores 0-7 y 16 figuran en la tabla reducida de la diapositiva (Figura 14.9, común a Modo Real y Modo Protegido); los vectores 12, 14 y 19 pertenecen al conjunto ampliado de excepciones propias del **Modo Protegido** del Pentium (rango 8-31, marcado como "reservado" en la tabla de Modo Real), detallado en el Cap. 14 de Angulo.

**11. ¿Qué es el código de error y qué información contiene?**

El **código de error** es un valor que la CPU **apila automáticamente** (además de CS, IP y FLAGS) cuando se produce **determinadas excepciones** (por ejemplo, falla de pila #12, falla de página #14, protección general, TSS inválido, segmento no presente), para informarle a la rutina de servicio **más detalles sobre la causa** de la excepción. Contiene, entre otros campos:

- El **índice del selector de segmento** relacionado con la excepción (si corresponde).
- Un bit **TI** (*Table Indicator*) que indica si el selector referencia la GDT o la LDT.
- Un bit **IDT** que indica si el selector hace referencia a una entrada de la IDT.
- Un bit **EXT** (*External event*) que indica si la excepción fue provocada por un evento externo a la instrucción que se estaba ejecutando.

No todas las excepciones generan código de error (por ejemplo, la excepción #0 de división no lo genera); en esos casos no se apila ningún valor adicional.

**12. Describa cómo opera el IDTR y el IDT al arrancar el Pentium en modo real.**

Al producirse el **arranque o reset** del Pentium en Modo Real, el sistema operativo carga la **base del IDTR con el valor `0000 0000H`**, ya que en este modo solo se direcciona el primer MB de memoria. La IDT se organiza como una tabla de **256 entradas de 4 bytes** cada una (2 bytes de IP + 2 bytes de CS), por lo que su tamaño máximo es **1 KB** y su **límite es `03FFH`**. No existe paginación en Modo Real, por lo que la IDT se maneja íntegramente por segmentación: cada entrada apunta directamente al par **CS:IP** donde comienza la rutina que atiende esa interrupción o excepción. → Sección 3.

**13. Esquematice el acceso a las interrupciones y excepciones en modo real.**

```
Evento (interrupción/excepción) con vector N
            │
            ▼
  IDTR (Base=0000:0000, Límite=03FFH)
            │  dirección de la entrada = Base + N × 4
            ▼
  Entrada N de la IDT  →  [ IP (2 bytes) | CS (2 bytes) ]
            │
            ▼
  Se cargan CS e IP de la CPU con esos valores
            │
            ▼
  Comienza a ejecutarse la rutina de servicio de interrupción
```

Antes de saltar a la rutina, la CPU resguarda en la pila (SS:SP) el **FLAGS, CS e IP** vigentes (fases 1 y 2 de atención, ver pregunta 14), y pone **IF = 0**. Al finalizar la rutina con **IRET**, se restauran esos valores desde la pila y se retoma la ejecución del programa interrumpido. → Secciones 3, 4 y 6.

**14. Describa brevemente cuáles son las fases de atención de una interrupción o excepción.**

- **Fase 1:** se comprueba si hay interrupciones pendientes; si hay varias, se atienden por **orden de prioridad**.
- **Fase 2:** se resguarda el entorno de la CPU (*context switch*) apilando **CS, IP y FLAGS**; se pone **IF = 0** para evitar que otra interrupción de menor importancia interrumpa a la rutina en curso.
- **Fase 3:** se busca la entrada correspondiente en la **IDT** y se cargan **CS** e **IP** con el valor de esa entrada, dando inicio a la rutina de atención.
- **Fase 4:** la rutina finaliza con **IRET**, se restaura el contexto de la CPU (*context switch*) desde la pila, el programa continúa en la instrucción siguiente a la interrumpida, y se pone **IF = 1** para reactivar las interrupciones enmascarables.

→ Sección 6.


---

## 10. Ejercicio tipo: dónde está la rutina de una interrupción (modo real)

El TP no tiene cuentas, pero las preguntas 12 y 13 suelen venir con un número de vector.

**En modo real** la **IDT** (también llamada tabla de vectores) empieza en la dirección física `00000h`. Tiene **256 vectores de 4 bytes** (2 bytes de IP y 2 de CS), y ocupa `256 × 4 = 1024 bytes = 400h` (por eso el límite del IDTR es `03FFh`).

```
Dirección del vector n = n × 4
Contenido: [n×4, n×4+1] = IP de la rutina   [n×4+2, n×4+3] = CS de la rutina
Dirección física de la rutina = CS × 10h + IP
```

**Ejemplo: INT 21h** (vector 33 decimal = 21h)
- Dirección del vector = `21h × 4` = **`84h`** (`0000:0084`).
- Los bytes `0084–0085` contienen el **IP** y `0086–0087` el **CS** de la rutina de servicio.
- Si ahí hubiera `IP = 0100h` y `CS = 0200h`, la rutina estaría en `0200h × 10h + 0100h` = **`02100h`**.

**Pasos que hace la CPU** (son las "fases de atención" de la pregunta 14):
1. Termina la instrucción en curso y reconoce la interrupción (si es enmascarable, solo con **IF = 1**).
2. **Apila** FLAGS, CS e IP (para poder volver).
3. **Pone IF = 0 y TF = 0.**
4. Obtiene el **número de vector** y lo multiplica por 4 para leer **CS:IP** de la IDT.
5. Salta a la rutina y la ejecuta. Termina con **IRET**, que **restaura** IP, CS y FLAGS.

> La primera fila a recordar: vector `n` → dirección `n × 4`.
