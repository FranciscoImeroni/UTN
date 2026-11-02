# Preguntas de parcial resueltas + práctica (multiple choice)

> **Parte 1:** las 15 preguntas del cuestionario que hiciste, cada una con cómo se resuelve y la respuesta correcta.
> **Parte 2:** 24 preguntas nuevas del mismo estilo para practicar, con la clave al final.
> Las que **fallaste** (6, 9, 10, 11 y 13) están marcadas con ❌ y tienen una explicación más larga sobre por qué se suele errar.
> Para las que acertaste (✅) no sé qué opción elegiste, solo que estaba bien. Las respuestas de las preguntas 12, 13 y 14 las tomé de la corrección que me mandaste; las demás las resolví yo.

## Resultado del cuestionario

| # | Tema | Resultado | Respuesta correcta |
|---|---|---|---|
| 1 | Modo de direccionamiento de `MOV AX,[0200]` | ✅ | Directo, sí accede a memoria |
| 2 | Seguir un programa con bucle (AX y BX) | ✅ | `AX = 6h` y `BX = 3h` |
| 3 | Qué pasa si `mov bx,7` pasa a `mov bx,9` | ✅ | Cambia la cantidad de veces que imprime |
| 4 | Valor de `AH` tras `8 + 3 − 0Ch` | ✅ | `AH = FF` |
| 5 | Qué es un bit | ✅ | La unidad mínima para medir la información |
| 6 | Tipo de excepción "se detecta y corrige antes" | ❌ | **Falta o Error** |
| 7 | Tamaño de 1500 caracteres en KB | ✅ | `1,46 KB` |
| 8 | Método de escritura "antes de leer, se escribe el dato modificado" | ✅ | **Escritura obligada** |
| 9 | Caché totalmente asociativa (tabla) | ❌ | **b** (`6E5D03` accede al dato `34`) |
| 10 | Cantidad de grupos en 1 vía (4 GB y 4 KB) | ❌ | **1048576** |
| 11 | "La paginación es un procedimiento de gestión de la memoria" | ❌ | ⚠️ **A confirmar** (ver más abajo) |
| 12 | Fallo de página y excepción | ✅ | Verdadero |
| 13 | "La paginación divide en porciones de tamaño variable" | ❌ | **Falso** |
| 14 | "Segmento presente → P = 0" | ✅ | Falso |
| 15 | Segmentación activa y paginación apagada | ✅ | Verdadero |

---

# Parte 1: las preguntas del cuestionario

## Pregunta 1 ✅ Modo de direccionamiento de `Mov AX, [0200]`

**Enunciado:** indicar el modo de direccionamiento de `Mov AX, [0200]` y si se accede a la memoria.

**Cómo se hace:**
- Los **corchetes** `[ ]` significan "el contenido de la dirección que está dentro". Sin corchetes (`MOV AX, 0200`) el 0200 sería el dato mismo (inmediato).
- Acá la instrucción trae **la dirección del operando** (`0200`) escrita directamente. Eso es **direccionamiento directo**: `EA = A`.
- Para ejecutarla, la CPU va a leer el valor guardado en `DS:0200`, así que **sí hay acceso a memoria** (uno solo).

**Respuesta:** **Directo**, y **sí se accede a la memoria**.

> Para no confundirse, la clave son los corchetes: sin corchetes es **inmediato** (no accede a memoria), con una dirección fija entre corchetes es **directo** (un acceso), con un registro entre corchetes (`[BX]`) es **indirecto con registro** (un acceso), y solo con registros (`MOV AX,BX`) es **por registro** (sin acceso a memoria).

---

## Pregunta 2 ✅ Seguir un programa con bucle

**Enunciado:**
```
0800:0100 MOV BX,6h
0800:0103 MOV AX,0h
0800:0106 INC AX
0800:0107 DEC BX
0800:0108 CMP AX,BX
0800:010A JNZ 0106h
0800:010C ADD AX,BX
0800:010E RET
```
Indicar el valor de AX y BX al finalizar.

**Cómo se hace:** ejecutá el programa a mano, anotando los valores en cada vuelta. La clave es entender el salto: `JNZ 0106h` vuelve a la instrucción `INC AX` **mientras `AX ≠ BX`**, porque `CMP AX,BX` hace `AX − BX` y deja `ZF = 1` solo si son iguales.

| Vuelta | `INC AX` | `DEC BX` | `CMP AX,BX` | ¿Salta? |
|---|---|---|---|---|
| inicio | AX = 0 | BX = 6 | – | – |
| 1 | AX = 1 | BX = 5 | 1 ≠ 5 → ZF = 0 | **sí** (a `0106`) |
| 2 | AX = 2 | BX = 4 | 2 ≠ 4 → ZF = 0 | **sí** |
| 3 | AX = 3 | BX = 3 | 3 = 3 → ZF = 1 | **no** (sigue) |

Cuando no salta, se ejecuta `ADD AX,BX`: `AX = 3 + 3 = 6`. `BX` queda en 3. Después `RET` termina.

**Respuesta:** **`AX = 6h` y `BX = 3h`** (opción c).

> Ojo: `AX` sube y `BX` baja hasta encontrarse en la mitad (6 / 2 = 3). La opción `AX = 6, BX = 6` sería el resultado si no se ejecutara el `DEC BX`.

---

## Pregunta 3 ✅ Qué pasa si se cambia `mov bx,7` por `mov bx,9`

**Enunciado:** el programa que imprime "que tengas un feliz dia!" con `mov bx,7`, `int 21` (función 9), `dec bx` y `jnz 138`. ¿Cómo afecta cambiar `mov bx,7` por `mov bx,9`?

**Cómo se hace:**
- `BX` es el **contador de repeticiones**. Se decrementa en cada vuelta (`dec bx`) y `jnz 138` vuelve a imprimir mientras `BX ≠ 0`.
- Con `BX = 7` el programa imprime **7 veces**. Con `BX = 9`, imprime **9 veces**.
- No se toca la función de `INT 21` (sigue siendo `AH = 9`), ni se pierde la condición de salida (BX igual llega a 0), por lo que no hay loop infinito.

**Respuesta:** **afecta la cantidad de veces que imprime** el mensaje (opción b).

---

## Pregunta 4 ✅ Valor de `AH`

**Enunciado:**
```
mov ah,8
add ah,3
sub ah,c
```
Indicar el valor de `AH` luego de la ejecución.

**Cómo se hace:** en DEBUG los números son **hexadecimales**.
- `mov ah,8` → `AH = 08`.
- `add ah,3` → `08 + 03 = 0B`.
- `sub ah,c` → `0B − 0C`. Como `0C` es mayor, el resultado es negativo: **−1**, que en 8 bits (complemento a 2) es **`FF`**.

Comprobación: `FF` → invertir `00` → sumar 1 `01` → con signo es −1. En decimal `8 + 3 − 12 = −1`.

**Respuesta:** **`AH = FF`** (opción c).

---

## Pregunta 5 ✅ Un bit es...

**Cómo se hace:** pregunta de definición.
- **Bit:** la unidad **mínima** de información (un 0 o un 1).
- La opción b (velocidad de transmisión) describe los **bits por segundo** (bps), no el bit en sí.
- La opción c (bytes necesarios para almacenar un carácter) es un número de bytes, no un bit.

**Respuesta:** **la unidad mínima utilizada para medir la información** (opción a).

---

## Pregunta 6 ❌ Tipo de excepción

**Enunciado:** "Se pueden detectar y corregir antes de que se produzca la ejecución de una instrucción determinada." Opciones: Trampa, Falta o Error, Aborto.

**Cómo se hace:** las excepciones se dividen en 3 tipos, que se distinguen por **cuándo se detectan**:

| Tipo | Cuándo se detecta | Ejemplo |
|---|---|---|
| **Falta o error** | **Antes** de ejecutar la instrucción, y se puede corregir y reintentar | Fallo de página: el SO carga la página y se repite la instrucción |
| **Trampa** | **Después** de ejecutar la instrucción que la provoca | Overflow |
| **Aborto** | No se puede ubicar la instrucción; se aborta el programa | Valor inválido en un registro de sistema |

La frase del enunciado ("detectar y corregir **antes** de ejecutar") es literalmente la definición de **falta**. Es la misma que figura en el resumen de la unidad 9.

**Respuesta:** **Falta o Error** (opción b).

> **Por qué se falla:** la palabra "corregir" confunde con "trampa", pero el dato clave es **antes**. Regla para recordar: **Falta = antes (se puede reintentar), Trampa = después, Aborto = no se sabe dónde**.

---

## Pregunta 7 ✅ Tamaño de 1500 caracteres

**Enunciado:** un archivo `.txt` con un texto de 1500 caracteres. ¿Cuál es su tamaño en KB?

**Cómo se hace:**
- En un `.txt` cada carácter ocupa **1 byte** (ASCII). Entonces el archivo pesa **1500 bytes**.
- `1 KB = 1024 bytes` (potencia de 2), no 1000.
- `1500 / 1024 = 1,4648...` → **1,46 KB**.

**Respuesta:** **1,46 KB** (opción b). La opción a (1,50 KB) usa 1000 en vez de 1024.

---

## Pregunta 8 ✅ Método de escritura en memoria principal

**Enunciado:** "Antes de leer en la memoria principal, se escribe en ella el dato modificado en la memoria caché."

**Cómo se hace:** hay tres métodos y cada uno se reconoce por **cuándo** actualiza la memoria principal:

| Método | Cuándo actualiza |
|---|---|
| **Inmediata** | En cada escritura de la CPU, siempre |
| **Diferida** | Cuando el bus del sistema queda libre |
| **Obligada** | **Solo cuando no queda otra**: antes de leer una posición modificada, o antes de desalojar una línea modificada |

La frase "antes de leer... se escribe el dato modificado" es el primero de los dos casos de la escritura obligada.

**Respuesta:** **método de escritura obligada** (opción b).

---

## Pregunta 9 ❌ Caché totalmente asociativa (tabla)

**Enunciado:** dada la tabla de una caché totalmente asociativa (etiquetas `ABC01`, `DEF10`, `010AB`, `AD057`, `BCDEF`, `C50BA`, `DCBA0`, `E078B` y 8 posiciones por línea), indicar la afirmación correcta.

**Cómo se hace:** primero sacá los datos básicos de la tabla:
- Cada etiqueta tiene 5 dígitos hexa = **20 bits**.
- Cada línea tiene 8 posiciones (7 a 0) = **3 bits** de posición.
- Cada celda tiene **2 dígitos hexa = 1 byte**.
- Dirección física = etiqueta (20) + posición (3) = **23 bits**.

Ahora cada opción:

| Opción | ¿Es verdadera? | Por qué |
|---|---|---|
| a. El dato ocupa 2 bytes | ❌ | Cada celda tiene 2 dígitos hexa = **1 byte** |
| **b. `6E5D03` accede al dato `34`** | ✅ | Ver cuenta abajo |
| c. Se usan 16 bits para la etiqueta | ❌ | 5 dígitos hexa = **20 bits** |
| d. La posición máxima es la `110` | ❌ | Con 3 bits el máximo es `111` (posición 7) |
| e. Se requieren 4 bits para identificar el dato | ❌ | Se necesitan **3 bits** (8 posiciones) |

**Cuenta de la opción b:** pasá `6E5D03` a binario y cortá en 20 + 3 bits.
```
6E5D03 = 110 1110 0101 1101 0000 0011        (23 bits)
etiqueta (20 bits) = 1101 1100 1011 1010 0000 = DCBA0
posición (3 bits)  = 011 = 3
```
La etiqueta `DCBA0` es la **línea 6**. En esa línea la posición 3 vale `34` (las posiciones van `7 6 5 4 3 2 1 0` con los valores `30 31 32 33 34 35 36 37`).

**Respuesta:** **b**.

> **Por qué se falla:** hay que **hacer la cuenta de pasar la dirección a binario**. Las otras opciones se descartan solo con los anchos de campo (20, 3 y 23 bits), y la b exige cortar la dirección. Si cortás mal (por ejemplo, tomando 4 bits de posición) buscás en otra posición y no te da `34`.

---

## Pregunta 10 ❌ Cantidad de grupos en 1 vía

**Enunciado:** memoria principal de 4 GB y caché de 4 KB. Indicar la cantidad de grupos en los que se divide la memoria principal, con organización asociativa de 1 vía.

**Cómo se hace:**
- En 1 vía, la memoria principal se divide en **grupos del tamaño de la caché**.
- `Cantidad de grupos = tamaño de MP / tamaño de la caché`.
- `4 GB / 4 KB = 2³² / 2¹² = 2²⁰ = 1 048 576` (aproximadamente 1 M de grupos).

**Respuesta:** **1048576**.

> **Por qué se falla:** se confunde con "cantidad de bloques" (que se calcula dividiendo entre el tamaño de la **línea**, no de la caché). Acá no te dan la línea, y la división correcta es entre el tamaño de la caché. Si te piden "bloques", la cuenta es `MP / tamaño de línea` y da otro número. El enunciado pide **grupos**.
> Atajo con potencias: `2³² / 2¹² = 2²⁰`. Y `2²⁰ = 1 048 576`.

---

## Pregunta 11 ❌ "La paginación es un procedimiento de gestión de la memoria"

**Enunciado:** Verdadero o Falso.

**Qué sé y qué no sé:**
- En la captura del cuestionario aparecía marcado **Verdadero**, y en la navegación esta pregunta figura como **incorrecta**.
- Por contenido (unidad 12), la paginación es una **técnica de manejo de memoria**, así que la afirmación parece verdadera.
- Esas dos cosas no coinciden, y **no puedo confirmar cuál fue la respuesta que marcaste al final**, ni qué respuesta da por correcta la cátedra.

**Qué hacer:** abrí la revisión de esa pregunta en el cuestionario: debajo del enunciado dice "La respuesta correcta es...". Mandame una captura y completo esta explicación. Hasta entonces, no la des por sabida.

---

## Pregunta 12 ✅ Fallo de página

**Enunciado:** "Si la página no reside en memoria principal se genera un fallo de página, que origina una excepción." Verdadero o Falso.

**Cómo se hace:**
- Cada entrada de la tabla de páginas tiene el bit **P** (presencia). Si **P = 0**, la página no está en memoria principal.
- La unidad de paginación guarda la dirección lineal en **CR2** y genera la **excepción 14** (fallo de página).
- Una rutina del sistema operativo trae la página desde el disco, pone P = 1 y reinicia la instrucción.

**Respuesta:** **Verdadero**.

---

## Pregunta 13 ❌ La paginación y el tamaño de las porciones

**Enunciado:** "La paginación divide y manipula los programas y datos en porciones de tamaño variable." Verdadero o Falso.

**Cómo se hace:** hay que distinguir segmentación y paginación por el **tamaño de los bloques**:

| | Tamaño de los bloques |
|---|---|
| **Segmentación** | **Variable** (cada segmento mide lo que necesite el programa) |
| **Paginación** | **Fijo** (páginas de 4 KB, o de 4 MB con PSE) |

La afirmación le atribuye a la paginación una característica de la segmentación.

**Respuesta:** **Falso**.

> **Por qué se falla:** se mezclan los dos conceptos. Regla: **Segmentación = variable (visible para el programador), Paginación = fijo (la maneja el SO)**.

---

## Pregunta 14 ✅ Segmento presente

**Enunciado:** "Cuando un segmento está presente en la memoria el bit de presencia es igual a 0, es decir, P = 0." Verdadero o Falso.

**Cómo se hace:** el bit **P** (presencia) del descriptor indica si el segmento está cargado en memoria.
- **P = 1:** el segmento **está presente** en memoria.
- **P = 0:** el segmento **está ausente** y se produce una excepción (segmento no presente).

La afirmación invierte los valores.

**Respuesta:** **Falso**.

---

## Pregunta 15 ✅ Segmentación activa y paginación apagada

**Enunciado:** "Si la unidad de segmentación está encendida y la unidad de paginación no está encendida, la dirección lineal coincide con la física." Verdadero o Falso.

**Cómo se hace:** recordá el recorrido de las direcciones:
```
Dirección virtual → [Unidad de segmentación] → Dirección lineal → [Unidad de paginación] → Dirección física
```
- Si la **paginación está apagada** (PG = 0 en CR0), no hay un paso que transforme la dirección lineal.
- Entonces la dirección lineal **es** la dirección física.

**Respuesta:** **Verdadero**.

---

# Parte 2: práctica (multiple choice)

> Mismo estilo que el cuestionario. Resolvé sin mirar la clave del final.

**1.** Indique el modo de direccionamiento de `MOV BX, 0200` y si se accede a la memoria.
- a. Directo, sí se accede a memoria
- b. Inmediato, no se accede a memoria
- c. Por registro, no se accede a memoria
- d. Indirecto con registro, sí se accede a memoria

**2.** Indique el modo de direccionamiento de `MOV AX, [BX]` y si se accede a la memoria.
- a. Inmediato, no se accede a memoria
- b. Directo, sí se accede a memoria
- c. Indirecto con registro, sí se accede a memoria
- d. Por registro, no se accede a memoria

**3.** Dado el siguiente código ejecutado en modo real:
```
0800:0100 MOV CX,4h
0800:0103 MOV AX,0h
0800:0106 ADD AX,3h
0800:0109 DEC CX
0800:010A JNZ 0106h
0800:010C RET
```
Indique el valor de AX y CX al finalizar.
- a. AX = 9h y CX = 1h
- b. AX = 0Fh y CX = 0h
- c. AX = 0Ch y CX = 0h
- d. AX = 0Ch y CX = 4h

**4.** Con el programa que imprime una cadena con `mov bx,7`, `int 21` (función 9), `dec bx` y `jnz 138`, ¿cómo afecta reemplazar `jnz 138` por `jmp 138`?
- a. Imprime la cadena una sola vez
- b. Imprime la cadena 7 veces igual que antes
- c. No imprime nada
- d. Entra en un loop infinito

**5.** Dado el siguiente programa:
```
mov ah,5
add ah,9
sub ah,10h
```
Indique el valor de AH luego de la ejecución.
- a. AH = 0E
- b. AH = FE
- c. AH = 02
- d. AH = FF

**6.** Un archivo `.txt` contiene 3000 caracteres. ¿Cuál es su tamaño en KB?
- a. 3,00 KB
- b. 2,93 KB
- c. Ninguna de las respuestas es correcta
- d. 29,30 KB

**7.** Indique el tipo de excepción al que hace referencia el enunciado: "Se informa después de ejecutarse la instrucción que la provocó, y permite continuar con la siguiente."
- a. Falta o Error
- b. Aborto
- c. Trampa

**8.** Indique el tipo de excepción: "No siempre se puede identificar la instrucción que la causó, y generalmente es un error grave que cancela el programa."
- a. Trampa
- b. Aborto
- c. Falta o Error

**9.** Indique a qué método de escritura en memoria principal se refiere: "Los cambios se acumulan en registros intermedios y se pasan a la memoria principal cuando el bus del sistema está libre."
- a. Método de escritura obligada
- b. Método de escritura inmediata
- c. Método de escritura diferida

**10.** Indique a qué método de escritura se refiere: "Cada vez que la CPU modifica la caché, el cambio también se escribe en la memoria principal."
- a. Método de escritura inmediata
- b. Método de escritura diferida
- c. Método de escritura obligada

**11.** Dada la siguiente información de una caché con organización totalmente asociativa (etiquetas de 4 dígitos hexa, 4 posiciones por línea):

| Etiqueta | Línea | Pos 3 | Pos 2 | Pos 1 | Pos 0 |
|---|---|---|---|---|---|
| 0F3C | 0 | 11 | 22 | 33 | 44 |
| A1B2 | 1 | 5A | 6B | 7C | 8D |
| 7D00 | 2 | 01 | 02 | 03 | 04 |
| 3E4F | 3 | AA | BB | CC | DD |

Indique la afirmación correcta:
- a. La dirección física tiene 20 bits
- b. Si la dirección física en hexadecimal es 286CA se puede acceder al dato 6B
- c. Se necesitan 3 bits para identificar la posición
- d. La posición máxima a la cual se puede acceder es la 100
- e. Cada dato ocupa 2 bytes

**12.** Si se sabe que una memoria principal tiene 2 GB y la caché ocupa 8 KB, indique la cantidad de grupos en los que se divide la memoria principal considerando una organización de caché asociativa de 1 vía. (Solo indique la cantidad, sin puntos.)

**13.** Una memoria principal de 4 GB se divide en bloques de 64 bytes. ¿Cuántos bloques tiene? (Solo indique la cantidad, sin puntos.)

**14.** (V/F) Si la unidad de paginación está encendida, la dirección lineal coincide con la física.
- a. Verdadero
- b. Falso

**15.** (V/F) Cuando un segmento no está presente en la memoria, el bit de presencia P es igual a 0.
- a. Verdadero
- b. Falso

**16.** (V/F) El registro CR3 contiene la dirección base del directorio de páginas.
- a. Verdadero
- b. Falso

**17.** Indique cómo se interpreta el selector `0013h` (índice, tabla y nivel de privilegio solicitado).
- a. Índice 2, TI = 0 (GDT), RPL = 3
- b. Índice 19, TI = 0 (GDT), RPL = 0
- c. Índice 2, TI = 1 (LDT), RPL = 3
- d. Índice 1, TI = 0 (GDT), RPL = 3

**18.** Con páginas de 4 KB, la dirección lineal `00800123h` se separa en:
- a. Directorio 0, Tabla 2, Desplazamiento 123h
- b. Directorio 2, Tabla 0, Desplazamiento 123h
- c. Directorio 2, Tabla 1, Desplazamiento 123h
- d. Directorio 1, Tabla 0, Desplazamiento 123h

**19.** En modo real, ¿en qué dirección de la tabla de vectores de interrupción está el vector de `INT 0Dh`?
- a. 0Dh
- b. 1Ah
- c. 34h
- d. 68h

**20.** Se ejecuta `ADD AL,01` con `AL = FF`. ¿Cómo quedan AL y las banderas ZF y CF?
- a. AL = 00, ZF = 0, CF = 1
- b. AL = 01, ZF = 0, CF = 0
- c. AL = FF, ZF = 0, CF = 0
- d. AL = 00, ZF = 1, CF = 1

**21.** ¿Cuál de las organizaciones de caché **no** necesita un algoritmo de reemplazo?
- a. Asociativa de N vías
- b. Totalmente asociativa
- c. Asociativa de 1 vía

**22.** Con una caché en **serie**, `tc = 4 ns`, `tp = 40 ns` y una tasa de acierto del 90%, ¿cuál es el tiempo medio de acceso?
- a. 4,0 ns
- b. 7,6 ns
- c. 8,0 ns
- d. 40 ns

**23.** Se necesita una memoria de **8K × 16** y se dispone de módulos de **4K × 8**. ¿Cuántos módulos hacen falta?
- a. 2
- b. 4
- c. 8
- d. 16

**24.** ¿En qué modalidad de entrada/salida el controlador transfiere los datos directamente entre el periférico y la memoria, sin pasar por el procesador?
- a. Transferencia controlada por programa
- b. Transferencia por interrupción
- c. Transferencia con acceso directo a memoria (DMA)

---

## Clave de respuestas de la práctica

> [!check]- Clave y explicación (abrir al terminar)
> 1. **b.** Sin corchetes, el `0200` es el dato mismo (inmediato) y no se accede a memoria.
> 2. **c.** Hay un registro entre corchetes: indirecto con registro. La dirección está en `BX` y se lee esa posición de memoria.
> 3. **c.** Se suma 3 a `AX` cuatro veces: `4 × 3 = 12 = 0Ch`. `CX` baja de 4 a 0 y ahí `JNZ` deja de saltar.
> 4. **d.** `JMP` salta siempre, así que nunca se llega a `INT 20` y el programa no termina.
> 5. **b.** `05 + 09 = 0E`. Después `0E − 10h` es negativo: `FEh` (−2 en complemento a 2).
> 6. **b.** Son 3000 bytes. `3000 / 1024 = 2,93 KB`.
> 7. **c.** Trampa: se detecta después de ejecutar la instrucción.
> 8. **b.** Aborto: no se puede ubicar la instrucción y se cancela el programa.
> 9. **c.** Escritura diferida: acumula los cambios y los pasa cuando el bus está libre.
> 10. **a.** Escritura inmediata: cada escritura va también a la memoria principal.
> 11. **b.** Etiqueta de 4 dígitos hexa = 16 bits, 4 posiciones = 2 bits, dirección = 18 bits (la opción a es falsa). `286CA` en binario (18 bits) es `1010 0001 1011 0010 10`: etiqueta `A1B2` (línea 1) y posición `10` = 2 → `6B`. La posición máxima es `11` (no `100`), y cada dato es 1 byte.
> 12. **262144.** `2 GB / 8 KB = 2³¹ / 2¹³ = 2¹⁸ = 262 144` grupos.
> 13. **67108864.** `4 GB / 64 B = 2³² / 2⁶ = 2²⁶ = 67 108 864` bloques (64 M).
> 14. **b (Falso).** Es al revés: si la paginación está **apagada**, la lineal coincide con la física.
> 15. **a (Verdadero).** Es la versión correcta de la pregunta 14 del cuestionario: segmento **no** presente → **P = 0** (segmento presente → P = 1).
> 16. **a (Verdadero).** CR3 apunta al directorio de páginas.
> 17. **a.** `0013h = 0000 0000 0001 0011`: índice = `00010` = 2, TI = bit 2 = 0, RPL = `11` = 3.
> 18. **b.** `Directorio = lineal >> 22 = 2`, `Tabla = (lineal >> 12) AND 3FFh = 800h AND 3FFh = 0`, desplazamiento = `123h`.
> 19. **c.** `0Dh × 4 = 13 × 4 = 52 = 34h`.
> 20. **d.** `FF + 01 = 100`: el resultado de 8 bits es `00` (ZF = 1) y hay acarreo (CF = 1).
> 21. **c.** En 1 vía cada bloque tiene una sola línea posible, así que no hay nada que elegir.
> 22. **c.** Serie: `0,9 × 4 + 0,1 × (4 + 40) = 3,6 + 4,4 = 8,0 ns`. (El 7,6 ns sería el caso paralelo.)
> 23. **b.** `(8K / 4K) × (16 / 8) = 2 × 2 = 4` módulos.
> 24. **c.** El DMA transfiere sin pasar por la CPU (la CPU solo programa la transferencia y recibe una interrupción al final).
