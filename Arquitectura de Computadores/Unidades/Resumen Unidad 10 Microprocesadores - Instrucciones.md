---
aliases:
  - Unidad 10 AdC
---

# Unidad 10 – Microprocesadores: Instrucciones, Modos de Direccionamiento y Formatos

> Basado en las diapositivas **"Microprocesadores – Instrucciones – Modos de direccionamiento"** (Prof. Silvana Panizzo), en el resumen del **Cap. 10 de Stallings** ("Conjunto de instrucciones: modos de direccionamiento y formatos") y en el **TP de Unidad 10** (bibliografía: Cap. 17 *"Repertorio de instrucciones"* de Angulo).
>
> Fuentes: [[Assets/Unidad10_Instrucciones_Direccionamiento.pdf|Diapositivas completas]] · [[Assets/Direccionamiento_Stallings_resumen.pdf|Resumen Stallings]] · [[Assets/TP_Unidad10_Instrucciones.pdf|TP de la unidad]]

## Objetivos

- Repasar el **ciclo de instrucción** (fetch/execute) como marco donde se ubican los modos de direccionamiento.
- Entender los **dos aspectos** que definen una instrucción: cómo se especifica la dirección de un operando (**modo de direccionamiento**) y cómo se organizan los bits de la instrucción (**formato**).
- Conocer en detalle los **7 modos de direccionamiento** más comunes: inmediato, directo, indirecto, registro, indirecto con registro, con desplazamiento (relativo, base, indexado) y de pila/implícito — con su algoritmo, ventajas, desventajas y ejemplos en Assembler.
- Repasar (con apoyo del Cap. 17 de Angulo) los **tipos de datos**, las **categorías de operandos**, las **instrucciones privilegiadas** y el **repertorio de instrucciones** del Pentium: aritméticas, lógicas, de rotación/desplazamiento, de salto, de transferencia, de control de señalizadores, multisegmento y de coprocesador — con ejercicios resueltos de cálculo de registros y flags.

## 1. Repaso: ciclo de instrucción

Como en unidades anteriores, la ejecución de una instrucción se divide en:

- **Fase de búsqueda (fetch):** cálculo de la dirección física de la instrucción, orden de lectura (RD), carga del registro **IR**.
- **Fase de ejecución (execute):** decodificar el código de la instrucción, incrementar el **IP**, buscar el dato (RD) o guardarlo (WR) si la instrucción lo afecta (acceso a memoria en ambos casos), y generar la orden al módulo que opera sobre ese dato.

Es precisamente en la fase de ejecución donde entra en juego el **modo de direccionamiento**: define **cómo** la CPU localiza el operando que necesita la instrucción.

## 2. Modos de direccionamiento: dos aspectos a tener en cuenta

Para especificar operandos y operaciones en una instrucción hay que resolver:

1. **Cómo especificar la dirección de un operando** → *modo de direccionamiento*.
2. **Cómo se organizan los bits de la instrucción** para definir las direcciones de los operandos y la operación que realiza → *formato de la instrucción*.

Todas las computadoras ofrecen **más de un modo de direccionamiento**; es la **unidad de control** la que define cuál emplea cada instrucción (normalmente indicado por bits del código de operación).

### Notación usada (Stallings)

| Símbolo | Significado |
|---|---|
| **A** | Contenido de un campo de dirección en la instrucción |
| **R** | Contenido de un campo de dirección en la instrucción que referencia un registro |
| **EA** | Dirección efectiva (*Effective Address*): dirección real de la posición que contiene el operando referenciado |
| **(X)** | Contenido de la posición X |

> **Dirección efectiva (EA):** en un sistema sin memoria virtual es una dirección de memoria principal o un registro; en un sistema con memoria virtual es una dirección virtual o un registro. La correspondencia con la dirección física depende del mecanismo de paginación y no es visible para el programador.

### Tabla resumen de los modos básicos

| Modo | Algoritmo | Principal ventaja | Principal desventaja |
|---|---|---|---|
| **Inmediato** | Operando = A | No referencia a memoria | Operando de magnitud limitada |
| **Directo** | EA = A | Es sencillo | Espacio de direcciones limitado |
| **Indirecto** | EA = (A) | Espacio de direcciones grande | Referencias a memoria múltiples |
| **Registro** | EA = R | No referencia a memoria | Número limitado de registros |
| **Indirecto con registro** | EA = (R) | Espacio de direcciones grande | Referencia extra a memoria |
| **Con desplazamiento** | EA = A + (R) | Flexibilidad | Complejidad |
| **Pila** | EA = cabecera de la pila | No referencia a memoria | Aplicabilidad limitada |

## 3. Direccionamiento inmediato

- El operando **está presente en la propia instrucción**: `Operando = A`.
- Se usa para definir/usar **constantes** o fijar **valores iniciales** de variables. El número suele almacenarse en **complemento a 2** (bit más a la izquierda = signo).
- **Ventaja:** una vez capturada la instrucción, no hace falta una referencia a memoria para obtener el operando (se ahorra un ciclo de memoria/caché).
- **Desventaja:** el tamaño del número está restringido a la longitud del campo de direcciones, generalmente pequeño frente a la longitud de palabra.

![[Assets/u10_direccionamiento_inmediato.png]]

**Ejemplo:** `MOV AH, 05` → no hay acceso a memoria; el valor 05 es constante.

## 4. Direccionamiento directo

- El campo de direcciones contiene la **dirección efectiva del operando**: `EA = A`.
- Solo requiere **una** referencia a memoria, sin cálculos especiales.
- Fue común en las primeras generaciones de computadoras; aún se usa en algunos sistemas.
- **Limitación:** espacio de direcciones restringido (el campo A tiene un tamaño fijo, no puede cubrir toda la memoria).

![[Assets/u10_direccionamiento_directo.png]]

**Ejemplo:** `MOV AX, [0200]` → un acceso a memoria; el valor no es constante (se lee de la posición 0200).

## 5. Direccionamiento indirecto

- El campo de direcciones **referencia la dirección de una palabra de memoria** que, a su vez, contiene la dirección completa del operando: `EA = (A)`.
- **Ventaja:** para una longitud de palabra de N bits se dispone de un espacio de direcciones de **2ⁿ**.
- **Desventaja:** requiere **dos** referencias a memoria (una para obtener la dirección, otra para obtener el valor).

![[Assets/u10_direccionamiento_indirecto.png]]

**Ejemplo:** `MOV AX, [BX]` → dos accesos a memoria; el valor no es constante. En un lenguaje de alto nivel (p. ej. C) equivale a acceder a través de una **variable puntero**.

## 6. Direccionamiento por registro

- Igual al directo, pero el campo de direcciones referencia un **registro** en lugar de una posición de memoria: `EA = R`.
- El campo de direcciones que referencia registros suele ser de **3 o 4 bits** (8 o 16 registros de uso general direccionables).
- **Ventajas:** (1) campo de direcciones pequeño en la instrucción; (2) no se requieren referencias a memoria — el acceso a un registro interno es mucho más rápido que a memoria principal.
- **Desventaja:** espacio de direcciones muy limitado (pocos registros disponibles).

![[Assets/u10_direccionamiento_registro.png]]

**Ejemplo:** `MOV CX, DX` → un "acceso" (a registro, no a memoria); el valor no es constante.

## 7. Direccionamiento indirecto con registro

- Análogo al indirecto: la diferencia es que el campo de direcciones referencia un **registro** que contiene la dirección de memoria (no una posición de memoria que contiene esa dirección): `EA = (R)`.
- **Ventajas:** las mismas del indirecto, pero con **una referencia menos a memoria** (solo un acceso a memoria, porque la dirección ya está en un registro y no hay que ir a buscarla a memoria).

![[Assets/u10_direccionamiento_indirecto_registro.png]]

**Ejemplo:** `MOV AX, [SI]` → un acceso a memoria; el valor no es constante. En C equivale al **acceso a un vector** mediante un puntero/índice.

## 8. Direccionamiento con desplazamiento

- Combina el direccionamiento **directo** y el **indirecto con registro**: `EA = A + (R)`.
- Requiere que la instrucción tenga **dos campos de direcciones** (al menos uno explícito): el valor de uno de ellos (**A**) se usa directamente, y el otro (explícito o implícito por el código de operación) referencia un **registro** cuyo contenido se suma a A para obtener la EA.

![[Assets/u10_direccionamiento_con_desplazamiento.png]]

**Ejemplo:** `MOV AX, [SI + 08]` → un acceso a memoria; el valor no es constante. En C equivale a `*(p + 8)`, con `p` un puntero.

### Tres usos más comunes del direccionamiento con desplazamiento

![[Assets/u10_direccionamiento_relativo_base_indexado.png]]

a) **Direccionamiento relativo:** la dirección de la instrucción actual se suma al campo de direcciones (número en complemento a 2) para producir la EA. La dirección efectiva es un **desplazamiento relativo** a la dirección de la instrucción — típico de los saltos condicionales de corto alcance.

b) **Direccionamiento con registro base:** el registro referenciado contiene una **dirección de memoria** (la "base"), y el campo de dirección contiene un **desplazamiento** (normalmente sin signo) desde esa dirección. La referencia al registro puede ser explícita o implícita.

c) **Indexado:** es lo opuesto al anterior — el campo de dirección referencia una dirección de **memoria principal**, y el registro referenciado (el "índice") contiene el desplazamiento positivo desde esa dirección. Se usa típicamente para recorrer arreglos/vectores.

## 9. Direccionamiento de pila (o implícito)

- La **pila** es una matriz lineal de posiciones: un bloque de posiciones reservado con un **puntero al tope** (cabecera).
- En este modo, las instrucciones **no incluyen una referencia explícita a memoria**: operan directamente sobre la cabecera de la pila.
- Es una forma de **direccionamiento implícito**, ya que no hace falta explicitar la referencia a memoria (queda implícita en la propia instrucción).

![[Assets/u10_direccionamiento_pila.png]]

**Ejemplos:** `PUSH`, `POP`, `CLA` (*clear accumulator*).

## 10. Tipos de datos, categorías de operandos e instrucciones privilegiadas (Cap. 17 Angulo)

> Esta sección resume el contenido teórico del **repertorio de instrucciones** del Pentium (Cap. 17 de Angulo), que es la bibliografía indicada para la parte del TP que no está cubierta por la diapositiva de direccionamiento.

### Tipos de datos generales del Pentium

1. **Bits y cadenas de bits** (bit y bit string).
2. **Enteros** de distinto tamaño: **byte** (8 bits), **palabra/word** (16 bits) y **palabra doble/dword** (32 bits), con y sin signo.
3. **Cadenas de bytes/caracteres** (byte strings), usadas por las instrucciones de manejo de cadenas.
4. **BCD empaquetado y desempaquetado**, para representar dígitos decimales.

### Tipos de datos del coprocesador matemático (7 tipos, en 3 grupos)

| Grupo | Tipos de datos |
|---|---|
| **Enteros** | Entero de palabra (16 bits), entero corto (32 bits), entero largo (64 bits) |
| **Reales en punto flotante** | Precisión simple (32 bits), precisión doble (64 bits), precisión extendida (80 bits) |
| **BCD empaquetado** | Empaquetado (18 dígitos decimales con signo) |

### Categorías para referenciar operandos (3 categorías)

1. **Registro:** el operando está en un registro de la CPU.
2. **Inmediato:** el operando forma parte de la propia instrucción.
3. **Memoria:** el operando está en una posición de memoria principal, a la que se accede mediante alguno de los modos vistos en las secciones 3 a 9 (directo, indirecto, con registro, con desplazamiento — relativo, base o indexado —, etc.).

### Instrucciones privilegiadas

- Son instrucciones que **solo puede ejecutar el programador de sistemas** (el sistema operativo), porque manipulan recursos **globales y críticos** de la CPU: si las ejecutara sin control un programa de aplicación, comprometería la protección, la estabilidad o la seguridad de todo el sistema.
- Por eso su ejecución está condicionada al **nivel de privilegio** (Unidad 8): solo se permiten en el nivel más privilegiado (nivel 0, reservado al núcleo del SO).
- Se agrupan en **4 grupos**:
  1. **Control interno del procesador:** instrucciones que cargan/leen registros de sistema (p. ej. `LGDT`, `LIDT`, `LLDT`, `LTR`, `SMSW`, `LMSW`, `CLTS`, `HLT`).
  2. **Control de la gestión de memoria:** instrucciones vinculadas a la validación de segmentos y niveles de privilegio (p. ej. `LAR`, `LSL`, `VERR`, `VERW`, `ARPL`).
  3. **Control de entrada/salida:** instrucciones de E/S condicionadas por el **IOPL** (nivel de privilegio de E/S) del EFLAGS (p. ej. `IN`/`OUT` con puertos protegidos).
  4. **Control de interrupciones:** instrucciones que habilitan/deshabilitan las interrupciones enmascarables (p. ej. `CLI`, `STI`), reservadas al SO para no comprometer la atención de otras tareas.

## 11. TP de la unidad — resuelto paso a paso

> Enunciado completo: [[Assets/TP_Unidad10_Instrucciones.pdf]]. Bibliografía indicada: Cap. 17 de Angulo, "Repertorio de Instrucciones". A diferencia de las diapositivas (centradas en los modos de direccionamiento), varias preguntas del TP requieren conocimientos generales del repertorio de instrucciones IA-32/Pentium — se resuelven con el criterio de detalle esperado, mostrando el paso a paso de cada cálculo con registros/flags.

**1. Indique brevemente los 4 tipos de datos que maneja Pentium.**

Bits y cadenas de bits; enteros (byte, word de 16 bits, dword de 32 bits, con y sin signo); cadenas de bytes/caracteres; y BCD empaquetado/desempaquetado. → Sección 10.

**2. Indique brevemente los 3 grupos en los cuales se clasifican los 7 tipos de datos que puede manejar el coprocesador matemático.**

- **Enteros:** de palabra (16 bits), corto (32 bits) y largo (64 bits).
- **Reales en punto flotante:** simple precisión (32 bits), doble precisión (64 bits) y precisión extendida (80 bits).
- **BCD empaquetado.**

→ Sección 10.

**3. Indique brevemente las 3 categorías en que se agrupan las formas de referenciar los operandos por el µP Pentium.**

**Registro** (el operando está en un registro), **Inmediato** (el operando viaja en la propia instrucción) y **Memoria** (el operando se busca en memoria principal, mediante alguno de los modos de direccionamiento: directo, indirecto, con registro, con desplazamiento, relativo, base o indexado). → Secciones 3 a 9.

**4. Explique las instrucciones privilegiadas, la razón de su uso y los 4 grupos.**

Son instrucciones reservadas al **programador de sistemas** (SO), ejecutables únicamente en el **nivel de privilegio 0**, porque manipulan recursos globales del sistema (memoria, tareas, E/S, interrupciones); si un programa de aplicación pudiera ejecutarlas libremente, se rompería la protección entre tareas y la estabilidad del sistema. Se agrupan en: **(1)** control interno del procesador, **(2)** control de la gestión de memoria, **(3)** control de entrada/salida y **(4)** control de interrupciones. → Sección 10.

**5. Si AX = 3330 y DX = 4879, al ejecutarse `ADD AX, DX` ¿cómo queda AX? Justifique.**

```
  3330
+ 4879
------
  7BA9
```
**AX = 7BA9h.** Justificación: se suman los valores en hexadecimal dígito a dígito (0+9=9, 3+7=A, 3+8=B, 3+4=7). El resultado (7BA9 = 0111 1011 1010 1001) no supera FFFF, por lo tanto **CF = 0** (no hay acarreo fuera del bit 15). Ambos operandos son positivos (bit de signo = 0 en 3330 y en 4879) y el resultado también es positivo (bit 15 de 7BA9 = 0) → **OF = 0** (no hay overflow, un overflow con signo solo puede darse si ambos operandos tienen el mismo signo y el resultado da signo distinto).

**6. Si AX = 99FF y DX = 4879, al ejecutarse `SUB AX, DX` ¿cómo queda AX? Justifique.**

```
  99FF
- 4879
------
  5186
```
**AX = 5186h.** Como el minuendo (99FF) es mayor que el sustraendo (4879) en valor sin signo, no hay pedido de préstamo → **CF = 0**. Para el signo: 99FF tiene el bit 15 en 1 (se leería como negativo si se interpreta con signo) y 4879 lo tiene en 0 (positivo); el resultado 5186 tiene el bit 15 en 0 (positivo). Como los signos de los operandos son distintos y el signo del resultado **no coincide** con el del minuendo, se produce **overflow: OF = 1**.

**7. Si AX = 99FF y DX = 4879, al ejecutarse `CMP AX, DX` ¿cómo queda el reg. EFLAGS? Justifique**

`CMP` hace internamente una **resta (AX − DX) sin guardar el resultado**, solo actualiza los flags (idénticos a los de la pregunta 6, ya que la resta interna es la misma: 99FF − 4879 = 5186):
- **CF = 0** (no hubo préstamo, 99FF ≥ 4879).
- **OF = 1** (cambio de signo entre minuendo y resultado, igual que en la pregunta 6).
- **ZF = 0** (el resultado 5186 no es cero → AX ≠ DX).
- **SF = 0** (bit 15 del resultado 5186 = 0 → resultado "positivo").
- **PF**: se calcula sobre el byte bajo del resultado (0x86 = 1000 0110, tiene 3 unos → paridad impar) → **PF = 0**.

**8. Explique brevemente que hacen las instrucciones aritméticas DEC, INC, MUL e IMUL.**

- **DEC** (*decrement*): resta 1 al operando indicado (registro o memoria). No afecta el flag CF (a diferencia de SUB), solo los demás flags aritméticos (ZF, SF, OF, PF, AF).
- **INC** (*increment*): suma 1 al operando indicado. Igual que DEC, no afecta CF.
- **MUL** (*multiplicación sin signo*): multiplica el operando indicado por el acumulador implícito (AL, AX o EAX según el tamaño) y deja el resultado (doble de ancho) en AX, DX:AX o EDX:EAX. Pone **CF = OF = 1** si la mitad alta del resultado no es cero (es decir, si el resultado no entra en la mitad baja).
- **IMUL** (*multiplicación con signo*): igual que MUL pero interpretando los operandos como números con signo (complemento a 2); también puede usarse en su forma de 2 o 3 operandos para multiplicar por un valor inmediato.

**9. Si AX = 3330 y DX = 4879, al ejecutarse `AND AX, DX` ¿cómo queda el reg. EFLAGS? Justifique**

```
3330 = 0011 0011 0011 0000
4879 = 0100 1000 0111 1001
AND  = 0000 0000 0011 0000  = 0030h
```
El resultado es **AX = 0030h** (no se pide en la consigna, pero es la base para los flags). `AND` (como toda instrucción lógica) **limpia CF = 0 y OF = 0** siempre. El resultado 0030h no es cero → **ZF = 0**; su bit 15 es 0 → **SF = 0**; la paridad se calcula sobre el byte bajo (0x30 = 0011 0000, dos unos → paridad **par**) → **PF = 1**.

**10. Si AX = AFE4, al ejecutarse `ROL AX, 1` ¿cómo queda AX? Justifique**

```
AFE4 = 1010 1111 1110 0100
```
`ROL` (*rotate left*) rota todos los bits un lugar a la izquierda; el bit que "sale" por la izquierda (bit 15) entra por la derecha (bit 0) y además se copia a **CF**.

- Bit 15 original = **1** → sale y entra en CF y en bit 0.
- Bits 14-0 se corren un lugar a la izquierda.

```
Resultado: 0101 1111 1100 1001 = 5FC9h
```
**AX = 5FC9h.** **CF = 1** (el bit que salió era 1). Para rotaciones de 1 bit, **OF** se define como el XOR entre CF y el nuevo bit de signo (bit 15 del resultado): nuevo bit 15 = 0, entonces **OF = CF ⊕ bit15 = 1 ⊕ 0 = 1**.

**11. Explique brevemente que hacen las instrucciones TEST, SAL y SAR.**

- **TEST:** hace un `AND` entre los dos operandos **sin guardar el resultado**, solo actualiza los flags (ZF, SF, PF; limpia CF y OF). Se usa para comprobar bits puntuales sin modificar el operando (análogo lógico de CMP).
- **SAL** (*Shift Arithmetic Left*): desplazamiento aritmético a la izquierda; es idéntico en efecto al `SHL` (desplazamiento lógico izquierdo) — entran ceros por la derecha y el bit que sale por la izquierda pasa a CF.
- **SAR** (*Shift Arithmetic Right*): desplazamiento aritmético a la derecha — a diferencia del desplazamiento lógico (`SHR`), **preserva el bit de signo**: por la izquierda entran copias del bit de signo original (para mantener el signo del número en complemento a 2), y el bit que sale por la derecha pasa a CF.

**12. Si AX = 3330 y DX = 4879, al ejecutarse `SHLD AX, DX, 2` ¿cómo quedan AX y DX?**

`SHLD dest, src, cuenta` desplaza **dest** a la izquierda la cantidad de bits indicada, y **completa los bits menos significativos que quedan libres con los bits más significativos de src** (src no se modifica).

```
AX = 3330 = 0011 0011 0011 0000
DX = 4879 = 0100 1000 0111 1001  (bits más significativos: "01")

AX << 2 (con ceros)      = 1100 1100 1100 00|00
reemplazo de los 2 bits bajos por los 2 bits altos de DX ("01"):
AX resultado             = 1100 1100 1100 00|01 = CCC1h
```
**AX = CCC1h**, **DX = 4879h (sin cambios)**. **CF** toma el último bit que sale de AX por la izquierda (el segundo bit desplazado, que era 0) → **CF = 0**.

**13. Explique cómo queda el registro PC al ejecutarse la instrucción `JMP FF456781`. Justifique**

Es un salto **incondicional directo** (con dirección inmediata de 32 bits). El **PC (EIP)** se carga directamente con el valor indicado: **PC = FF456781h**. Justificación: en el direccionamiento inmediato/directo de los saltos, el operando de la instrucción *es* (o referencia directamente) la nueva dirección a ejecutar, por lo que no hay cálculo de desplazamiento relativo ni acceso adicional a memoria: el flujo de control continúa a partir de esa dirección absoluta.

**14. Explique brevemente que hacen las instrucciones de salto JA, JC y JE.**

- **JA** (*Jump if Above*): salta si el operando es "mayor que" en una comparación sin signo previa, es decir si **CF = 0 y ZF = 0**.
- **JC** (*Jump if Carry*): salta si **CF = 1** (hubo acarreo/préstamo en la operación anterior).
- **JE** (*Jump if Equal*): salta si **ZF = 1** (el resultado de la comparación/resta anterior fue cero, es decir, los operandos eran iguales).

**15. Explique brevemente que hacen las instrucciones de transferencia de datos MOV, IN, POP y PUSH.**

- **MOV:** copia un dato desde el origen (registro, memoria o inmediato) hacia el destino (registro o memoria), sin afectar los flags.
- **IN:** lee un dato desde un **puerto de E/S** (dirección de puerto en DX o inmediata) y lo carga en el acumulador (AL/AX/EAX).
- **POP:** extrae (lee) el dato en la cima de la pila (apuntada por SS:SP) hacia el operando destino, y luego **incrementa SP**.
- **PUSH:** **decrementa SP** y luego almacena el operando origen en la nueva cima de la pila.

**16. Explique brevemente que hacen las instrucciones de control de señalizadores CLC y CLI.**

- **CLC** (*Clear Carry*): pone en **0** el flag de acarreo (**CF = 0**).
- **CLI** (*Clear Interrupt*): pone en **0** el flag de habilitación de interrupciones (**IF = 0**), con lo cual la CPU deja de atender interrupciones **enmascarables** hasta que se vuelva a habilitar con `STI`.

**17. Explique y dé un ejemplo de la instrucción BT.**

**BT** (*Bit Test*) copia al flag **CF** el valor de un bit determinado de un operando (registro o memoria), indicado por un índice, **sin modificar el operando**. Se usa para consultar el estado de un bit puntual (por ejemplo, un flag dentro de una máscara) antes de decidir un salto condicional.

*Ejemplo:* `BT AX, 3` → copia a CF el valor del **bit 3** de AX. Si AX = 0000 0000 0000 1000b (bit 3 = 1), luego de ejecutar la instrucción **CF = 1**.

**18. Explique brevemente que hacen las instrucciones multisegmento CALL, RET, INT e IRET.**

- **CALL:** invoca un procedimiento/subrutina. En su forma **multisegmento (far)** apila el **CS y el IP** de retorno, y carga CS:IP con la dirección del procedimiento destino (en otro segmento de código).
- **RET:** retorna de un procedimiento. En su forma **far**, extrae de la pila **IP y CS** (en ese orden) y los restaura, devolviendo el control a la instrucción siguiente a la del CALL original.
- **INT:** invoca una **interrupción de software** por su número de vector; apila **FLAGS, CS e IP**, pone **IF = 0** (y TF = 0), y carga CS:IP con la dirección de la rutina de servicio indicada en la IDT (ver Unidad 9).
- **IRET:** retorna de una rutina de interrupción o excepción; extrae de la pila **IP, CS y FLAGS** (restaurando también IF y TF), retomando la ejecución del programa interrumpido.

**19. Explique brevemente que hacen las instrucciones HLT, LGDT, LIDT y LLDT.**

- **HLT** (*Halt*): detiene la ejecución de instrucciones de la CPU hasta que se reciba una interrupción (NMI, una interrupción enmascarable con IF = 1, o un reset). Es una instrucción **privilegiada**.
- **LGDT** (*Load GDT Register*): carga el registro **GDTR** (base y límite) con el valor indicado en el operando de memoria, estableciendo dónde está la Tabla de Descriptores Globales (Unidad 11). Privilegiada.
- **LIDT** (*Load IDT Register*): carga el registro **IDTR** (base y límite) con el valor del operando, estableciendo dónde está la IDT (Unidad 9). Privilegiada.
- **LLDT** (*Load LDT Register*): carga el registro **LDTR** con el selector indicado, activando una determinada Tabla de Descriptores Locales. Privilegiada.

**20. Explique brevemente qué hace la instrucción del coprocesador WAIT.**

**WAIT** (también `FWAIT`) suspende la ejecución del procesador principal hasta que el **coprocesador matemático** termine la operación de punto flotante que tenga en curso (o hasta que señale una excepción pendiente). Se usa para **sincronizar** la CPU y el coprocesador, evitando que la CPU continúe con una instrucción que dependa de un resultado del coprocesador todavía no disponible.

**21. Explique brevemente qué hace y para qué se usa la instrucción NOP.**

**NOP** (*No Operation*) no realiza ninguna operación: solo consume un ciclo (o los ciclos que le tome buscarse y decodificarse) y avanza el IP a la instrucción siguiente, sin modificar registros ni flags. Se usa para **rellenar espacio** (alinear código o datos en memoria), para **retardos** controlados de tiempo, o como **marcador temporal** en depuración (por ejemplo, para reemplazar más adelante por otra instrucción sin desplazar el resto del código).


---

## 12. TP de laboratorio: Assembler bajo DEBUG (resuelto y explicado)

> Enunciado: *TP10 LABORATORIO ASSEMBLER BAJO DEBUG*. Hay 7 prácticos. No es teoría: se carga un programa chico, se ejecuta con *trace* y se explican los registros. Las respuestas salen de **leer el volcado de registros**.

### 12.1 Comandos de DEBUG que usa el TP

| Comando | Qué hace |
|---|---|
| `-a 0100` | **Assemble**: escribir instrucciones en assembler a partir del desplazamiento 0100 (los programas `.COM` arrancan en 100h). |
| `-u 100 10a` | **Unassemble**: muestra la dirección, el **código de máquina** y la instrucción de ese rango. |
| `-r` | Muestra todos los registros y banderas, y la próxima instrucción. |
| `-r IP` | Muestra y deja **modificar** un registro (se escribe el nuevo valor y Enter). |
| `-t` | **Trace**: ejecuta **una** instrucción y muestra de nuevo los registros. |
| `-g` | **Go**: ejecuta el programa entero. |
| `-e 0200` | **Enter**: muestra el byte en `DS:0200` y deja cambiarlo. |
| `-d` | **Dump**: volcado de memoria (útil para verificar los resultados). |

> **Regla del TP:** al terminar cada programa, el IP queda al final. Para correr el programa siguiente hay que **volver a poner `IP = 0100`** con `-r IP`.

### 12.2 Cómo leer el volcado (esto responde casi todas las preguntas)

```
AX=0800  BX=0000  CX=0000  DX=0000  SP=FFEE  BP=0000  SI=0000  DI=0000
DS=0CD7  ES=0CD7  SS=0CD7  CS=0CD7  IP=0102   NV UP EI PL NZ NA PO NC
0CD7:0102 80C403        ADD     AH,03
```

- **Línea 1: registros de propósito general.** AX acumulador, BX base, CX contador, DX datos y puertos de E/S, SP puntero de pila, BP base de pila, SI y DI índices de cadenas.
- **Línea 2:**
  - Registros de segmento: DS datos, ES extra, SS pila, CS código.
  - IP (puntero de instrucciones).
  - Banderas.
- **Línea 3: la próxima instrucción a ejecutar** (todavía no se ejecutó). Se lee así: `0CD7:0102` es la dirección segmentada (segmento:desplazamiento), `80C403` es el código de máquina y `ADD AH,03` es el mnemónico.

**Banderas** (cada una tiene dos nombres, según valga 0 o 1):

| Bandera | Valor 0 | Valor 1 |
|---|---|---|
| Overflow (OF) | **NV** | **OV** |
| Dirección (DF) | **UP** | DN |
| Interrupciones (IF) | DI | **EI** |
| Signo (SF) | **PL** (positivo) | **NG** (negativo) |
| Cero (ZF) | **NZ** | **ZR** |
| Acarreo auxiliar (AF) | **NA** | **AC** |
| Paridad (PF) | **PO** (impar) | **PE** (par) |
| Acarreo (CF) | **NC** | **CY** |

> Paridad: **PE** significa que el resultado tiene una **cantidad par de unos**. Ejemplo: FF tiene ocho unos, o sea par, y da PE. 07 tiene tres unos, o sea impar, y da PO.

### 12.3 Práctico 1: suma y resta de constantes (`8 + 3 − 4`)

```
mov ah,8   →  B4 08
add ah,3   →  80 C4 03
sub ah,4   →  80 EC 04
```

**Modo de direccionamiento de las tres instrucciones:** **inmediato**. El dato (8, 3 o 4) viaja **dentro de la instrucción**. El destino, AH, está direccionado **por registro**. La combinación se llama "registro ← inmediato".

**Códigos de máquina, código de operación y campo DATA:**

| Instrucción | Código de máquina | Código de operación | Campo DATA |
|---|---|---|---|
| `MOV AH,08` | `B4 08` | `B4` (MOV r8,imm8: B0 + número de registro, AH es 4) | `08` |
| `ADD AH,03` | `80 C4 03` | `80 C4` (80 = grupo aritmético inmediato, C4 = ADD sobre AH) | `03` |
| `SUB AH,04` | `80 EC 04` | `80 EC` (SUB sobre AH) | `04` |

**Base y desplazamiento.** En una dirección segmentada `0CD7:0105`, la **base** es el segmento (`0CD7`) y el **desplazamiento** es lo que se suma (`0105`). Para la 3.ª instrucción:
- Base = `0CD7`, desplazamiento = `0105`.
- Dirección física = `0CD7 × 10h + 0105` = `0CD70 + 0105` = **`0CE75`**.

**`-r`:** muestra los registros (también puede modificarlos). En este ejemplo no carga nada. Lo que vale la pena observar es que `IP = 0100`, porque ahí se ensambló el programa. Se usa `-r IP` para **reubicar el IP en 0100** cuando se quiere volver a ejecutar.

**Banderas después de IP:** `NV UP EI PL NZ NA PO NC`. Se leen como: sin overflow, dirección hacia arriba (UP), interrupciones habilitadas, resultado positivo, no cero, sin acarreo auxiliar, paridad impar, sin acarreo.

**Tercera línea del primer trace:** `0CD7:0102 80C403 ADD AH,03` es la dirección de la **próxima** instrucción, su código de máquina y su mnemónico.

### 12.4 Práctico 2: resultado negativo (`8 + 3 − 12`)

El resultado es `0B − 0C = −1`, que en 8 bits es `FF`.

- **AH en binario:** `1111 1111`. El bit 7 (el de la izquierda) es el **signo** (1 = negativo). En complemento a 2 los demás bits, junto con el de signo, dan el valor.
- **Rango signado de 8 bits:** −128 a +127 (`80h` a `7Fh`).
- **Comprobar que `FF = −1`:** se invierte (`0000 0000`) y se suma 1 (`0000 0001` = 1), con el signo negativo, o sea −1.
- **Banderas** (`NV NG NZ AC PE CY`):
  - **NG:** el resultado es negativo.
  - **CY:** hubo acarreo (en restas significa "pidió prestado").
  - **AC:** hubo acarreo del bit 3.
  - **PE:** FF tiene ocho unos (par).
  - **NV:** no hay overflow, porque −1 entra en 8 bits con signo.
- **¿Qué banderas cambiaron?**
  - **MOV → ADD:** ninguna. MOV no toca las banderas, y `8 + 3 = 0B` no activa ninguna.
  - **ADD → SUB:** cambian PL→NG, NA→AC, PO→PE y NC→CY.

**Mapa parcial de memoria** (segmento `0CD7`, un byte por dirección):

| Desplazamiento | Contenido | Pertenece a |
|---|---|---|
| 0100 | `B4` | MOV AH,08 (código de operación) |
| 0101 | `08` | MOV AH,08 (dato) |
| 0102 | `80` | ADD AH,03 |
| 0103 | `C4` | ADD AH,03 |
| 0104 | `03` | ADD AH,03 (dato) |
| 0105 | `80` | SUB AH,0C |
| 0106 | `EC` | SUB AH,0C |
| 0107 | `0C` | SUB AH,0C (dato) |

> En el listado del TP, `0108 JZ 011F` no es parte de tu programa. Es basura que ya estaba en memoria.

### 12.5 Práctico 2 (parte B): MOV con memoria

```
0100  B8 08 00     MOV AX,0008        ; AX ← 0008
0103  A3 00 02     MOV [0200],AX      ; memoria ← AX
0106  B8 03 00     MOV AX,0003
0109  A3 03 02     MOV [0203],AX
010C  A1 00 02     MOV AX,[0200]      ; AX ← memoria
010F  03 06 03 02  ADD AX,[0203]
0113  A3 05 02     MOV [0205],AX
```

**Preguntas del TP y respuestas:**

- **Diferencia entre `MOV AH,08` y `MOV AX,0008`.** Con AH solo cambia el byte alto, así que `AX = 0800` y AL queda como estaba. Con AX cambian los 16 bits, así que `AX = 0008`.
- **¿El cambio de registro afecta el código de operación?** Sí, porque el registro destino y el tamaño están **dentro del opcode**. `B4` es MOV AH,imm8, con un dato de 1 byte (`08`). `B8` es MOV AX,imm16, con un dato de 2 bytes (`08 00`).
- **`DS:0200 = 0008`.** Es el operando de memoria: el desplazamiento `0200` dentro del segmento `DS`, y entre paréntesis lo que hay en esa palabra de memoria. Ojo: DEBUG muestra lo que hay en memoria **antes** de ejecutar la instrucción. En una instrucción de escritura puede mostrar el valor viejo. No te asustes si en tu máquina el valor no coincide con la guía.
- **`-E 0200` y `-E 0201`.** Muestran `08` y `00`. Se cargan **dos bytes** porque AX es de 16 bits y cada dirección guarda **1 byte**. Se guarda en **little endian**: el byte bajo (`08`) en la dirección menor y el alto (`00`) en la mayor.
- **`MOV AX,0003`:** se borra el `0008` de AX y queda `0003`.
- **`MOV [0203],AX`** tiene opcode `A3` y campo data (la dirección) `0203`, que se guarda como `03 02` por el little endian. Modifica **0203 = `03`** y **0204 = `00`**.
- **`MOV AX,[0200]`** trae de vuelta el 8, así que `AX = 0008`.
- **`ADD AX,[0203]`:** `8 + 3` da `AX = 000B`.
- **`MOV [0205],AX`:** `0205 = 0B` y `0206 = 00`.

**Tabla a completar:**

| Instrucción | Destino | Contenido |
|---|---|---|
| `MOV AX,0008` | AH / AL | `00` / `08` |
| `MOV [0200],AX` | `0CD7:0200` / `0201` | `08` / `00` |
| `MOV AX,0003` | AH / AL | `00` / `03` |
| `MOV [0203],AX` | `0CD7:0203` / `0204` | `03` / `00` |
| `MOV AX,[0200]` | AH / AL | `00` / `08` |
| `ADD AX,[0203]` | AH / AL | `00` / `0B` |
| `MOV [0205],AX` | `0CD7:0205` / `0206` | `0B` / `00` |

### 12.6 Práctico 3: multiplicación

- `MUL CX` es **sin signo**: multiplica `AX × CX`. El resultado de 32 bits queda en el **par `DX:AX`** (DX tiene la parte alta).
  - `FFFF × FFFF = FFFE0001`, o sea `DX = FFFE` y `AX = 0001`.
  - Las banderas **OV** y **CY** se activan cuando la parte alta (DX) **no es cero**, es decir, cuando el resultado no entra en AX.
  - El TP tiene un error de tipeo: `mul , cx`. Lo correcto es `mul cx`.
- `IMUL` es **con signo**: se usa para `4 × −1`.
  - Con `imul cl` el operando es de **8 bits**, entonces `AL × CL → AX`. Acá `04 × FF (−1) = FFFC`, que es −4.
  - **NG** indica negativo. Para pasarlo a decimal se hace el complemento a 2 de `FFFC`, que da `0004`, o sea **−4**.
  - Con operando de 8 bits el resultado queda en **AX**, y DX no se toca. Si quisieras el par `DX:AX`, tendrías que usar `imul cx` (16 bits): `DX = FFFF`, `AX = FFFC`.

### 12.7 Práctico 4: división

- `DIV BL` divide **AX entre BL**. El **cociente** queda en **AL** y el **resto** en **AH**.
  - `7 / 3`: **AL = 02** (cociente) y **AH = 01** (resto). Por eso AX = `0102`.
- `IDIV BL` es la versión con signo. Ejemplo: `1C ÷ FF`.
  - Dividendo `1C` = **+28**.
  - Divisor `FF` = **−1** (en complemento a 2).
  - Cociente `E4` = **−28** (el complemento a 2 de `E4` es `1C`).
  - Resto `00`.

### 12.8 Práctico 5: desplazamientos y rotaciones (valor `A0 = 1010 0000`)

| Instrucción | Qué hace | Resultado | CF |
|---|---|---|---|
| `SHR AL,1` | Desplaza a la derecha y entra 0 por la izquierda. Es lo mismo que **dividir por 2** sin signo. | `0101 0000` = `50` | **0** (el bit que salió) |
| `ROL AL,1` | **Rota** a la izquierda. El bit 7 sale y vuelve a entrar por el bit 0. | `0100 0001` = `41` | **1** (copia del bit que salió) |
| `ROR AL,1` | Rota a la derecha. El bit 0 vuelve a entrar por el bit 7. | `0101 0000` = `50` | **0** |
| `RCL AL,1` | Rota a la izquierda **a través del carry** (9 bits: carry + AL). Entra el carry viejo por la derecha. | `0100 0000` = `40` | **1** |
| `RCR AL,1` | Rota a la derecha a través del carry. Entra el carry viejo por la izquierda. | `0101 0000` = `50` | **0** |

> **Ojo:** el TP dice "desplazamiento aritmético", pero usa `SHR`, que es **lógico**. El aritmético correcto es `SAR`: mantiene el bit de signo, así que `A0` daría `D0`.
> `RCL` y `RCR` dependen del **carry anterior** (acá era 0). Si el carry hubiera sido 1, el resultado cambia (por ejemplo, `RCR` daría `D0`).

### 12.9 Práctico 6: saltos, bucles y la INT 21h

**Programa:**

```
0100 JMP 130              ; salta por encima del mensaje (que está guardado desde 102)
0102 "que tengas...$"     ; los datos viven en el segmento de código
0130 MOV BX,7             ; contador de repeticiones
0133 MOV DX,102           ; DX apunta al mensaje
0136 MOV AH,9             ; función 9 de INT 21h
0138 INT 21               ; imprime la cadena
013A DEC BX               ; contador - 1 (afecta a ZF)
013B JNZ 138              ; si BX ≠ 0, repite
013D INT 20               ; fin del programa
```

**Preguntas:**

- **INT 21h, función 9:**
  - Es la interrupción de servicios del DOS.
  - Se llama con `INT 21h` con **AH = 09h** y **DX = desplazamiento de la cadena** en DS, que debe terminar en **`$`**.
  - **Imprime la cadena** en pantalla.
- **¿Para qué sirve BX y por qué se decrementa?** Es el **contador de repeticiones**. `DEC BX` baja 1 por vuelta, y cuando llega a 0 se activa **ZF**. Así `JNZ` ya no salta y el programa termina.
- **JNZ 138 con BX = 4:** ZF = 0, así que **salta** y `IP = 0138`.
- **JNZ 138 con BX = 0:** ZF = 1, **no salta**, y `IP = 013D` (la instrucción siguiente, `INT 20`).
- **Otros nombres:**
  - Salto **incondicional** = bifurcación incondicional o salto absoluto (`JMP`).
  - Salto **condicional** = bifurcación condicional o salto condicionado (`JNZ`, `JZ`, etc., según las banderas).
- **¿`INT` es de ruptura de secuencia?** Sí. También lo son `JMP`, `Jcc`, `CALL`, `RET`, `IRET` y `LOOP`: todas alteran el orden normal de ejecución.

| Instrucción | Qué hace |
|---|---|
| `JMP` | Salto incondicional. `IP ← destino`. No guarda el retorno. |
| `JNZ` (`Jcc`) | Salta solo si se cumple la condición (acá, ZF = 0). |
| `CALL` | **Guarda el IP en la pila** (y CS si es lejano) y salta a la subrutina. |
| `RET` | **Saca el IP de la pila** y vuelve de la subrutina. |
| `INT n` | Guarda **FLAGS, CS e IP** en la pila, desactiva IF y TF, y salta a la rutina del vector `n`. |
| `IRET` | Restaura IP, CS y FLAGS desde la pila: vuelve de una interrupción. |
| `LOOP` | Hace `CX ← CX − 1` y salta si **CX ≠ 0** (no toca las banderas). |

- **INT 20h:** termina el programa y devuelve el control al sistema operativo (o a DEBUG). Por eso aparece "El programa ha terminado de forma normal".
- **Variante con `JCXZ`:** usa CX como contador (`dec cx; jcxz 13f; jmp 138`). `JCXZ` salta **si CX = 0**, sin mirar las banderas.
- **Variante con `LOOP`:** reemplaza `dec cx` + salto + contador por `loop 138`. En el TP hay un error de tipeo: `0CD5:0141 jmp 130` debería ser `0100 jmp 130`.

### 12.10 Práctico 7: la pila (PUSH y POP)

En memoria: `1100 = D8` y `1101 = D7`, o sea la palabra `D7D8` (little endian). Registros: `AX = D5D6`, `BX = D3D4`, `CX = D1D2`. SP arranca en `FFEE`.

**Reglas de la pila:**
- La pila **crece hacia abajo**.
- `PUSH`: primero `SP ← SP − 2` y después escribe la palabra (byte bajo en la dirección menor).
- `POP`: lee la palabra y después `SP ← SP + 2`.
- Orden **LIFO**: el último en entrar es el primero en salir.

**Carga de la pila:**

| Instrucción | SP después | Dirección y contenido |
|---|---|---|
| `PUSH [1100]` | `FFEC` | `FFEC = D8`, `FFED = D7` |
| `PUSH AX` | `FFEA` | `FFEA = D6`, `FFEB = D5` |
| `PUSH BX` | `FFE8` | `FFE8 = D4`, `FFE9 = D3` |
| `PUSH CX` | `FFE6` | `FFE6 = D2`, `FFE7 = D1` |

**Descarga de la pila:**

| Instrucción | Registro destino | SP después |
|---|---|---|
| `POP AX` | `AX = D1D2` | `FFE8` |
| `POP BX` | `BX = D3D4` | `FFEA` |
| `POP CX` | `CX = D5D6` | `FFEC` |
| `POP DX` | `DX = D7D8` | `FFEE` |

> El único cambio visible en los `PUSH` es que **SP baja de a 2**. Se puede guardar tanto un registro como el contenido de una posición de memoria (`push [1100]`). Los datos salen **en orden inverso**, así que AX termina con lo que se apiló en último lugar (CX).
