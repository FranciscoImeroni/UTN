---
aliases:
  - Unidad 1 AdC
---

### Fuentes
- [[Assets/ArquitecturaIntroduccion_Unidad_I.pdf|Diapositivas: Introducción a la Arquitectura de Computadores]] (Prof. Silvana Panizzo)
- [[Assets/Capitulo1-PatriciaQuiroga.pdf|Capítulo 1 - Patricia Quiroga: Evolución del procesamiento de datos]]

# Unidad 1 – Procesamiento de datos

## Conceptos clave

- Arquitectura de computadora / Organización de computadora
- Algoritmo, programa, lenguaje de programación
- Tipos de datos
- E/S (Entrada/Salida)
- Tipos de memorias
- Buses
- Partes de una computadora

## 1. Dato e información

- **Dato:** unidad mínima que compone la información. Un dato por sí mismo no es información.
- **Información:** conjunto de datos seleccionados y ordenados con el propósito de brindar información específica; es la consecuencia de **procesar** los datos para que tengan un significado en un contexto real que permita al usuario tomar decisiones.

```
Datos  →  Procesamiento  →  Información
```

> Ejemplo: "38 °C" es un dato aislado; en el contexto de un pronóstico del clima significa que hará calor, pero como temperatura corporal indica fiebre. El significado depende del contexto, no del dato en sí.

Los datos que procesa una computadora no tienen significado para ella, sino para el usuario que los interpreta en su propio contexto.

**Sistema de información (o sistema de informática):** conjunto coordinado de elementos, datos y procesos cuya interacción permite obtener información. En sistemas automatizados, la "unidad de procesamiento" es la computadora (hardware + software), o un conjunto de computadoras relacionadas.

Procesar datos implica realizar alguna operación sobre ellos: **cálculo, comparación, clasificación** (ordenamientos, intercalaciones), **transformación** (p. ej., de datos numéricos a gráficos), **transferencia, almacenamiento, consulta**. Los datos de entrada (*input*) se transforman en resultados (*output*).

Los datos de entrada son, internamente, una estructura binaria organizada según un convenio llamado **código**, que relaciona los dígitos binarios con los símbolos que el usuario utiliza (números, letras, caracteres especiales, etc.). Las salidas también están codificadas en binario y se **decodifican** para representarse en símbolos comprensibles para el usuario.

Un dato puede ser una cadena de caracteres (*string*), o bien puede estar organizado en estructuras mayores denominadas **archivos** (*files*).

## 2. ¿Qué es una computadora?

Es un **dispositivo electrónico** diseñado para aceptar datos de entrada y realizar operaciones sobre ellos (organizadas en una secuencia lógica y predeterminada por un algoritmo), para elaborar resultados que se puedan obtener como salidas.

**Funciones básicas:**
- Procesamiento
- Almacenamiento
- Transferencia de datos
- Control

Un **algoritmo computacional** es una secuencia finita de operaciones que permite resolver un problema computacional, representada con instrucciones que la computadora puede interpretar y ejecutar. Al conjunto de instrucciones que representa un algoritmo se lo denomina **programa**; dicho de otra manera, un programa es la representación de un algoritmo en un lenguaje de programación.

### Componentes de una computadora (Fig. 1.1)

![[Assets/u1_componentes_computadora.png]]

Los tres cuadros en gris constituyen la **unidad central de proceso (CPU** o *Central Processing Unit*).

- **Memoria principal:** almacena datos de entrada, todas las instrucciones del programa y los resultados.
- **Registros de la CPU:** almacenan el contexto de la instrucción que se está ejecutando (tanto la instrucción como los datos con los que va a operar).
- **Unidad de control:** interpreta y ejecuta instrucciones, y genera las señales de control para habilitar las operaciones.
- **Unidades de cálculo:** realizan operaciones aritméticas y lógicas sobre los datos.
- **Buses de interconexión:** transfieren datos, instrucciones y resultados.
- **Dispositivos de entrada:** permiten el ingreso de programas y datos a la memoria principal.
- **Dispositivos de entrada/salida:** por ejemplo, un disco que permite almacenar datos, instrucciones y resultados con la computadora apagada.
- **Dispositivos de salida:** permiten el egreso de resultados desde la memoria principal.

### Organización vs. Arquitectura

- **Organización de una computadora:** la "relación" entre los distintos componentes y su diseño y tecnología (sea en un nivel de detalle mayor o menor). Permite identificar los componentes desde el punto de vista de su estructura, cómo se relacionan entre sí y las cuestiones tecnológicas.
- **Arquitectura de computadoras:** incluye los aspectos relacionados con el **formato del conjunto de instrucciones** que el procesador pueda ejecutar, la **representación interna de los datos** y el estudio de los **módulos de hardware** que sostienen la dinámica del conjunto, desde la perspectiva del sistema informático. Es decir, a la descripción de los componentes (organización) se le agrega la descripción de la manera en que nos comunicamos con la computadora: qué instrucciones tiene definidas su CPU, con qué tipos de datos puede operar, cómo se accede a ellos y cómo se atienden eventos externos (**interrupciones**: eventos externos producidos por dispositivos de E/S).

> Ambos conceptos están muy relacionados: la organización (componentes y sus relaciones) es parte del diseño de la arquitectura.

### ¿Por qué estudiar Arquitectura de Computadores?

- Interesa el funcionamiento de cada parte y cómo se conectan entre sí.
- Interesa saber las características y cómo combinarlas para obtener resultados.
- ¿A qué hace referencia que una computadora sea de 32 o 64 bits?

Para:
- Diseñar programas de usuario.
- Optimizar programas.
- Diseñar programas de base: compiladores, intérpretes, lenguajes de programación, sistemas operativos y drivers.
- Evaluar el desempeño, para responder a necesidades de cómputo y almacenamiento teniendo en cuenta los costos.

## 3. Estratificación del software

La jerarquía más alta corresponde a los programas de uso particular de los usuarios, llamados **aplicaciones** (programadas en lenguajes de alto nivel); en el extremo opuesto están las señales que genera la unidad de control para gobernar los dispositivos físicos (por ejemplo, una orden de lectura a memoria).

![[Assets/u1_estratificacion_software.png]]

- Un usuario que solo envía e-mails se comunica con la interfaz gráfica del sistema operativo y no requiere conocimientos de ciencias de la computación.
- Un programador de aplicaciones requiere conocimientos formales en arquitectura de computadoras, sistemas operativos, algoritmos, lenguajes y estructuras de datos.
- Un programador de software de sistema necesita conocimientos profundos en arquitectura, lenguajes que comanden el hardware y sistemas operativos (por ejemplo, para programar un "supervisor" de un dispositivo físico).

Todos los programas se compilan o reciben algún proceso de traducción a **código de máquina**: el lenguaje que interpreta la CPU y que pertenece al nivel de arquitectura del set de instrucciones. Por esta "interpretación", la CPU genera señales sincronizadas en el tiempo que controlan el hardware implicado (p. ej., "orden de suma a una unidad de cálculo"); quien realiza finalmente la operación es el hardware.

**Arquitectura del set de instrucciones (ISA — Instruction Set Architecture):** determina el formato de las instrucciones, los tipos de datos que puede operar, las distintas formas de obtener datos de memoria (**modo de direccionamiento**) y la forma en que se atienden eventos externos.

Cada instrucción implica "algo que hacer", un "verbo", denominado técnicamente **código de operación** (grupo de bits que interpreta un diseño específico de CPU). La forma en que se implementan los códigos de operación se denomina **nivel de microarquitectura**, que determina cómo se ejecuta la instrucción. Dos CPU pueden compartir el mismo set de instrucciones pero tener distinta microarquitectura — es el caso de AMD, que ejecuta software de la industria 80x86 de Intel, manteniendo compatibilidad de software.

## 4. Evolución del procesamiento de datos

### Los comienzos

- El **ábaco** (~3.000 a.C.) es probablemente la primera máquina digital usada para cálculos aritméticos. Usado en Grecorromana, Egipto y China.
- Siglo XVII: **John Napier** introduce el concepto de **logaritmo**, simplificando la multiplicación. Surgen las máquinas de cálculo **analógicas** (o de medida).

### Primeras máquinas y su evolución

| Año | Autor | Aporte |
|---|---|---|
| 1642 | **Blaise Pascal** | Primera calculadora digital (suma y resta) mediante ruedas dentadas de 10 posiciones. |
| 1671 | **Gottfried W. von Leibniz** | Máquina que automatiza la multiplicación por sumas sucesivas; usaba el sistema binario. |
| 1823 | **Charles Babbage** | Empieza la primera computadora digital, con tarjetas perforadas (un "programa perforado"). |
| 1833 | **Charles Babbage** | Concibe la **máquina analítica** (calculadora digital universal); no pudo construirse por falta de tecnología. |
| S. XIX | **George Boole** | Desarrolla el **Álgebra de Boole**: relación entre conceptos lógicos (no números), base de la teoría de circuitos. |

### La máquina de tarjetas perforadas

- **Herman Hollerith** (oficina de censos de EE.UU.) inventó la técnica de procesar grandes volúmenes de datos mediante tarjetas perforadas (censos de EE.UU. 1890 y Gran Bretaña 1911).
- **IBM** perfeccionó la idea con la tarjeta perforada estándar de 80 columnas (leída por conmutadores eléctricos); en 1969 introdujo la tarjeta de 96 columnas para el Sistema/3.
- Entre 1930 y 1970 la tarjeta perforada reemplazó buena parte del procesamiento manual.

> Fig. 1.2 (esquema): `Programa + Datos → CPU + Memoria → Resultados → Impresora`. Un sistema operativo organizaba el procesamiento con una orden de lectura a la lectora de tarjetas; tarjetas de control marcaban el inicio/fin del lote de instrucciones y del lote de datos. Al finalizar la lectura, instrucciones y datos quedaban en memoria y recién entonces se ordenaba la ejecución; los resultados se imprimían en papel.

- La cinta de papel perforada fue otro medio primitivo de almacenamiento, usado en máquinas de sumar, contabilidad y cajas registradoras.

### La calculadora secuencial automática (IBM) y ENIAC

- 1944: la **calculadora secuencial automática de IBM**, a cargo de **Howard Aiken**, en Harvard (partes electromecánicas, controlada por cinta de papel perforada). Luego, Aiken diseñó la **MARK II** para la Marina de EE.UU.
- Durante la Segunda Guerra Mundial se desarrolló **ENIAC** (*Electronic Numerical Integrator And Calculator*): el cambio de programas se hacía recableando borneras manualmente.

### El programa almacenado

- 1945: **John von Neumann** logra una máquina de **programa almacenado**, de propósito general (no diseñada para una aplicación concreta), capaz de almacenar instrucciones y datos en una memoria, sustituyendo el conexionado fijo por un programa intercambiable.

**Los 3 principios de la máquina de Von Neumann** (aún vigentes):
1. Máquina electrónica digital que trabaja con información codificada en **binario**.
2. **Programa almacenado** en memoria.
3. Posibilidad de provocar una **ruptura de secuencia** de instrucciones en un programa (salto a una instrucción que no es la siguiente).

### Esquema básico de una computadora Von Neumann (Fig. 1.3)

![[Assets/u1_von_neumann.png]]

- **CPU:** contiene la **Unidad de Control (CU)** y la **Unidad Aritmético-Lógica (ALU** o *Arithmetic Logic Unit*).
- La **CU** emite las órdenes (p. ej., orden de lectura a memoria, orden de suma a la ALU) que llevan a cabo, en forma secuencial y sincronizada, las operaciones elementales para ejecutar instrucciones. También actúa como canalizador de datos, gestionando la transferencia de información entre módulos.
- Los módulos (CPU, Memoria principal, Unidades de E/S) se relacionan mediante **colectores de datos e instrucciones**, también llamados **buses**.

## 5. Clasificación de las computadoras

### Analógicas
- Se implementaron para estudiar un modelo análogo a una ecuación; el resultado se obtenía midiendo el valor de las salidas del modelo.
- Desventajas: falta de exactitud (por el carácter continuo de la magnitud) y el modelo construido servía solo para una única aplicación.
- Uso actual: procesos industriales (refinerías de petróleo), simuladores de vuelo, simuladores de redes eléctricas.
- Ventaja: procesan datos no discretos (temperatura, presión, altura) y permiten simular modelos a menor costo.

### Digitales
- Procesan "dígitos" binarios (ceros y unos) que representan datos codificados. Realizan cálculos precisos y su "modelo" es un programa intercambiable (no requiere cambiar partes físicas).
- Desventaja: para procesar variables continuas requerirían datos con diferencias infinitesimales, lo cual es inadecuado.

### Híbridas
- Combinan analógico (toma datos continuos: temperatura, presión) y digital (efectúa los cálculos). Se construyen para propósitos especiales — por ejemplo, el **GPS** de un auto.

## 6. Generaciones de computadoras digitales

| Generación | Período | Tecnología | Características principales |
|---|---|---|---|
| **1ª** | 1954–1959 | Válvulas de vacío | Ejecución secuencial: carga de programa (cargador) → ejecución instrucción por instrucción → impresión de resultados. Alto calor y tamaño. |
| **2ª** | 1959–1964 | Transistores, circuitos impresos | Posibilita simultaneidad cálculo/E-S mediante **procesamiento por lotes** (batch), usando cinta magnética como soporte intermedio entre tarjetas e impresora. |
| **3ª** | 1964–1971 | Circuitos integrados SSI/MSI | **Multiprogramación** (varios programas residen en memoria; mientras uno espera E/S, otro usa la CPU). Memoria dividida en zona de usuario y de conversión/carga. Carga continua (no por lotes) desde disco. Nace el **teleproceso**, el **tiempo compartido** (*time sharing*), sistemas **multiusuario/multitarea** y el concepto de **máquina virtual**. |
| **4ª** | 1971–1987 | LSI (*Large Scale Integration*) | CPU completa en un solo chip = **microprocesador**. Procesamiento en tiempo real, bancos de datos, cajeros automáticos, hojas de cálculo, procesadores de texto. |
| **5ª** | En desarrollo (desde 1980s) | VLSI, arquitecturas RISC, *pipelines*, superescalaridad, niveles de caché | Búsqueda de funciones "inteligentes" (IA): comprensión de lenguaje natural, reconocimiento de formas y de escritura. Ejecución de instrucciones en paralelo. Impulsada fuertemente por Japón (conferencia de Tokio, 1981). Se reemplazan lenguajes de alto nivel por otros orientados a símbolos y lógica (Prolog, Lisp). Objetivo: **Ingeniería del conocimiento**. |

> **Multiprogramación** (varios programas en memoria en estado de ejecución) ≠ **Multiprocesamiento** (varios programas ejecutándose simultáneamente en CPUs diferentes relacionadas entre sí).

### Procesamiento por lotes (2ª y 3ª generación)

- 2ª generación: `Tarjetas → Computadora auxiliar → Cinta → Computadora principal → Cinta → Computadora auxiliar → Impresora`.
- 3ª generación: `Tarjetas/Impresora ↔ CPU+memoria ↔ Conversión de soporte / Procesamiento de programas del usuario ↔ Disco magnético` (carga continua, con cola de espera y prioridades gestionadas por el sistema operativo).

## 7. Sistemas sincrónicos de propósito general

- **Sistemas de propósito específico:** la tarea es fija, definida por los circuitos de la unidad de control; la memoria solo soporta datos de entrada y resultados. Cambiar la tarea implica alterar el hardware.
- **Sistemas de propósito general (computadoras):** la tarea (aplicación) se puede cambiar porque está definida en software. La CPU recibe las instrucciones desde la memoria principal, donde se almacenaron junto con los datos de entrada.
  - Requieren programación: elaborar un algoritmo y representarlo en un lenguaje → **programa**.
  - Cada instrucción se transfiere de la memoria a la CPU; la **CU** organiza en el tiempo las operaciones elementales (**microoperaciones**) que permiten su ejecución.

## 8. Arquitectura de computadoras: primeros conceptos

- La tarea fundamental de una computadora digital es realizar **cálculos o comparaciones** entre datos u operandos.
- "Digital" implica que el valor de los datos es discreto y limitado, representado con combinaciones de 0 y 1 (máquinas binarias). Los bits constituyen el "alfabeto" de la computadora.
- **Hardware:** conjunto de dispositivos electrónicos y electromecánicos, la estructura física ("lo duro").
- **Software:** conjunto de programas para procesar datos, la parte lógica ("lo blando"). Sin software, el hardware no podría procesar datos o quedaría limitado a una tarea fija.
- **Firmware:** dispositivos físicos programados que fusionan hardware y software (p. ej., la programación de un electrodoméstico).

### Unidades de información

- **Bit** (*binary digit*): unidad mínima de información. Valores: 0/1 (también: off/on, falso/verdadero, no/sí). Se representa físicamente con niveles de tensión (1 lógico = tensión alta/*high*; 0 lógico = tensión baja/*low*).
- **Byte:** combinación de **8 bits**; es el "bloque de construcción" real de un dato.
- **Nibble:** medio byte, **4 bits**. Un byte está formado por dos nibbles: el de orden superior y el de orden inferior.
- **Palabra de CPU:** unidad de trabajo o procesamiento de la CPU, expresada en bits. Su longitud se relaciona con el tamaño de los registros de la CPU (puede coincidir o no con la "palabra de memoria"). Si el tamaño de palabra es menor que el del operando, la operación requiere más pasos (menor velocidad).

## 9. Arquitectura de una Unidad Central de Proceso (CPU)

La CPU es el módulo físico más importante; su capacidad de trabajo determina la capacidad de trabajo de la computadora.

### Tres bloques funcionales de una CPU Intel (Fig. 1.8)

![[Assets/u1_cpu_intel_bloques.png]]

- **Unidad de prebúsqueda:** obtiene instrucciones de la memoria principal a través del bus, y las dispone en una **cola de instrucciones**.
- **Unidad de decodificación:** cada instrucción espera en la cola y es interpretada por la unidad de predecodificación, que la traduce a **microcódigo** (lenguaje de menor nivel que el código de máquina, que representa las microoperaciones organizadas secuencialmente en el tiempo).
- **Unidad de ejecución:** ejecuta la instrucción con el soporte de la **ALU** y los **registros** de trabajo de la CPU; se comunica con los componentes necesarios aunque no pertenezcan a la CPU.

**ALU (Arithmetic Logic Unit):** se encarga de operar los datos recibidos de memoria y obtener el resultado. Operaciones aritméticas (suma, resta, desplazamiento) o lógicas (OR, AND, NOT).

**Registros (*registers*):** pequeña memoria local dentro de la CPU que guarda información transitoria del procesamiento en curso (operandos, direcciones de memoria, etc.). Su nombre y cantidad varían según el procesador.

**Memoria Principal (PM o Principal Memory):** tecnología de semiconductores; también llamada memoria central, interna o de trabajo. La CPU la usa para obtener instrucciones y datos, y almacenar resultados.

## 10. Lógica digital y componentes electrónicos

El **nivel de lógica digital** permite describir un componente con más detalle que el nivel de bloque funcional.

### Tres niveles de detalle (Fig. 1.9)

![[Assets/u1_tres_niveles_detalle.png]]

1. **Nivel de arquitectura:** la ALU realiza la suma (abstracción, caja negra).
2. **Nivel de lógica digital:** el circuito lógico sumador de bits (compuertas OR y AND) que produce el *bit suma* y el *bit de acarreo*.
3. **Nivel de operación aritmética:** el cálculo concreto, por ejemplo `1 + 0 = 1`.

- **Chip:** circuito electrónico diseñado sobre una estructura de **silicio** (material de la arena). Sobre la base de silicio se implementan cadenas de interruptores unidas por pistas/conductores; en ~0,6 cm² se incluyen millones de componentes.
- **Circuito integrado (IC):** soporte físico que incluye conjuntos de compuertas lógicas.
- Evolución: componentes discretos (válvulas, resistencias, condensadores) → **transistores** → miniaturización creciente.
- Una **compuerta lógica** se arma con transistores conectados según la tabla de verdad (tabla booleana) de la compuerta.

### Clasificación de los IC según su complejidad

| Sigla | Nombre | Compuertas |
|---|---|---|
| **SSI** | Small Scale Integration | hasta 10 |
| **MSI** | Medium Scale Integration | menos de 100 |
| **LSI** | Large Scale Integration | hasta 1.000 |
| **VLSI** | Very Large Scale Integration | más de 1.000 |

### Clasificación de la arquitectura del set de instrucciones

- **CISC** (*Complex Instruction Set Computer*): muchas instrucciones distintas y de alta complejidad; permite programas más cortos.
- **RISC** (*Reduced Instruction Set Computer*): pocas instrucciones simples y rápidas (parte de la premisa de que el 80% de un programa usa instrucciones simples); requiere más instrucciones, programas más largos, pero instrucciones más rápidas.
- **EPIC** (*Explicitly Parallel Instruction Computer*): combina ambas filosofías ejecutando múltiples instrucciones en paralelo.

- **Palabra de procesador:** unidad de trabajo/cantidad de información que puede procesar en un paso (en bits). Longitudes comunes actuales: **32 y 64 bits**.
- **Capacidad de dirección:** determinada por la cantidad de líneas que transfieren los bits de una dirección física (p. ej., con 32 bits hay 2³² direcciones posibles). Determina cuánta memoria principal puede direccionar la CPU.
- **Número de instrucciones:** cuántas operaciones distintas puede ejecutar una CPU (sumar, restar, transferir, incrementar, etc.).

### Señal de reloj (Fig. 1.10)

![[Assets/u1_senal_reloj.png]]

- La **velocidad de ejecución** de un procesador depende, en primer término, de la velocidad del reloj del sistema.
- **Señal de reloj (clock):** señal que oscila a intervalos regulares entre 0 y 1 (con un tiempo de transición no instantáneo entre ambos valores).
- **Ciclo:** desde el inicio de la transición de 0 a 1, pasando por el tiempo en 1, la transición de 1 a 0, el tiempo en 0, hasta el comienzo de la siguiente transición de 0 a 1. El tiempo que tarda en completarse un ciclo es el **período de reloj**.
- La señal se genera con un **circuito oscilador** que aplica tensión a un cristal (p. ej., cuarzo), el cual vibra a una frecuencia regular.
- **Frecuencia:** cantidad de ciclos por segundo, expresada en **Hz** (con múltiplos kilo, mega, giga, etc.), según `T = 1/f`.
  - Ejemplo: si `f = 1 GHz = 1.000.000.000 ciclos/seg = 10⁹ ciclos` → `T = 1/10⁹ = 10⁻⁹ seg = 1 nanosegundo`.
- El procesador y demás componentes operan con una **fuente de alimentación**, que convierte corriente alterna de alto voltaje (p. ej., 220 V) en corriente continua de bajo voltaje (p. ej., +5 V, -5 V) y estabiliza la tensión. Su capacidad se mide en **watts**.

### Caso de ejemplo: la placa madre (motherboard)

- Placa de circuito impreso que interconecta los componentes básicos de un sistema con microprocesador, mediante líneas o **buses**, clasificados en:
  - **Buses de control**
  - **Buses de datos**
  - **Buses de direcciones**
- Componentes fundamentales: zócalo de microprocesador (*socket*), microprocesador (CPU), reloj del sistema, slot de fuente de alimentación, slots de memoria, slots para buses de expansión (periféricos).
- **Periféricos:** dispositivos que permiten la comunicación del microprocesador y la memoria con el entorno. Se conectan mediante **interfaces** (involucran a la CPU en la transferencia) o **DMA** (*Direct Memory Access* — transfieren datos entre memoria y periférico sin intervención de la CPU).
  - **Periféricos de entrada, de salida,** y **de E/S** (memorias externas, p. ej. discos).
  - Aspectos clave: su conexión a la computadora y los programas que definen su funcionamiento (**drivers**).

**Controlador del periférico** (Fig. 1.11): gestiona la comunicación entre uno o varios periféricos y un bus. Está formado por:
- Un **buffer interno** (una RAM) que almacena la información que viaja desde/hacia el soporte.
- Una **lógica de control** que interpreta comandos y genera señales de control para el periférico.

`CPU + memoria ↔ Controlador (buffer) ↔ Periférico`

Funciones del controlador:
- Aislar el hardware del software de transferencia (estándar de industria).
- Compatibilizar la velocidad de transferencia entre periféricos y CPU.
- Convertir el formato de datos entre CPU y periféricos (y viceversa).
- Amplificar señales entre CPU y periféricos.

Un sistema típico incluye: adaptador de video, controlador de teclado, puertos, controladores de disco rígido y otros medios de almacenamiento masivo.

**Puerto (*port*):** componente de la comunicación que permite que la CPU se enlace con un periférico; desde el software, es una dirección del espacio de direccionamiento de memoria principal que actúa como memoria temporal para datos que se envían/reciben desde un periférico.
- **Puerto paralelo:** envía una agrupación de bits en paralelo (todos al mismo tiempo); bidireccional.
- **Puerto serie:** envía/recibe bits en serie (uno tras otro); bidireccional.

## 11. El Sistema Operativo. La dinámica del sistema

- Analogía: el hardware es como las piezas de un juego de ajedrez — sin conocer las reglas, son objetos de poca utilidad. El **sistema operativo** son las reglas que coordinan el trabajo de las piezas físicas.
- Cuanto mayor sea el conocimiento del software y la arquitectura por parte de un programador, mejor el aprovechamiento de la herramienta de trabajo.
- Las actividades críticas del sistema operativo forman el **núcleo** (*kernel* en Linux) y permanecen en memoria principal la mayor parte del tiempo para responder con rapidez. El resto de los programas de servicio reside en memoria masiva (p. ej., disco rígido) y se invocan por comandos del usuario o llamadas al sistema.
- El software de base (sistema operativo + utilitarios) y las aplicaciones se ejecutan alternadamente en la CPU, haciendo efectivo el procesamiento de datos.

## 12. Modelo de Von Neumann (resumen — diapositivas)

**3 componentes principales:**
- **CPU:** Unidad de Control + ALU + Registros.
- **Memoria principal:** almacena programas y datos temporalmente.
- **Sistema de Entrada/Salida.**

## Resumen del capítulo

- El capítulo distingue **organización** (componentes y sus relaciones) de **arquitectura** (formato de instrucciones, representación de datos, módulos de hardware).
- La función más importante de una computadora es **procesar datos**; sus funciones básicas se identifican con sus componentes: procesamiento (CPU), transferencia (buses) y almacenamiento (memoria principal a corto plazo, memorias secundarias a largo plazo).
- La **unidad de control** controla el funcionamiento del hardware ejecutando instrucciones en lenguaje de máquina.
- Se establece la clásica división en **generaciones de computadoras**, mostrando la incorporación de hardware/software según la evolución tecnológica de los últimos ~60 años.

## Actividades sugeridas (diapositivas)

- Leer el capítulo I del libro *Arquitectura de Computadoras* de Patricia Quiroga.
- Responder las preguntas del Trabajo Práctico 1.
- Video: [Historia de la computadora](https://www.youtube.com/watch?v=a8Q2xpI7hbs)
- Video: [Modelo de Von Neumann](https://www.youtube.com/watch?v=6f2Mxa9KIRA)
