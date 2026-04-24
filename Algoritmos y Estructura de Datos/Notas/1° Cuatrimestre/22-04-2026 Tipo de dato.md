Junio 10 examen 1 sobre tp1

## [[AED_25_Slides_03.pdf|PowerPoint]]

# Tipo de dato

### Cardinalidad de un tipo de dato

Ej bool tiene cardinal = 2. es v o f

## Simple

### bool 
Ej: 'true', 'false'
(finito)
Registro: Bit (1 o 0)
### char
Ej: 'a', '1'
(finito)
Registro: Byte (8 bits)
Guarda numeros en 8 bit para encontrarlas en ascii
### unigned
Ej: '0u', '2u'
(infinito)
Registro: Word (n bytes)
### int 
Ej: '1', '5', '1452'
(infinito)
Registro: Word (n bytes)
### double
Ej: '1.5', '1e5'
(finito)
Registro: Floating point 80-bits

## Compuesto
### string
Ej: "a", "Hola", "1452"
(finito)
Bloque contiguo en memoria principal

## Uso de Tipos Numericos

### Contable
cantidades. 
Ejemplos:
1. Stock 
2. Participantes de una clase  
3. Unidades monetarias (centavos)
#### Punto fijo
Siempre solo tiene dos dijitos despues del '.'. sirve para $. 
Ej: 2.99$

### No contable
Continua
Ejemplos:
1. Temperatura de una habitación  
2. Volumen de agua de un recipiente  
3. Probabilidad de ocurrencia
#### Double
Un numero con muchos decimales.
Ej: 3,14286