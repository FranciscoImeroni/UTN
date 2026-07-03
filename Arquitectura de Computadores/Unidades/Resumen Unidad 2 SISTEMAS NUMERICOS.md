### Saber:
 -  saber hacer todos los pasajes ya que hay que escribir el camino y luego se puede verificar no calculadora
 - 

[[Sistemas numericos-TP2.pdf|Ejercicios TP2]] (Ejercicios sobre pasajes SNP)

### [PowerPoint clase](https://drive.google.com/file/d/1H9x_xUrWkUgAkAeS_mhO8mdrlz2bZstt/view)

### [[Assets/SistemasNumeración_Unidad_II.pdf|Diapositivas Unidad 2]]

### [[Capitulo2-PatriciaQuiroga.pdf|Unidad 2 pdf]]

## Introducción: representación de la realidad

La información se puede representar de dos maneras:

- **Externa** (utilizada por las personas): números, letras, gráficos.
- **Interna** (utilizada por la computadora): representación de números, representación de caracteres.

En este capítulo se hace especial hincapié en el **sistema binario**, por ser el que utiliza la computadora. El sistema binario representa todos los símbolos del usuario (letras, números), los caracteres (signos de puntuación) o los comandos (`<ENTER>`) con una secuencia de bits. No solo los datos que se procesan están representados en binario, sino también las **instrucciones** de los programas. Por ejemplo, la letra "A" se codifica como `01000001` y la "B" como `01000010`.

# Sistemas numéricos

#### ✓ No Posicionales: el valor del símbolo no depende la posición que este ocupe dentro del número.

Ejemplo: CCXXI (Números Romanos) 221 en decimal

#### ✓ Posicionales (SNP): el valor del símbolo depende la posición que este ocupe dentro del número o sea cada símbolo está afectado por un factor de escala.

Ejemplo: 521 ≠ 125 El significado varia según la ubicación (numeración Decimal)

## Sistemas de notación posicional (SNP)

La suma de cada digito multiplicado por su “peso” permite obtener el valor final del número. En
SNP toda cantidad puede ser expresado como un polinomio de potencias de la base.
![[Pasted image 20260413203838.png]]

### Sistema decimal

El sistema de numeración decimal es un sistema de notación posicional formado por 10 símbolos (0, 1, 2, 3, 4, 5, 6, 7, 8, 9). Entonces, el número decimal 1.023 estará conformado por la suma de las siguientes cantidades: 

![[Pasted image 20260413195423.png]]


### Sistema binario

El sistema binario, o de base 2 formado por dos símbolos (0,1) a los que se denomina bits. El sistema binario permite analogar sus dos símbolos (0 y 1) con dos “estados” posibles; por lo tanto, los valores 0 y 1 se pueden interpretar, por ejemplo, como: OFF ON ,Falso Verdadero ,No Sí 

Un número binario está compuesto por un conjunto de bits y su equivalente decimal será igual a la suma que resulte de multiplicar cada bit por las potencias de 2 (la base B) que correspondan a su posición. Así, el número binario:

![[Pasted image 20260413195714.png]]

### Sistema octal

El sistema octal, o de base 8, es un sistema posicional formado por ocho símbolos (0, 1, 2, 3, 4, 5, 6, 7); el peso de cada cifra son las potencias sucesivas de 8. De esta manera:

![[Pasted image 20260413195915.png]]

### Sistema hexadecimal

El sistema hexadecimal, o de base 16, que representan los valores decimales de 0 a 15

Luego, si queremos hallar el equivalente decimal del número hexadecimal 1A2, lo haremos de la siguiente forma:

![[Pasted image 20260413200032.png]]


### Tabla de equivalencias entre sistemas (Tabla 2-1)

| Decimal | Binario | Octal | Hexadecimal |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 1 | 1 | 1 | 1 |
| 2 | 10 | 2 | 2 |
| 3 | 11 | 3 | 3 |
| 4 | 100 | 4 | 4 |
| 5 | 101 | 5 | 5 |
| 6 | 110 | 6 | 6 |
| 7 | 111 | 7 | 7 |
| 8 | 1000 | 10 | 8 |
| 9 | 1001 | 11 | 9 |
| 10 | 1010 | 12 | A |
| 11 | 1011 | 13 | B |
| 12 | 1100 | 14 | C |
| 13 | 1101 | 15 | D |
| 14 | 1110 | 16 | E |
| 15 | 1111 | 17 | F |

El **"10"** (uno, cero) es el número que en cualquier sistema representa a la base. Por ejemplo, en base 5:

```
10(5) = 5(10)
100(5) = 5² = 25(10)
```

### Número de cifras. Cantidad decimal máxima

Llamando **n** a la cantidad de dígitos en juego:

- Con **n bits** se pueden representar los valores decimales comprendidos entre `0` y `2ⁿ − 1`.
- Con **n dígitos octales** se pueden representar los valores decimales comprendidos entre `0` y `8ⁿ − 1`.
- Con **n dígitos hexadecimales** se pueden representar los valores decimales comprendidos entre `0` y `16ⁿ − 1`.

Para analizar los contenidos de los registros internos de la computadora, se utilizan los sis- temas octal y hexadecimal como “métodos taquigráficos”, que permiten representar su conte- nido binario compactado. Esto es posible debido a que 8 y 16 son potencias exactas de 2 (8 = 2^3 y 16 = 2^4); en consecuencia, es factible convertir números del sistema binario al octal y al hexadecimal tomando agrupaciones de 3 y 4 bits, respectivamente. Por ejemplo, el número binario 11110010(2) = 362(8) y F2(16)

Para lograr esta conversión, la cadena binaria 11110010 se divide en grupos de 3 bits, de derecha a izquierda y, si fueran necesarios para completar el grupo, se agregan ceros a la izquierda del último bit:

![[Pasted image 20260413201516.png]]

 se puede inferir que cuanto más grande es la base, más importante es la reducción. Entonces, por qué no utilizar un sistema base 32 o 64?
la razón es simple, resultaría engorroso recordar 32 o 64 símbolos diferentes y se estaría ante un problema casi mayor que el de operar con bits

## Métodos de conversión de números enteros y fraccionarios (Pasajes)

### Binario a decimal

#### Enteros
se obtendrá su valor decimal multiplicando cada bit por la potencia de dos que corresponda a su posición y sumando los valores finales

![[Pasted image 20260413203615.png]]


#### Fracciones

Sea el número 0,101(2), se obtendrá su valor decimal multiplicando cada bit por la potencia negativa de dos que corresponde a su posición, a partir de la coma y sumando los valores finales. 

![[Pasted image 20260413204406.png]]

Para la izquierda va sumando en positivo y para la derecha en negativo, el primer numero a la izquierda de la coma es el ^0

**Para recordar:** 
2^−1 = 0,5 
2^−2 = 0,25 
2^−3 = 0,125 
2^−4 = 0,0625 
2^−5 = 0,03125

### Octal a decimal

se obtendrá su valor decimal multiplicando cada dígito octal por la potencia de ocho que corresponda a su posición y sumando los valores finales.

![[Pasted image 20260413205127.png]]

### Hexadecimal a decimal

Sea el número 1A,0F(16), se obtendrá su valor decimal multiplicando cada dígito hexadecimal por la potencia de dieciséis que corresponda a su posición y sumando los valores finales.

![[Pasted image 20260413205321.png|515]]


### Método de divisiones sucesivas (para convertir un número entero decimal a otras bases)

Para convertir un número entero decimal a cualquier sistema, se divide el número por la base que corresponda hasta hallar el último cociente (o hasta que el último cociente sea cero), que formará con todos los restos anteriores (desde el último hasta el primero) el número buscado. 

![[Pasted image 20260413205753.png]]

### Método de multiplicaciones (para convertir un número fraccionario decimal a otras bases)

1. Multiplicar la fracción por la base de conversión (en el caso de que la conversión se realice a sistema binario, la base es 2), cuyo resultado tiene una parte entera, que se considerará uno de los dígitos fraccionarios buscados.
2. Del resultado se toma sólo la parte fraccionaria y se vuelve a multiplicar por la base, para hallar el siguiente dígito fraccionario buscado. Este procedimiento se realiza tantas veces como sea necesario, hasta cancelar el método por alguno de los motivos que siguen: 
	-   La parte fraccionaria en una iteración se hace cero.
	-   En la conversión comienza a repetirse una secuencia de dígitos fraccionarios, lo que implica que es una fracción periódica.
	-   Se hallaron varios dígitos y se considera que la fracción obtenida tiende al resultado esperado por la conversión, y el error es infinitesimal.

#### Decimal a binario

Si aparece por ejemplo 10,4 hay que hacer el 10 con el metodo de diviciones y el 4 con el de multiplicaciones

![[Pasted image 20260413210945.png]]

![[Pasted image 20260413211125.png]]

![[Pasted image 20260413211138.png]]

El arco en el resultado señala el conjunto de números que se repiten, o sea, la periodicidad 

![[Pasted image 20260413211138.png]]

El método se cancela cuando la parte fraccionaria resultante de una multiplicación da 0, cuando se obtiene periodicidad o cuando se suspende luego de varias iteraciones

#### Decimal a octa

![[Pasted image 20260413211511.png]]

#### Decimal a hexadecimal
![[Pasted image 20260413211555.png]]


### Pasaje directo entre las bases 2 a 8 y 2 a 16

Pasaje directo o convertir directamente se refiere a no pasar por base (10) por ejemplo pasar de base 2 a 16 sin la necesidad de pasar por la 10. Esto es posible entre bases que son potencias exactas de 2, como base 2 a 16 y 2 a 8
#### Conversión directa de binario a octal (2) a (8)

Si se observa la tabla de equivalencias entre sistemas, se ve que todo símbolo octal se representa, a lo sumo, con una combinación de 3 bits

![[Pasted image 20260414235431.png]]

Llamaremos **LSB** (Least Significant Bit o de menor peso) al primer bit de la derecha y **MSB** (Most Significant Bit o de mayor peso)

##### Conversión directa de binario a octal 
1. Se divide el número binario en grupos de 3 bits, en el sentido siguiente: para enteros desde el LSB hacia el MSB y para fracciones en sentido inverso. 
2. Luego se sustituye cada grupo de 3 bits por el correspondiente dígito octal, con cuidado de completar con ceros a la derecha la parte fraccionaria, de ser necesario para obtener el grupo de 3.

##### Enteros

Sea el número 1011001(2), tomado como ejemplo para la conversión de binario a octal, el seguimiento del procedimiento da como resultado

![[Pasted image 20260414235724.png]]

##### Fracciones

En primer término se deben formar grupos de 3 dígitos binarios, teniendo presente que en la parte fraccionaria cambia el sentido de la agrupación, que se encuentra representado por la orientación de las flechas

![[Pasted image 20260414235832.png]]
Los dos ceros de la fracción se agregan para completar el grupo y se señalaron como superíndices para marcar el procedimiento

#### Conversión directa Octal a binario (8) a (2)

En forma inversa a la enunciada antes, se puede convertir directamente un número octal a su correspondiente en base 2, reemplazando cada dígito octal por el equivalente binario en grupos de 3 bits

##### Enteros

Así, si se toma el ejemplo anterior, 65(8) se convertirá directamente a binario reemplazando cada dígito octal por el grupo de tres dígitos binarios que le correspondan.

![[Pasted image 20260415002352.png]]
##### Fracciones

El sentido de las agrupaciones para números fraccionarios es siempre el mismo.

![[Pasted image 20260415002421.png]]


#### Conversión directa Binario a hexadecimal (2) a (16)

##### Enteros

Nótese que cada símbolo hexadecimal se representa, a lo sumo, con una combinación de 4 bits. Si se toma el número 110101(2) y se lo divide en grupos de 4 bits empezando por el LSB, al sustituir cada grupo por su dígito hexadecimal correspondiente, se obtendrá:

![[Pasted image 20260415002608.png]]
El criterio de formar grupos de 4 bits es el mismo que se tomó para la conversión a octal

##### Fracciones

![[Pasted image 20260415002712.png]]


#### Conversión directa Hexadecimal a binario (16) a (2)

De manera inversa, se podrá convertir directamente un hexadecimal a binario, reemplazando cada dígito hexadecimal por su correspondiente binario en grupos de 4 bits.
0
##### Entero

![[Pasted image 20260415002932.png|236]]

##### Fracciones

Si se desea convertir una fracción hexadecimal a binario en forma directa, esto se realizará de manera semejante a la conversión octal-binaria, sólo que los grupos se deberán armar de 4 dígitos binarios.
![[Pasted image 20260415003104.png|237]]







# Operaciones fundamentales 
## Operaciones fundamentales en binario

### Suma

^c0b2f9

Se opera de derecha a izquierda siguiendo las siguientes reglas
**0+0=0
0+1=1
1+0=1
1+1=10 (escribir 0 y llevar 1 al sig)**

![[Pasted image 20260419173028.png]]

### Resta 

La operación de resta en una compu- tadora se calcula mediante la suma del minuendo y el complemento a la base del sustraendo

![[Pasted image 20260419173207.png]]![[Pasted image 20260419173242.png]]

#### Método alternativo (manual): "¿cuánto le falta...?"

Si no se recuerdan las combinaciones de la resta directa, para cada par de dígitos se puede preguntar: *¿cuál es el dígito que sumado al sustraendo me da el minuendo?* o *¿cuánto le falta al sustraendo para llegar al minuendo?*.

- Si el minuendo es menor que el sustraendo en una columna, hay que "pedir 1" a la columna siguiente (equivalente a pedirle una unidad a la base, que **siempre** equivale a `10` en esa base) y sumarla al minuendo.
- Ese "pedido" debe reflejarse sumándolo al sustraendo de la columna donde se pidió, antes de continuar restando en esa columna.

Ejemplo: `11011(2) − 1110(2) = 01101(2)` (comprobación decimal: `27 − 14 = 13`).

Ambos mecanismos (resta directa por combinaciones, y resta manual por "pedido") son útiles para seguir manualmente la ejecución de un programa cuyos resultados no son los esperados, aunque en la computadora la resta se implementa siempre como una suma con el complemento del sustraendo (así se **reutiliza el mismo circuito sumador**).

## Operaciones fundamentales en octal y hexadecimal

### Suma octal 

![[Pasted image 20260419173520.png]]

### Suma Hexadecimal

![[Pasted image 20260419173558.png]]

### Técnica para sumar números grandes (más de 2 sumandos) en cualquier base

Cuando hay que sumar varios dígitos de una misma columna (por ejemplo, `7(8) + 7(8) + 7(8)`):

1. Sumar los números de la columna **en base decimal**: `7 + 7 + 7 = 21`.
2. Restar la base de origen (8) las veces que sea necesario hasta que el resto sea menor que esa base: `21 − 8 = 13`, `13 − 8 = 5`.
3. El **resto final** (menor que la base, en este caso `5`) es el dígito resultado de la suma en la base de origen.
4. La **cantidad de veces que se restó la base** (en este caso `2`) es el acarreo para la columna siguiente.

Si los números tienen más de una columna, se repite la técnica en cada una, sumando el acarreo a la columna siguiente.

> Ejemplo en hexadecimal: `F(16) + F(16) + F(16)` → en decimal `15+15+15 = 45` → `45 − 16 = 29` → `29 − 16 = 13` (se restó la base 2 veces) → resultado `D(16)` con acarreo `2` → total `2D(16)`.

# Complemento de un número

Dado que muchas computadoras digitales basan su operatoria aritmética en la suma, se usa el concepto de **complemento** para poder hacer restas mediante sumas y para representar números signados (negativos).

## Complemento a la base / a la raíz / auténtico (C2 en binario)

Para un número `N` de `n` dígitos, su complemento a la base `N'` cumple: `N + N' = Bⁿ`, de donde:

```
Cb N = Bⁿ − N
```

En el sistema binario, el complemento a la base se llama **complemento a 2 (C2)**, ya que la base es 2.

**Ejemplos en base 10 (para entender el concepto antes de aplicarlo en binario):**
```
C10 7   = 10¹ − 7     = 3
C10 548 = 10³ − 548   = 1000 − 548 = 452
C10 05  = 10² − 05    = 95
```

## Complemento restringido/a la base -1 (C1)

Para un número `N` de `n` dígitos, su complemento restringido `N'` cumple: `N + N' = Bⁿ − 1`, de donde:

```
Cb−1 N = (Bⁿ − 1) − N
```

En el sistema binario, el complemento a la base −1 se llama **complemento a 1 (C1)** y coincide con el complemento booleano (invertir los bits).

**Ejemplos en base 10:**
```
C10⁻¹ 7   = (10¹ − 1) − 7   = 9 − 7     = 2
C10⁻¹ 548 = (10³ − 1) − 548 = 999 − 548 = 451
C10⁻¹ 05  = (10² − 1) − 05  = 99 − 05   = 94
```

Invertir todos los bits

Si te dan un binario que no sea 4 bits hay que pasarloa 4 o a 8 

## Complemento autentico/a la base (C2)

Invertir todos los bits y sumar ( [[#^c0b2f9]] ) 1 al resultado

## Representación de binarios negativos mediante complemento

Para convertir un decimal negativo (p. ej. `−3`) a un binario con signo de `n` bits:

- El **bit de extrema izquierda** representa el signo: `1` = negativo.
- Los bits restantes representan el complemento (C1 o C2) del valor absoluto.

**Con complemento a 2 (C2)** — signo de 3 bits para `−3`:
```
Valor absoluto de 3 en 2 bits: 11
Complemento a 2 de 11: invertir (00) + 1 = 01
Resultado: 1 01   →   signo=1, magnitud complementada=01
```

**Con complemento a 1 (C1)** — signo de 3 bits para `−3`:
```
Valor absoluto de 3 en 2 bits: 11
Complemento a 1 de 11 (invertir): 00
Resultado: 1 00   →   signo=1, magnitud complementada=00
```

> Para **descomplementar** un número (hallar de nuevo su valor absoluto) se aplica el mismo método que para hallar el complemento (complemento del complemento).

La cantidad de bits `n` usados para representar un número binario con signo **siempre incluye el bit de signo**.

# Resumen de conversión entre sistemas numéricos (diapositivas)

- **De octal, binario o hexadecimal a decimal:** mediante el polinomio de potencias de la base (parte entera y fraccionaria).
- **De decimal a hexadecimal, octal o binario:**
  - Método de divisiones sucesivas por la base (parte entera).
  - Método de multiplicaciones sucesivas por la base (parte fraccionaria).
- **De hexadecimal u octal a binario** (y viceversa): pasaje directo entre bases.
- **De octal a hexadecimal:** se convierte primero de octal a decimal (o binario) y luego el valor obtenido a hexadecimal.
- **De hexadecimal a octal:** se convierte primero de hexadecimal a decimal (o binario) y luego el valor obtenido a octal.

# Unidades

## Unidades básicas de información

| Medida | Unidad | Equivalencia | Notación exponencial |
|---|---|---|---|
| bit | bit | 1 bit | — |
| Byte | B | 8 bits | — |
| Kilobyte | KB | 1024 bytes | 2¹⁰ |
| Megabyte | MB | 1024 KB | 2²⁰ |
| Gigabyte | GB | 1024 MB | 2³⁰ |
| Terabyte | TB | 1024 GB | 2⁴⁰ |
| Petabyte | PB | 1024 TB | 2⁵⁰ |
| Exabyte | EB | 1024 PB | 2⁶⁰ |
| Zettabyte | ZB | 1024 EB | 2⁷⁰ |
| Yottabyte | YB | 1024 ZB | 2⁸⁰ |
| Brontobyte | BB | 1024 YB | 2⁹⁰ |
| Geopbyte | GeB | 1024 BB | 2¹⁰⁰ |

## Unidades en las que trabaja la computadora

- **4 bits** → *nibble*
- **8 bits** → *byte*
- **16 bits** → *word*
- **32 bits** → *double word* (dWord)
- **64 bits** → *quadruple word* (qWord)

## Submúltiplos (para tiempos, p. ej. ciclos de reloj)

| Prefijo | Valor |
|---|---|
| Nano | 10⁻⁹ |
| Pico | 10⁻¹² |
| Femto | 10⁻¹⁵ |
| Atto | 10⁻¹⁸ |
| Zepto | 10⁻²¹ |
| Yocto | 10⁻²⁴ |

## Tipos de datos primitivos y su tamaño

| Tipo | Tamaño |
|---|---|
| Byte | 8 bits |
| Short | 16 bits |
| Int | 32 bits |
| Long | 64 bits |
| Float | 32 bits |
| Double | 64 bits |
| Boolean | 32 bits |
| Char | 8 bits por carácter |

# Aplicaciones prácticas del hexadecimal/octal (Capítulo 2 - Resumen)

## Byte de estado de un periférico (ejemplo: impresora)

Una impresora puede enviar un código hexadecimal que representa su estado actual, por ejemplo `D0(16)`, que corresponde al binario `11010000`. Cada bit tiene un significado (de mayor a menor peso, bit 7 a bit 0):

| Bit | Significado |
|---|---|
| 7 | Encendida |
| 6 | No ocupada |
| 5 | Sin papel |
| 4 | Seleccionada entre otras impresoras |
| 3 | Atasco de papel |
| 2 | Bajo nivel de tinta |
| 1 | Error de dispositivo |
| 0 | Tiempo excedido para enviar el byte |

Con `D0(16) = 11010000(2)`, los bits en `1` son el 7, 6 y 4 → la impresora está **encendida, no ocupada y seleccionada**, por lo que el software puede enviar el byte para imprimir.

## Direcciones de red: hexadecimal + decimal

Es común combinar sistemas numéricos en una configuración de red: la **dirección física del adaptador (MAC)** suele expresarse en **hexadecimal**, mientras que la **dirección IP** se muestra convertida a **decimal** por la propia computadora (por ejemplo, `200.115.237.194`). Si no fuera así, habría que convertir manualmente cada octeto hexadecimal de la IP a decimal.

> En definitiva: el complemento de un número binario permite operar restas mediante sumas de forma simple para los componentes electrónicos, y los sistemas octal/hexadecimal permiten "empaquetar" bits para que un informático pueda leer e interpretar más fácilmente el contenido binario de registros, bytes de estado, direcciones, etc.

