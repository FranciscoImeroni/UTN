### [[Capitulo1-PatriciaQuiroga.pdf|Capitulo 1 pdf]]

# Conceptos clave

Arquitectura de Computadora
Algoritmo
Tipo de datos
E/S (Entrada/Salida)
Programa
Lenguaje de prograacion
Tipos de Memorias
Buses
Partes de una computadora

Código de máquina: es el lenguaje que interpreta la CPU y pertenece al nivel de arquitectura del set de instrucciones.

**La arquitectura del set de instrucciones** (ISA o Instruction Set Architecture) determi- na el formato de las instrucciones, los tipos de datos que puede operar, las distintas formas de obtener datos de memoria, que se denominan “modo de direccionamiento”, y la forma en que se atienden eventos externos


# Que es una computadora?

dispositivo electrónico, diseñado para aceptar datos de entrada y realizar operaciones sobre ellos (organizadas en una secuencia lógica y predeterminada por un algoritmo), para elaborar resultados que se puedan obtener como salidas.

Un algoritmo computacional se determina por una secuencia de operaciones finita que permite resolver un problema computacional. Se representa con instrucciones que la computadora puede interpretar y ejecutar. Al conjunto de instrucciones que representa un algoritmo se lo denomina programa; expresado de otra manera, un programa es la representación de un algoritmo en un lenguaje de programación

Componentes:
![[Pasted image 20260406213834.png]]Los tres cuadros en gris que constituyen la unidad central de proceso (CPU o Central Processing Unit)

La “relación” entre los distintos componentes y su diseño y tecnología, sea en un nivel de detalle como el presentado o en uno menos abstracto, se define como **organización de una computadora**. 

El **concepto de arquitectura de computadoras** incluye los aspectos rela- cionados con el formato del conjunto de instrucciones que el procesador pueda ejecutar, la representación interna de los datos y el estudio de los módulos de hardware que sostienen la dinámica del conjunto, desde la perspectiva del sistema informático.


# Estratificación del software

la jerarquía más alta corresponde a los programas de uso particular de los usuarios, denominados **aplicaciones** (que se programan en lenguajes de alto nivel)

Podemos ver la relación entre las distintas jerar- quías de software y el hardware en el esquema siguiente:
![[Pasted image 20260406220053.png]]

Todos los programas se compilan o reciben algún proceso de traducción a código de máquina, que es el lenguaje que interpreta la CPU y pertenece al nivel de arquitectura del set de instrucciones. Por efecto de esta “interpretación”, la CPU genera señales sincronizadas en el tiempo que controlan el hardware implicado en la operación, por ejemplo, “orden de suma a una unidad de cálculo”. Por último, el que realiza la operación es el hardware.

La arquitectura del set de instrucciones (ISA o Instruction Set Architecture) determi- na el formato de las instrucciones, los tipos de datos que puede operar, las distintas formas de obtener datos de memoria, que se denominan “modo de direccionamiento”, y la forma en que se atienden eventos externos. 

Cada instrucción implica “algo que hacer”, un “verbo”, que en lenguaje técnico se denomi- na código de operación (grupo de bits que interpreta un diseño específico de CPU). La forma en que se implementan los códigos de operación se denomina nivel de microarquitectura. La microarquitectura determina la forma en que se ejecuta la instrucción. Dos CPU pueden compartir el mismo set de instrucciones pero estar diseñadas con distintas microarquitecturas, como es el caso de las CPU AMD, que ejecutan software de la industria 80X86 de Intel, lo que les permite “ejecutar las mismas instrucciones” y mantener así la compatibilidad del software


# Evolución del procesamiento de datos

la primera máquina típicamente digital que el hombre utilizó para resolver problemas aritméticos haya sido el ábaco. 