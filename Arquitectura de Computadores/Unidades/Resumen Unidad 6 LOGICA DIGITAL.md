### [[Assets/Logica_Digital_Unidad_VI-PrimeraParte.pdf|Diapositivas Primera Parte]] · [[Assets/Logica_Digital_Unidad_VI-SegundaParte.pdf|Diapositivas Segunda Parte]]

### [[Assets/Capitulo6-PatriciaQuiroga.pdf|Capítulo 6 - Lógica Digital]]

# Circuitos combinacionales

Los circuitos combinacionales generan una salida basada únicamente en el valor de sus entradas actuales. Por el contrario, los circuitos secuenciales incorporan elementos de memoria, por lo que su salida depende de las entradas actuales y del estado previo del sistema

Cada compuerta es un circuito lógico combinacional en sí. De esta manera se
puede “armar” el esquema del comportamiento total del circuito sin
necesidad de conocer los elementos electrónicos que lo constituyen.

Los circuitos lógicos se clasifican en dos:

## Circuitos combinacionales

### Circuito Semi-Sumador:

![[Pasted image 20260518222400.png]]


![[Pasted image 20260518222550.png]]

Para sumar dos cantidades de n bits es necesario disponer de n bloques funcionales como el obtenido. Si bien permiten sumar bit a bit, al no tener en cuenta el acarreo no implementan correctamente la suma. Por este motivo este bloque se denomina semisumador o half adder.

![[Pasted image 20260518222703.png]]



De menor a mayor nivel de abstracción encontramos en primer lugar la descripción del circuito electrónico, en segundo lugar los diagramas de lógica, luego la representación algebraica y por último el nivel algorítmico de diseño de componentes.

Así como se utilizan lenguajes de programación como FORTRAN, Pascal y C para describir programas de computadora de naturaleza secuencial.

En el campo del diseño digital, los diseñadores sintieron la necesidad de un lenguaje estándar para describir los circuitos digitales. Por lo tanto, surgieron los lenguajes de descripción de hardware (HDL).

Los HDL permitieron a los diseñadores modelar la concurrencia de procesos encontrados en elementos de hardware.

Los lenguajes de descripción de hardware como Verilog HDL y VHDL son los mas populares. Verilog HDL se originó en 1983 en Gateway Design Automation. Más tarde, VHDL se desarrolló bajo contrato de DARPA. Los simuladores Verilog y VHDL para simular grandes circuitos digitales ganaron rápidamente la aceptación de los diseñadores.

VHDL se estandarizó con los estandares 13641995,1364-2001, 1364-2005.
http://ieeexplore.ieeee.org/xls/standardstoc.jsp?isnumber=33945

### Circuito generador de paridad

Un circuito generador de paridad permite la detección de error en la transmisión de información
entre un emisor y un receptor de información binaria cuyo coeficiente de probabilidad de error
sea menor o igual a un bit.

![[Pasted image 20260518222924.png]]

Es un sistema digital que añade un bit adicional (llamado _bit de paridad_) a un grupo de datos binarios antes de transmitirlos

#### ¿Cómo funciona?
1. **El emisor y la regla:** Antes de enviar los datos, el emisor y el receptor acuerdan qué tipo de paridad utilizarán (par o impar).

	- **Paridad Par**: El circuito cuenta la cantidad de unos (1) en los datos originales. Si la cantidad es impar, el generador añade un 1 al final. Si ya es par, añade un 0. El objetivo final es que la cantidad total de unos sea un número par.

	- **Paridad Impar**: El circuito añade un bit para que la cantidad total de unos sume un número impar. 

1. **La transmisión:** Los datos originales más el bit de paridad viajan juntos por el canal de comunicación.

2. **El receptor:** Al recibir el mensaje, el circuito en el otro extremo vuelve a contar los bits. Si el conteo no coincide con la regla acordada, el sistema detecta que ocurrió un error en la transmisión.


### Circuito comparador de magnitud

Es común que se necesite comparar dos números binarios de n bits para saber si éstos son
iguales, o si uno es mayor o menor que otro. Podemos simplificar el problema analizando la
comparación de dos bits.

![[Pasted image 20260518223104.png]]



### Circuito codificador

Resumen: Como funciona un teclado, muchas entradas que solo se activa una a la vez, el circuito en si no importa ya que es gigantesco así que pasa por la caja negra, y devuelve el resultado en BCD puro (pasa un input de teclado a binario, en el del ejemplo es un teclado numérico)

Las señales binarias provenientes de un teclado numérico pueden transformarse en salidas codificadas, por ejemplo, en BCD. Consideremos teclas numéricas sin tener en cuenta las teclas de operación ( +, -, *, etc.) comunes en todo teclado.

Uno de los códigos más usados en la representación de números es el BCD 8421 o BCD puro.

La siguiente tabla de verdad muestra 10 combinaciones de entrada en decimal y la salida en BCD.

![[Pasted image 20260518223225.png]]

Estas funciones **no se pueden minimizar por Karnaugh** porque cada una está definida en términos de más de 6 variables (una por cada tecla); sin embargo, se pueden representar algebraicamente en forma directa, como un simple OR de las teclas que activan cada salida (Cap. 6):

```
F0 = T1 + T3 + T5 + T7 + T9
F1 = T2 + T3 + T6 + T7
F2 = T4 + T5 + T6 + T7
F3 = T8 + T9
```

> Ejercicio del libro: si no se presiona ninguna tecla, la salida también es `0000` (igual que al presionar la tecla "0"). Para diferenciar ambos casos hay que agregar una entrada adicional (por ejemplo, una señal de "tecla presionada" o *strobe*).

### Circuito decodificador

Opera en forma inversa al codificador en el siguiente sentido: la información que ingresa en
el circuito implica combinaciones binarias pertenecientes a alguna convención de
representación de caracteres (numéricos o alfanuméricos) y las convierte a señales binarias
para representar en algún dispositivo de salida de información


Dice codificador pero es decodificador
![[Pasted image 20260518223748.png]]
Entra BCD puro y lo decodifica a mas salidas

Display de 7 Segmentos
Las X son por que como es un display que muestra del 0 al 9 no nos interesa del 10 al 15
![[Pasted image 20260518223833.png]]

El decodificador BCD→7 segmentos recibe un dígito BCD (A,B,C,D) y activa las salidas (a,b,c,d,e,f,g) que "dibujan" el número en el display; cada segmento se enciende con un LED que se activa con un "1". Las combinaciones `1010` a `1111` no se usan (son las "X" de la tabla, ya que el BCD solo llega hasta 9).

#### Aplicaciones de los decodificadores (Cap. 6)

1. En **chips de memoria**: posibilitan el acceso *random*, convirtiendo la dirección de una celda en su fila y columna.
2. En **bancos de memoria**: permiten identificar el banco correspondiente.
3. Permiten la **selección de dispositivos de E/S** de un microprocesador.
4. Facilitan la **decodificación de instrucciones** en la CPU para activar señales de control.
5. Decodificador BCD-7 segmentos (visto arriba) y codificadores de teclado.

### Circuito decodificador n.2n

Su función es simple: **le das un número en binario por sus entradas, y el circuito activa la salida que tiene ese mismo número de índice.**

Existe otro tipo de decodificadores que permiten señalar en una de sus salidas qué
combinación binaria se dio en la entrada. Los decodificadores de este tipo se denominan
“nX2n ” (se lee “n x 2 a la n” o “n a 2 a la n”) y contemplan una salida activa para cada
posible combinación de entradas.
Diagrama de bloque sin entrada de habilitación

![[Pasted image 20260518223950.png]]
Diagrama de bloque del decodificador n · 2^n = 2 · 2^2 = 2 · 4 sin entrada de habilitación

![[Pasted image 20260518224104.png]]

![[Pasted image 20260518224138.png]]




## RESUMEN COD y DEC
Un codificador y un decodificador son circuitos que hacen trabajos opuestos para conectar el mundo humano con el de la computadora. El codificador recibe muchas señales independientes (como las teclas de un teclado) y las comprime en un código binario más chico, mientras que el decodificador hace exactamente lo contrario: agarra ese código binario compacto de pocas líneas y lo expande para activar una salida específica entre muchas opciones, como cuando elegís una celda de memoria o encendés los leds de una pantalla.

Cuando hablamos de la relación de "n a dos elevado a la n", nos referimos a la capacidad del decodificador: si tiene dos cables de entrada, puede controlar cuatro salidas posibles; si tiene tres entradas, controla ocho salidas, y así sucesivamente. Para lograr esto por dentro, el decodificador usa minitérminos, que son combinaciones lógicas específicas con compuertas multiplicadoras para asegurarse de que cada código binario de entrada encienda una sola salida a la vez. Por último, la función de habilitación o "enable" actúa como un interruptor general de corriente para todo el chip; si este pin no está activado, el circuito ignora por completo las entradas y apaga todas las salidas, lo que permite apagar o encender bloques enteros de componentes dentro de la computadora según haga falta.



### Circuitos Multiplexor y demultiplexor

#### Un demultiplexor
es un circuito cuya función digital es “encaminar” la información que viene en una sola línea de entrada, en 1 de 2n. Se puede pensar en un decodificador en el que la entrada de habilitación es la portadora del bit que se ha de “encaminar” y la selección de la línea de salida (habilitada por líneas de selección) determina qué salida es la encargada de transmitirlo.

Por este motivo se lo conoce como decodificador/demultiplexor. Algunas de las aplicaciones de un demultiplexor son, por ejemplo, distribuir una entrada de datos en serie a una salida en paralelo o transferir bits de una línea de bus a un registro determinado; por eso también se lo denomina distribuidor.

![[Pasted image 20260518224251.png]]


#### Un multiplexor 
es un circuito combinacional cuya función digital es “encaminar” las señales binarias de 1 de 2n
líneas posibles de entrada en una única línea de salida. La línea de entrada de dato se “elige” a partir de los valores que puedan tomar las n líneas de selección.

![[Pasted image 20260518224402.png]]

##### Aplicaciones de los multiplexores

1. Un MUX se puede implementar con cierto número de compuertas lógicas, porque básicamente es un decodificador con sus salidas asociadas a una única compuerta.
2. Los MUX de pocas entradas se utilizan en los dispositivos de lógica programables descriptos más abajo.
3. El MUX se utiliza para la conversión de bits transferidos en paralelo a serie, esto es, de a uno por vez, utilizando una sola línea.
4. La multiplexación de bits de dirección en memorias se emplea, por ejemplo, cuando un bloque de caché supera la capacidad del bus, si el bloque es de 128 bits y el bus de 64; entonces, primero se transfiere un grupo de 64 bits y luego el otro.
5. Se puede armar un módulo desplazador que permite mover bits a derecha e izquierda en registros de desplazamiento bidireccionales, bajo microoperaciones de control, por ejemplo, las que genera la unidad de control cuando se ejecuta una instrucción de desplazamiento (ver sección "Registros con facilidad de desplazamiento").

Resumen
![[Pasted image 20260518224540.png]]


##### Ejemplo
Ejemplo de una unidad aritmético-lógica (ALU) para 4 operaciones de tipo lógico utilizando un multiplexor. Supongamos las operaciones NOT (negación o complemento), AND (conjunción o producto lógico), OR (disyunción inclusiva o suma lógica), XOR (disyunción excluyente o suma exclusiva), que identificamos con los números 00, 01, 10, 11, respectivamente. Las señales de control indican cuál de las "instrucciones" pasan por la línea de resultado, al operar los bits Op1 y Op2. Para este ejemplo, A = 0 y B = 1, entonces, el resultado R es el producto lógico entre ambos operadores: Op1 AND Op2.

![[Pasted image 20260518224653.png]]


###### Bus asociado a un multiplexor-demultiplexor

Un bus es un conjunto de conductores que permiten relacionar registros que actúan de emisores o de receptores de bits. En este caso se indica que los registros están asociados “a un bus común o bus único”.

Cada multiplexor habilita el paso de una señal sobre el bus comandado por la dirección en sus entradas de selección. Así: A = 1 y B = 1 indican que D3 es la línea de entrada seleccionada para salir por F.

![[Pasted image 20260518224800.png]]


###### Circuitos “programables” para múltiples funciones

![[Pasted image 20260518224852.png]]

### RESUMEN
Un multiplexor y un demultiplexor funcionan como los selectores de caminos de una computadora. El multiplexor toma datos que vienen de muchos canales distintos y elige solo uno para enviarlo por una única salida común, lo que permite que la unidad de control decida qué operación de la ALU mostrar o qué registro de memoria puede usar el bus de datos principal. Por el contrario, el demultiplexor recibe un único flujo de datos y lo encamina hacia una salida específica entre muchas opciones posibles. Cuando estos componentes se combinan con matrices de compuertas unidas por enlaces electrónicos fusibles, se crean los circuitos programables, donde es posible definir o cambiar las funciones lógicas del chip simplemente destruyendo o conservando las conexiones internas de forma eléctrica.

# Memoria sólo lectura

El arreglo de compuertas AND-OR, programado de esta manera, es un ejemplo simple de
memoria ROM (Read Only Memory). Desde el punto de vista de los circuitos lógicos, forma parte de los dispositivos de lógica programable y se caracterizan por tener conexiones fijas en el arreglo de compuertas AND y conexiones programables en el arreglo de compuertas OR.

Una vez establecida la combinación binaria de las salidas, éstas se mantienen inalterables.

## Distribución de líneas en una memoria de sólo lectura
• La distribución de una memoria forma una matriz de M x N bits (M direcciones de N bits cada una).
• El bus de direcciones tiene p líneas, tal que 2p = M.
• El bus de datos tiene N líneas para transferir los N bits leídos.
• El bus de control tiene una línea que habilita el chip y normalmente se denomina CS (Chip Select).
• En cualquiera de los ROM, la red AND es inalterable y la red OR es programable.

Diagramade bloque de cualquiera de las memorias de sólo lectura.
![[Pasted image 20260518225048.png]]


## Diagrama de lógica de una ROM sin programar

Una memoria ROM no es más que otra forma derepresentar los minitérminos para cada función de p variables; así, en el diagrama siguiente se representan las ocho combinaciones posibles de tres variables (a, b y c) como enlaces fijos en una red AND que, acoplada a una red OR, podrá
“programarse” para cada una de las dos funciones de salida F0 y F1

![[Pasted image 20260518225229.png]]



## Dispositivos tipo PAL

En una nueva categoría de dispositivos de lógica programable, se encuentran los denominados PAL (Programable Array Logic), más flexibles y rentables que las típicas ROM. Su función es similar a la de la ROM, pero en este caso se invierten los papeles de las redes ANDOR. La red AND es programable y la OR es fija. El diagrama para el ejemplo anterior puede ser:

![[Pasted image 20260518225323.png]]


## Dispositivos tipo PLA o F-PLA (field-PLA)

Los dispositivos tipo F-PLA (field = campo) constituyen otra categoría de los dispositivos de lógica programable, y tienen aún mayor flexibilidad, dado que ambas redes son programables.
Diagrama de lógica para un PLA no programado, esta vez de tres entradas y tres salidas: F0, F1 y F2.

![[Pasted image 20260518225412.png]]

# Circuitos secuenciales

Un circuito es **secuencial** cuando sus salidas dependen tanto del valor de las entradas **como del estado anterior** del circuito. Por eso su representación debe tener en cuenta que las funciones de salida se realimentan como entradas: se dice que el circuito tiene uno o más **lazos de realimentación**.

- **Asincrónicos:** los cambios se producen únicamente por activación de alguna entrada, sin necesidad de una señal de reloj.
- **Sincrónicos:** los cambios se producen cuando se establecen las entradas **y además** se genera una transición de la señal de reloj (`Ck`).

## Biestables o Flip-Flops

Un **biestable** es una celda binaria capaz de almacenar 1 bit. Tiene dos salidas: una para el valor almacenado (`Q`) y otra para su complemento (`Q̄`). Se llama "biestable" porque tiene solo 2 estados posibles de funcionamiento, y permanece en cualquiera de ellos mientras las entradas no fuercen un cambio.

Para que un circuito sea un **elemento de memoria** debe cumplir 2 condiciones:
1. **Retener** un bit, incluso después de desactivar las entradas.
2. Poder ser **puesto a 0 o a 1** ("set"/"reset"), independientemente del valor almacenado antes.

> Una simple compuerta OR realimentada (`F' = F + S`) solo cumple la condición 1 (puede memorizar un 1 indefinidamente), pero no puede volver a 0. Agregando una compuerta AND con una línea de "borrado" (**R**, *reset*) y una entrada de "puesta a 1" (**S**, *set*), se logra un circuito que cumple ambas condiciones: el **biestable R-S**.

### Biestable R-S asincrónico

| R | S | Qt (estado anterior) | Qt+1 (estado nuevo) |
|---|---|---|---|
| 0 | 0 | 0 | 0 (memoria: recuerda) |
| 0 | 0 | 1 | 1 (memoria: recuerda) |
| 0 | 1 | X | 1 (Set) |
| 1 | 0 | X | 0 (Reset) |
| 1 | 1 | X | indeterminado |

Se implementa típicamente con **2 compuertas NOR** cruzadas (la salida de cada una realimenta una entrada de la otra).

### Biestable R-S sincrónico (temporizado)

Igual al anterior, pero las entradas solo modifican el estado cuando hay un pulso de reloj (`Ck = 1`). La señal `Ck` oscila entre 0 y 1 a intervalos regulares; si varios biestables comparten la misma señal de sincronismo, todos cambian **en el mismo instante**.

### Biestable J-K sincrónico

Se comporta como el R-S, pero **resuelve el caso indeterminado**: cuando `J=K=1`, la salida se **complementa**.

| J | K | Qt | Qt+1 |
|---|---|---|---|
| 0 | 0 | Q | Q (recuerda) |
| 0 | 1 | X | 0 (Reset) |
| 1 | 0 | X | 1 (Set) |
| 1 | 1 | Q | Q̄ (complementa) |

### Biestable T sincrónico

Se deriva del J-K uniendo las entradas J y K (`J=K=T`):

| T | Qt | Qt+1 |
|---|---|---|
| 0 | Q | Q (recuerda) |
| 1 | Q | Q̄ (complementa) |

Al igualar J-K en un biestable T, este **solo recuerda o complementa** su valor — es la base de los **contadores**.

### Biestable D sincrónico

La "D" es de *Delay* (demora el pasaje del dato). Se deriva del J-K uniendo J con K **negada** (`J=D`, `K=D̄`):

| D | Qt | Qt+1 |
|---|---|---|
| 0 | X | 0 |
| 1 | X | 1 |

El biestable D simplemente **copia** el valor de D a la salida en el próximo pulso de reloj (no "recuerda" ni "complementa": siempre adopta el valor de la entrada).

## Registros

Un **registro** es una función digital que retiene información binaria temporalmente; puede estar formado por uno o más biestables. La CPU tiene una pequeña memoria de acceso rápido formada por registros (PC, IR, AC, SR, etc.).

### Registro Paralelo-Paralelo

Transfiere la información que llega por líneas de entrada en paralelo a salidas en paralelo. Se arma con un biestable **D** por cada bit (por ejemplo, D2-D1-D0 → Q2-Q1-Q0), todos sincronizados por el mismo `Ck`.

### Registro contador progresivo (de 8 eventos, con biestables T)

Cuenta eventos en **orden ascendente**: con `n` biestables cuenta de `0` a `2ⁿ-1` y luego vuelve a 0. Se arma con biestables T encadenados con compuertas AND (la salida Q de cada biestable habilita el siguiente), y la entrada T0 siempre recibe un `1`.

| Ck | Q2 | Q1 | Q0 |
|---|---|---|---|
| — | 0 | 0 | 0 |
| 1 | 0 | 0 | 1 |
| 2 | 0 | 1 | 0 |
| ... | ... | ... | ... |
| 8 | 0 | 0 | 0 (vuelve al inicio) |

### Registro contador regresivo (de 8 eventos, con biestables T)

Análogo, pero cuenta en **orden descendente** (de `2ⁿ-1` a `0`): se usa la salida **Q̄** (en vez de Q) de cada biestable para alimentar el siguiente.

## Registros con facilidad de desplazamiento

El set de instrucciones de una CPU incluye instrucciones de desplazamiento, ya que la multiplicación se puede resolver con sumas sucesivas y desplazamientos (y la división, con restas sucesivas y desplazamientos) — igual que correr la coma al multiplicar/dividir por la base en decimal.

### Tipos de desplazamiento

- **Lógicos:** el bit que queda libre en el extremo se rellena con **0**.
- **Circulares (rotación):** los bits que salen por un extremo entran por el otro — no hay pérdida de bits, solo "circulan".
- **Aritméticos:** afectan números signados o no; son como los lógicos pero **mantienen el bit de signo**. Producen una multiplicación o división por una potencia de 2.
- **Concatenados:** afectan un conjunto de 2 o más elementos unidos: dos registros, un registro con el biestable de acarreo, un registro con el biestable de signo, o combinaciones.

> Un **desplazamiento** mueve los bits en forma lineal; una **rotación** lo hace de forma circular.

### Instrucciones de desplazamiento (Assembler 80x86)

| Instrucción | Significado |
|---|---|
| **SHL** (*Shift Left*) | Desplazamiento lógico a la izquierda |
| **SHR** (*Shift Right*) | Desplazamiento lógico a la derecha |
| **SAL** (*Shift Arithmetic Left*) | Desplazamiento aritmético a la izquierda (signados) |
| **SAR** (*Shift Arithmetic Right*) | Desplazamiento aritmético a la derecha (signados) |

- **SHL/SAL:** desplazan todos los bits a la izquierda (excepto el más significativo, que se inserta en el **bit de acarreo** del registro de banderas) y colocan `0` en el bit menos significativo. Equivale a **multiplicar por 2**.
- **SHR:** desplaza todos los bits a la derecha (excepto el menos significativo, que va al acarreo) y coloca `0` en el bit más significativo.
- **SAR:** igual que SHR, pero **no modifica el bit de signo** (se mantiene). Equivale a **dividir por 2** (con posible redondeo por defecto si hay resto).

> Los desplazamientos lógicos **no conservan el signo**; para valores signados se usan los aritméticos.

### Instrucciones de rotación

| Instrucción | Significado |
|---|---|
| **ROL** (*Rotate Left*) | Rotación a la izquierda |
| **ROR** (*Rotate Right*) | Rotación a la derecha |
| **RCL** (*Rotate Left through Carry*) | Rotación a la izquierda incluyendo el acarreo |
| **RCR** (*Rotate Right through Carry*) | Rotación a la derecha incluyendo el acarreo |

- **ROL:** rota a la izquierda; el bit más significativo pasa al **acarreo** y también al bit menos significativo (entra "por los dos lados").
- **ROR:** rota a la derecha; el bit menos significativo pasa al acarreo y también al bit más significativo.
- **RCL/RCR:** usan el **bit de acarreo** como una extensión del registro durante la rotación (el acarreo entra/sale como si fuera un bit más del registro).

#### Ejemplo resuelto: ROL

Valor inicial `10010010` con acarreo `0`:
```
Antes:    0 | 0 1 0 0 1 0 0 1 0
Después:  1 | 0 0 1 0 0 1 0 1 0    (el bit más significativo pasa al acarreo Y al bit 0)
```

#### Ejercicio resuelto: ROR de `01010101` con acarreo en `1`

```
Inicialmente:        1 | 0 1 0 1 0 1 0 1
Luego de la rotación: 1 | 1 0 1 0 1 0 1 0
```

#### Ejercicios resueltos: RCL y RCR

| Valor inicial | RCL | RCR |
|---|---|---|
| A) 110100100 | 101001001 | 011010010 |
| B) 011001110 | 110011100 | 001100111 |
| C) 101111000 | 011110001 | 010111100 |
| D) 000010100 | 000101000 | 000101000 |

> (En estos ejemplos el bit de más a la izquierda representa el acarreo de entrada/salida de la rotación.)

## Resumen: combinacionales vs. secuenciales

- **Circuitos combinacionales:** la salida depende **solo** de la combinación de entradas en ese instante (compuertas, sumadores, codificadores/decodificadores, mux/demux, ROM/PAL/PLA).
- **Circuitos secuenciales:** la salida depende de las entradas **y** del estado anterior (biestables, registros, contadores). Se los puede pensar como un circuito combinacional con realimentación de una o más salidas.
- En un sistema secuencial **sincrónico**, el instante en que se "habilitan" las entradas se produce durante un pulso de reloj.
- Los biestables son los circuitos secuenciales básicos, con los que se construyen matrices de memoria estática, registros de desplazamiento, contadores, banderas, punteros, etc.
- Los registros pueden conectarse a un bus mediante un **multiplexor** (registro→bus) y un **demultiplexor** (bus→registro), o mediante compuertas **buffer triestado** (en alta impedancia, el registro se "desconecta" del bus).

## Ejercicios propuestos (Capítulo 6)

1. Dado un circuito con entradas `a, b` y salida `f`: hallar su tabla de verdad, su FNC y su FND, y verificar que ambas formas son equivalentes para `a=0, b=1`.
2. Diseñar un circuito de 4 bits (a,b,c,d) cuya salida `f` sea verdadera si la entrada representa un número **impar**.
3. Diseñar un circuito que tome un BCD de 4 bits y produzca 4 salidas en código **Aiken (2421)**.
4. Diseñar un decodificador para las 4 luces de un semáforo (`00`=Roja, `01`=Amarilla, `10`=Verde, `11`=flecha de Giro), con tabla de verdad y una entrada de habilitación que apague todas las luces en `0`.
5. Implementar un flip-flop **T** a partir de un **J-K**, y su tabla de verdad.
6. Implementar un biestable **J-K** a partir de un **R-S** con compuertas NOR, y su tabla de verdad.
7. Diseñar un **contador sincrónico progresivo** de 4 bits que cuente de `0000` a `1111`, con su tabla de verdad.


