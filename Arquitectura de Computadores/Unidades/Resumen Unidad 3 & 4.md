# [Power Point Unidad 3/4](https://drive.google.com/file/d/11EG_JdS-dVqHZFfoHDlbLudj6xOmlbkW/view)

# Algunas Definiciones

**Código alfanumérico:** es una convención que determina una combinación binaria para
cada símbolo que se quiera representar.

**Formato**: de una entidad binaria es la estructura y la cantidad de bits de un determinado
tipo de dato para su tratamiento dentro de la computadora.

**Datos primitivos en C:**
![[Pasted image 20260501182657.png]]
El rango es el rango de representacion que tiene ese tipo, por ejemplo el char solo puede representa de -128 a 127

# Tipos de formato de datos

![[Pasted image 20260501182913.png]]
Los que tiene una X son los que vamos a ver

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

### Código de representación binaria de números enteros

#### Coma fija o punto fijo sin signo para representar números enteros positivos

![[Pasted image 20260501231512.png]]


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

## Suma

![[Pasted image 20260502014928.png]]


## Resta

(A−B→A+C2 de B),

![[Pasted image 20260502015017.png]]


# Endianness - Extremidad

Referencia al orden en el que se almacenan los bytes dentro de la memoria.
Si tenemos la siguiente cadena en hexadecimal 90AB12CD para almacenar en la memoria, según el orden:

![[Pasted image 20260502015139.png]]


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

![[Pasted image 20260502021010.png]]