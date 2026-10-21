---
aliases:
  - Resumen Unidad 3 & 4 REPRECENTACION DE DATOS/ARITMETICA DEL COMPUTADOR
---
# [Power Point Unidad 3/4](https://drive.google.com/file/d/11EG_JdS-dVqHZFfoHDlbLudj6xOmlbkW/view)

### [[Assets/Unidad_III-IV-PrimeraParte.pdf|Diapositivas Primera Parte]] · [[Assets/Unidad_III-IV-SegundaParte.pdf|Diapositivas Segunda Parte]]

### [[Assets/Capitulo3-PatriciaQuiroga.pdf|Capítulo 3 - Representación de datos]] · [[Assets/Capitulo4-PatriciaQuiroga.pdf|Capítulo 4 - Aritmética de la computadora]]

# Algunas Definiciones

**Código alfanumérico:** es una convención que determina una combinación binaria para
cada símbolo que se quiera representar.

**Formato**: de una entidad binaria es la estructura y la cantidad de bits de un determinado
tipo de dato para su tratamiento dentro de la computadora.

**Datos primitivos en C:**
![[Pasted image 20260501182657.png]]
El rango es el rango de representacion que tiene ese tipo, por ejemplo el char solo puede representa de -128 a 127

Tabla completa (Cap. 3, datos primitivos en C):

| Tipo | Descripción | Bytes | Rango |
|---|---|---|---|
| char | Carácter | 1 | -128 a 127 |
| int | Entero con signo | 2 | -32768 a 32767 |
| short | Entero corto con signo | 2 | -32768 a 32767 |
| long | Entero largo con signo | 4 | -2147483648 a 2147483647 |
| unsigned | Entero sin signo | 2 | 0 a 65535 |
| float | Flotante simple | 4 | -3,4·10⁻³⁸ a 3,4·10³⁸ |
| double | Flotante doble | 8 | -1,7·10⁻³⁰⁸ a 1,7·10³⁰⁸ |
| long double | Flotante extendido | 10 | -3,4·10⁻⁴⁹³² a 1,1·10⁴⁹³² |

# Tipos de formato de datos

![[Pasted image 20260501182913.png]]
Los que tiene una X son los que vamos a ver

## Mapa general de representaciones (Tabla 3-1, Capítulo 3)

| Categoría | Representaciones |
|---|---|
| **Alfanuméricas** | ASCII, UNICODE |
| **Decimales (BCD)** | BCD puro o natural (8421), BCD exceso tres, BCD 2421 (Aiken) |
| **Binarias — números enteros** | Coma fija sin signo · Coma fija con signo (signo y magnitud) · Coma fija con complemento a la base (C2) · Coma fija con complemento restringido (C1) |
| **Binarias — números reales** | Coma flotante · Coma flotante con mantisa normalizada · Coma flotante IEEE P754 · Coma flotante "exceso 64" |
| **Redundantes (detección de errores)** | Paridad vertical (a nivel carácter), paridad horizontal (a nivel bloque), paridad entrelazada, código de Hamming |

> Este es el mapa completo del capítulo: la nota se enfoca en ASCII, BCD puro, los formatos binarios de enteros (sin signo, C1, C2), IEEE 754 y la paridad, que son los que marca la diapositiva ("los que tienen una X").

## Códigos de representación de caracteres alfanuméricos

### ASCII

#### Tabla Ascii
Código ASCII de 7 bits permite determinar 128 combinaciones posibles que representan 128
símbolos distintos.
	• 1 carácter se representa con 7 bits
	• Permite 27 = 128 combinaciones distintas


![[Pasted image 20260501183315.png]]
Bits de zona: Los primeros 4 bits de mayor significación en cada combinación se denominan bits de zona y en la tabla de doble entrada numeran las filas 0 a 7.

Bits de digitos: Los últimos 4 bits (3, 2, 1 y 0) en cada combinación se denominan bits de dígito y en la tabla numeran las columnas de 0 a 15, o de 0 a F en hexadecimal.

Ejemplo:
“A” = 0100 0001 = 41(h) = 65(d)


#### Tabla Ascii extendido
Código ASCII de 8 bits ó ASCII extendido surge debido a la necesidad de agregar caracteres
	• 1 carácter se representa con 8 bit
	• Permite 28 = 256 combinaciones distintas

![[Pasted image 20260501183450.png]]


### Código de representación decimal (BCD)

Es una convención que permite la representación de números entre 0 y 9 en bloques de
binarios de 4 bits

#### BCD puro o natural o 8421
![[Pasted image 20260501184612.png]]
![[Pasted image 20260501184710.png]]
El BCD puro es poner el numero individual en binario

##### Suma en BCD 8421
![[Pasted image 20260501184853.png]]

#### Resta en BCD 8421
![[Pasted image 20260501185348.png]]

#### BCD empaquetado y desempaquetado

- **Desempaquetado (unpacked):** cada dígito decimal ocupa un byte completo (4 bits de "zona" + 4 bits de dígito), tal como ingresan por teclado en código ASCII. Le "sobra" la zona para operarlo aritméticamente.
- **Empaquetado (packed):** se elimina la zona y cada dígito queda representado con solo 4 bits; el signo se ubica en la cabecera o cola del número (en el nibble sobrante del último octeto).

```
+3456   desempaquetado: 2B 33 34 35 36     empaquetado: 03456B   (signo + = B)
+279    desempaquetado: 2B 32 37 39        empaquetado: 279B
-3456   desempaquetado: 2D 33 34 35 36     empaquetado: 03456D   (signo − = D)
```

El empaquetado ahorra espacio pero la ALU solo puede operar un grupo reducido de dígitos decimales por vez, por lo que operandos largos requieren varios accesos.

#### BCD exceso tres

Se obtiene sumando `3` (binario `0011`) a cada dígito del BCD puro. Es un código **autocomplementario**: el complemento restringido de un dígito se obtiene simplemente invirtiendo sus bits.

| Decimal | BCD exceso tres |
|---|---|
| 0 | 0011 |
| 1 | 0100 |
| 2 | 0101 |
| 3 | 0110 |
| 4 | 0111 |
| 5 | 1000 |
| 6 | 1001 |
| 7 | 1010 |
| 8 | 1011 |
| 9 | 1100 |

#### BCD 2421 (Aiken)

Cada bit "1" tiene un peso `2 4 2 1` (en vez de `8 4 2 1`). También es autocomplementario.

| Decimal | BCD 2421 |
|---|---|
| 0 | 0000 |
| 1 | 0001 |
| 2 | 0010 |
| 3 | 0011 |
| 4 | 0100 |
| 5 | 1011 |
| 6 | 1100 |
| 7 | 1101 |
| 8 | 1110 |
| 9 | 1111 |

> Ejemplo: el 7 en BCD 2421 es `1101`, ya que `2+4+1 = 7`.

#### Regla general para sumar en BCD (cualquier código, Cap. 4)

Al sumar dígito a dígito (más el acarreo de la columna anterior), el resultado binario puede llegar como máximo a `19` (`9+9+1`). Como la tabla BCD queda "desfasada" en 6 unidades respecto de la binaria pura:

> Si la suma parcial `Sᵢ > 9` **o** generó acarreo `Cᵢ = 1`, se **suma 6 (0110)** al resultado parcial para volver a la representación BCD correcta, y se genera un acarreo de `1` para la columna siguiente.

**Ejemplo:** `123 + 456 = 579` en BCD 8421 → cada columna suma sin exceder 9, no hace falta corrección. **Ejemplo con corrección:** `456 + 789 = 1245`, donde varias columnas superan 9 y requieren sumar 6 (0110) con su acarreo correspondiente.

#### Regla general para restar en BCD (complemento a 9, Cap. 4)

El código BCD 8421 **no es autocomplementario** (a diferencia del exceso-3 y el 2421): no alcanza con invertir los bits para hallar su complemento a 9. El algoritmo es:

1. Sumar `6 (0110)` al dígito BCD.
2. Invertir los bits del resultado.

Esto equivale a calcular `9 − N` (complemento restringido decimal) a partir de la lógica binaria `15 − (6+N)`. Luego, la resta `M − S` se resuelve como `M + Complemento(S) + 1`, igual que en binario puro.

> Ejemplo: `456 − 123 = 333`. Con complemento: `999 − 123 = 876`; `456 + 876 = 1332`, se le suma 1 (por ser complemento restringido) → `1333`; se desprecia el exceso de `10ⁿ` → resultado `333`.

### Código de representación binaria de números enteros

#### Coma fija o punto fijo sin signo para representar números enteros positivos

![[Pasted image 20260501231512.png]]

Rango de representación: `0 ≤ x ≤ 2ⁿ − 1`, donde `n` es la cantidad de bits del formato. Por ejemplo, con `n = 3` bits el rango es `0` a `7`.

Si el resultado de una suma supera el rango, se pierde el bit más significativo y el resultado es incorrecto (**overflow**); las unidades de cálculo de la CPU señalan esto con las banderas o flags.

#### Coma fija o punto fijo con signo — Signo y magnitud (sin complemento)

Igual al formato anterior, pero reserva el bit de extrema izquierda para el signo (`0` positivo, `1` negativo); los `n-1` bits restantes representan siempre la **magnitud binaria real**, tanto para positivos como para negativos (no hay complemento).

Rango: `−(2ⁿ⁻¹ − 1) ≤ x ≤ +(2ⁿ⁻¹ − 1)`. Para `n = 3` bits el rango es `−3` a `+3`.

> Particularidad: existen **dos ceros** (`+0` y `−0`), ya que el negativo surge solo de cambiar el bit de signo sobre la misma magnitud.

#### Coma fija o punto fijo con signo — Complemento restringido (C1)

Igual al formato de signo y magnitud, pero los negativos se representan con el **complemento restringido (C1)** de su magnitud (bits invertidos), en vez de la magnitud real.

Rango: `−(2ⁿ⁻¹ − 1) ≤ x ≤ +(2ⁿ⁻¹ − 1)`. También existen **dos ceros** (`+0` y `−0`).

#### Coma fija o punto fijo con signo para representar números enteros positivos y negativos en C2

Convenio de representación:
Para poder representar magnitudes enteras positivas y negativas, lo primero que se debe definir es como se representa el signo. Este convenio adopta que el bit mas significativo (**MSB**) se lo utilice para identificar el signo, siendo 0 para los positivos y 1 para los negativos.

![[Pasted image 20260501231707.png]]
Por lo tanto, si una variable está definida como entera todo número positivo comenzará de izquierda a derecha con 0 y todo número negativo comenzará con 1.


Convenio de representación continuación:
Es de pensar que, si tenemos formato n = 3 las posibles combinaciones serán 8 desde 000 hasta 111, por lo
tanto las siguientes combinaciones 000, 001, 010, 011 representarán números positivos y las combinaciones 100, 101, 110 y 111 representarán números negativos.

Existen diferentes formas de poder representar los valores y esto dependerá del convenio elegido, pudiendo ser Signo y Magnitud, Complemento a 1 ó Complemento a 2.

Para el convenio coma fija o punto fijo con signo, vamos a definir la utilización del Complemento a 2, que enuncia que los números positivos estarán representados por su magnitud binaria en los n-1 bits restantes del formato (excluye el bit de signo) y los números negativos estarán representados por el complemento a 2 de la magnitud binaria en los n-1 bits restantes del formato.


![[Pasted image 20260502013737.png]]

Rango de representación en C2: `−2ⁿ⁻¹ ≤ x ≤ +(2ⁿ⁻¹ − 1)`. Para `n = 3` bits el rango es `−4` a `+3` (a diferencia de signo-magnitud y C1, en C2 hay **un solo cero**, y el rango negativo tiene un valor más que el positivo).

### Comparación de las 3 formas de representar un negativo (Cap. 4)

Ejemplo con `−3(10)` en un formato de 8 bits:

```
1. Signo y magnitud:  1 0000011
2. Complemento a 1:   1 1111100
3. Complemento a 2:   1 1111101
```

# Flags

### Overflow
Para las formas de variable vistas (Enteras Sin Signo (Naturales), Enteras con Signo y Reales,
cuando el resultado de una operación supera los límites definidos por el rango (mayor que el
máximo o menor que el mínimo), entonces es incorrecto.

Este error se conoce como overflow de resultado y actualiza un bit (flag de overflow) en un
registro asociado a la ALU, conocido como registro de estado o status register.

### Underflow
Solo para variables Reales, cuando el resultado de una operación cae en el hueco definido por los
límites inferiores del rango (valores muy cercanos a cero, del que se excluye el cero). El error se
conoce como underflow de resultado.

## Flag V (Overflow)

indica si hubo o no overflow o desborde de registro, después de haberse llevado a cabo la operación. Si almacena un valor “1” significa que hubo overflow; si almacena un valor “0”
significa que no hubo overflow.

Indica un error de rango en números signados. Ocurre si sumamos dos números del mismo signo y el resultado tiene el signo opuesto. Aquí: **Negativo + Negativo = Positivo**. Esto indica que el resultado real (−138) no cabe en 8 bits (rango: −128 a +127).

## Flag C (Carry)

indica el acarreo o carry que se produce en la operación. Se pone en “1” si Cn es igual a “1”, o se pone en “0” si Cn es igual a “0”.

Representa el acarreo de salida del bit más significativo. Indica que la suma excedió la capacidad del registro si se tratara de números sin signo.


## Flag S (Sign)

indica el signo del resultado de la operación. Se pone en “1” si el resultado es negativo,
o se pone en “0” si el resultado es positivo.

## Flag Z (Zero)

indica si el resultado de la operación es 0 o distinto de 0. Se pone en “1” si el resultado
es 0, o se pone en “0” si el resultado es distinto de 0.

## Flag P (Parity) 

si la cantidad de bits 1 del resultado de la operación es impar se pone un “1”, si la cantidad de bits 1 del resultado de la operación es par se pone un “0”.

## Flag A (Auxiliary carry)

indica el acarreo o carry que se produce del 4 bit. Se pone en “1” si C4 es
igual a “1”, o se pone en “0” si el resultado es negativo.

# Operaciones en Convenio Punto Fijo con C2

## Los 4 casos posibles de suma de enteros signados (Cap. 4)

Con los negativos representados en C2, toda suma se resuelve como una suma binaria normal entre los bits (signo incluido), en cualquiera de estos 4 casos:

```
(+A)+(+B)          (+A)+(-B)         (-A)+(+B)          (-A)+(-B)
(+3)+(+3) = +6     (+10)+(-7) = +3   (-7)+(+1) = -6     (-64)+(-32) = -96
  00000011           00001010          11111001           11000000
+ 00000011         + 11111001        + 00000001         + 11100000
  00000110           00000011          11111010           10100000
```

- El **signo se suma como cualquier otro bit**.
- Se consideran **todos** los bits del formato, aunque sean ceros no significativos.
- Cuando el resultado es negativo, queda expresado en su forma **complementada**; para conocer su magnitud real hay que volver a complementarlo (complemento del complemento).
- El acarreo final (`carry` que "se sale" del formato) se **desprecia** cuando no indica overflow.

### Regla práctica para detectar overflow: comparar los dos últimos acarreos

Hay overflow si el **último acarreo (Cₙ) es distinto del penúltimo (Cₙ₋₁)**. Para un formato de 8 bits (`C7` = acarreo al bit de signo, `C6` = acarreo hacia el bit de signo):

```
  +127   01111111        -128   10000000
+ +127   01111111       + -128   10000000
  +254   11111110        -256   00000000
  (C7=0, C6=1 -> overflow de positivos)   (C7=1, C6=0 -> overflow de negativos)
```

> El overflow **solo puede producirse entre operandos del mismo signo**: si uno es positivo y otro negativo, el resultado siempre entra en el formato (en la práctica, uno se resta del otro).

### Ejemplo del libro: esquema de registros para suma de 8 bits

La ALU (con acumulador **AC**, un registro para el segundo operando y el **Registro de Estado — SR** con los flags S, Z, V, C) resuelve, por ejemplo:

```
A = -117(10)   B = +68(10)   ->   (-117) + 68 = -49
```

## Suma

![[Pasted image 20260502014928.png]]

### Suma paso a paso (ejemplo diapositivas): 116(10) + 5(10) = 121(10), n = 8 bits

```
1) Paso a binario:        116 = 1110100(2)         5 = 101(2)
2) Completo a n=8 bits:   116 = 01110100(2)         5 = 00000101(2)
3) Sumo en formato n=8:      01110100
                          +  00000101
                          =  01111001
4) Verifico signo/resultado: 01111001(2) = +121(10)
5) Flags:  S=0  Z=0  V=0  C=0
```


## Resta

(A−B→A+C2 de B),

![[Pasted image 20260502015017.png]]

### Resta por C2 paso a paso (ejemplo diapositivas): 116(10) − 5(10) = 111(10), n = 8 bits

```
1) Paso a binario:            116 = 1110100(2)        5 = 101(2)
2) Completo a n=8 bits:       116 = 01110100(2)       5 = 00000101(2)
3) C2 al valor negativo (-5): 00000101 -> invierto -> 11111010 -> sumo 1 -> 11111011
4) Sumo en formato n=8:          01110100
                               + 11111011
                               =  01101111
5) Verifico: 01101111(2) = +111(10)
   Flags:  S=0  Z=0  V=0  C=1  (hay acarreo final, que se desprecia)
```

### Resta como suma con el complemento (algoritmo general, Cap. 4)

Para restar `A − B`, la ALU (que solo tiene circuito sumador) hace `A + C2(B)`:

1. Invertir los bits del sustraendo (complemento restringido, C1).
2. Sumarle 1 (se obtiene el complemento auténtico, C2).
3. Sumar ese resultado al minuendo.

```
(+A) - (-B) = (+A) + (+B)      Ejemplo: (+10) - (-7) = +17
+10 = 00001010      -7 = 11111001
1. Invertir -7:        00000110
2. Sumar 1:             00000111
3. Sumar al minuendo: 00001010 + 00000111 = 00010001(2) = +17(10)
```

### Ejercicios propuestos (diapositivas) — formato n=8 bits, negativos en C2

1. `124(10) + 15(10) =`
2. `-7(10) − 6(10) =`
3. `126(10) + 1(10) =`
4. `256(10) − 256(10) =`

## Multiplicación en complemento a 2: Algoritmo de Booth (Cap. 4)

Permite multiplicar dos números en C2 usando solo sumas, restas y desplazamientos. Usa 4 elementos: **A** (acumulador, inicia en 0), **Q** (multiplicando), **Q₋₁** (bit auxiliar, inicia en 0) y **M** (multiplicador). También una **cuenta** = cantidad de bits del formato.

Pasos (se repiten hasta que `cuenta = 0`):
1. Comparar los bits `Q₀` (LSB de Q) y `Q₋₁`.
2. Si son `0 1`: `A = A + M`.
3. Si son `1 0`: `A = A − M` (con complemento a la base de M).
4. Si son `0 0` o `1 1`: no se suma ni resta.
5. Desplazar aritméticamente a derecha (**SAR**) el grupo `A, Q, Q₋₁` (se conserva el signo en el bit más alto de A).
6. Decrementar la cuenta; si no llegó a 0, volver al paso 1.

El resultado final de la multiplicación queda alojado en `A` y `Q` juntos.

> Ejemplo (formato de 4 bits): multiplicar `-4 × +2`. Resultado esperado: `-8` → `11111000` en el registro `A,Q`.

# Endianness - Extremidad

Referencia al orden en el que se almacenan los bytes dentro de la memoria.
Si tenemos la siguiente cadena en hexadecimal 90AB12CD para almacenar en la memoria, según el orden:

![[Pasted image 20260502015139.png]]

### Tablas exactas del ejemplo (diapositivas) — cadena `90AB12CD`

**Big Endian (Motorola):** el byte más significativo se guarda en la dirección más baja.

| Dirección | Valor (hex) | Valor (binario) |
|---|---|---|
| xxx0 | 90 | 10010000 |
| xxx1 | AB | 10101011 |
| xxx2 | 12 | 00010010 |
| xxx3 | CD | 11001101 |

**Little Endian (Intel):** el byte menos significativo se guarda en la dirección más baja.

| Dirección | Valor (hex) | Valor (binario) |
|---|---|---|
| xxx0 | CD | 11001101 |
| xxx1 | 12 | 00010010 |
| xxx2 | AB | 10101011 |
| xxx3 | 90 | 10010000 |

> Atención: **no se invierten los octetos/bytes en sí** (no se guarda "DC" en vez de "CD"); solo cambia el **orden** en que se ubican los bytes completos. **ARM y Power PC** pueden trabajar con ambos esquemas (*bi-endian*).

# IEEE 754

![[Pasted image 20260502015357.png]]

![[Pasted image 20260502015436.png]]


### Como se hace?

Como queda representada en memoria la variable A definida como Real en Simple precisión:
A = - 53,25
Primero debemos pasar el decimal a Binario natural. TENER EN CUENTA QUE PUNTO FLOTANTE NO TRABAJA CON COMPLEMENTO.
Da como resultado en binario A = - 110101,01
Luego normalizamos: - 1,1010101 x 10101 donde el exponente p está también en binario que es el 5 decimal.
Con esto pasamos a la representación:
Campo Bit de Signo S = 1 por ser número negativo
Campo exponente (Característica) C = p + 127, es decir 5 + 127 = 132 que se representará con los 8 bits de dicho campo. (10000101)
Campo mantisa (es lo que está en la parte fraccionaria de la expresión normalizada) es 1010101
Con esto podemos ver como queda la representación de la variable en los 32 bits que utiliza este format.

![[Pasted image 20260502015556.png]]

### Otros ejemplos de representación IEEE 754 simple precisión (diapositivas)

| Variable | S | Característica (±E+127) | Mantisa |
|---|---|---|---|
| A = -53,25 | 1 | 10000100 | 10101010000000000000000 |
| B = 0,125 | 0 | 01111100 | 00000000000000000000000 |
| C = -205,2 | 1 | 10000110 | 10011010011001100110011 |

### Cómo se almacena el ejemplo A = -53,25 en memoria (Big Endian vs Little Endian)

Los 32 bits del formato (`1 10000100 10101010000000000000000`) se agrupan en 4 bytes: `11000010 01010101 00000000 00000000` (hex: `C2 55 00 00`).

**Big Endian:**

| Dirección | Valor (hex) | Valor (binario) |
|---|---|---|
| xxx0 | C2 | 11000010 |
| xxx1 | 55 | 01010101 |
| xxx2 | 00 | 00000000 |
| xxx3 | 00 | 00000000 |

**Little Endian:**

| Dirección | Valor (hex) | Valor (binario) |
|---|---|---|
| xxx0 | 00 | 00000000 |
| xxx1 | 00 | 00000000 |
| xxx2 | 55 | 01010101 |
| xxx3 | C2 | 11000010 |

En hexadecimal completo: `B = 0,125` → `3E000000` · `C = -205,2` → `C34D3333`.

✓Punto flotante a su vez puede trabajar con lo que se llama Doble Precisión y también con
Precisión Extendida.
✓Para estos casos varía tanto el campo característica como el campo mantisa. En el primer caso
se logra ampliar los límites de representación (poder representar números aún mas grandes y
mas chicos), y en el segundo se aumenta la precisión con la cual vamos la variable se define
(cuantos decimales de precisión va a tener)

![[Pasted image 20260502020554.png]]
Donde dice 34 es 64
# Overflow y Underflow

Valores que representan el cero y el infinito:
✓ La representación del **Número cero** en este convenio es con los 32 bits iguales a cero
✓ **El infinito** se representa con todos 1 en el campo exponente (característica) y todos 0 en la
mantisa.
Como consecuencia:

Característica mínima 00000000 por lo tanto exponente mínimo -127
Característica máxima 11111111 por lo tanto el exponente máximo 128

Es decir que los valores extremos de las características se los utiliza para representar el cero y el
infinito, es decir que el resto de los valores puede variar entre 00000001 y 11111110 y esto da
como resultado no poder representar números muy cercanos a cero.


Limites de representación de las variables reales:

Para simple precisión serán: Mínimo aproximadamente -1038 y Máximo aproximadamente 1038

Pero a raíz de los valores reservados para la Característica mencionados anteriormente resulta
que números muy cercanos al cero no podrán representarse y los mismos son:
Entre – 10-^38 y 10^-38 aproximadamente excluyendo el cero que si se puede representar.

![[Pasted image 20260502020437.png]]




# Representaciones redundantes

• El cambio de un bit en el almacenamiento o la manipulación de datos origina resultados 
erróneos.

• Para detectar e incluso corregir estas alteraciones se usan códigos que agregan “bits redundantes”.

• Por ejemplo, los bits de paridad se agregan al dato en el momento de su envío y son corroborados en el momento de su recepción por algún componente de la computadora, y lo mismo sucede al
revés.

• El resultado de “medir” la cantidad de “ruido” que puede afectar la información se representa con un coeficiente conocido como tasa de error.

• De acuerdo con el valor de la tasa de error, se selecciona un código, entre los distintos
desarrollados, para descubrir e incluso corregir los errores.

• Estos códigos reciben el nombre de códigos de paridad.

• Paridad vertical simple o a nivel carácter
![[Pasted image 20260502020743.png]]

• Paridad entrelazada

Utilizando en conjunto el código de paridad vertical con el horizontal se construye el código de paridad entrelazada, que además de detectar errores permite corregirlos.

Ejemplo: Supongamos una transmisión de cuatro caracteres en código ASCII de 7 bits, con paridad par. El primer grupo indica cómo deberían llegar loscaracteres a su lugar de destino, de no haberse  producido errores en la transmisión. El segundo grupo indica con un recuadro el bit que tuvo un error en la transmisión.

![[Pasted image 20260502020912.png]]



• Paridad entrelazada
La forma de detectar el error es verificar tanto los bits del mensaje como los de paridad, generando un nuevo bit de paridad (en este caso llamado Verificador) Donde hay 1 es que hay error.

La forma de corregir el error es invertir el bit señalado como erróneo.

Si hay más de un error en la misma fila o columna, quizá se pueda detectar, pero no se podrá determinar con exactitud cuál es el error.

## Paridad horizontal a nivel de bloque (Cap. 3)

Por cada bloque de caracteres se crea un byte adicional de paridad: el bit 0 de ese byte es la paridad de todos los bits 0 del bloque, el bit 1 es la paridad de todos los bits 1, y así sucesivamente. Es la paridad "PH" que se combina con la paridad vertical "PV" para formar la **paridad entrelazada** vista arriba. En la práctica, la paridad entrelazada no se considera óptima por la cantidad de bits de paridad que agrega, por lo que suele preferirse el **código de Hamming**.

## Código de Hamming (Cap. 3)

Permite **detectar y corregir** errores agregando `p` bits de paridad que cumplan:

```
2^p ≥ i + p + 1
```
donde `i` es la cantidad de bits de información a transmitir y `p` la cantidad de bits de paridad.

- Con 2 bits de paridad se puede corroborar 1 bit de información.
- Con 3 bits de paridad, hasta 8 bits de información (`2³ ≥ 4+3+1` se cumple, por ejemplo, para 4 bits de datos).
- Con 4 bits de paridad, hasta 11 bits de información.

Los bits de paridad se ubican en las posiciones potencias de 2 (`b1, b2, b4, b8, ...`) de la cadena a transmitir; el resto son bits de datos. Cada bit de paridad corrobora un subconjunto específico de posiciones (aquellas cuya representación binaria tiene un "1" en la posición correspondiente):

```
p1 = b1 + b3 + b5 + b7 + ...
p2 = b2 + b3 + b6 + b7 + ...
p3 = b4 + b5 + b6 + b7 + ...
```

**Ejemplo:** se transmite `1101` (con 3 bits de paridad par agregados) y se recibe con un error. En la verificación se recalculan `p3 p2 p1` en el receptor: si el resultado (leído como binario) es distinto de `000`, indica **el número del bit erróneo**. Por ejemplo, si la verificación da `111(2) = 7`, el error está en el bit 7. Para corregirlo, simplemente se **invierte ese bit**.

## Strings: delimitación de cadenas de caracteres (Cap. 3)

Las cadenas de caracteres (*strings*) pueden almacenarse de dos formas:

- **Longitud fija:** cada dato ocupa siempre la misma cantidad de bytes; el comienzo y el final se conocen de inmediato.
- **Longitud variable:** para saber dónde termina el dato hay dos métodos:
  - Un campo al inicio indica la **longitud en bytes** del string.
  - Un **separador** marca el final (no forma parte del dato). Los dos más usados son el **byte 0 (NUL)** o el carácter de **retorno de carro (CR)** — en el teclado, la tecla `<ENTER>`.

> Ejemplo: el string `"PRIMER AÑO"` se representa en ASCII de 8 bits (hexadecimal) como: `50 52 49 4D 45 52 20 41 A5 4F 0D` (el último byte `0D` es el CR que marca el final).

![[Pasted image 20260502021010.png]]