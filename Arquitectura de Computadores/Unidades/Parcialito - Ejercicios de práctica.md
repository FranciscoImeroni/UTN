# Parcialito de práctica (unidades 8 a 13)

> Un ejercicio por cada tipo de la **Parte 1** de [[Guía de Examen - Ejercicios, Mapa de la PC y Fórmulas]]. Los hice en el mismo orden de la tabla (1 a 12).
> **Cómo usarlo:** resolvé cada ejercicio en papel, **sin abrir la solución**. Después desplegá el recuadro de abajo ("Cómo se hace y respuesta") y compará.
> **De dónde sale cada ejercicio** está marcado con la etiqueta **Fuente**. Los TPs de segmentación (U11) y paginación (U12) son solo teóricos, no traen cuentas. Para esos tipos (2, 3, 4 y 5) **armé los ejercicios yo**, con los métodos de las diapositivas. Los demás salen de los TPs, de las diapositivas de la cátedra o de los ejemplos de la clase.
> Tiempo sugerido para hacerlo todo seguido: ~2 horas.

---

## Ejercicio 1: dirección física en modo real

**Fuente:** diapositivas de la unidad 11, TP de DEBUG (unidad 10) y ejemplo de la unidad 9.

Calculá la dirección física de cada dirección lógica:
- a) `3A2B:1301`
- b) `0CD7:0105`
- c) `13E0:0106`

> [!check]- Cómo se hace y respuesta
> **Método:** `Física = Segmento × 10h + Desplazamiento`. Multiplicar por `10h` es agregar un `0` hexa a la derecha del segmento.
> - a) `3A2B0 + 1301` = **`3B5B1`**
> - b) `0CD70 + 0105` = **`0CE75`**
> - c) `13E00 + 0106` = **`13F06`**
> 
> Sumá en hexa, dígito a dígito, de derecha a izquierda, llevando "1" cuando pasás de F.

---

## Ejercicio 2: decodificar un selector

**Fuente:** armado por mí (el TP 11 pregunta el concepto, no pide cuentas).

Para cada selector indicá **Índice, TI (qué tabla) y RPL**, y la dirección donde está el descriptor:
- a) `002Bh`
- b) `0017h`

> [!check]- Cómo se hace y respuesta
> **Método:** el selector tiene 16 bits: `Índice (13) | TI (1) | RPL (2)`.
> - Índice = selector desplazado 3 bits a la derecha (dividir por 8).
> - TI = bit 2 (0 = GDT, 1 = LDT).
> - RPL = los 2 bits bajos.
> - Descriptor en `(GDTR o LDTR) + Índice × 8`.
> 
> a) `002Bh` = `0000 0000 0010 1011`
> - Índice = `0000 0000 0010 1` = **5**, TI = **0** (GDT), RPL = **3**.
> - Descriptor en **`GDTR + 28h`** (5 × 8 = 40 = 28h).
> 
> b) `0017h` = `0000 0000 0001 0111`
> - Índice = `0000 0000 0001 0` = **2**, TI = **1** (LDT), RPL = **3**.
> - Descriptor en **`LDTR + 10h`** (2 × 8 = 16 = 10h).

---

## Ejercicio 3: dirección lineal con descriptor

**Fuente:** armado por mí (se apoya en la pregunta 17 del TP 11: base, límite y granularidad).

Un descriptor tiene **Base = 10000000h** y **Límite = 0001Fh**. Calculá la dirección lineal, o decí qué pasa, en estos casos:
- a) **G = 0** y desplazamiento `00000010h`
- b) **G = 0** y desplazamiento `00000020h`
- c) **G = 1** y desplazamiento `0001FFFFh`

> [!check]- Cómo se hace y respuesta
> **Método:** primero calculás el tamaño del segmento y verificás `desplazamiento ≤ límite`. Si pasa, `Lineal = Base + Desplazamiento`.
> - Si G = 0, el límite está en bytes: el desplazamiento máximo es `Límite = 1Fh`.
> - Si G = 1, el límite está en páginas de 4 KB: el desplazamiento máximo es `(Límite + 1) × 4 KB − 1`.
> 
> a) `10h ≤ 1Fh`, está dentro. Lineal = `10000000h + 10h` = **`10000010h`**.
> 
> b) `20h > 1Fh`, se pasa del límite. **Excepción de protección general** (no se calcula dirección).
> 
> c) Con G = 1, el tamaño es `(1Fh + 1) × 4 KB = 32 × 4 KB = 128 KB = 20000h`. El desplazamiento máximo es `1FFFFh`, así que `1FFFFh` entra justo. Lineal = `10000000h + 1FFFFh` = **`1001FFFFh`**.

---

## Ejercicio 4: traducir una dirección lineal con paginación

**Fuente:** armado por mí (el TP 12 es teórico). Usa el mecanismo de las diapositivas de la unidad 12.

Se tiene `CR3 = 00100000h` y la dirección lineal `00403025h`. La entrada de directorio contiene `00200007h` y la entrada de tabla contiene `00ABC067h`.
- a) Separá la dirección lineal en Directorio, Tabla y Desplazamiento (páginas de 4 KB).
- b) ¿En qué direcciones físicas están la entrada de directorio y la entrada de tabla?
- c) ¿Cuál es la dirección física final?
- d) Decodificá los atributos de la entrada de tabla (`067h`).
- e) ¿Cómo cambia todo si PSE = 1 (páginas de 4 MB)?

> [!check]- Cómo se hace y respuesta
> **a)** `00403025h` = `0000 0000 0100 0000 0011 0000 0010 0101`
> - Directorio (10 bits altos) = `0000000001` = **1**
> - Tabla (10 bits siguientes) = `0000000011` = **3**
> - Desplazamiento (12 bits bajos) = **`025h`**
> 
> Atajos: `Directorio = lineal >> 22`, `Tabla = (lineal >> 12) AND 3FFh`, `Desplazamiento = lineal AND FFFh`.
> 
> **b)** Cada entrada ocupa 4 bytes, así que `dirección = base + índice × 4`.
> - Entrada de directorio: `CR3 + 1 × 4` = **`00100004h`**.
> - Esa entrada contiene `00200007h`: la base de la tabla es `00200000h` (los 12 bits bajos son atributos).
> - Entrada de tabla: `00200000h + 3 × 4` = **`0020000Ch`**.
> 
> **c)** La entrada de tabla contiene `00ABC067h`: la base del marco es `00ABC000h` (se descartan los 12 bits de atributos). 
> `Física = 00ABC000h + 025h` = **`00ABC025h`**.
> 
> **d)** `067h` = `0110 0111`, de derecha a izquierda:
> - bit 0 (**P**) = 1 → página presente.
> - bit 1 (**R/W**) = 1 → lectura y escritura.
> - bit 2 (**U/S**) = 1.
> - bit 3 (**PWT**) = 0, bit 4 (**PCD**) = 0.
> - bit 5 (**A**) = 1 → fue accedida.
> - bit 6 (**D**) = 1 → fue escrita (sucia).
> - bit 7 (**SIZ**) = 0.
> 
> **e)** Con PSE = 1 la dirección se parte `Directorio (10) | Desplazamiento (22)`.
> - Directorio = **1**.
> - Desplazamiento = `403025h AND 3FFFFFh` = **`003025h`**.
> - Hay **un solo acceso** (no hay tabla de páginas): la entrada de directorio da directamente la base de la página de 4 MB.

---

## Ejercicio 5: tiempo medio con TLB

**Fuente:** armado por mí. Es la misma cuenta que el tiempo medio de la caché, aplicada a la TLB (unidad 12, diapositiva de TLB).

Una TLB tiene **95% de aciertos** y un tiempo de acceso de **2 ns**. La memoria principal tarda **60 ns**. Un fallo de TLB obliga a leer directorio y tabla (2 accesos) y después el dato.
- a) Tiempo de una referencia con **acierto** y con **fallo** de TLB.
- b) Tiempo medio.
- c) Tiempo sin TLB y cuántas veces es más rápido con TLB.

> [!check]- Cómo se hace y respuesta
> **a)**
> - Acierto: `t_TLB + t_mem` = 2 + 60 = **62 ns**.
> - Fallo: `t_TLB + 2 × t_mem` (directorio y tabla) `+ t_mem` (el dato) = 2 + 120 + 60 = **182 ns**.
> 
> **b)** `0,95 × 62 + 0,05 × 182` = 58,9 + 9,1 = **68 ns**.
> 
> **c)** Sin TLB: 3 accesos × 60 ns = **180 ns**. `180 / 68 ≈` **2,65 veces** más rápido con TLB.

---

## Ejercicio 6: dirección del vector de interrupción

**Fuente:** TP de interrupciones (unidad 9, pregunta 10, vectores) y ejemplo de la clase (INT 21h).

En **modo real**:
- a) ¿En qué dirección de la IDT está el vector de `INT 10h`?
- b) ¿Y el de la excepción #14 (fallo de página)?
- c) Para `INT 21h`, si en el vector hay `IP = 0100h` y `CS = 0200h`, ¿dónde está la rutina?
- d) ¿Cuántos bytes ocupa la IDT? ¿Cuál es el límite del IDTR?

> [!check]- Cómo se hace y respuesta
> **Método:** en modo real la IDT empieza en `0000:0000` y cada vector ocupa 4 bytes (2 de IP y 2 de CS). `Dirección del vector n = n × 4`.
> 
> - a) `10h × 4` = **`40h`**.
> - b) `14 × 4 = 56` = **`38h`**.
> - c) El vector de `21h` está en `21h × 4` = `84h`. La rutina: `CS × 10h + IP` = `02000h + 0100h` = **`02100h`**.
> - d) 256 vectores × 4 bytes = **1024 bytes = 400h**. El límite del IDTR es **`03FFh`** (el último byte válido).

---

## Ejercicio 7: flags después de una operación

**Fuente:** TP de la unidad 10, preguntas 5, 6, 9 y 10 (con los mismos números).

Indicá el resultado y los flags **CF, OF, ZF, SF y PF**:
- a) `ADD AX, DX` con `AX = 3330` y `DX = 4879`
- b) `SUB AX, DX` con `AX = 99FF` y `DX = 4879`
- c) `AND AX, DX` con `AX = 3330` y `DX = 4879`
- d) `ROL AX, 1` con `AX = AFE4` (indicá solo CF y OF)

> [!check]- Cómo se hace y respuesta
> **Reglas:**
> - **CF:** acarreo (suma) o préstamo (resta) con los operandos **sin signo**.
> - **OF:** resultado incorrecto **con signo** (dos positivos dan negativo, o dos negativos dan positivo; en resta, el signo del resultado no coincide con el del minuendo).
> - **ZF:** el resultado es cero.
> - **SF:** el bit más alto del resultado.
> - **PF:** la cantidad de unos del **byte bajo** del resultado es **par**.
> 
> **a)** `3330 + 4879 = 7BA9`. No se pasa de 16 bits: **CF = 0**. Dos positivos dan positivo: **OF = 0**. **ZF = 0**, **SF = 0** (7BA9 empieza con 0). Byte bajo `A9 = 1010 1001`, cuatro unos (par): **PF = 1**.
> 
> **b)** `99FF − 4879 = 5186`. El minuendo es mayor, no hay préstamo: **CF = 0**. `99FF` es negativo con signo y `4879` positivo, y el resultado es positivo: **OF = 1**. **ZF = 0**, **SF = 0**. Byte bajo `86 = 1000 0110`, tres unos (impar): **PF = 0**.
> 
> **c)** `3330 AND 4879 = 0030`. `AND` siempre deja **CF = 0** y **OF = 0**. **ZF = 0**, **SF = 0**. Byte bajo `30 = 0011 0000`, dos unos (par): **PF = 1**.
> 
> **d)** `AFE4 = 1010 1111 1110 0100`. `ROL` rota a la izquierda: el bit 15 (que vale 1) sale, entra por el bit 0 y se copia a CF. Resultado `0101 1111 1100 1001 = 5FC9`. **CF = 1**. Para una rotación de 1 bit, `OF = CF XOR bit 15 del resultado` = 1 XOR 0 = **1**. Las demás flags (ZF, SF, PF) no se modifican con `ROL`.

---

## Ejercicio 8: seguir un programa en DEBUG

**Fuente:** TP de laboratorio de DEBUG (unidad 10): prácticos 2, 3, 4, 5 y 7.

Resolvé estos cinco ítems (cada uno parte desde cero; la pila arranca con `SP = FFEE`):

**a)** Después de ejecutar todo el programa, ¿cuánto vale `AX` y qué hay en memoria?
```
mov ax,8
mov [0200],ax
mov ax,3
mov [0203],ax
mov ax,[0200]
add ax,[0203]
mov [0205],ax
```
**b)** `mov ax,FFFF` / `mov cx,FFFF` / `mul cx`. ¿Cuánto valen `DX` y `AX`? ¿Y los flags CF y OF?

**c)** `mov ax,1C` / `mov bl,FF` / `idiv bl`. ¿Cuánto valen `AL` y `AH`? ¿Qué significan en decimal?

**d)** Con `AX = D5D6` y `BX = D3D4`: `push ax` / `push bx` / `pop cx` / `pop dx`. ¿Cuánto valen `CX`, `DX` y `SP` al final?

**e)** `mov al,A0` / `rcl al,1`, con el carry en 0 al inicio. ¿Cuánto valen `AL` y `CF`?

> [!check]- Cómo se hace y respuesta
> **a)** Seguí el programa instrucción por instrucción. Recordá **little endian**: el byte bajo va en la dirección menor.
> - `mov ax,8` → `AX = 0008`
> - `mov [0200],ax` → `0200 = 08`, `0201 = 00`
> - `mov ax,3` → `AX = 0003`
> - `mov [0203],ax` → `0203 = 03`, `0204 = 00`
> - `mov ax,[0200]` → `AX = 0008`
> - `add ax,[0203]` → `AX = 0008 + 0003 =` **`000B`**
> - `mov [0205],ax` → **`0205 = 0B`**, **`0206 = 00`**
> 
> Resumen: **AX = 000B** y la memoria queda `0200 = 08`, `0201 = 00`, `0203 = 03`, `0204 = 00`, `0205 = 0B`, `0206 = 00`. La dirección `0202` no se tocó.
> 
> **b)** `MUL CX` (sin signo) deja el resultado de 32 bits en el par `DX:AX`. `FFFF × FFFF = FFFE0001h`, entonces **`DX = FFFE`** y **`AX = 0001`**. Como la parte alta (`DX`) no es cero, el resultado no entra en 16 bits: **CF = 1 y OF = 1** (en DEBUG se ve `OV` y `CY`).
> 
> **c)** `IDIV BL` es división **con signo**: `AX / BL`, cociente en `AL` y resto en `AH`.
> - `1C = +28` y `FF = −1` (complemento a 2).
> - Cociente = `28 / (−1) = −28`, que en 8 bits es `E4h` (invertir `E4` da `1B`, más 1 da `1C = 28`).
> - Resto = 0.
> 
> Resultado: **`AL = E4`** (−28) y **`AH = 00`**.
> 
> **d)** La pila crece hacia abajo. `PUSH` baja `SP` en 2 y luego escribe; `POP` lee y luego sube `SP` en 2.
> - `push ax` → `SP = FFEC`, `FFEC = D6`, `FFED = D5`
> - `push bx` → `SP = FFEA`, `FFEA = D4`, `FFEB = D3`
> - `pop cx` → saca lo último que entró: **`CX = D3D4`**, `SP = FFEC`
> - `pop dx` → **`DX = D5D6`**, `SP = FFEE`
> 
> Final: **CX = D3D4, DX = D5D6, SP = FFEE** (LIFO: salen en orden inverso).
> 
> **e)** `A0 = 1010 0000`. `RCL` rota a la izquierda **a través del carry** (9 bits: carry + AL). El bit 7 (que vale 1) sale al carry, y el carry viejo (0) entra por el bit 0. Resultado `0100 0000`: **`AL = 40`** y **`CF = 1`**.

---

## Ejercicio 9: campos de la dirección en la caché

**Fuente:** diapositivas de caché (Memoria Caché P2: MP de 4 GB, caché de 4 KB, línea de 32 B) y una variante armada por mí.

**a)** MP de **4 GB**, caché de **4 KB**, líneas de **32 bytes**. Completá los bits de **Etiqueta, Línea o Conjunto y Posición** para:
- totalmente asociativa
- asociativa de 1 vía
- asociativa de 2 vías

**b)** (Variante) MP de **1 GB**, caché de **8 KB**, líneas de **64 bytes**. Completá lo mismo para totalmente asociativa, 1 vía y **4 vías**.

> [!check]- Cómo se hace y respuesta
> **Método:**
> - Bits de dirección = `log₂(MP)`.
> - Posición = `log₂(bytes por línea)`.
> - Líneas = `tamaño de caché / tamaño de línea`; Línea = `log₂(líneas)`.
> - Conjuntos = `tamaño de caché / (vías × tamaño de línea)`; Conjunto = `log₂(conjuntos)`.
> - Etiqueta = bits de dirección − (línea o conjunto) − posición. En la totalmente asociativa no hay línea ni conjunto.
> 
> **a)** Dirección = 32 bits (4 GB = 2³²). Posición = 5 (32 = 2⁵). Líneas = 4 KB / 32 B = 128 → 7 bits. 2 vías: conjuntos = 4 KB / (2 × 32) = 64 → 6 bits.
> 
> | Organización | Etiqueta | Línea / Conjunto | Posición |
> |---|---|---|---|
> | Totalmente asociativa | **27** | – | **5** |
> | 1 vía | **20** | **7** (línea) | **5** |
> | 2 vías | **21** | **6** (conjunto) | **5** |
> 
> **b)** Dirección = 30 bits (1 GB = 2³⁰). Posición = 6 (64 = 2⁶). Líneas = 8 KB / 64 B = 128 → 7 bits. 4 vías: conjuntos = 8 KB / (4 × 64) = 32 → 5 bits.
> 
> | Organización | Etiqueta | Línea / Conjunto | Posición |
> |---|---|---|---|
> | Totalmente asociativa | **24** (30 − 6) | – | **6** |
> | 1 vía | **17** (30 − 7 − 6) | **7** (línea) | **6** |
> | 4 vías | **19** (30 − 5 − 6) | **5** (conjunto) | **6** |

---

## Ejercicio 10: armar direcciones en hexa y sacar tamaños

**Fuente:** PDF de la cátedra "Ejercicios de Caché resueltos" (adaptado: acá solo piden una parte).

Se tiene la siguiente caché. Las etiquetas son de 5 dígitos hexa y cada línea guarda 8 bytes (posiciones 7 a 0). Hay 8 líneas.

| Línea | Etiqueta | Pos 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|---|---|---|---|---|---|---|---|---|---|
| 0 | ABC01 | 00 | 01 | 11 | AB | BA | CD | DC | BA |
| 1 | DEF10 | BC | DB | CB | EA | AE | FA | 12 | 34 |
| 2 | 010AB | 56 | 78 | 9A | BC | 21 | 43 | 65 | 87 |
| 3 | AD057 | 10 | 20 | 30 | 40 | 50 | 70 | 80 | 90 |
| 4 | BCDEF | 01 | 02 | 03 | 04 | 05 | 06 | 07 | 08 |
| 5 | C50BA | 60 | 6A | 6B | 6C | 6D | 6E | 71 | 72 |
| 6 | DCBA0 | 30 | 31 | 32 | 33 | 34 | 35 | 36 | 37 |
| 7 | E078B | B0 | B1 | B3 | B5 | B7 | B9 | BB | FF |

- a) Para la organización **totalmente asociativa**, escribí la dirección física (en binario y en hexa) de los datos que corresponden a **números en ASCII** (códigos `30h` a `39h`), para las etiquetas DEF10, AD057 y DCBA0 (de esta última, solo la posición 0).
- b) Para la organización de **1 vía**, la dirección física de la etiqueta DCBA0, línea 110, posición 000.
- c) **Memoria física** que hace falta instalar en cada organización: totalmente asociativa, 1 vía y N vías (conjunto de 7 bits).
- d) **Tamaño de la caché** en N vías (cada conjunto tiene 64 B y hay 128 conjuntos).
- e) **Tamaño del bloque** que se transfiere de la memoria principal en cada actualización.

> [!check]- Cómo se hace y respuesta
> **Pasos:**
> 1. Anchos de campo: etiqueta = 5 hexa = **20 bits**; posición = 8 posiciones = **3 bits**; línea = 8 líneas = **3 bits**; conjunto = **7 bits**.
> 2. Buscar en la tabla los datos entre `30h` y `39h` (dígitos ASCII).
> 3. Concatenar los campos según la organización y **agrupar de a 4 bits desde la derecha** para pasar a hexa.
> 
> **a)** Datos que son dígitos ASCII: línea 1, posición 0 (`34`), línea 3, posición 5 (`30`) y toda la línea 6 (`30`–`37`). Totalmente asociativa: `etiqueta (20) + posición (3)` = 23 bits.
> - DEF10, posición 000: `1101 1110 1111 0001 0000 000` → **`6F7880h`**
> - AD057, posición 101: `1010 1101 0000 0101 0111 101` → **`5682BDh`**
> - DCBA0, posición 000: `1101 1100 1011 1010 0000 000` → **`6E5D00h`**
> 
> **b)** 1 vía: `etiqueta (20) + línea (3) + posición (3)` = 26 bits. `1101 1100 1011 1010 0000 110 000` → agrupando desde la derecha: **`372E830h`**.
> 
> (Ojo: el PDF resuelto de la cátedra tiene un error en esta parte: usa los bits de DEF10 en las filas de DCBA0 y da `37BC430h`. El valor correcto para DCBA0 es `372E830h`.)
> 
> **c)** Memoria instalada = `2^(bits de dirección)`.
> - Totalmente asociativa: 23 bits → **8 MB**.
> - 1 vía: 26 bits → **64 MB**.
> - N vías: `20 + 7 + 3` = 30 bits → **1 GB**.
> 
> **d)** `64 B × 128 conjuntos = 8192 B =` **8 KB**.
> 
> **e)** Se transfiere **una línea completa**: **8 bytes (64 bits)**.

---

## Ejercicio 11: tiempo medio, factor de velocidad y eficacia

**Fuente:** ppt del grupo (ejemplo de la diapositiva 7), PDF de la cátedra (punto 6) y variante armada por mí.

**a)** `t_MP = 40 ns` y `t_caché = 6 ns`. ¿Cuál es el factor de velocidad? Si el tiempo medio de acceso es **18 ns**, ¿cuál es la eficacia?

**b)** Un µP de 2,5 GHz, tiempo de acceso a la caché de 3 ns, a la MP de 30 ns, tasa de acierto de **80%**. Calculá el factor de velocidad, el tiempo medio (caché en serie), la eficacia y el rendimiento.

**c)** Con los mismos datos de b), ¿cuál es el tiempo medio si la caché está **en paralelo** y qué eficacia da?

> [!check]- Cómo se hace y respuesta
> **Fórmulas:**
> - Factor de velocidad = `t_MP / t_caché`.
> - Serie: `t medio = α · tc + (1 − α) · (tc + tp)`.
> - Paralelo: `t medio = α · tc + (1 − α) · tp`.
> - Eficacia = `tc / t medio`; Rendimiento = eficacia × 100.
> 
> **a)** Factor de velocidad = `40 / 6 =` **6,67**. Eficacia = `6 / 18 =` **0,33** (33%).
> 
> (Ojo: la diapositiva 7 de la ppt del grupo calcula `6,66 / 18 = 0,37`, que está mal: usa el factor de velocidad en vez del tiempo de la caché.)
> 
> **b)** Período = `1 / 2,5 GHz = 0,4 ns` (dato que no se usa en las cuentas).
> - Factor de velocidad = `30 / 3 =` **10**.
> - `t = 0,8 × 3 + 0,2 × (3 + 30)` = 2,4 + 6,6 = **9 ns**.
> - Eficacia = `3 / 9 =` **0,33**; rendimiento = **33%**.
> 
> **c)** `t = 0,8 × 3 + 0,2 × 30` = 2,4 + 6 = **8,4 ns**. Eficacia = `3 / 8,4 ≈` **0,357** (35,7%). Mejora porque en un fallo no se paga el tiempo de la caché.

---

## Ejercicio 12: armar memoria con módulos

**Fuente:** diapositivas de "Memoria: organización" (unidad 11/13: ejemplos de 16K×8 con 8K×8 y 16K×16 con 16K×8) y una variante armada por mí.

**a)** Se quiere una memoria de **16K × 8** con módulos de **8K × 8**, bus de 14 bits. ¿Cuántos módulos? ¿Cuántos bits direccionan dentro del módulo y cuántos seleccionan el módulo?

**b)** Una memoria de **16K × 16** con módulos de **16K × 8**. ¿Cuántos módulos y cómo se acceden?

**c)** (Variante) Una memoria de **32K × 16** con módulos de **8K × 8**, bus de 15 bits. ¿Cuántos módulos, cuántos bits seleccionan el módulo y cómo se acceden?

> [!check]- Cómo se hace y respuesta
> **Método:**
> - Nº de módulos = `(capacidad total / capacidad del módulo) × (ancho total / ancho del módulo)`.
> - Bits dentro del módulo = `log₂(posiciones del módulo)`.
> - Bits que seleccionan = bits del bus − bits dentro del módulo.
> - Si hay que ampliar **posiciones**, los módulos se **alternan** (selecciona el decodificador). Si hay que ampliar el **ancho**, los módulos se acceden **a la vez**.
> 
> **a)** Módulos = `16K / 8K = ` **2** (el ancho es el mismo). Cada módulo usa 13 bits (8K = 2¹³). Sobra `14 − 13 =` **1 bit** que selecciona: `A13 = 0` módulo 0, `A13 = 1` módulo 1.
> 
> **b)** Módulos = `(16K / 16K) × (16 / 8) = 1 × 2 =` **2**. Los dos módulos se acceden **a la vez** (comparten todas las líneas de dirección): uno guarda el byte alto y el otro el byte bajo.
> 
> **c)** Módulos = `(32K / 8K) × (16 / 8) = 4 × 2 =` **8**. Cada módulo usa 13 bits. Sobran `15 − 13 =` **2 bits** que seleccionan una de las **4 filas** (por las posiciones). En cada fila hay **2 módulos que se acceden a la vez** (por el ancho de 16 bits).

---

## Autoevaluación

Marcá cómo te fue en cada tipo. Los que no te salieron, repasalos en la Parte 3 de la guía (fórmulas) y rehacelos con otros números.

| # | Tipo | ¿Me salió solo? |
|---|---|---|
| 1 | Dirección física en modo real | ☐ |
| 2 | Decodificar un selector | ☐ |
| 3 | Dirección lineal con descriptor | ☐ |
| 4 | Traducción con paginación | ☐ |
| 5 | Tiempo medio con TLB | ☐ |
| 6 | Dirección del vector de interrupción | ☐ |
| 7 | Flags | ☐ |
| 8 | Seguir un programa en DEBUG | ☐ |
| 9 | Campos de la dirección en la caché | ☐ |
| 10 | Direcciones en hexa y tamaños | ☐ |
| 11 | Tiempos de la caché | ☐ |
| 12 | Módulos de memoria | ☐ |
