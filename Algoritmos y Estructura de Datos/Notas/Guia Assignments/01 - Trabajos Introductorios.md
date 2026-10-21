⬅️ [[00 - Índice y Requisitos Generales]]

# 01 — Trabajos Introductorios (Capítulos 3 a 6 del libro)

Esta nota cubre los cuatro primeros capítulos "de verdad" del libro (el 1 y 2 son prólogo y requisitos generales, ya resumidos en la nota índice). Son la base de **todo** lo que viene después: cómo se documenta un problema, cómo se arma el entorno, y el primer contacto con tipos de datos y `assert`.

---

## 3. Problemas y Soluciones

**Objetivos:** este capítulo no plantea un ejercicio de programación en sí, sino que fija **qué tiene que contener** todo `readme.md` que actúe como *front page* de una resolución: un análisis del problema y un diseño de la solución, escritos *antes* de tocar el código.

**Temas:**
- Etapa #1: Análisis del Problema.
  - Transcripción del problema.
  - Refinamiento del problema e hipótesis de trabajo.
  - Modelo IPO (Entradas, Proceso, Salidas).
- Etapa #2: Diseño de la Solución.
  - Léxico del Algoritmo.
  - Representación visual o textual del Algoritmo.

**Problema:** definir el proceso documental obligatorio que precede a la escritura de código en cualquier trabajo del curso. Concretamente: todo `readme.md` de front page tiene que dejar registro de dos etapas separadas — primero entender el problema (transcribirlo tal cual está, refinarlo si tiene ambigüedades, plantear una hipótesis de trabajo, y modelarlo como caja negra con el modelo IPO), y recién después diseñar el algoritmo que lo resuelve (con su léxico — nombres y tipos de las variables involucradas — y una representación del algoritmo, visual como un diagrama de flujo, o textual como pseudocódigo).

**Restricciones:**
- El programa (el código fuente) debe **seguir** la definición del algoritmo — nunca al revés. Es decir: primero se diseña el algoritmo en el `readme.md`, y el `.cpp` es una traducción fiel de ese diseño, no algo que se escribe "a mano" y después se documenta para justificar lo ya hecho.

**Productos:** no hay una carpeta de trabajo propia para este capítulo — es la metodología que se aplica en la práctica desde el capítulo 5 (Adición) en adelante. El "producto" es el hábito de escribir, en cada `DD-NombreTrabajo/readme.md`, el Análisis del Problema y el Diseño de la Solución antes del código.

### 📘 Teoría necesaria

#### Modelo IPO (Input – Process – Output)

Es una forma de mirar cualquier programa como una **caja negra** con tres partes: qué entra, qué hace internamente, y qué sale. No es específico de C++, es una herramienta de análisis previa a cualquier lenguaje.

| Parte | Qué es | Se documenta como |
|---|---|---|
| Entradas (Input) | Los datos que el programa necesita del exterior | Nombre + tipo de dato de cada entrada |
| Proceso | Lo que el programa hace con esos datos | Nombre descriptivo del proceso (no el código, solo la idea) |
| Salidas (Output) | Lo que el programa produce/muestra | Nombre + tipo de dato de cada salida |

**Ejemplo mínimo** (fuera de cualquier TP, solo para fijar el formato):

> Problema: dado el radio de un círculo, calcular su área.
>
> | IPO | Nombre | Tipo |
> |---|---|---|
> | Entrada | radio | `double` |
> | Proceso | CalcularAreaCirculo | — |
> | Salida | area | `double` |

En este capítulo el IPO todavía no se aplica a ningún TP concreto — se aplica por primera vez en el capítulo 5 (Adición), donde vas a ver el modelo lleno con datos reales.

#### Léxico del Algoritmo

Antes de escribir el algoritmo (en diagrama o en pseudocódigo), hay que fijar el **vocabulario**: qué nombre va a tener cada variable/constante que aparece, y de qué tipo es. Es el mismo espíritu que un glosario: evita que a mitad del diseño uses dos nombres distintos para la misma cosa, o que te olvides de declarar algo que usás.

**Ejemplo mínimo:**

> Léxico para "área de un círculo":
> - `radio: double` — el radio recibido como entrada.
> - `area: double` — el resultado a mostrar.
> - `Pi: double` — constante matemática necesaria para el cálculo.

En este trabajo se aplica en la práctica en el capítulo 5, donde el léxico de `Adición` va a listar los dos números de entrada, el resultado, y el nombre del proceso.

#### Representación visual o textual del Algoritmo

El algoritmo (el "cómo") se puede representar de dos formas, y el libro pide **al menos una** (salvo en Adición, capítulo 5, donde pide **las dos**):

- **Representación visual**: un diagrama de flujo (óvalos para inicio/fin, rectángulos para procesos, rombos para decisiones, flechas para el orden de ejecución).
- **Representación textual**: pseudocódigo, una descripción en pasos numerados en lenguaje natural estructurado, sin la sintaxis exacta de ningún lenguaje en particular.

**Ejemplo mínimo (textual, para "área de un círculo"):**
```
Inicio
  Leer radio
  area ← Pi * radio * radio
  Mostrar area
Fin
```

Este es el puente entre el Análisis (IPO) y el código: el `.cpp` final tiene que poder leerse como una traducción línea por línea de este pseudocódigo (o del diagrama).

### 🧭 Cómo encararlo

Este capítulo no se "resuelve" con código — se interioriza como **rutina** para encarar cualquier TP de ahora en más. La forma de aplicarlo:

1. Cuando te llega un enunciado nuevo, **no abras el editor todavía**. Primero copiá el enunciado tal cual (transcripción) en el `readme.md` del trabajo.
2. Reformulalo con tus palabras (refinamiento) y escribí explícitamente qué asumís que no está dicho (hipótesis de trabajo) — por ejemplo, si el enunciado no aclara si los números pueden ser negativos, documentá la decisión que tomaste.
3. Armá la tabla IPO: identificá cuántas entradas hay y de qué tipo, nombrá el proceso central, e identificá las salidas.
4. Recién ahí diseñás el algoritmo: elegís nombres para el léxico (consistentes con el IPO) y lo representás en pseudocódigo o diagrama.
5. El código C++ es el último paso, y su estructura tiene que calcar el pseudocódigo: si el pseudocódigo dice "Leer, Calcular, Mostrar", el `main()` debería poder leerse igual, en ese orden, sin lógica escondida que no esté en el diseño.
6. Chequeo de coherencia antes de entregar: releé el `readme.md` y el `.cpp` uno al lado del otro. Si hay algo en el código que no está en el algoritmo diseñado (o viceversa), hay que corregir uno de los dos — nunca dejarlos desincronizados.

### ✅ Solución completa

> [!example]- Plantilla de `readme.md` con Análisis y Diseño (click para expandir)
> ```markdown
> # DD-AreaCirculo
>
> # Autor
> * [Usuario GitHub] [Legajo] [Apellido, Nombre]
>
> # Enunciado
> **Transcripción:** Dado el radio de un círculo, calcular su área.
>
> **Refinamiento e hipótesis de trabajo:** Se asume que el radio es un número real positivo (no se
> contempla radio negativo ni cero, ya que no representan un círculo válido).
>
> **Modelo IPO:**
>
> | IPO | Nombre | Tipo |
> |---|---|---|
> | Entrada | radio | `double` |
> | Proceso | CalcularAreaCirculo | — |
> | Salida | area | `double` |
>
> # Diseño de la solución
>
> **Léxico del algoritmo:**
> * `radio: double` — entrada, el radio del círculo.
> * `Pi: double` — constante, 3.14159...
> * `area: double` — salida, el área calculada.
>
> **Representación textual del algoritmo:**
> ```
> Inicio
>   Leer radio
>   area ← Pi * radio * radio
>   Mostrar area
> Fin
> ```
> ```
> Esta plantilla **no** es la solución de ningún TP puntual — es el molde de front page que se reutiliza (con el problema real de cada capítulo) desde el capítulo 5 en adelante. Notá que todavía no aparece ni una línea de C++: eso es intencional, ese paso viene después.

### 💡 Trampas típicas / posibles preguntas

- Escribir el `readme.md` **después** de terminar el código (justificando lo ya hecho) en vez de antes: invierte el orden que pide el capítulo ("el programa debe seguir la definición del algoritmo, y no al revés").
- Copiar el enunciado tal cual como si fuera el "refinamiento": la transcripción y el refinamiento son pasos distintos — el segundo tiene que aportar interpretación propia, no repetir el texto.
- No declarar hipótesis de trabajo cuando el enunciado es ambiguo (ej. no aclarar si acepta números negativos) y descubrir el problema recién al programar.
- Confundir "Proceso" del IPO con el nombre de una función de C++: en esta etapa el proceso es conceptual, todavía no hay sintaxis de ningún lenguaje.
- ¿Por qué el libro exige separar Análisis de Diseño en dos etapas? Porque son preguntas distintas: el Análisis responde "¿qué problema es este?" y el Diseño responde "¿cómo lo resuelvo?" — mezclarlas lleva a diseñar soluciones sin haber entendido bien el problema.

---

## 4. "Hello, World!" en C++

**Objetivos:** parafraseando el capítulo, se busca demostrar que podés editar, compilar y ejecutar un programa C++ simple; tener el primer contacto con las herramientas que vas a necesitar en el resto del curso; crear tu repositorio personal en `git`; y empezar a armar el equipo de trabajo para los TPs grupales que vienen después.

**Temas:**
- Sistema de control de versiones.
- Lenguaje de programación C++.
- Proceso de compilación.
- Pruebas.

**Problema:** adquirir y dejar preparados los recursos necesarios para poder resolver el resto de los trabajos del curso (cuenta de GitHub, repositorio, compilador funcionando, y un primer programa mínimo que demuestre que todo el circuito edita→compila→ejecuta funciona de punta a punta).

**Restricciones:**
- Ninguna.

**Productos:**
```
Usuario                // Usuario GitHub
`-- AED                // Repositorio público personal para la materia
    |-- readme.md       // Front page del usuario
    `-- 00-CppHelloWorld
        |-- readme.md   // Front page del trabajo
        |-- hello.cpp   // Programa fuente
        `-- output.txt  // Salida del programa redireccionada a archivo
```

### 📘 Teoría necesaria

#### Sistema de control de versiones (Git)

Un sistema de control de versiones registra el historial de cambios de un conjunto de archivos, permite volver a versiones anteriores, y — con GitHub como servicio remoto — compartir ese historial entre varias personas (necesario para los TPs grupales que vienen después).

**Sintaxis mínima:**
```bash
git init                          # inicializa un repo en la carpeta actual
git add hello.cpp                 # marca el archivo para el próximo commit
git commit -m "Agrego hello.cpp"  # guarda una "foto" del estado actual
git remote add origin <url>       # asocia el repo local con GitHub
git push -u origin main           # sube los commits al repositorio remoto
```

**Relación con este TP:** el enunciado pide crear la cuenta de GitHub, el repositorio público `AED`, y publicar en él la carpeta `00-CppHelloWorld` con los tres archivos pedidos. Es el primer uso de git del curso; todos los TPs posteriores se entregan de la misma manera.

#### Proceso de compilación

Un compilador de C++ traduce el código fuente (texto que entendemos los humanos) a un ejecutable (instrucciones que entiende la CPU). El proceso pasa, a grandes rasgos, por preprocesamiento (resuelve los `#include`), compilación (traduce a código objeto) y linkeo (une los códigos objeto en un único ejecutable).

**Sintaxis mínima:**
```bash
g++ --version                  # verifica que el compilador está instalado y su versión
g++ -std=c++17 -o hello hello.cpp   # compila hello.cpp -> ejecutable "hello", fijando el estándar
./hello                        # ejecuta el programa
./hello > output.txt           # ejecuta y redirige la salida estándar a un archivo
```

> 💡 El libro remarca que la **versión del compilador** (ej. g++ 13) y la **versión del estándar de C++** que ese compilador soporta (ej. C++17) son dos cosas distintas: un mismo compilador suele poder compilar varias versiones del lenguaje según el flag `-std=`.

**Relación con este TP:** hay que elegir, instalar y "probar" un compilador (mínimo C++11, idealmente C++17/20), registrar en el `readme.md` cuál se usó y qué versión de C++ compila, y usarlo para compilar, ejecutar y redirigir la salida de `hello.cpp`.

#### Pruebas

En este TP puntual "probar" significa simplemente verificar a ojo que la salida del programa es la esperada (`Hello, World!` o similar) y que, al redirigir la salida a un archivo, ese archivo contiene lo mismo. Todavía no aparece `assert` — eso arranca recién en el capítulo 6 — pero ya se instala la idea de que **ejecutar y mirar la salida no alcanza**, hay que dejar evidencia (el `output.txt`) de que se verificó.

**Relación con este TP:** el tercer paso de la tarea es justamente "ejecute el programa con la salida redireccionada a un archivo `output.txt`; verifique su contenido" — ese archivo es la prueba que se entrega.

### 🧭 Cómo encararlo

1. Cuenta de GitHub: si no la tenés, creala; asociale (y hacé pública) tu email `@frba`, porque es la forma en que la cátedra identifica a los alumnos y envía las invitaciones a los repos privados de equipo.
2. Repositorio personal: creá un repo público llamado `AED`. En la raíz va un `readme.md` que es el *front page* de tu cuenta para la materia (según los requisitos generales del capítulo 2: materia, curso, cuatrimestre/año, legajo, apellido, nombre).
3. Dentro de ese repo, creá la carpeta `00-CppHelloWorld` con su propio `readme.md` (front page de *este* trabajo puntual, no de toda la cuenta).
4. Instalá y probá un compilador C++ por línea de comandos (`g++ --version` o `clang++ --version`). Decidí qué versión del estándar vas a usar (`-std=c++17` es una opción segura) y anotá compilador + versión + estándar en el `readme.md`.
5. Escribí `hello.cpp`: un `main()` que solo tiene que enviar una línea de texto a `cout` (algo como `Hello, World!`). No hace falta nada más — ni funciones, ni `assert`, ni lectura de `cin`.
6. Compilalo, ejecutalo y mirá la salida en pantalla. Después ejecutalo de nuevo redirigiendo la salida a `output.txt` (`./hello > output.txt`) y abrí ese archivo para confirmar que el contenido es correcto.
7. Publicá los tres archivos (`readme.md`, `hello.cpp`, `output.txt`) dentro de `00-CppHelloWorld/` en tu repo `AED`, con un commit.
8. Recién en este punto, considerá armar equipo con compañeros para los trabajos grupales que siguen (el enunciado aclara que este TP es individual, pero recomienda colaborar/organizarse para lo que viene).

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> `hello.cpp`:
> ```cpp
> /* hello.cpp
>  * Primer programa en C++ del curso: saluda al mundo por consola.
>  * [Tu usuario GitHub] [Tu legajo] [Tu apellido, nombre]
>  * 20260917
>  */
> #include <iostream>
> using namespace std;
>
> int main() {
>     cout << "Hello, World!" << endl;
>     return 0;
> }
> ```
>
> Compilación y prueba (registrar esto en el `readme.md`):
> ```bash
> g++ --version
> # g++ (Ubuntu 13.2.0-4ubuntu3) 13.2.0   -> versión del COMPILADOR
>
> g++ -std=c++17 -o hello hello.cpp
> # -std=c++17 fija la versión del LENGUAJE que se compila
>
> ./hello
> # Hello, World!
>
> ./hello > output.txt
> cat output.txt
> # Hello, World!
> ```
>
> `readme.md` (front page de `00-CppHelloWorld/`):
> ```markdown
> # 00-CppHelloWorld
>
> # Autor
> * [Usuario GitHub] [Legajo] [Apellido, Nombre]
>
> # Enunciado
> **Objetivo:** Demostrar capacidad de editar, compilar y ejecutar un programa C++ simple; preparar
> las herramientas necesarias para el resto del curso.
>
> **Compilador:** g++ 13.2.0 — compila hasta C++20 (se usó -std=c++17).
>
> # Diseño de la solución
> El programa envía por `cout` el literal `"Hello, World!"` seguido de salto de línea. No hay entradas
> ni cálculos: es la prueba mínima de que el circuito editar → compilar → ejecutar funciona.
> ```
>
> No hay lógica que probar con `assert` acá — el "test" de este TP es la inspección del `output.txt`, que es justamente uno de los tres productos pedidos.

### 💡 Trampas típicas / posibles preguntas

- Confundir versión del compilador con versión del estándar de C++ (son dos números distintos, y el libro insiste en esta distinción).
- Olvidarse de asociar y hacer pública la dirección `@frba` en GitHub: sin eso la cátedra no puede identificarte ni invitarte al repositorio privado del equipo más adelante.
- Publicar el ejecutable compilado (`hello`, sin extensión, o `hello.exe`) junto al código fuente: no corresponde subir binarios al repo (para eso está el `.gitignore` que mencionan los requisitos generales).
- Entregar `output.txt` con contenido distinto al que realmente imprime `hello.cpp` (por ejemplo, copiado a mano) en vez de generarlo con la redirección real.
- ¿Por qué importa que el TP sea individual pero se recomiende ya pensar en equipo? Porque a partir del próximo grupo de TPs la mayoría son grupales, y conviene arrancar esa coordinación temprano.

---

## 5. Resolución de Problemas — Adición

**Objetivos:** demostrar, con un problema simple, el manejo de las etapas de resolución de problemas vistas en el capítulo 3 (Análisis del Problema y Diseño de la Solución) aplicadas por primera vez a un ejercicio real de programación.

**Temas:**
- Resolución de problemas.
- Entrada de datos.
- Tipos numéricos.
- Adición.
- Léxico.
- Representación de algoritmos.

**Problema:** obtener del usuario dos números y mostrarle la suma.

**Restricciones:**
- Ninguna.

**Productos:**
```
DD-Adición
  |-- readme.md
  `-- Adición.cpp
```

### 📘 Teoría necesaria

#### Resolución de problemas

(ver arriba, capítulo 3 — Modelo IPO, Léxico y Representación del Algoritmo). La diferencia con el capítulo 3 es que acá **se aplica en serio, por primera vez**, a un problema con entradas reales: hay que completar el `readme.md` con la transcripción, el refinamiento/hipótesis, la tabla IPO, el léxico y **ambas** representaciones del algoritmo (visual y textual — este capítulo es la única excepción donde el libro pide las dos, en el resto alcanza con una).

#### Entrada de datos

C++ ofrece `std::cin` (definido en `<iostream>`) para leer datos que ingresa el usuario por teclado, usando el operador de extracción `>>`.

**Sintaxis:**
```cpp
int numero;
cout << "Ingrese un numero: ";
cin >> numero;   // lee, convierte a int, y lo guarda en "numero"
```

`cin >> variable` devuelve el propio stream, por lo que se pueden encadenar varias lecturas: `cin >> a >> b;` lee primero `a` y después `b`.

**Relación con este TP:** es la primera vez en el libro que un programa necesita datos que **no** son literales fijos en el código — el usuario los provee en tiempo de ejecución.

#### Tipos numéricos

C++ tiene varios tipos para representar números, según si son enteros o con parte decimal, y el rango que necesitan representar.

| Tipo | Qué representa | Ejemplo |
|---|---|---|
| `int` | Entero con signo | `-5`, `0`, `42` |
| `unsigned` | Entero sin signo (solo ≥ 0) | `10u` |
| `double` | Punto flotante de doble precisión | `3.14` |

**Relación con este TP:** hay que decidir qué tipo usar para los dos números de entrada y para la suma. Si el enunciado no aclara si pueden ser decimales, es una decisión de diseño para documentar como hipótesis de trabajo (ver capítulo 3): usar `int` es lo más simple y cubre "dos números" en el sentido más literal, pero `double` sería necesario si se quisieran admitir decimales.

#### Adición

La operación de suma en C++ se representa con el operador `+`, y funciona igual sobre `int`, `unsigned` o `double` (siempre que ambos operandos sean del mismo tipo, o uno se convierta implícitamente al del otro).

**Sintaxis:**
```cpp
int resultado = 3 + 4;       // 7
double resultado2 = 2.5 + 1.5;  // 4.0
```

**Relación con este TP:** es literalmente el "Proceso" del modelo IPO de este trabajo — tomar las dos entradas y aplicarles `+`.

#### Léxico

(ver arriba, capítulo 3). Para este TP el léxico es mínimo: dos variables de entrada, una de salida, y el nombre del proceso.

#### Representación de algoritmos

(ver arriba, capítulo 3 — Representación visual y textual). Este es el primer TP donde el libro exige **las dos representaciones** en el mismo `readme.md`: un diagrama de flujo Y un pseudocódigo equivalente para el mismo algoritmo.

### 🧭 Cómo encararlo

1. Aplicá el proceso completo del capítulo 3 en el `readme.md`: transcribí el enunciado ("Obtener del usuario dos números y mostrarle la suma"), refinalo (¿son enteros o pueden ser decimales? documentá la decisión que tomes) y armá la tabla IPO con dos entradas numéricas, un proceso ("Sumar" o similar) y una salida numérica.
2. Elegí el tipo de dato para las entradas y la salida (`int` es razonable si no se aclara nada sobre decimales) y anotalo en el léxico.
3. Diseñá el algoritmo en pseudocódigo, con tres pasos claros: leer, sumar, mostrar. Dibujá también el diagrama de flujo equivalente (óvalo de inicio, rectángulo/paralelogramo para leer cada entrada, rectángulo para el cálculo, paralelogramo para mostrar, óvalo de fin) — el enunciado pide **ambas** representaciones acá.
4. Al programar, separá conceptualmente el "Proceso" de la "Entrada/Salida": conviene escribir una función pequeña que solo sume (sin leer ni imprimir nada), y probarla con casos fijos antes de conectarla a `cin`/`cout`. Esto deja el "cálculo puro" testeable de forma aislada, sin depender de que el usuario tipee algo.
5. Pensá qué casos convendría cubrir en esas pruebas: números positivos, un negativo y un positivo, dos negativos, y el caso con cero — para confirmar que la suma se comporta como se espera en cada combinación de signos.
6. Recién después de validar la función de suma, armá el `main()` que lee las dos entradas con `cin`, llama a la función, y muestra el resultado con `cout`.
7. Revisá que el código final sea una traducción directa del pseudocódigo del `readme.md` (mismo orden de pasos: leer, sumar, mostrar) — si el código hace algo que el diseño no contempla, hay que ajustar uno de los dos documentos.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> `readme.md`:
> ```markdown
> # DD-Adición
>
> # Autor
> * [Usuario GitHub] [Legajo] [Apellido, Nombre]
>
> # Enunciado
> **Transcripción:** Obtener del usuario dos números y mostrarle la suma.
>
> **Refinamiento e hipótesis de trabajo:** Se asume que "dos números" se refiere a números enteros
> (no se pide explícitamente soportar decimales); si el usuario ingresa un valor no numérico, el
> comportamiento queda indefinido por el enunciado (no se contempla validación de entrada).
>
> **Modelo IPO:**
>
> | IPO | Nombre | Tipo |
> |---|---|---|
> | Entrada | primerNumero | `int` |
> | Entrada | segundoNumero | `int` |
> | Proceso | Sumar | — |
> | Salida | suma | `int` |
>
> # Diseño de la solución
>
> **Léxico del algoritmo:**
> * `primerNumero: int` — primer número ingresado.
> * `segundoNumero: int` — segundo número ingresado.
> * `suma: int` — resultado de sumar ambos.
>
> **Representación textual del algoritmo:**
> ```
> Inicio
>   Leer primerNumero
>   Leer segundoNumero
>   suma ← primerNumero + segundoNumero
>   Mostrar suma
> Fin
> ```
>
> **Representación visual del algoritmo:**
> ```
> ( Inicio )
>      |
>      v
> [ Leer primerNumero ]
>      |
>      v
> [ Leer segundoNumero ]
>      |
>      v
> [ suma <- primerNumero + segundoNumero ]
>      |
>      v
> [ Mostrar suma ]
>      |
>      v
>  (  Fin  )
> ```
> ```
>
> `Adición.cpp`:
> ```cpp
> /* Adición.cpp
>  * Obtiene del usuario dos números y muestra la suma.
>  * [Tu usuario GitHub] [Tu legajo] [Tu apellido, nombre]
>  * 20260917
>  */
> #include <cassert>
> #include <iostream>
> using namespace std;
>
> // Proceso puro: no lee ni escribe nada, solo calcula. Esto permite probarlo con
> // assert de forma aislada, sin depender de lo que tipee el usuario.
> int Sumar(int a, int b) {
>     return a + b;
> }
>
> int main() {
>     // Pruebas del proceso antes de usarlo con datos reales.
>     assert(Sumar(2, 3) == 5);
>     assert(Sumar(-2, 2) == 0);
>     assert(Sumar(0, 0) == 0);
>     assert(Sumar(-5, -7) == -12);
>
>     // Entrada
>     int primerNumero, segundoNumero;
>     cout << "Ingrese el primer numero: ";
>     cin >> primerNumero;
>     cout << "Ingrese el segundo numero: ";
>     cin >> segundoNumero;
>
>     // Proceso
>     int suma = Sumar(primerNumero, segundoNumero);
>
>     // Salida
>     cout << "La suma es: " << suma << endl;
>
>     return 0;
> }
> ```
>
> Los `assert` de arriba validan la función `Sumar` con casos fijos (positivos, negativos, cero) antes de que el `main` dependa de la entrada real por teclado — así, si algo falla, se sabe que el problema está en la lógica y no en lo que tipeó el usuario en esa corrida puntual.

### 💡 Trampas típicas / posibles preguntas

- No separar el cálculo (`Sumar`) de la entrada/salida: si todo queda mezclado en el `main`, no hay forma de probar la suma con `assert` sin además tener que simular la entrada por teclado.
- Presentar solo una representación del algoritmo (visual **o** textual): este capítulo, a diferencia del resto del libro, pide **las dos**.
- Usar `unsigned` para los números sin justificarlo: si el enunciado no descarta números negativos, `unsigned` puede dar resultados incorrectos (un negativo se interpreta como un entero enorme).
- Leer los dos números en una sola línea de `cin >> a >> b;` sin haber aclarado en el diseño si el usuario los ingresa juntos o por separado — es un detalle menor de UX que conviene decidir a propósito, no por default del código.
- ¿Por qué probar `Sumar` con casos negativos si "sumar" parece trivial? Porque valida que no haya errores de signo o de tipo (ej. overflow con tipos muy chicos) antes de confiar en el resultado con datos reales del usuario.

---

## 6. Valores y Operaciones de Tipos de Datos — Ejemplos

**Objetivos:** demostrar la aplicación de los tipos de datos básicos de C++ mediante un programa ejemplo, verificado con pruebas (`assert`) en vez de inspección visual por `cout`.

**Temas:**
- Valores.
- Operaciones.
- Tipos de datos.
- Representación literal de valores en programas C++.
- `assert`.

**Problema:** diseñar un programa C++ que ejemplifique, con pruebas, la aplicación de los tipos de datos vistos en clase.

**Restricciones:**
- Utilizar la notación de C++ para representar los valores.
- No utilizar variables.
- No extraer valores de `cin`; usar valores literales (constantes).
- No enviar valores a `cout`.

**Productos:**
```
DD-EjemploTipos
  |-- readme.md
  `-- EjemploTipos.cpp
```

> 💡 En el repo del equipo esta carpeta se llama `01-Valores y Operaciones de Tipos de Datos` (no `06-...`), porque la numeración de carpetas la fija el curso y no coincide 1 a 1 con el número de capítulo del libro — ver la aclaración en la nota índice.

Es un trabajo **no estructurado**: consiste en escribir un programa que ejemplifique el uso de los tipos básicos vistos en clase — `bool`, `char`, `unsigned`, `int`, `double` y `string` — con `assert` demostrando operaciones válidas dentro del dominio de cada tipo. Además hay dos consignas de crédito extra: (1) buscar, para cada tipo, una notación literal alternativa si la tiene, y justificar si la lista de tipos dada es realmente completa; (2) intentar probar que sumar diez veces `0.1` da `1.0`, y explicar qué está pasando.

### 📘 Teoría necesaria

#### Valores y su representación literal en C++

Un **literal** es un valor escrito directamente en el código fuente, sin pasar por una variable. Cada tipo tiene su propia notación:

| Tipo | Literal típico | Literal(es) alternativo(s) |
|---|---|---|
| `bool` | `true`, `false` | — |
| `char` | `'A'`, `'@'` (un solo carácter entre comillas simples) | código numérico equivalente, ej. `64` para `'@'` |
| `int` | `23`, `-5` | hexadecimal `0x17`, octal `027` |
| `unsigned` | `10u` (sufijo `u`) | hexadecimal `0xFu` |
| `double` | `3.14`, `0.5` | notación científica `5e-1` |
| `string` (`std::string`) | `"hola"s` (sufijo `s`, requiere `using namespace std::string_literals;` o `using namespace std;` + `<string>`) | `std::string("hola")` (constructor explícito) |

**Ejemplo mínimo** (no es la solución del TP, solo ilustra la idea):
```cpp
assert(0x10 == 16);        // literal hexadecimal de int
assert(3.0e2 == 300.0);    // notación científica de double
```

**Relación con este TP:** es el tema central — las restricciones obligan a **no usar variables**, así que todo el programa consiste en literales combinados con operadores dentro de expresiones que se verifican con `assert`.

#### Operaciones por tipo de dato

Cada tipo define qué operadores tiene sentido usar sobre él, y qué devuelven:

- `bool`: lógicos — `&&` (y), `||` (o), `!` (no); también con palabras clave `and`, `or`, `not`.
- `char`: comparación (`<`, `==`, ...) porque internamente es un código numérico (ASCII); también admite aritmética (`'A' + 1 == 'B'`) por esa misma razón.
- `int` / `unsigned`: aritméticos (`+ - * / %`), de comparación, y bit a bit.
- `double`: aritméticos y de comparación (con el cuidado de la imprecisión de punto flotante, ver más abajo).
- `string`: concatenación con `+`, comparación lexicográfica (`<`, `==`), acceso a un carácter con `.at(indice)`, y `.length()`.

**Ejemplo mínimo:**
```cpp
assert('a' < 'b');          // char se compara como número (código ASCII)
assert("ab"s + "c"s == "abc"s);  // concatenación de string
```

**Relación con este TP:** el programa tiene que mostrar, con al menos un `assert` por tipo, una operación característica de ese tipo (no alcanza con declarar el valor, hay que **operar** con él).

#### `assert`

`assert(condición)` (de `<cassert>`) verifica en tiempo de ejecución que `condición` sea verdadera. Si es falsa, el programa aborta inmediatamente e imprime el archivo y la línea del `assert` que falló. Es la forma de "probar" que pide el libro, en reemplazo de imprimir con `cout` y mirar a ojo si el resultado es el esperado.

**Sintaxis:**
```cpp
#include <cassert>
assert(2 + 2 == 4);   // si la condición es falsa, el programa termina con un mensaje de error
```

**Relación con este TP:** es la restricción más fuerte de este capítulo — está explícitamente prohibido usar `cout` para mostrar resultados; toda verificación tiene que hacerse con `assert`, de forma que el programa "pasa" en silencio si todo está bien.

#### Tipos de datos — completitud de la lista

El enunciado enumera `bool`, `char`, `unsigned`, `int`, `double` y `string`, pero eso **no** agota todos los tipos básicos vistos en clase — el crédito extra pide justamente notar esto. Existen además, entre otros: `float` (punto flotante de precisión simple, menos preciso que `double`), `long` / `long long` (enteros de mayor rango que `int`), y variantes con signo/sin signo de cada entero (`short`, `long unsigned`, etc.).

### 🧭 Cómo encararlo

1. Como no se pueden usar variables, pensá el programa como una **lista de aseveraciones independientes**: cada `assert` es una mini demostración autocontenida de que un tipo se comporta como se espera.
2. Organizá el archivo en bloques, uno por tipo (`bool`, `char`, `int`, `double`, `string`, `unsigned`), con un comentario que indique dónde empieza cada bloque — mejora la legibilidad aunque no lo pida el enunciado.
3. Para cada tipo, elegí al menos una operación que sea **característica** de ese tipo (no cualquier cosa): por ejemplo, en `string` conviene mostrar concatenación (`+`) y comparación lexicográfica, no solo igualdad.
4. Con `climits` (`INT_MAX`, `LONG_MAX`, etc.) podés aseverar límites conocidos del tipo, lo cual es una buena forma de "demostrar" el dominio de valores que representa sin necesitar variables.
5. Para el crédito extra de literales alternativos, buscá para cada tipo una notación distinta a la más obvia (hexadecimal para enteros, notación científica para `double`, constructor explícito para `string`) y probala también con `assert`.
6. Para el crédito extra de "diez veces 0.1", pensá primero en la hipótesis antes de escribir el `assert`: como los `double` no representan exactamente todas las fracciones decimales en binario, es esperable que la suma acumulada **no** dé exactamente `1.0`. Conviene primero aseverar que la igualdad estricta **falla** (`!=`), y después aseverar que la diferencia contra `1.0` es menor a un margen de tolerancia pequeño (comparación con épsilon), que es la forma correcta de comparar `double` por aproximación.
7. Revisá con cuidado el balanceo de paréntesis en asserts largos (como el de la suma de diez `0.1`): es un error fácil de cometer y el compilador lo va a señalar como error de sintaxis, no como fallo de `assert`.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* EjemploTipos.cpp
>  * Ejemplifica, con pruebas (assert), la aplicación de los tipos de datos
>  * básicos de C++ vistos en clase: bool, char, unsigned, int, double y string.
>  * Esteban Capandegui, Andrés Palluzzi, Boris Bernabé Mamani Choque, De Vincenzo Mariano Ariel
>  * 20260917
>  */
> #include <cassert>
> #include <iostream>
> #include <string>
> #include <climits> // para INT_MAX y LONG_MAX
> using namespace std;
>
> int main() {
> // A probar: bool, char, unsigned, int, double, y string. ¿Otros que vimos en clase?
>
> // Bloque con BOOL
> assert(true && !false);
> assert(true == (false || true));
>
> // Bloque con CHAR
> assert('@' == 64);
> assert('H' != 0);
> assert('A' < 'Z');
>
> // Bloque con INT
> assert(19 + 4 == 23);
> assert(100 % 99 == 1);
> assert(INT_MAX == 2147483647);
> assert(LONG_MAX >= 2147483647L);
> assert(25 / 5 == 5);
>
> // Bloque con DOUBLE (punto flotante doble precision)
> assert(2.5 * 2.0 == 5.0);
> assert(1.0 / 2.0 == 0.5);
> assert(10.0 + 11.0 > 20.0);
> assert(3.14 > 3.0);
>
> // Bloque con STRING
> assert("hola"s == "hola");
> assert("abc"s.length() == 3);
> assert("hola"s + " mundo"s == "hola mundo");
> assert("Algoritmos 2026"s.at(0) == 'A');
> assert("abc"s < "utn"s);
>
> // Bloque con UNSIGNED
> assert(10u + 5u == 15u);
> assert(20u - 8u == 12u);
>
>
> // Crédito Extra: ¿Son los enumerados en la sección anterior realmente todos los tipos que usamos en clase?
> /*
>  * bool, char, unsigned, int, double no son todos los tipos de datos de C++ vistos en clase. También
>  * vimos, por ejemplo, float y long. La diferencia es que estos dos últimos no son tan precisos como
>  * puede llegar a ser double, que reduce los errores de redondeo en los cálculos.
>  */
>
>
> // Crédito Extra: para cada tipo de dato, notación literal alternativa (si la tiene).
> // BOOL
> assert(true and not false);
> // CHAR
> assert('A' + 1 == 'B');
> assert('b' - 'a' == 1); // operación aritmética
> // INT
> assert(0x17 == 23); // hexadecimal
> // DOUBLE
> assert(5e-1 == 0.5); // notación científica
> // STRING
> assert("hola"s == string("hola"));
> // UNSIGNED
> assert(0xFu == 15u); // hexadecimal
>
> // Crédito Extra: intente probar que la suma de diez veces un décimo (0.1) es uno (1.0). ¿Qué está ocurriendo?
> assert(0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 != 1.0);
> // No se puede aseverar que diez veces un décimo (0.1) es uno (1.0), ya que los números de punto
> // flotante se almacenan en representación binaria, y no es posible representar exactamente 0.1 en
> // esa notación. La computadora lo guarda como un valor cercano a 0.1000000000000000055..., y al
> // sumarlo diez veces el error se acumula, por lo que el resultado no da exactamente 1.0.
> //
> // Para probar el comportamiento real, comparamos contra 1.0 con una tolerancia (épsilon) en vez de
> // pedir igualdad exacta:
> assert((0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 + 0.1 - 1.0) < 0.00000001);
>
> return 0;
> }
> ```
>
> Esta versión es el código real del equipo (`26-053-01/01-Valores y Operaciones de Tipos de Datos/valoresYTiposDeDatos.cpp`), con el header comment agregado (faltaba, y es requisito del capítulo 2) y un bug real corregido: el último `assert` del archivo original tenía un paréntesis de más antes del `<` y le faltaba el `;` final — `assert(((0.1 + ... + 0.1) - 1.0)) < 0.00000001)` cierra el `assert(...)` **antes** de la comparación con épsilon, dejando `< 0.00000001)` como código suelto fuera de cualquier llamada: eso no compila. La versión de arriba balancea bien los paréntesis (`assert((suma - 1.0) < 0.00000001);`), que es lo que la explicación del propio comentario dice que se quiso probar.

### 💡 Trampas típicas / posibles preguntas

- Comparar `double` con `==` para verificar un resultado que involucra sumas de fracciones no exactas en binario (como `0.1`): casi siempre falla por el error de representación, hay que comparar con un margen de tolerancia.
- Usar una variable "auxiliar" sin darse cuenta (por ejemplo, declarar `int x = 5;` para después hacer `assert(x == 5);`) — viola la restricción explícita de "no utilizar variables".
- Extraer algún valor de `cin` "para probar más rápido" durante el desarrollo y olvidarse de sacarlo antes de entregar — el enunciado prohíbe expresamente leer de `cin` en este TP.
- Dejar algún `cout` de depuración en el archivo final — también está prohibido explícitamente.
- ¿Por qué `'@' == 64` es válido en C++? Porque `char` es, en el fondo, un entero de 8 bits que representa un código (ASCII/UTF-8); comparar un `char` contra un `int` compara esos códigos numéricos.
- ¿Qué otros tipos básicos de C++ vistos en clase no están en la lista del enunciado? Al menos `float` y `long` (y sus variantes), como señala el propio crédito extra resuelto por el equipo.

---
