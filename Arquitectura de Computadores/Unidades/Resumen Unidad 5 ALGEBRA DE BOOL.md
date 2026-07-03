# Algebra de bool

### [[Assets/Algebra_Boole_Unidad_V-PrimeraParte.pdf|Diapositivas Primera Parte]] · [[Assets/Algebra_Boole_Unidad_V-SegundaParte.pdf|Diapositivas Segunda Parte]]

### [[Assets/Capitulo5-PatriciaQuiroga.pdf|Capítulo 5 - Álgebra de Boole]]

## Introducción

- En esta unidad se analizan los principios básicos de la **lógica digital** aplicados al diseño de una computadora digital.
- En **1854** (el libro de la cátedra dice 1884, pero la publicación original de Boole es de 1854) **George Boole** publicó su trabajo sobre un Álgebra para representar la Lógica: buscaba capturar la "matemática del pensamiento" con declaraciones del tipo "la puerta está abierta" / "la puerta no está abierta".
- El Álgebra de Boole, en la forma que se usa hoy para diseñar circuitos, fue desarrollada por **Claude Shannon** (1916-2001), ingeniero eléctrico y matemático, considerado el fundador de la teoría de la información. Demostró que el Álgebra booleana se podía aplicar al análisis y la síntesis de la conmutación de circuitos digitales.
- Los circuitos de las computadoras digitales se **diseñan y analizan** con el Álgebra de Boole:
  - **Análisis:** forma concisa de describir el funcionamiento de un circuito digital.
  - **Diseño:** dada una función deseada, se aplica el álgebra para obtener una función simplificada equivalente.

### Álgebra proposicional (antecedente, Cap. 5)

El Álgebra proposicional estudia la relación entre **proposiciones** (enunciados de los que tiene sentido decir si son verdaderos o falsos), vaciándolas de contenido semántico para evitar conclusiones absurdas (por ejemplo: "los gatos son animales", "los perros son animales" no implica que "los gatos son perros"). Sus operadores lógicos básicos son la **conjunción (Λ)**, la **disyunción inclusiva (V)** y la **disyunción exclusiva**, y el operador **negación (~)** aplicado a una sola proposición.

| p | q | p∧q | p∨q | p⊻q |
|---|---|---|---|---|
| F | F | F | F | F |
| F | V | F | V | V |
| V | F | F | V | V |
| V | V | V | V | F |

El Álgebra de Boole reemplaza las proposiciones (V/F) por **variables booleanas (1/0)**: el 0 se asocia al falso y el 1 al verdadero. Es el fundamento teórico del diseño de circuitos digitales.

## Operadores fundamentales

- El Álgebra de Boole utiliza **variables** (que toman el valor 1 = Verdadero, o 0 = Falso) y **operaciones**.
- Reconoce dos operadores fundamentales (algunos autores incluyen un tercero):
  - **Suma lógica** (`+`, OR) ↔ disyunción.
  - **Producto lógico** (`·`, AND) ↔ conjunción.
  - **Complemento o NOT** (`-`) ↔ negación.
- Estos operadores y cualquier función booleana quedan definidos por sus **tablas de verdad**.

## Propiedades del Álgebra de Boole

| Propiedad | Enunciado |
|---|---|
| **Conmutativa** | `a + b = b + a` · `a · b = b · a` |
| **Distributiva** | `a · (b+c) = (a·b)+(a·c)` · `a + (b·c) = (a+b)·(a+c)` |
| **Identidad** | `a + 0 = a` · `a · 1 = a` (0 y 1 son los elementos neutros) |
| **Complementación / inverso** | `a + ā = 1` (a y ā no pueden ser 0 a la vez) · `a · ā = 0` (a y ā no pueden ser 1 a la vez) |
| **Anulación de la variable** | `a + 1 = 1` · `a · 0 = 0` |
| **Idempotencia** | `a + a = a` · `a · a = a` |
| **Absorción** | `a + (a·b) = a` · `a · (a+b) = a` |
| **Asociativa** | `a+b+c = (a+b)+c = a+(b+c)` · `a·b·c = (a·b)·c = a·(b·c)` |
| **Involución (doble negación)** | `ā̄ = a` |

De las leyes de anulación y de identidad se deduce: `0+0=0`, `0+1=1`, `1+1=1`, `0·0=0`, `0·1=0`, `1·1=1`.

> **Principio de dualidad:** cualquier expresión válida del Álgebra de Boole sigue siendo válida si se intercambian `+` por `·` y `0` por `1`.

### Teorema de De Morgan

Permite transformar sumas en productos y productos en sumas:

```
1) a + b = ā · b̄
2) a · b = ā + b̄
```

Consecuencia (aplicando involución):
```
1) a + b = ā · b̄ = a + b     (doble negación)
2) a · b = ā + b̄ = a · b
```

### Ejercicio de simplificación resuelto (diapositivas)

```
Z = A.B.C + A.B.C̄ + A.B̄.C + Ā.B.C
```

1. `Z = A.B.C + A.(B.C̄ + B̄.C + B.C)` → distributiva, agrupando lo que multiplica a A
2. `Z = A.B.C + A.(B.(C̄+C) + B̄.C)` → distributiva
3. `Z = A.B.C + A.(B.1 + B̄.C)` → complemento (`C̄+C=1`)
4. `Z = A.B.C + A.B + A.B̄.C` → identidad y distributiva
5. `Z = (B.C).(A+Ā) + A.B` → agrupando y sacando factor común
6. `Z = B.C + A.B` → complemento (`A+Ā=1`) — **resultado simplificado**

### Más ejercicios (diapositivas, con solución)

1. `(A.B).(A+B) + A.B.C + A.B` → Solución: `B`
2. `A.B.C.D + A.B.C̄.D + A.B̄.C.D̄ + A.B̄.C̄.D̄` → Solución: `A.(B.C + C̄.D̄)`
3. `A.B.C.D + A.B.C.D̄ + A.B.C̄.D + A.B.C̄.D̄` → Solución: `A.B`
4. `(A + B) . (C + A)` → Solución: `(A.B) + (C.A)`

## Compuertas Logicas

Los dispositivos físicos que implementan una función booleana simple (un operador booleano) se denominan **compuertas lógicas** o, en inglés, *gates*.

Proposiciones Conjuntos Boole

### Logica positiva
Las variables son binarias y solo pueden toamr dos valores

Estos valores se definen como:

- 1 : SI/ALTO/VERDADER/ON
- 0 : NO/BAJO/FALSO/OFF

#### Lógica positiva vs. lógica negativa (voltajes, Cap. 5)

Los componentes electrónicos binarios operan con dos niveles de tensión. En **lógica positiva** la tensión lógica "1" es más positiva que la del "0"; en **lógica negativa**, al revés.

| Valor lógico | Lógica positiva | Valor lógico | Lógica negativa |
|---|---|---|---|
| 1 | High +5V | 0 | High −0,3V |
| 0 | Low +0,2V | 1 | Low −4V |

Entre el rango de "1 lógico" y "0 lógico" hay una **zona de transición** que no se toma en cuenta como valor lógico válido (por ejemplo: +5V a +2V = 1 lógico; +0,8V a 0V = 0 lógico; la franja intermedia es transición).

### OR
Suma logica

En la compuerta OR la salida toma el valor de 1 si al menos una entrada es 1. Hace la operación de suma booleana. Solo es **falsa** cuando todas las entradas están en 0.

| A | B | F=A+B |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

![[Pasted image 20260504212106.png]]

### AND
Producto logico

En la compuerta AND la salida toma el valor de 1 únicamente si ambas entradas son 1. Hace la operación de multiplicación booleana. Solo es **verdadera** cuando todas las entradas están en 1.

| A | B | F=A·B |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

![[Pasted image 20260504212051.png]]
### NOT
Complementacion

La compuerta NOT solamente tiene una entrada y la salida es el valor opuesto a la entrada. Hace la operación de negación lógica.

| A | F=Ā |
|---|---|
| 0 | 1 |
| 1 | 0 |

![[Pasted image 20260504212119.png]]

### XOR
La compuerta XOR toma el valor de 1 si sus entradas son diferentes entre sí. Esta compuerta también se suele llamar OR exclusiva. Es verdadera solo cuando es **impar** la cantidad de entradas en 1.

| A | B | F=A⊕B |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

![[Pasted image 20260504212018.png]]


### NOR
Es verdadero cuando todas las variables de entrada son 0

| A | B | F=A+B (negado) |
|---|---|---|
| 0 | 0 | 1 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 0 |

![[Pasted image 20260504212201.png]]

### NAND
En la compuerta NAND la salida toma el valor de 1 si al menos una entrada es 0.

| A | B | F=A·B (negado) |
|---|---|---|
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

![[Pasted image 20260504212308.png]]

### XNOR
La compuerta XNOR toma el valor de 1 si sus entradas son iguales entre sí.

| A | B | F=A⊙B |
|---|---|---|
| 0 | 0 | 1 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

![[Pasted image 20260504212348.png]]

### Compuerta YES (separador)

Un triángulo solo (sin círculo de negación) representa un circuito **separador**: la salida repite el valor de la entrada (`F=A`). No implementa ninguna función lógica nueva; se usa para **amplificar la señal**, ya que la corriente de salida puede ser mucho mayor que la de entrada. Esto permite excitar más compuertas de las que la señal de entrada podría alimentar por sí sola — se lo relaciona con el concepto de **abanico de salida** (cantidad de dispositivos que un terminal de salida puede abastecer).

### Compuerta triestado (buffer triestado)

Además de los niveles lógicos 0 y 1, existe un tercer estado de salida: la **alta impedancia**, en el que la salida queda "desconectada" o flotante (análogo a desenchufar un dispositivo). Se controla con una señal adicional de **habilitación** (`d`, desconectado): con `d=1` se activa el estado de alta impedancia. Es muy útil para que varios registros compartan un mismo **bus**.

### NAND y NOR como compuertas universales

Aplicando **De Morgan**, las tres funciones elementales (suma, producto e inversión) pueden construirse solo con compuertas NAND o solo con NOR:

- **OR con NAND:** `a + b = ¬(¬a · ¬b)` → se niega dos veces (involución), y se aplica De Morgan a una de las negaciones: la compuerta OR se reemplaza por compuertas **NOT y NAND**.
- **AND con NOR:** `a · b = ¬(¬a + ¬b)` → de forma análoga, la compuerta AND se reemplaza por compuertas **NOT y NOR**.
- **NOT con NAND/NOR:** se logra con una compuerta NAND o NOR con **ambas entradas iguales** (unidas entre sí).

Por esta razón, **NAND y NOR se consideran las "compuertas básicas"** para el diseño de circuitos: su implementación con transistores es más simple que la de AND/OR (que requieren un inversor adicional a la salida).

### Resumen de compuertas (diapositivas)

| A | B | AND (A·B) | OR (A+B) | XOR (A⊕B) | NAND | NOR | XNOR |
|---|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 | 1 | 1 | 1 |
| 0 | 1 | 0 | 1 | 1 | 1 | 0 | 0 |
| 1 | 0 | 0 | 1 | 1 | 1 | 0 | 0 |
| 1 | 1 | 1 | 1 | 0 | 0 | 0 | 1 |

## Funciones booleanas

Un conjunto de variables booleanas vinculadas mediante suma lógica, producto lógico y complementación constituye una **función booleana**. Se puede expresar como tabla de verdad (representación estándar), expresión literal (polinómica o factorial) o expresión simbólica. La **forma canónica** de una función es aquella cuyos términos contienen la totalidad de las variables del problema.

### Tablas de verdad

Una lista ordenada de la 2^n combinacoines distintas de 0 y 1 que se pueda obtener

### Las 16 funciones posibles de 2 variables (Cap. 5)

Para `n` variables se pueden definir `2^(2ⁿ)` funciones distintas. Con 2 variables (`n=2`) hay `2⁴ = 16` funciones posibles:

| a | b | f0 | f1 | f2 | f3 | f4 | f5 | f6 | f7 | f8 | f9 | f10 | f11 | f12 | f13 | f14 | f15 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| 0 | 1 | 0 | 0 | 0 | 0 | 1 | 1 | 1 | 1 | 0 | 0 | 0 | 0 | 1 | 1 | 1 | 1 |
| 1 | 0 | 0 | 0 | 1 | 1 | 0 | 0 | 1 | 1 | 0 | 0 | 1 | 1 | 0 | 0 | 1 | 1 |
| 1 | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 1 |

Cada una se expresa algebraicamente: `f0=0`, `f1=a·b`, `f2=a·b̄`, `f3=a`, `f4=ā·b`, `f5=b`, `f6=a·b̄+ā·b` (XOR), `f7=a+b` (OR), `f8=¬(a+b)` (NOR), `f9=a·b+ā·b̄` (XNOR), `f10=b̄`, `f11=a+b̄`, `f12=ā`, `f13=ā+b`, `f14=¬(a·b)` (NAND), `f15=1`.

### Circuito lógico (Cap. 5)

Un **circuito lógico** (o *diagrama de lógica*) es la representación gráfica de una red de compuertas cuyo comportamiento se expresa algebraicamente mediante una o varias funciones booleanas: las variables son las entradas y las funciones son las salidas. Por ejemplo, `f(a,b,c) = (a·b) + c` define un circuito de 3 entradas y 1 salida.

**Transistor:** es el conmutador electrónico elemental con el que se construyen las compuertas; puede operar como interruptor abierto (bit 0) o cerrado (bit 1). Una sola etapa de transistor típicamente implementa la función **NOT**; combinaciones simples de transistores implementan directamente **NAND** (en serie) y **NOR** (en paralelo), que por eso resultan más simples de fabricar que AND/OR.

### Formas normales o canonicas de una funcion

Un **término canónico** es todo producto o toda suma en la que aparecen **todas las variables** de la función, en su forma directa o negada.

#### minitermino o producto canonico

Es "el producto de las variables en juego (o sus negaciones individuales) que hace que el producto valga 1". Representa una combinación de la tabla de verdad mediante producto lógico. Con `n` variables hay `2ⁿ` minitérminos posibles.

| a | b | Minitérmino |
|---|---|---|
| 0 | 0 | ā·b̄ |
| 0 | 1 | ā·b |
| 1 | 0 | a·b̄ |
| 1 | 1 | a·b |

**Forma normal disyuntiva (FND):** si se expresa la función como la **suma lógica de los minitérminos** cuya fila en la tabla de verdad vale 1, se obtiene la FND.

#### maxitermino o suma canonica

Es "la suma de las variables en juego (o sus negaciones individuales) que hace que la suma valga 0". Los `0` de la combinación se expresan con la variable sin negar, y los `1` con la variable negada. Con `n` variables hay `2ⁿ` maxitérminos posibles.

| a | b | Maxitérmino |
|---|---|---|
| 0 | 0 | a+b |
| 0 | 1 | a+b̄ |
| 1 | 0 | ā+b |
| 1 | 1 | ā+b̄ |

**Forma normal conjuntiva (FNC):** si se expresa la función como el **producto lógico de los maxitérminos** cuya fila en la tabla de verdad vale 0, se obtiene la FNC.

> FND y FNC son las **formas normales o canónicas** de una función; se elige la que tenga menos términos, para favorecer la implementación del circuito.

#### Ejemplo: formas canónicas del semisumador

A partir de la tabla de verdad `s0 = a0⊕b0` y `cy0 = a0·b0`:

```
FND(s0) = (ā0·b0) + (a0·b̄0)
FNC(s0) = (a0+b0) · (ā0+b̄0)

FND(cy0) = a0·b0
FNC(cy0) = (a0+b0) · (a0+b̄0) · (ā0+b0)
```

## Circuito sumador-binario en paralelo (Cap. 5 / diapositivas)

Concepto de **UAL** (Unidad Aritmética y Lógica): componente de la CPU que realiza operaciones lógicas (AND, OR, XOR, etc.) y aritméticas (suma y resta), estructurado en dos bloques — la **unidad lógica (UL)** y la **unidad aritmética (UA)** — que reciben los operandos y una orden de operación indicando qué hacer.

### Semisumador (SS) o Half Adder (HA)

Suma 2 bits **sin** considerar el acarreo anterior. Tiene 2 salidas: suma y acarreo (*carry*).

| a | b | S (suma) | C (acarreo) |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |

```
S = a ⊕ b   (compuerta XOR)
C = a · b   (compuerta AND)
```

### Sumador completo (SC) o Full Adder (FA)

Suma 2 bits **más** el acarreo de la etapa anterior (`c₋₁`). Genera el resultado de la suma y el nuevo acarreo.

| a | b | c₋₁ | S | C |
|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

```
S = a ⊕ b ⊕ c₋₁
C = (a·b) + (a+b)·c₋₁
```

Con las formas canónicas: `FND(S) = (ā·b·c̄) + (ā·b̄·c) + (a·b̄·c̄) + (a·b·c)` y `FND(C) = (ā·b·c) + (a·b̄·c) + (a·b·c̄) + (a·b·c)`.

### Sumador binario paralelo con acarreo serie

Para sumar dos números de `n` bits se encadenan **1 semisumador** (para el bit menos significativo) y **(n−1) sumadores completos**: cada bloque recibe el acarreo del anterior, de modo que el acarreo se propaga en serie mientras los bits de A y B entran en paralelo.

```
B4 A4    B3 A3    B2 A2    B1 A1
  SC       SC       SC       SS
  S4       S3       S2       S1     (con acarreos C4..C1 encadenados)
```

Un **sumador de 8 bits** se arma con 1 SS + 7 SC en cadena (acarreo inicial `c₋₁ = 0`).

## Circuitos equivalentes

Dos circuitos son **equivalentes** cuando se representan con distintas formas algebraicas pero responden a la **misma tabla de verdad**. Por ejemplo, el circuito con `FND = (ā·b) + (a·b̄)` y el circuito con `FNC = (a+b) · (ā+b̄)` son ambos formas de expresar la función `a ⊕ b` (XOR) — representan la misma tabla de verdad con expresiones diferentes.

## Minimización de circuitos: Mapas de Karnaugh

La minimización busca el circuito más simple (menor cantidad de compuertas y de entradas) que cumpla la misma función. El método más usado es el **mapa de Veitch-Karnaugh**: una matriz con todas las combinaciones posibles de las variables (2×2 para 2 variables, 4×2 para 3 variables, 4×4 para 4 variables).

**Procedimiento:**
1. Completar el mapa con los `1` (y opcionalmente los `0`) según la tabla de verdad.
2. Agrupar la mayor cantidad posible de `1` **adyacentes**, siempre en **potencias de 2** (primero grupos de 8, luego 4, luego 2).
3. Las adyacencias se dan por aristas, no por vértices; además el borde superior es adyacente al inferior y el izquierdo al derecho (el mapa se "enrosca" como un cilindro/toro).
4. Cada grupo define un término (producto de las variables que **no cambian** dentro del grupo); la función mínima es la **suma** de todos los términos.

### Ejemplos de minimización (Cap. 5)

**Ejemplo 1 — 2 variables**, 2 unos adyacentes cubriendo toda la fila `b`:
```
f = a·b̄ + ā·b̄  =  b̄·(a+ā) = b̄·1 = b̄        (la variable a se elimina)
```

**Ejemplo 2 — 2 variables**, unos no adyacentes: no se puede minimizar → `fm = a·b̄ + ā·b`.

**Ejemplo 3 — 3 variables**, un solo "1" aislado: no se puede minimizar → `fm = ā·b·c̄` (ejemplo genérico, sin agrupar).

**Ejemplo 4 — 3 variables**, dos pares de unos adyacentes → `fm = ā·b̄ + a·b`.

**Ejemplo 5 — 3 variables**, agrupación óptima con un "1" reutilizado en dos grupos → `fm = ā·c̄ + ā·b̄` (el grupo vertical elimina la variable `b`).

**Ejemplo 6 — 3 variables**, cuatro unos adyacentes en un solo grupo → `fm = ā` (se eliminan `b` y `c`).

**Ejemplo 7 — 3 variables**, grupo "envolvente" (los unos de los extremos del mapa son adyacentes entre sí) → `fm = b̄` (se eliminan `a` y `c`).

**Ejemplo 8 — 3 variables**, caso complejo con 3 grupos (uno sin poder agruparse) → `fm = ā·b̄ + b̄·c + ā·b·c`.

> El método de Karnaugh es un atajo **visual** equivalente a aplicar los teoremas y postulados del Álgebra de Boole algebraicamente; reduce la cantidad de compuertas y de entradas, mejorando la eficiencia del circuito final.

## La resta en la UAL (diapositivas)

La resta se implementa con el **mismo sumador**, gracias a la propiedad de complemento en notación posicional. Para un número `N` de `p` dígitos en base `b`:

```
COMPLEMENTO DIRECTO (a la base - 1):    CD(N) = (b^p - 1) - N
COMPLEMENTO AUTÉNTICO (a la base):      CA(N) = b^p - N = CD(N) + 1
```

En binario, el complemento directo se obtiene **invirtiendo todos los bits**; sumándole 1 se obtiene el complemento auténtico (igual que el C1/C2 de la Unidad 3-4).

### Sumador-restador en complemento auténtico

Un circuito puede resolver tanto `a+b` como `a−b` con una sola línea de control **S/R** (Suma/Resta):

- Si `S/R = 0`: el bit `b` pasa sin cambios (se realiza la **suma**).
- Si `S/R = 1`: el bit transferido es el **complemento directo** de `b` (invertido) y además se inyecta un `1` como acarreo de entrada (`c₋₁`) para completar el complemento auténtico (se realiza la **resta**).

Esto se logra con una compuerta XOR entre `b` y `S/R` (que invierte `b` solo cuando `S/R=1`) alimentando al sumador completo, y conectando la línea `S/R` también como acarreo inicial `c₋₁`. Encadenando `n` de estos bloques (con sus acarreos en serie) se obtiene un **sumador-restador de n bits**.

## Ejemplo de aplicación: sistema contra incendios (diapositivas)

Un depósito tiene 3 sensores `S0, S1, S2`. Si **2 de 3** sensores están activos, se enciende la alarma luminosa `AL`. Si además `S2` está activo, se enciende también la alarma sonora `AS`.

| S2 | S1 | S0 | AL | AS |
|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 0 | 0 |
| 0 | 1 | 0 | 0 | 0 |
| 0 | 1 | 1 | 1 | 0 |
| 1 | 0 | 0 | 0 | 1 |
| 1 | 0 | 1 | 1 | 1 |
| 1 | 1 | 0 | 1 | 1 |
| 1 | 1 | 1 | 1 | 1 |

**Expresión literal completa (suma de productos):**
```
AL = S̄2.S1.S0 + S2.S̄1.S0 + S2.S1.S̄0 + S2.S1.S0
AS = S2.S̄1.S̄0 + S2.S̄1.S0 + S2.S1.S̄0 + S2.S1.S0
```

**Expresión en minitérminos (forma canónica, ya que cada término tiene todas las variables):**
```
AL = m3 + m5 + m6 + m7
AS = m4 + m5 + m6 + m7
```

A partir de estas expresiones (simplificadas con Karnaugh o Boole) se arma el circuito con compuertas AND/OR/NOT que recibe `S0, S1, S2` y genera `AL` y `AS`.

## Ejercicios propuestos (Capítulo 5)

1. Representar la tabla de verdad de `f = a·b̄ + ā·b + b·c`.
2. Representar el diagrama lógico de la función anterior.
3. Dada `f = a·b̄ + ā·b`: representarla con compuertas AND+OR+NOT, luego solo con OR+NOT, y luego solo con AND+NOT.
4. Dada una tabla de verdad, elegir la forma normal más conveniente (FND o FNC) para cada función.
5. Para `n=3`, verificar que la suma de todos los minitérminos de una función es igual a `1` (cubre toda la tabla).
6. Para `n=3`, verificar que el producto de todos los maxitérminos de una función es igual a `0`.
7. Dada una tabla de verdad con dos funciones `f1` y `f2`: representar su FND y FNC, y sus diagramas de lógica usando solo compuertas NAND o NOR.
