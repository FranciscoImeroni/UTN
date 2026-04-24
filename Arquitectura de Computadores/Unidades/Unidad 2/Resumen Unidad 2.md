### Saber:
 -  saber hacer todos los pasajes ya que hay que escribir el camino y luego se puede verificar no calculadora
 - 

[[Sistemas numericos-TP2.pdf|Ejercicios TP2]] (Ejercicios sobre pasajes SNP)

### [PowerPoint clase](https://drive.google.com/file/d/1H9x_xUrWkUgAkAeS_mhO8mdrlz2bZstt/view)

### [[Capitulo2-PatriciaQuiroga.pdf|Unidad 2 pdf]]


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

##### Entero

![[Pasted image 20260415002932.png|236]]

##### Fracciones

Si se desea convertir una fracción hexadecimal a binario en forma directa, esto se realizará de manera semejante a la conversión octal-binaria, sólo que los grupos se deberán armar de 4 dígitos binarios.
![[Pasted image 20260415003104.png|237]]







# Operaciones fundamentales 
## Operaciones fundamentales en binario

### Suma

![[Pasted image 20260419173028.png]]

### Resta 

La operación de resta en una compu- tadora se calcula mediante la suma del minuendo y el complemento a la base del sustraendo

![[Pasted image 20260419173207.png]]![[Pasted image 20260419173242.png]]

## Operaciones fundamentales en octal y hexadecimal

### Suma octal 

![[Pasted image 20260419173520.png]]

### Suma Hexadecimal

![[Pasted image 20260419173558.png]]


# Complemento de un número

## Complemento a la base, a la raíz o auténtico
