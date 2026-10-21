⬅️ [[00 - Índice y Requisitos Generales]]

# 02 — Funciones (Trabajos 7 a 12)

Seis trabajos que giran alrededor de **funciones en C++ como implementación de funciones matemáticas**: primero con floats y tolerancia (7), después con el operador ternario (8), con precedencia de operadores booleanos (9), con recursividad (10), con un sistema de funciones que se invocan entre sí (11), y por último funciones que implementan conceptos matemáticos "de biblioteca" — sumatoria, productoria, integral (12).

Dos de estos trabajos ya tienen código resuelto por el equipo en `26-053-01/`: el capítulo 8 en `08-FuncionesyOperadorCondicional/` y el capítulo 10 en `10-FuncionesRecursivasConOperadorCondicional/`. Para esos dos, la "Solución completa" de abajo es el código real que ya está en el repo (pulido con header comment), no una reescritura desde cero.

---

## 7. Funciones y Comparación de Valores en Punto Flotante — Celsius y Fahrenheit

**Objetivos:** Demostrar el manejo de funciones y valores en punto flotante.

**Temas:**
- Funciones.
- Tipo `double`.
- División entera y flotante.
- Pruebas con `assert`.
- Argumentos con valor por defecto.
- Introducción a iteraciones con `for`.

**Problema:** Se necesita una tabla que presente las temperaturas Celsius convertidas en Fahrenheit, y otra en el sentido opuesto. Las fórmulas de conversión (relación lineal entre ambas escalas):

$$F = \frac{9}{5}C + 32 \qquad\qquad C = \frac{5}{9}(F - 32)$$

Desafíos de formato de la tabla:
- La columna izquierda muestra temperaturas con valores **enteros** (sin décimas).
- La columna derecha debe tener **precisión de una décima**.
- Las dos columnas deben estar alineadas a la derecha.

**Restricciones:**
- Se deben construir **dos funciones**, una para cada conversión, ambas $\mathbb{R} \rightarrow \mathbb{R}$.
- Las pruebas deben realizarse con `assert`.

**Análisis (dos sub-problemas antes de poder implementar):**
1. Para `Celsius`, el problema del valor de la fracción $\tfrac{5}{9}$ versus la división entera de la expresión `5/9` en C++; para `Fahrenheit`, $\tfrac{9}{5}$ versus `9/5`. La solución: hacer la división entre **flotantes**, no enteros.
2. Para poder *probar* la implementación (comparar resultados con `assert`), el problema de que **la presentación no precisa de tipos flotantes** (redondeás para mostrar) pero la **comparación de igualdad entre `double` sí es un problema real** — hace falta una función `bool` que reciba dos flotantes y una tolerancia, y devuelva verdadero si están "cerca". El libro propone la función `AreNear`.

**Tareas:**
1. Función `AreNear`: (a) léxico/definición matemática, (b) pruebas en `main`, (c) prototipo antes de `main`, (d) definición después de `main`, (e) compilar, ejecutar, evaluar la salida.
2. Función `Celsius` que calcula Celsius a partir de Fahrenheit: mismos pasos.
3. Función `Fahrenheit` que calcula Fahrenheit a partir de Celsius: mismos pasos.
4. (Opcional) Escribir en `main` las iteraciones que imprimen las tablas (pueden ser dos funciones `void`).
5. (Opcional) Desarrollar funciones que grafiquen ambas funciones lineales.

**Productos:**
```
DD-Temp
 |-- readme.md
 `-- Temp.cpp
```

### 📘 Teoría necesaria

**Qué es una función matemática vs. función en C++.** Una función matemática $f: A \to B$ es una correspondencia que a cada elemento del **dominio** $A$ le asigna **exactamente un** elemento del **codominio** $B$. El **léxico** es su definición formal escrita: fórmula, dominio, y casos especiales si los hay. Una función en C++ (`double Fahrenheit(double c)`) es la *implementación* de esa idea: recibe argumentos, ejecuta código, devuelve un valor. Antes de escribir la función en C++ conviene escribir el léxico en un comentario — eso evita ambigüedades (¿qué pasa en los bordes del dominio? ¿hay más de un caso?).

**Prototipo vs. definición.** El **prototipo** (o declaración) le dice al compilador la *firma* de la función — nombre, tipos de parámetros, tipo de retorno — sin el cuerpo. La **definición** es el prototipo más el cuerpo `{ ... }`. Separarlos permite usar la función en `main` (arriba en el archivo) aunque su cuerpo esté escrito más abajo — así el `main`, que es lo que más te interesa leer primero, queda arriba.

```cpp
double Fahrenheit(double celsius);   // prototipo: promesa al compilador

int main(){
  assert(Fahrenheit(0) == 32);        // ya se puede usar acá
}

double Fahrenheit(double celsius){   // definición: cumple la promesa
  return celsius * 9.0/5.0 + 32.0;
}
```

**División entera vs. flotante en C++.** Si ambos operandos de `/` son enteros, C++ hace **división entera** (trunca hacia cero, descarta el resto): `5/9` da `0`, no `0.555...`. Para forzar división real hace falta que **al menos uno** de los operandos sea `double`: `5.0/9` o `5/9.0` o `static_cast<double>(5)/9` ya dan `0.555...`. Esta es exactamente la trampa que señala el libro para `Celsius`/`Fahrenheit`: si escribís `celsius * 9/5 + 32` con enteros, el resultado queda mal.

```cpp
int a = 9/5;       // 1  (división entera, trunca)
double b = 9.0/5;  // 1.8 (flotante, uno de los dos operandos es double)
```

**Comparación de punto flotante — por qué `a == b` con `double` es peligroso.** Los `double` no pueden representar exactamente la mayoría de los números reales (son una aproximación binaria de precisión finita). Dos cálculos que "matemáticamente" dan el mismo resultado pueden diferir en el último bit por errores de redondeo acumulados. Por eso **nunca** se compara igualdad exacta entre flotantes calculados; se compara si están **suficientemente cerca**, con una tolerancia (épsilon).

```cpp
#include <cmath>   // std::abs

bool AreNear(double a, double b, double tolerancia = 1e-6){
  return std::abs(a - b) <= tolerancia;
}
```

Léxico de `AreNear`: $\text{AreNear}(a, b, \varepsilon) = |a - b| \le \varepsilon$, con $\varepsilon$ un valor por defecto (ver siguiente punto).

**Argumentos con valor por defecto.** Un parámetro puede tener un valor que se usa automáticamente si no lo pasás en la llamada. Se define solo en el prototipo (o en la única declaración visible), y **debe ir al final** de la lista de parámetros — no puede haber un parámetro sin default después de uno con default.

```cpp
bool AreNear(double a, double b, double tolerancia = 1e-6);

AreNear(1.0, 1.0000001);          // usa tolerancia = 1e-6
AreNear(1.0, 1.1, 0.5);           // tolerancia explícita = 0.5
```

**Introducción a iteraciones con `for`.** Para imprimir una tabla (parte opcional de este trabajo) hace falta recorrer un rango de temperaturas con un `for` clásico:

```cpp
for (int c = -40; c <= 100; c += 10) {
  std::cout << c << " -> " << Fahrenheit(c) << '\n';
}
```

### 🧭 Cómo encararlo

1. Escribí primero el léxico de las tres funciones en comentario: `Fahrenheit(c) = 9/5·c + 32`, `Celsius(f) = 5/9·(f - 32)`, `AreNear(a,b,ε) = |a-b| ≤ ε`. Identificá dominio ($\mathbb{R}$ en los tres casos, sin restricciones físicas — el libro no pide validar el cero absoluto) y que no hay casos especiales.
2. Pensá los asserts *antes* de implementar: valores conocidos de memoria (`Fahrenheit(0) == 32`, `Fahrenheit(100) == 212`, `Celsius(32) == 0`, `Celsius(212) == 100`), y un caso ida-y-vuelta (`Celsius(Fahrenheit(x))` cerca de `x`, usando `AreNear` porque ahí sí hay error de redondeo real).
3. Escribí los prototipos de las tres funciones (con el default de `AreNear` en el prototipo).
4. Implementá `AreNear` primero (la vas a necesitar para probar las otras dos con tolerancia).
5. Implementá `Fahrenheit` y `Celsius`, cuidando que la división `9/5` y `5/9` se haga entre flotantes.
6. (Opcional) Escribí una función `void ImprimirTabla...()` con un `for` y `std::setw`/`std::fixed` + `std::setprecision(1)` para el formato pedido (entero a la izquierda, una décima a la derecha, alineado a la derecha).

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* Temp.cpp
>  * Conversión Celsius <-> Fahrenheit, con comparación de flotantes por tolerancia (AreNear)
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
> #include <cmath>
> #include <iostream>
> #include <iomanip>
>
> // Léxico: AreNear(a,b,tolerancia) = |a - b| <= tolerancia
> bool AreNear(double a, double b, double tolerancia = 1e-6);
>
> // Léxico: Fahrenheit(c) = 9/5 * c + 32      (R -> R)
> double Fahrenheit(double celsius);
>
> // Léxico: Celsius(f) = 5/9 * (f - 32)       (R -> R)
> double Celsius(double fahrenheit);
>
> void ImprimirTablaCelsiusAFahrenheit();
> void ImprimirTablaFahrenheitACelsius();
>
> int main(){
>   // Pruebas AreNear
>   assert(AreNear(1.0, 1.0));
>   assert(AreNear(1.0, 1.0000001));
>   assert(!AreNear(1.0, 1.1));
>   assert(AreNear(1.0, 1.4, 0.5));
>
>   // Pruebas Fahrenheit
>   assert(Fahrenheit(0) == 32);
>   assert(Fahrenheit(100) == 212);
>   assert(Fahrenheit(-40) == -40);   // punto donde ambas escalas coinciden
>
>   // Pruebas Celsius
>   assert(Celsius(32) == 0);
>   assert(Celsius(212) == 100);
>   assert(Celsius(-40) == -40);
>
>   // Ida y vuelta: acá sí puede haber error de redondeo -> se usa AreNear
>   assert(AreNear(Celsius(Fahrenheit(37.5)), 37.5));
>
>   ImprimirTablaCelsiusAFahrenheit();
>   ImprimirTablaFahrenheitACelsius();
> }
>
> bool AreNear(double a, double b, double tolerancia){
>   return std::abs(a - b) <= tolerancia;
> }
>
> double Fahrenheit(double celsius){
>   return celsius * 9.0/5.0 + 32.0;
> }
>
> double Celsius(double fahrenheit){
>   return (fahrenheit - 32.0) * 5.0/9.0;
> }
>
> void ImprimirTablaCelsiusAFahrenheit(){
>   for (int c = -40; c <= 100; c += 10){
>     std::cout << std::setw(4) << c << " C = "
>                << std::fixed << std::setprecision(1) << std::setw(6)
>                << Fahrenheit(c) << " F\n";
>   }
> }
>
> void ImprimirTablaFahrenheitACelsius(){
>   for (int f = -40; f <= 212; f += 10){
>     std::cout << std::setw(4) << f << " F = "
>                << std::fixed << std::setprecision(1) << std::setw(6)
>                << Celsius(f) << " C\n";
>   }
> }
> ```
> `9.0/5.0` fuerza división flotante (evita el bug de `9/5 == 1`). `AreNear` con default `1e-6` se usa solo donde hay conversión ida-y-vuelta, porque ahí el redondeo de flotantes sí puede introducir un error mínimo; en las comparaciones directas contra valores exactos conocidos (`Fahrenheit(0) == 32`) la igualdad estricta funciona porque no hay acumulación de error.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué `9/5` da `1` en C++? Porque ambos son `int`: división entera, trunca. Hay que forzar flotante con al menos un operando `double`.
- ¿Por qué no comparar `double` con `==` directamente? Porque el redondeo binario puede hacer que dos cálculos "iguales" en matemática difieran en el último bit.
- ¿Dónde tiene que ir el parámetro con valor por defecto en la firma de una función? Al final de la lista de parámetros.
- ¿`AreNear(a, b)` y `AreNear(b, a)` dan lo mismo? Sí, porque `|a-b| == |b-a|` (valor absoluto de la resta es simétrico).
- Pregunta de oral típica: ¿-40 es un buen caso de prueba? Sí — es el único punto donde Celsius y Fahrenheit coinciden numéricamente, un buen ancla para validar ambas fórmulas a la vez.

---

## 8. Funciones y Operador Condicional

**Objetivos:** Demostrar manejo de funciones y del operador condicional.

**Temas:**
- Operador condicional.
- Funciones.

**Problema:** Desarrollar las siguientes funciones:
1. Valor absoluto.
2. Valor mínimo entre dos valores.
3. Función `F3`, definida por el siguiente gráfico (transcripción del diagrama del libro):

En el rango $(-3, 3]$ (es decir, $x > -3$ y $x \le 3$) la función vale $-x$ (la diagonal invertida del gráfico). Fuera de ese rango (es decir $x < -3$ o $x > 3$) la función vale $x$ (identidad — las dos semirrectas del gráfico). El punto $x = -3$ es un caso aparte: como queda excluido del primer tramo (el $>$ es estricto), se define explícitamente $F3(-3) = 3$ — es el círculo relleno (incluido) que se ve a la izquierda del gráfico, en contraste con el círculo vacío (excluido) que queda en $(3, 3)$ sobre la recta identidad y en $(-3,-3)$ también sobre la identidad.

$$
F3(x) = \begin{cases}
3 & \text{si } x = -3 \\
-x & \text{si } -3 < x \le 3 \\
x & \text{si } x < -3 \text{ o } x > 3
\end{cases}
$$

**Restricciones:**
- Las pruebas deben realizarse con `assert`.
- Cada función debe aplicar el operador condicional.

**Tareas:** Por cada función:
1. Escribir el léxico, es decir, la definición matemática de la función.
2. Escribir las pruebas.
3. Escribir los prototipos.
4. Escribir las definiciones.

**Productos:**
```
DD-Cond
 |-- readme.md
 |-- Abs.cpp
 |-- Min.cpp
 `-- F3.cpp
```

### 📘 Teoría necesaria

**El operador condicional ternario `? :`.** Es un **operador**, no una sentencia: forma parte de una **expresión** y produce un **valor**. Sintaxis: `condición ? valor_si_verdadero : valor_si_falso`.

```cpp
int a = 5, b = 3;
int mayor = (a > b) ? a : b;   // mayor = 5
```

Se usa en vez de un `if`/`else` cuando lo que hace falta es **producir un valor** (para asignar, retornar o pasar como argumento) en una sola expresión, no ejecutar varias sentencias. Un `if`/`else` ejecuta *bloques de código*; el ternario *elige entre dos valores*. Es exactamente lo que hace falta para implementar funciones matemáticas como una sola expresión de retorno (`return condición ? a : b;`).

Se puede **anidar** para representar funciones con más de dos casos (como `F3`, que tiene tres):

```cpp
return (x == -3) ? 3
     : (x > -3 && x <= 3) ? -x
     : x;
```

El ternario es **asociativo a derecha**: `a ? b : c ? d : e` se lee `a ? b : (c ? d : e)`.

**Léxico y dominio.** Ver arriba (capítulo 7) para la distinción función matemática vs. función en C++. Para `F3`, el dominio completo es $\mathbb{R}$, pero la fórmula cambia según en qué tramo cae $x$ — de ahí que se llame función **por partes** (piecewise).

### 🧭 Cómo encararlo

1. Para cada función, escribí primero el léxico en un comentario: `abs(x) = x si x≥0, -x si x<0`; `min(a,b) = a si a≤b, b si a>b`; `F3` como arriba (los tres casos, prestando atención a qué extremos son abiertos y cuáles cerrados según el gráfico).
2. Para `F3` en particular: identificá primero el caso "raro" (el punto suelto, `x=-3`) antes de armar el ternario anidado — es el que más fácil se pasa por alto.
3. Prototipos: `int Abs(int)`, con qué tipo conviene `Min` (¿`int` o `double`? el gráfico de `F3` sugiere `double` porque trabaja con la recta real completa).
4. Asserts antes de implementar, con casos borde: para `Abs`, cero y un negativo; para `Min`, iguales y con signos distintos; para `F3`, los tres tramos **y** los dos bordes exactos ($x=-3$ y $x=3$) y algo bien afuera del rango (para no confundir "afuera del [-3,3]" con "cerca de 0").
5. Implementá cada función como un solo `return` con el ternario (anidado si hace falta).

### ✅ Solución completa

Código real del equipo, ya en `26-053-01/08-FuncionesyOperadorCondicional/`. Se agregó el header comment que exige `00 - Índice y Requisitos Generales.md` (todavía no estaba en los `.cpp`).

> [!example]- Solución completa (click para expandir)
> `Abs.cpp`
> ```cpp
> /* Abs.cpp
>  * Valor absoluto de un entero, con operador condicional
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> // Léxico: abs(a) = a si a >= 0 ; -a si a < 0
> int abs(int);
>
> int main(){
>   assert( 6 == abs(6) );
>   assert( 30 == abs(-30) );
>   assert( 0 == abs(0) );
> }
>
> int abs(int a){
>   return a<0? -a : a;
> }
> ```
> `Min.cpp`
> ```cpp
> /* Min.cpp
>  * Mínimo entre dos enteros, con operador condicional
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> // Definicion de la funcion:
> // Compara dos numeros y devuelve el mas chico de los dos
> // min(a,b) = a si a<=b ; b si a>b
>
> int min(int, int);
>
> int main(){
>   assert( 3 == min(5,3) );
>   assert( -2 == min(-2,10) );
>   assert( 2 == min(2,2) );
> }
>
> int min(int a, int b){
>   return (a <= b) ? a : b;
> }
> ```
> `F3.cpp`
> ```cpp
> /* F3.cpp
>  * Función por partes F3 (ver diagrama del enunciado), con operador condicional
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> // Léxico:
> // F3(x) = 3   si x = -3
> //       = -x  si -3 < x <= 3
> //       = x   si x < -3 o x > 3
> double f3(double x);
>
> int main() {
>   assert(f3(-2) == 2);   // dentro del rango
>   assert(f3(0) == 0);
>   assert(f3(2) == -2);
>   assert(f3(-3) == 3);   // límite izquierdo (caso especial)
>   assert(f3(3) == -3);   // límite derecho (incluido en el tramo -x)
>   assert(f3(-4) == -4);  // fuera del rango, semirrecta identidad
>   assert(f3(5) == 5);    // fuera del rango, semirrecta identidad
> }
>
> double f3(double x) {
>   return (x > -3 && x <= 3) ? -x : ((x == -3) ? 3 : x);
> }
> ```
> El orden de evaluación del ternario anidado resuelve primero si `x` cae en `(-3, 3]` (el tramo `-x`, que cubre la mayoría de los casos incluido el borde derecho); si no, recién ahí distingue el único punto suelto `x == -3` del resto de la identidad. Es equivalente en resultado al orden inverso (`x==-3` primero), pero así queda un solo chequeo compuesto seguido de un chequeo simple.

**Chequeo contra el libro:** el ternario `(x > -3 && x <= 3) ? -x : ((x == -3) ? 3 : x)` coincide exactamente con el diagrama del capítulo 8 (círculo relleno en $(-3,3)$, círculo vacío en $(3,3)$ y en $(-3,-3)$, círculo relleno en $(3,-3)$) — no hay discrepancia entre el PDF y `F3.cpp`.

> [!warning] Para completar en el `readme.md` real
> La sección **Hipótesis** del `readme.md` de `08-FuncionesyOperadorCondicional/` está vacía — hay que completarla con qué se esperaba lograr al leer el enunciado, antes de entregar.

> [!warning] Detalle de diseño en `Min.cpp`
> Los parámetros de `min` son `int`, pero algunos asserts del archivo real prueban con literales `double` (`min(-7.6,-6.7)`, `min(3.0, 9.0)`) — C++ los trunca silenciosamente a `int` antes de llamar a la función (`-7.6` → `-7`, `3.0` → `3`), y por eso los asserts igual pasan, pero no están probando lo que el nombre sugiere. Si la intención es que `Min` funcione con flotantes, conviene cambiar la firma a `double Min(double, double)`.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué `F3` necesita un ternario anidado y no alcanza con uno solo? Porque tiene **tres** casos, no dos — cada ternario adicional agrega un caso más.
- ¿Qué significa el círculo vacío vs. relleno en el gráfico? Vacío = ese punto **no** pertenece a la gráfica en ese tramo (desigualdad estricta); relleno = sí pertenece (desigualdad no estricta, o caso explícito).
- Si cambiaras `x > -3 && x <= 3` por `x >= -3 && x <= 3`, ¿qué pasaría con `F3(-3)`? Entraría al primer tramo y daría `-(-3) = 3` — coincide por casualidad con el resultado correcto en este caso puntual, pero rompería la estructura de "caso especial" y sería frágil ante cambios.
- ¿Cuál es la diferencia entre usar `int` y `double` en las firmas cuando el enunciado no lo aclara? Hay que mirar el dominio real del problema — `F3` está definida sobre $\mathbb{R}$ en el gráfico, por eso usa `double`.

---

## 9. Precedencia de Operadores — Bisiesto

**Objetivos:** Demostrar el uso de operadores booleanos y expresiones complejas.

**Temas:**
- Expresiones.
- Operadores booleanos: `and`, `or`, y `not`.
- Operador resto: `%`.
- Asociatividad de Operadores: ID ó DI.
- Precedencia de Operadores.
- Orden de evaluación de Operandos.
- Funciones.
- Precondiciones.

**Problema:** Desarrollar una función que, dado un año, determine si es bisiesto.

**Restricciones:**
- El nombre de la función debe ser `IsBisiesto` (por convención, se usa el prefijo `Is` para predicados, es decir, funciones que retornan un valor lógico).
- Aplicar operadores booleanos.
- **No** aplicar expresión condicional (`?:`, el operador ternario).
- **No** aplicar sentencia condicional (`if`, `if-else`, ni `switch`).
- Las pruebas deben realizarse con `assert`.

**Tareas:**
1. Escribir en `readme.md` la definición matemática de la función, y, si tiene, las precondiciones.
2. Escribir en `IsBisiesto.cpp` las pruebas.
3. Escribir en `IsBisiesto.cpp` el prototipo.
4. Escribir en `IsBisiesto.cpp` la definición.
5. Incluir en `readme.md` el **árbol de expresión** asociado a la expresión de retorno de la función.

**Créditos extra:**
- **Conceptos de expresiones:** explicar en `readme.md`, haciendo referencia al árbol de expresión: asociatividad de operadores, precedencia de operadores, orden de evaluación de operandos.
- **Sentencia de selección:** desarrollar en `IsBisiestoIf.cpp` una versión que en vez de operadores de conexión lógica (`and`, `or`) use `if` o `if-else`; comparar pros y contras de cada implementación.
- **Tipo Año:** desarrollar en `IsBisiestoY.cpp` una versión que en vez de recibir un `natural` reciba un **año** (tipo con sufijo `y`, p. ej. `assert(not IsBisiesto(1582y)); assert(IsBisiesto(2020y));`) — explicar el sufijo `y`.
- **Biblioteca estándar:** investigar si la biblioteca estándar provee una funcionalidad similar, y de ser así desarrollar en `IsBisiestoStd.cpp` un programa que la pruebe.

**Productos:**
```
DD-Bisiesto
 |-- readme.md
 |-- IsBisiesto.cpp
 |-- IsBisiestoIf.cpp    // Opcional
 |-- IsBisiestoY.cpp     // Opcional
 `-- IsBisiestoStd.cpp   // Opcional
```

### 📘 Teoría necesaria

**El operador resto `%`.** Devuelve el resto de la división entera. `a % b` solo está bien definido para enteros (`b != 0`). Es la herramienta clave para "es divisible por": `n % d == 0` ⟺ `d` divide a `n`.

```cpp
7 % 3;    // 1
8 % 4;    // 0  -> 8 es divisible por 4
```

**Operadores booleanos `and`, `or`, `not`.** Son **alias** (palabras clave, no símbolos) de `&&`, `||`, `!` respectivamente — hacen exactamente lo mismo, es solo una forma alternativa de escribirlos, más legible en expresiones largas. Requieren `#include <ciso646>` en algunos compiladores viejos, pero en C++ moderno ya son parte del lenguaje estándar sin include adicional.

```cpp
bool esBisiesto = (n % 4 == 0) and ((n % 100 != 0) or (n % 400 == 0));
```

**Precedencia de operadores.** Cuando una expresión combina varios operadores sin paréntesis, la precedencia decide **qué operador se aplica primero** (como en matemática, donde `*` "liga más fuerte" que `+`). De mayor a menor precedencia (subconjunto relevante acá):

| Nivel | Operadores | Ejemplo |
|---|---|---|
| Más alto | `%`, `*`, `/` | `a % b` antes que comparar |
| | `+`, `-` (binarios) | |
| | `<`, `<=`, `>`, `>=` | comparaciones relacionales |
| | `==`, `!=` | igualdad |
| | `&&` / `and` | Y lógico |
| Más bajo | `\|\|` / `or` | O lógico |

Por eso `n % 4 == 0 && n % 100 != 0 || n % 400 == 0` se resuelve, **sin ningún paréntesis**, como: `((n % 4 == 0) && (n % 100 != 0)) || (n % 400 == 0)` — el `%` liga antes que `==`, que liga antes que `&&`, que liga antes que `||`. Igual, para legibilidad humana conviene poner paréntesis aunque el compilador no los necesite.

**Asociatividad de operadores: ID ó DI.** Cuando hay **varios operadores del mismo nivel de precedencia** en fila, la asociatividad decide en qué orden se agrupan: **ID** (izquierda-a-derecha) o **DI** (derecha-a-izquierda). La mayoría de los operadores binarios en C++ son ID: `a - b - c` se lee `(a - b) - c`, no `a - (b - c)`. La asignación `=` y el ternario `?:` son de los pocos DI.

```cpp
10 - 3 - 2;   // ID: (10-3)-2 = 5, NO 10-(3-2) = 9
a = b = 5;    // DI: a = (b = 5)
```

**Orden de evaluación de operandos.** Distinto de precedencia/asociatividad: es la pregunta de **en qué orden se ejecutan las subexpresiones** cuando hay efectos de lado (por ejemplo llamadas a función con efectos). Para `&&` y `||`, C++ garantiza evaluación de **izquierda a derecha con cortocircuito**: en `a && b`, si `a` es falso, `b` **ni se evalúa** (no hace falta, el resultado ya es falso); análogamente `a || b` no evalúa `b` si `a` ya es verdadero. Para la mayoría de los demás operadores (como `+`), el **orden no está especificado** por el estándar — no hay que asumir izquierda-a-derecha si hay efectos de lado en los operandos.

```cpp
bool f(){ /* con efecto de lado */ return true; }
bool g(){ /* con efecto de lado */ return false; }
g() && f();   // f() NUNCA se ejecuta: cortocircuito, porque g() ya dio false
```

**Árbol de expresión.** Es la representación en forma de árbol de una expresión: cada operador es un nodo interno (con sus operandos como hijos), cada valor/variable es una hoja. El árbol deja explícita la precedencia y asociatividad sin ambigüedad — se arma "de adentro hacia afuera" según lo que se evalúa primero. Para `(n % 4 == 0) && (n % 100 != 0 || n % 400 == 0)`:

```
                &&
              /    \
           ==        ||
          /  \      /    \
        %     0    !=      ==
       / \        /  \    /  \
      n   4      %    0  %    0
                / \      / \
               n  100   n  400
```

### 🧭 Cómo encararlo

1. Léxico primero: un año $n$ es bisiesto si es divisible por 4, salvo que sea divisible por 100 — excepto si además es divisible por 400. $$\text{IsBisiesto}(n) = (n \bmod 4 = 0) \land \big((n \bmod 100 \neq 0) \lor (n \bmod 400 = 0)\big)$$ Precondición: $n$ debe ser un año válido según el calendario gregoriano que se esté usando (natural, distinto de cero según convención astronómica vs. histórica — anotalo como supuesto).
2. Restricción clave: **no** `if`, **no** `?:` — la función tiene que ser **una sola expresión booleana** con `&&`/`||`/`!` (o `and`/`or`/`not`), `return`eada directamente. Esto fuerza a pensar la función como fórmula lógica pura, no como flujo de control.
3. Asserts antes de implementar, con los casos históricamente reveladores: 2020 (divisible por 4, no por 100 → bisiesto), 1900 (divisible por 100, no por 400 → NO bisiesto), 2000 (divisible por 400 → bisiesto), 2021 (no divisible por 4 → no bisiesto).
4. Prototipo: `bool IsBisiesto(unsigned n);` — el prefijo `Is` avisa que es un predicado.
5. Implementá la única expresión de retorno respetando la restricción de no usar `if`/`?:`.
6. Dibujá el árbol de expresión de esa línea de `return` para el `readme.md`.
7. (Extra) Para `IsBisiestoIf.cpp`, la misma lógica pero con `if`/`if-else` anidados — comparar: la versión booleana es más corta y "declarativa"; la versión `if` es más "paso a paso" y más fácil de debuggear con breakpoints, pero más larga.

### ✅ Solución completa

Este capítulo no tiene código todavía en `26-053-01/`.

> [!example]- Solución completa (click para expandir)
> `IsBisiesto.cpp`
> ```cpp
> /* IsBisiesto.cpp
>  * Determina si un año es bisiesto, usando solo operadores booleanos (sin if ni ?:)
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> // Léxico:
> // IsBisiesto(n) = (n % 4 == 0) and ((n % 100 != 0) or (n % 400 == 0))
> // Precondición: n es un año positivo del calendario gregoriano (n >= 1582)
> bool IsBisiesto(unsigned n);
>
> int main(){
>   assert(IsBisiesto(2020));         // divisible por 4, no por 100
>   assert(not IsBisiesto(2021));     // no divisible por 4
>   assert(not IsBisiesto(1900));     // divisible por 100, no por 400
>   assert(IsBisiesto(2000));         // divisible por 400
>   assert(IsBisiesto(2400));
>   assert(not IsBisiesto(1800));
>   assert(IsBisiesto(1600));
> }
>
> bool IsBisiesto(unsigned n){
>   return (n % 4 == 0) and ((n % 100 != 0) or (n % 400 == 0));
> }
> ```
> `IsBisiestoIf.cpp` (crédito extra — misma lógica con sentencia de selección)
> ```cpp
> /* IsBisiestoIf.cpp
>  * Misma función que IsBisiesto, pero con if/if-else en vez de operadores booleanos
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> bool IsBisiesto(unsigned n);
>
> int main(){
>   assert(IsBisiesto(2020));
>   assert(not IsBisiesto(2021));
>   assert(not IsBisiesto(1900));
>   assert(IsBisiesto(2000));
> }
>
> bool IsBisiesto(unsigned n){
>   if (n % 4 != 0) return false;
>   if (n % 100 != 0) return true;
>   if (n % 400 == 0) return true;
>   return false;
> }
> ```
> La versión booleana es una única expresión (más cerca del léxico matemático); la versión `if` tiene **cuatro puntos de retorno**, más pasos pero más fácil de seguir con un debugger paso a paso.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué 1900 no es bisiesto pero 2000 sí, si ambos son "años redondos"? Porque la regla de los 100 años (excepción) tiene a su vez una excepción con los 400: 1900 no es divisible por 400, 2000 sí.
- ¿Qué pasa si escribís `n % 4 == 0 && n % 100 != 0 || n % 400 == 0` sin paréntesis? Da el resultado correcto igual, porque `&&` tiene más precedencia que `||` — pero es más difícil de leer sin paréntesis explícitos.
- ¿Por qué `and`/`or`/`not` en vez de `&&`/`||`/`!`? Son sinónimos exactos; el enunciado los pide así por legibilidad, no cambia la semántica.
- Si `g()` devuelve `false` en `g() && f()`, ¿se evalúa `f()`? No — cortocircuito. Esto es relevante si `f()` tiene efectos de lado (ej. imprime algo, modifica una variable).
- Pregunta de árbol de expresión: ¿cuál es la raíz del árbol para `a % 4 == 0 && b`? El operador de **menor precedencia** entre los presentes, en este caso `&&` (el `%` y el `==` quedan más profundo, del lado izquierdo).

---

## 10. Funciones Recursivas con Operador Condicional

**Objetivos:** Demostrar manejo de funciones definidas recursivamente e implementadas con el operador condicional.

**Temas:**
- Funciones recursivas.
- Operador condicional (ver arriba, capítulo 8).

**Problema:** Desarrollar las siguientes funciones:
1. División entera de naturales: `Div`.
2. MCD (Máximo Común Denominador): `Mcd`.
3. Factorial: `Fact`. *(Un número factorial puede ser muy grande, por eso hay que elegir el tipo de la función correctamente.)*
4. Fibonacci: `Fib`. *(Notar que esta función es doblemente recursiva.)*
5. Repetir frase.
6. Una función recursiva definida por ustedes y que no sea una de las vistas.
7. Crédito extra: Fractal en SVG.

**Restricciones:**
- Las pruebas deben realizarse con `assert`.
- Cada función debe aplicar el operador condicional.

**Tareas:** Por cada función:
1. Escribir el léxico, es decir, la definición matemática de la función.
2. Escribir las pruebas.
3. Escribir los prototipos.
4. Escribir las definiciones.

**Productos:**
```
DD-Recur
 |-- readme.md
 |-- Div.cpp
 |-- Mcd.cpp
 |-- Factorial.cpp
 |-- Fibonacci.cpp
 |-- Frase.cpp
 |-- Rec.cpp
 `-- Svg.cpp
```

### 📘 Teoría necesaria

**Recursividad: caso base y caso recursivo.** Una función recursiva es una función que se invoca **a sí misma**. Para que termine (y no entre en loop infinito / stack overflow), necesita:
- Un **caso base**: al menos un valor de entrada para el cual la función devuelve un resultado directamente, sin volver a invocarse.
- Un **caso recursivo**: para el resto de las entradas, la función se expresa en términos de sí misma aplicada a una entrada "más chica" — que eventualmente tiene que llegar al caso base.

Cómo se arma el **call stack** de una función recursiva: cada invocación hace *push* de un nuevo stack frame (con sus propias variables locales/parámetros), hasta llegar al caso base; ahí empiezan los *pop*, cada uno usando el resultado de la invocación de más arriba. Ver `[[Call Stack y Heap]]` para el mecanismo general de push/pop y stack frames — acá el único agregado es que la función que hace push es **la misma** que está en la base, muchas veces apilada.

```
fact(3)
 └─ 3 * fact(2)
     └─ 2 * fact(1)
         └─ 1                    <- caso base, empieza a hacer pop
     └─ 2 * 1 = 2
 └─ 3 * 2 = 6
```

**Recursión con operador condicional en vez de `if`/`else`.** El patrón que pide este capítulo: escribir el caso base y el caso recursivo como las dos ramas de un único `?:`, en un solo `return`:

```cpp
int fact(int n){
  return n <= 1 ? 1 : n * fact(n - 1);
}
```

Esto es exactamente el léxico matemático traducido línea por línea:
$\text{fact}(n) = 1 \text{ si } n \le 1,\ n \cdot \text{fact}(n-1) \text{ si } n > 1$.

**Doblemente recursiva.** `Fibonacci` es un caso donde el caso recursivo invoca a la función **dos veces** (`fib(n-1) + fib(n-2)`), no una — cada invocación genera dos ramas nuevas en el árbol de llamadas (no una cadena lineal como `Fact`), lo que hace que el costo crezca exponencialmente si no se optimiza (fuera del alcance de este TP, pero vale saberlo).

### 🧭 Cómo encararlo

1. Para cada función, primero el léxico con caso base y caso recursivo bien identificados (ver ejemplos reales abajo).
2. Elegir el tipo con cuidado: `Fact` crece muy rápido (`13!` ya no entra en 32 bits sin signo), por eso el código real usa `unsigned` y prueba hasta `Fact(12)` — quedar registrado en el `readme.md` como limitación conocida en vez de ignorarlo.
3. Asserts con casos borde: el caso base exacto (`n=0` o `n=1` según corresponda), un caso chico calculable a mano, y para `Div`/`Mcd` casos con cero (`Mcd(0,16)`, `Div(0,5)`).
4. Prototipo + definición como un solo `return` con el ternario (anidado si hay más de dos casos, como en `Fib`).
5. Para `Frase` (repetir una frase N veces) y la función libre elegida por el equipo (acá: contar dígitos de un número), mismo patrón: identificar qué reduce el problema en cada paso recursivo (n-1 repeticiones, o `n/10` para pelar un dígito).
6. Para el fractal SVG, la recursión no devuelve un valor sino que tiene un **efecto de lado** (escribir en el archivo) — el caso base es "niveles == 0", ahí no hace falta el operador condicional para retornar un valor (la restricción de "aplicar operador condicional" aplica más naturalmente a las funciones que sí devuelven un valor matemático).

### ✅ Solución completa

Código real del equipo, ya en `26-053-01/10-FuncionesRecursivasConOperadorCondicional/`. La tarea 5 ("repetir frase") está resuelta en `repetirFrase.cpp` (el libro sugiere `Frase.cpp` como nombre de producto, el archivo real se llama distinto — mantené el nombre real, es solo una convención). La tarea 6 (función recursiva propia) está resuelta con `contarDigitos.cpp`. La tarea 7 (fractal SVG) con `fractal.cpp`.

> [!example]- Solución completa (click para expandir)
> `Div.cpp`
> ```cpp
> /* Div.cpp
>  * División entera de naturales, por restas sucesivas, recursiva con operador condicional
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> // Léxico: b > 0
> // Div(a,b) = 0            si a < b
> //          = 1 + Div(a-b, b)  si a >= b
> int Div(int a, int b);
>
> int Div(int a, int b){
>   return (a < b) ? 0 : 1 + Div(a - b, b);
> }
>
> void probar_div(){
>   assert(Div(10,2) == 5);
>   assert(Div(7,3) == 2);    // dividido 3 es 2 (con resto 1)
>   assert(Div(0,5) == 0);    // 0 dividido cualquier numero da 0
>   assert(Div(4,5) == 0);    // si el dividendo es menor, da 0
>   assert(Div(8,8) == 1);
>   assert(Div(15,1) == 15);
>   assert(Div(1000,25) == 40);
> }
>
> int main(){
>   probar_div();
> }
> ```
> `Mcd.cpp`
> ```cpp
> /* Mcd.cpp
>  * Máximo Común Divisor por el algoritmo de Euclides, recursivo con operador condicional
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> // Definición formal de MCD: Máximo Común Divisor
> // mcd: (Z x Z) - {(0,0)} -> N / mcd(a,b) = d <-> d|a ^ d|b ^ para todo c natural, (c|a ^ c|b) => c|d
> unsigned mcd(int a, int b);
> int absoluto(int n);
>
> int main(){
>   assert( mcd(0,16) == 16 );
>   assert( mcd(15,0) == 15 );
>   assert( mcd(-21,522) == 3 );
>   assert( mcd(22,-74) == 2);
>   assert( mcd(0,0) == 0 );        // matemáticamente es una indeterminación
>   assert( mcd(17,3) == 1);
>   assert( mcd(24,6) == 6);
>   assert( mcd(-5,-5) == 5);
> }
>
> unsigned mcd(int a, int b){
>   return (b==0) ? absoluto(a) : mcd(b, a%b);
> }
>
> int absoluto(int n){
>   return (n>=0) ? n : (-n);
> }
> ```
> `Fact.cpp`
> ```cpp
> /* Fact.cpp
>  * Factorial, recursivo con operador condicional
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> // Léxico:
> // fact(n) = 1                si 0 <= n <= 1
> //         = n * fact(n - 1)  si n > 1
> unsigned Fact(unsigned int n);
>
> int main(){
>   assert( 1 == Fact(0));
>   assert( 1 == Fact(1));
>   assert( 24 == Fact(4));
>   assert( 39916800 == Fact(11));
>   assert( 479001600 == Fact(12));   // con unsigned int solo se puede calcular hasta Fact(12)
> }
>
> unsigned Fact(unsigned int n){
>   return 0 <= n && n <= 1 ? 1 : n * Fact(n - 1);
> }
> ```
> `Fibonacci.cpp`
> ```cpp
> /* Fibonacci.cpp
>  * Sucesión de Fibonacci, doblemente recursiva, con operador condicional
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> unsigned long fib(unsigned int n);
>
> // Léxico:
> // Si n = 0, fib(n) = 0
> // Si n = 1, fib(n) = 1
> // Si n > 1, fib(n) = fib(n-1) + fib(n-2)
> unsigned long fib(unsigned int n){
>   return (n==0)?0:
>           (n==1)?1:
>           fib(n-1) + fib(n-2);
> }
>
> int main(){
>   assert(fib(0) == 0);
>   assert(fib(1) == 1);
>   assert(fib(2) == 1);
>   assert(fib(3) == 2);
>   assert(fib(4) == 3);
>   assert(fib(5) == 5);
>   assert(fib(6) == 8);
> }
> ```
> `repetirFrase.cpp`
> ```cpp
> /* repetirFrase.cpp
>  * Repite una frase n veces concatenándola, recursivo con operador condicional
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <string>
> #include <cassert>
> using namespace std;
>
> string repetirFrase(string frase, int n);
>
> int main() {
>   assert(repetirFrase("Hola", 0) == "");
>   assert(repetirFrase("UTN", 1) == "UTN");
>   assert(repetirFrase("Hola! como estas?", 2) == "Hola! como estas?Hola! como estas?");
> }
>
> string repetirFrase(string frase, int n) {
>   string resultado = (n == 0) ? "" : (frase + repetirFrase(frase, n - 1));
>   return resultado;
> }
> ```
> `contarDigitos.cpp` (tarea 6 — función recursiva propia)
> ```cpp
> /* contarDigitos.cpp
>  * Cuenta la cantidad de dígitos de un natural, recursivo con operador condicional
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
>
> // Léxico:
> // contarDigitos(n) = 1                         si 0 <= n < 10
> //                  = 1 + contarDigitos(n/10)    si n >= 10
> unsigned contarDigitos(unsigned int n);
>
> int main(){
>   assert(contarDigitos(0) == 1);
>   assert(contarDigitos(10) == 2);
>   assert(contarDigitos(100) == 3);
>   assert(contarDigitos(1000) == 4);
>   assert(contarDigitos(10000) == 5);
> }
>
> unsigned contarDigitos(unsigned int n){
>   return (n < 10) ? 1 : 1 + contarDigitos(n/10);
> }
> ```
> `fractal.cpp` (crédito extra — fractal en SVG, resumido; ver el archivo completo en el repo)
> ```cpp
> // dibujarLineasRecursivas: dibuja un "árbol" tipo T recursivo en un archivo SVG.
> // Caso base: niveles == 0 (efecto de lado, no hay valor que retornar -> no aplica ?:).
> void dibujarLineasRecursivas(std::ofstream& archivo, double x, double y,
>                               double longitud, int direccion, int niveles) {
>   if (niveles == 0) return;
>   // ... dibuja la línea actual, y llama recursivamente dos veces con niveles-1
>   //     (ramas izquierda/derecha o arriba/abajo, alternando dirección)
> }
> ```
> El fractal es la única función de este grupo que **no** devuelve un valor matemático (es `void`, con efecto de lado de escritura a archivo) — por eso la restricción "aplicar operador condicional" se cumple en espíritu con el `if (niveles == 0) return;` como caso base, sin forzar un ternario donde no hay valor que producir.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué `Fact` usa `unsigned` en vez de `int`? Porque el factorial nunca es negativo, y así se aprovecha el doble de rango positivo — aun así, se desborda rápido (`Fact(13)` ya no entra en 32 bits sin signo), por eso el archivo se detiene en pruobar hasta `Fact(12)`.
- ¿Qué pasa si a `Div` le pasás `b <= 0`? La precondición del léxico exige `b > 0`; con `b = 0` la resta `a - b` nunca reduce `a`, la recursión no llega nunca al caso base → stack overflow.
- ¿Por qué `Fib` se llama "doblemente recursiva"? Porque el caso recursivo hace **dos** invocaciones (`fib(n-1) + fib(n-2)`), no una — el árbol de llamadas se ramifica en dos por cada nivel.
- ¿`Mcd(0,0)` da qué, y por qué? Da `0` en la implementación (porque `absoluto(0) = 0`), aunque matemáticamente el MCD de `(0,0)` es una indeterminación — está documentado como comentario en el código, es una decisión de diseño explícita, no un bug escondido.
- Relación con call stack: si `repetirFrase("x", 100000)` se invoca, ¿qué puede pasar? Cada nivel de recursión apila un nuevo stack frame; con suficiente profundidad se agota el espacio del stack → stack overflow (ver `[[Call Stack y Heap]]`).

---

## 11. Sistema de Funciones — Días del Mes

**Objetivos:** Demostrar el uso de funciones para resolver problemas.

**Temas:**
- Expresiones.
- Expresión condicional.
- Especificación de funciones.
- Funciones puras.
- Precondiciones.
- Poscondiciones.
- Funciones que invocan funciones.
- Efecto de lado de una expresión.
- Transparencia referencial.
- Entrada estándar.
- Pruebas y aplicación.

**Problema:** Desarrollar un programa que informe la cantidad de días de un mes.

**Restricciones:**
- Los datos se extraen de la entrada estándar.
- El año se pide **solo para Febrero**.
- La solución debe basarse en la función **pura** `GetCantidadDeDías`.
- La función `GetCantidadDeDías` debe invocar `IsBisiesto` (ver trabajo
  [[02 - Funciones#9. Precedencia de Operadores — Bisiesto|Precedencia de Operadores — Bisiesto]]).
- La función `main` tiene dos responsabilidades: probar `GetCantidadDeDías` y resolver el problema con una aplicación.
- La prueba y la aplicación se hacen desde funciones invocadas por `main`, o en bloques distintos delimitados por llaves `{` y `}`.
- Las pruebas deben realizarse con `assert`.
- Desarrollar funciones **no puras** para la extracción de datos de la entrada estándar y para el envío de resultados a la salida estándar.
- Aplicar expresiones condicionales y sentencias condicionales según corresponda.

**Tareas:**
1. Escribir la especificación matemática de la función `GetCantidadDeDías`.
2. Escribir las pruebas.
3. Escribir el prototipo.
4. Escribir la definición.
5. Diseñar las funciones de la aplicación.
6. Escribir la aplicación.

**Productos:**
```
DD-CantidadDeDiasDelMes
 |-- readme.md
 `-- CantidadDeDiasDelMes.cpp
```

### 📘 Teoría necesaria

**Especificación de funciones: precondiciones y poscondiciones.** Una especificación completa de una función no es solo la fórmula, sino también:
- **Precondición:** lo que tiene que cumplir la entrada para que la función tenga sentido (ej. `1 <= mes <= 12`).
- **Poscondición:** lo que garantiza la salida, dado que se cumplió la precondición (ej. el resultado está entre 28 y 31).

Documentarlas en comentario junto al léxico deja explícito **de quién es la culpa** si algo sale mal: si violás la precondición (pasás `mes = 13`), el comportamiento de la función queda sin garantías.

**Funciones puras vs. no puras, y transparencia referencial.** Una función es **pura** si: (1) su resultado depende **únicamente** de sus argumentos (no de variables globales, ni de estado externo, ni de `cin`), y (2) **no tiene efectos de lado** (no modifica nada fuera de sí misma: ni variables globales, ni hace `cout`, ni lee `cin`). Una función pura tiene **transparencia referencial**: se puede reemplazar cualquier llamada por su valor de retorno sin cambiar el comportamiento del programa — llamarla mil veces con los mismos argumentos siempre da el mismo resultado.

```cpp
// Pura: mismo argumento -> siempre mismo resultado, sin tocar nada externo
int Doble(int x){ return x * 2; }

// NO pura: depende de cin (estado externo) y tiene efecto de lado (consume entrada)
int LeerEntero(){
  int x;
  std::cin >> x;
  return x;
}
```

`GetCantidadDeDías` tiene que ser pura — recibe mes (y opcionalmente año) y devuelve la cantidad de días, sin leer ni imprimir nada ella misma. Leer de `cin` e imprimir a `cout` son trabajo de **otras** funciones (no puras), que la aplicación (`main` u otras funciones) invoca por separado.

**Funciones que invocan funciones.** `GetCantidadDeDías` tiene que invocar `IsBisiesto` (capítulo 9) para resolver el caso de febrero — es un ejemplo de **composición**: una función pura puede invocar a otra función pura sin dejar de ser pura ella misma (invocar `IsBisiesto` no introduce efectos de lado ni depende de estado externo).

**Entrada estándar.** Ver el patrón de bucle de lectura con `std::cin >> x` en `26-053-01/18-Arreglos & Dimensiones - Total de Ventas/Guia-Examen-Oral.md` (tema 6 y 7 de esa guía) — acá se usa una sola vez por ejecución (mes, y opcionalmente año), no en loop.

**Expresión condicional vs. sentencia condicional — cuándo cada una.** Ver el ternario en el capítulo 8. Acá el enunciado pide aplicar **ambas** según corresponda: el ternario adentro de `GetCantidadDeDías` (es una expresión que produce un valor, función pura), y probablemente un `if`/`else` en la aplicación (para decidir *si* pedirle el año al usuario — eso es una decisión de **flujo**, no de valor).

### 🧭 Cómo encararlo

1. Especificación de `GetCantidadDeDías`: dominio (mes 1-12, año opcional/solo relevante si mes=2), fórmula. Escribí la precondición (`1 <= mes <= 12`) y la poscondición (resultado en `{28,29,30,31}`). $$ \text{GetCantidadDeDías}(mes, año) = \begin{cases} 31 & mes \in \{1,3,5,7,8,10,12\} \\
   30 & mes \in \{4,6,9,11\} \\
   29 & mes = 2 \land \text{IsBisiesto}(año) \\
   28 & mes = 2 \land \neg\text{IsBisiesto}(año)
   \end{cases}
   $$
2. Decidí la firma: `int GetCantidadDeDías(int mes, int año = 0)` — con default, para no forzar el año en meses donde no importa (la restricción dice "el año se pide solo para Febrero").
3. Asserts antes de implementar: los 12 meses en un año no bisiesto, y por lo menos un caso de febrero bisiesto vs. no bisiesto.
4. Implementación de `GetCantidadDeDías` como expresión pura (podés usar un conjunto de ternarios anidados, o combinar con operadores relacionales — sin leer ni imprimir nada adentro).
5. Diseñá las funciones **no puras** de la aplicación: una para pedir el mes (y condicionalmente el año) por `cin`, otra para mostrar el resultado por `cout`. Usá un `if` para decidir si hace falta pedir el año (mes == 2).
6. Armá `main` con **dos bloques separados** (o dos funciones separadas invocadas desde `main`): uno que prueba `GetCantidadDeDías` con `assert`, otro que resuelve el problema real leyendo de `cin`.

### ✅ Solución completa

Este capítulo no tiene código todavía en `26-053-01/`.

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* CantidadDeDiasDelMes.cpp
>  * Sistema de funciones: informa la cantidad de días de un mes (pide año solo para Febrero)
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
> #include <iostream>
>
> // Reutilizada del trabajo "Precedencia de Operadores - Bisiesto"
> bool IsBisiesto(unsigned n){
>   return (n % 4 == 0) and ((n % 100 != 0) or (n % 400 == 0));
> }
>
> // Léxico:
> // GetCantidadDeDías(mes, anio) =
> //   31  si mes en {1,3,5,7,8,10,12}
> //   30  si mes en {4,6,9,11}
> //   29  si mes = 2 y IsBisiesto(anio)
> //   28  si mes = 2 y no IsBisiesto(anio)
> // Precondición: 1 <= mes <= 12; anio solo se usa si mes == 2
> // Poscondición: el resultado está en {28,29,30,31}
> // Es PURA: no lee cin ni escribe cout, el resultado depende solo de mes y anio.
> int GetCantidadDeDías(int mes, int anio = 0);
>
> void ProbarGetCantidadDeDías();       // no pura: solo agrupa asserts
> void ResolverAplicación();            // no pura: lee cin, escribe cout
> int LeerMes();                        // no pura: efecto de lado (lee cin)
> int LeerAnio();                       // no pura: efecto de lado (lee cin)
> void MostrarResultado(int dias);      // no pura: efecto de lado (escribe cout)
>
> int main(){
>   ProbarGetCantidadDeDías();
>   ResolverAplicación();
> }
>
> int GetCantidadDeDías(int mes, int anio){
>   return (mes == 4 || mes == 6 || mes == 9 || mes == 11) ? 30
>        : (mes == 2) ? (IsBisiesto(anio) ? 29 : 28)
>        : 31;
> }
>
> void ProbarGetCantidadDeDías(){
>   assert(GetCantidadDeDías(1) == 31);
>   assert(GetCantidadDeDías(4) == 30);
>   assert(GetCantidadDeDías(2, 2020) == 29);   // bisiesto
>   assert(GetCantidadDeDías(2, 2021) == 28);   // no bisiesto
>   assert(GetCantidadDeDías(2, 1900) == 28);   // divisible por 100, no por 400
>   assert(GetCantidadDeDías(2, 2000) == 29);   // divisible por 400
>   assert(GetCantidadDeDías(12) == 31);
> }
>
> int LeerMes(){
>   int mes;
>   std::cout << "Mes (1-12): ";
>   std::cin >> mes;
>   return mes;
> }
>
> int LeerAnio(){
>   int anio;
>   std::cout << "Anio: ";
>   std::cin >> anio;
>   return anio;
> }
>
> void MostrarResultado(int dias){
>   std::cout << "Cantidad de dias: " << dias << '\n';
> }
>
> void ResolverAplicación(){
>   int mes = LeerMes();
>   int dias = (mes == 2) ? GetCantidadDeDías(mes, LeerAnio())
>                          : GetCantidadDeDías(mes);
>   MostrarResultado(dias);
> }
> ```
> `GetCantidadDeDías` nunca toca `cin`/`cout` — toda la interacción con el usuario vive en `LeerMes`/`LeerAnio`/`MostrarResultado`/`ResolverAplicación`, que son funciones **no puras** separadas. `main` tiene exactamente dos responsabilidades, cada una en su propia función invocada, tal como pide la restricción ("bloques distintos").

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué `GetCantidadDeDías` tiene que ser pura? Para poder probarla con `assert` de forma determinística y sin depender de qué haya en `cin` en ese momento — y porque una función que mezcla cálculo con I/O es más difícil de reusar y de testear.
- ¿`GetCantidadDeDías(2)` sin año, qué debería pasar? Con el valor por defecto `anio=0`, `IsBisiesto(0)` da `true` (0 % 4 == 0) — un caso raro que conviene documentar como parte de la precondición ("el año es obligatorio si mes==2", aunque el código no lo fuerce a nivel de tipo).
- ¿Por qué pedir el año "solo para febrero" y no siempre? Porque en el resto de los meses el año es irrelevante para la cantidad de días — pedirlo igual sería ruido innecesario para el usuario.
- Diferencia entre efecto de lado de una función y de una **expresión**: una expresión como `x++` tiene efecto de lado (modifica `x`) aunque no sea una llamada a función — la transparencia referencial se rompe apenas hay *cualquier* dependencia o modificación de estado externo a la expresión.
- ¿`IsBisiesto` sigue siendo pura al ser invocada desde `GetCantidadDeDías`? Sí — invocar una función pura desde otra función pura no introduce impureza, siempre que no se agregue I/O ni estado externo en el camino.

---

## 12. Funciones — Implementación de Conceptos Matemáticos

**Objetivos:** Demostrar la implementación de conceptos matemáticos clásicos como funciones en C++.

**Temas:**
- Funciones puras y recursivas (ver arriba, capítulos 10 y 11).
- Funciones como parámetros de otras funciones.

**Problema:** Desarrollar las siguientes funciones (listado textual del libro, sin desarrollo adicional en el texto — hay que completar la especificación de cada una a partir de su definición matemática estándar):

1. `Xor(a,b)` — disyunción exclusiva booleana.
2. `Sum(i,n)` — sumatoria de `i` a `n` // recursiva.
3. `Sum_i2(i,n)` — sumatoria de los cuadrados entre `i` y `n` // `i²`.
4. `Sum_f(i,n,f)` — sumatoria de `f(k)` para `k` entre `i` y `n`, con `f` una función pasada como parámetro // difícil.
5. `Prod(i,n)` — productoria de `i` a `n`.
6. `Prod_f(i,n,f)` — productoria de `f(k)` para `k` entre `i` y `n`, con `f` como parámetro // difícil.
7. `Integral(a,b,f)` — aproximación numérica de la integral de `f` entre `a` y `b` // difícil.

**Restricciones:** El libro no detalla restricciones adicionales para este capítulo puntual — aplican las restricciones generales de la materia (probar con `assert`, léxico antes de código; ver `[[00 - Índice y Requisitos Generales]]`). Por consistencia con los capítulos anteriores del mismo bloque (10 y 11), conviene aplicar también: operador condicional donde haya expresión de retorno con casos, y resolver `Sum`/`Prod` de forma recursiva (como indica explícitamente el ítem 2).

**Tareas:** El libro no lista tareas explícitas para este capítulo; se recomienda seguir el mismo patrón de los capítulos anteriores por cada función: (1) léxico, (2) pruebas con `assert`, (3) prototipo, (4) definición.

**Productos:** El libro no especifica una carpeta puntual para este capítulo. Por consistencia con el resto (formato `DD-NombreTrabajo`), se sugiere:
```
DD-ConceptosMatematicos
 |-- readme.md
 `-- ConceptosMatematicos.cpp
```

### 📘 Teoría necesaria

**Funciones como parámetros de otras funciones (function pointers / `std::function`).** En C++, una función puede recibir **otra función** como argumento — necesario para `Sum_f`, `Prod_f` e `Integral`, que tienen que poder aplicarse a *cualquier* `f` sin que `Sum_f` conozca de antemano cuál. Dos formas:

```cpp
#include <functional>

// Forma 1: puntero a función (funciona con funciones libres, no con lambdas con captura)
double Sum_f(int i, int n, double (*f)(double));

// Forma 2: std::function (más flexible: acepta funciones libres, lambdas, functores)
double Sum_f(int i, int n, std::function<double(double)> f);

double cuadrado(double x){ return x * x; }

Sum_f(1, 3, cuadrado);                       // pasa la función por nombre
Sum_f(1, 3, [](double x){ return x*x; });    // o una lambda equivalente
```

En este TP alcanza con el puntero a función clásico (`double (*f)(double)`), porque las funciones que se van a pasar (`cuadrado`, `identidad`, etc.) son funciones libres simples, sin variables capturadas.

**Sumatoria y productoria — de la notación matemática a la recursión.** La sumatoria $\sum_{k=i}^{n} k$ y la productoria $\prod_{k=i}^{n} k$ tienen exactamente la misma estructura recursiva que `Fact` o `Div` (capítulo 10): un caso base cuando el rango se vacía, y un caso recursivo que combina el primer término con la sumatoria/productoria del resto del rango.

$$
\text{Sum}(i, n) = \begin{cases} 0 & \text{si } i > n \\ i + \text{Sum}(i+1, n) & \text{si } i \le n \end{cases} \qquad \text{Prod}(i, n) = \begin{cases} 1 & \text{si } i > n \\ i \cdot \text{Prod}(i+1, n) & \text{si } i \le n \end{cases} $$

El caso base de `Sum` es `0` (elemento **neutro** de la suma: sumarlo no cambia nada) y el de `Prod` es `1` (elemento neutro de la multiplicación) — es el mismo patrón que "el caso base de `Fact` es `1`, no `0`", solo que ahora aplicado a dos operaciones distintas.

**`Xor` — disyunción exclusiva.** $a \oplus b$ es verdadero si **exactamente uno** de los dos operandos es verdadero (no ambos, no ninguno). Tabla de verdad:

| a | b | Xor(a,b) |
|---|---|---|
| V | V | F |
| V | F | V |
| F | V | V |
| F | F | F |

Se puede implementar con `!=` sobre `bool` (dos valores booleanos son distintos exactamente cuando uno es verdadero y el otro falso — que es la definición de Xor), o de forma explícita con `&&`/`||`/`!`.

**Integral — aproximación numérica.** Fuera del alcance de una fórmula "exacta" (eso es cálculo simbólico), acá se pide una **aproximación numérica**: la más simple es la regla del rectángulo o del trapecio, que divide `[a,b]` en `n` particiones chicas y suma el área de cada franja.

$$\int_a^b f(x)\,dx \approx \sum_{k=0}^{n-1} f\big(a + k \cdot \Delta x\big) \cdot \Delta x, \qquad \Delta x = \frac{b-a}{n}$$

Cuantas más particiones (`n` más grande), mejor la aproximación — es un trade-off precisión/tiempo de cómputo típico de métodos numéricos.

### 🧭 Cómo encararlo

1. Para `Xor`, `Sum`, `Sum_i2`, `Prod`: escribí primero la definición matemática estándar (tablas de verdad o sumatoria/productoria en notación $\Sigma$/$\Pi$), después el caso base + caso recursivo, después el prototipo, después el `return` con ternario.
2. Para `Sum_i2`: es literalmente `Sum_f` con `f(x) = x*x` fijo — conviene implementarla en términos de `Sum_f` en vez de duplicar la recursión (reutilización, evita reescribir el mismo patrón).
3. Para `Sum_f`/`Prod_f`: primero decidí la firma con función como parámetro (`double (*f)(double)`), después la recursión es igual a `Sum`/`Prod` pero aplicando `f(i)` en vez de `i` directamente.
4. Para `Integral`: no hay "caso base/recursivo" en el mismo sentido — es un `for` (o una recursión que acumula sobre las `n` particiones) más que una fórmula por partes. Definí `n` (cantidad de particiones) como parámetro con valor por defecto razonable (ej. `1000`).
5. Asserts: para `Xor`, las 4 combinaciones de la tabla de verdad. Para `Sum`/`Prod`, casos calculables a mano (`Sum(1,5) == 15`, `Prod(1,5) == 120`) y el caso borde `i > n` (rango vacío). Para `Sum_f`/ `Prod_f`/`Integral`, usar funciones simples y conocidas (`identidad`, `cuadrado`) para poder calcular el resultado esperado a mano o con una fórmula cerrada conocida (ej. $\int_0^1 x^2\,dx = 1/3$, con tolerancia tipo `AreNear` del capítulo 7 porque es una aproximación).

### ✅ Solución completa

Este capítulo no tiene código todavía en `26-053-01/`. El libro solo lista los nombres de las siete funciones sin desarrollar el enunciado — la solución de abajo completa la especificación matemática estándar de cada una.

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* ConceptosMatematicos.cpp
>  * Implementación de conceptos matemáticos como funciones: Xor, sumatoria, productoria, integral
>  * Equipo 26-053-01
>  * 2026-09-17
>  */
> #include <cassert>
> #include <cmath>
>
> // Léxico: Xor(a,b) = verdadero si exactamente uno de los dos es verdadero
> bool Xor(bool a, bool b);
>
> // Léxico: Sum(i,n) = 0 si i > n ; i + Sum(i+1,n) si i <= n     (recursiva)
> int Sum(int i, int n);
>
> // Léxico: Sum_i2(i,n) = 0 si i > n ; i*i + Sum_i2(i+1,n) si i <= n
> int Sum_i2(int i, int n);
>
> // Léxico: Sum_f(i,n,f) = 0 si i > n ; f(i) + Sum_f(i+1,n,f) si i <= n
> double Sum_f(int i, int n, double (*f)(double));
>
> // Léxico: Prod(i,n) = 1 si i > n ; i * Prod(i+1,n) si i <= n
> int Prod(int i, int n);
>
> // Léxico: Prod_f(i,n,f) = 1 si i > n ; f(i) * Prod_f(i+1,n,f) si i <= n
> double Prod_f(int i, int n, double (*f)(double));
>
> // Léxico: Integral(a,b,f,particiones) ~= sum_{k=0}^{particiones-1} f(a + k*dx) * dx,  dx=(b-a)/particiones
> double Integral(double a, double b, double (*f)(double), int particiones = 1000);
>
> bool AreNear(double a, double b, double tolerancia = 1e-3);
>
> // Funciones auxiliares para pasar como parámetro en las pruebas
> double identidad(double x){ return x; }
> double cuadrado(double x){ return x * x; }
>
> int main(){
>   // Xor: tabla de verdad completa
>   assert(Xor(true, true) == false);
>   assert(Xor(true, false) == true);
>   assert(Xor(false, true) == true);
>   assert(Xor(false, false) == false);
>
>   // Sum / Prod
>   assert(Sum(1, 5) == 15);       // 1+2+3+4+5
>   assert(Sum(3, 3) == 3);
>   assert(Sum(5, 1) == 0);        // rango vacío
>   assert(Prod(1, 5) == 120);     // 5!
>   assert(Prod(3, 3) == 3);
>   assert(Prod(5, 1) == 1);       // rango vacío
>
>   // Sum_i2
>   assert(Sum_i2(1, 3) == 14);    // 1 + 4 + 9
>
>   // Sum_f / Prod_f con funciones pasadas por parámetro
>   assert(AreNear(Sum_f(1, 5, identidad), 15));
>   assert(AreNear(Sum_f(1, 3, cuadrado), 14));
>   assert(AreNear(Prod_f(1, 5, identidad), 120));
>
>   // Integral: aproximación conocida, con tolerancia (es numérica, no exacta)
>   assert(AreNear(Integral(0, 1, cuadrado), 1.0/3.0, 1e-3));   // integral de x^2 en [0,1] = 1/3
>   assert(AreNear(Integral(0, 2, identidad), 2.0, 1e-3));      // integral de x en [0,2] = 2
> }
>
> bool Xor(bool a, bool b){
>   return a != b;
> }
>
> int Sum(int i, int n){
>   return (i > n) ? 0 : i + Sum(i + 1, n);
> }
>
> int Sum_i2(int i, int n){
>   return (i > n) ? 0 : i * i + Sum_i2(i + 1, n);
> }
>
> double Sum_f(int i, int n, double (*f)(double)){
>   return (i > n) ? 0.0 : f(i) + Sum_f(i + 1, n, f);
> }
>
> int Prod(int i, int n){
>   return (i > n) ? 1 : i * Prod(i + 1, n);
> }
>
> double Prod_f(int i, int n, double (*f)(double)){
>   return (i > n) ? 1.0 : f(i) * Prod_f(i + 1, n, f);
> }
>
> double Integral(double a, double b, double (*f)(double), int particiones){
>   double dx = (b - a) / particiones;
>   double area = 0.0;
>   for (int k = 0; k < particiones; ++k){
>     area += f(a + k * dx) * dx;
>   }
>   return area;
> }
>
> bool AreNear(double a, double b, double tolerancia){
>   return std::abs(a - b) <= tolerancia;
> }
> ```
> `Sum_i2` está escrita como recursión propia (paralela a `Sum`) en vez de delegar en `Sum_f(i,n,cuadrado)` por simplicidad de tipos (`int` vs `double`); si querés practicar reutilización, probá reescribirla como `Sum_i2(i,n) = (int)Sum_f(i,n,cuadrado)`. `Integral` usa un `for` (no recursión) porque con miles de particiones una versión recursiva metería miles de stack frames — un `for` es la elección correcta acá, aun cuando el resto del capítulo insiste con recursividad.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué el caso base de `Sum` es `0` y el de `Prod` es `1`? Porque son los **elementos neutros** de cada operación — sumar/multiplicar por el neutro no cambia el resultado acumulado hasta ese punto.
- ¿`Sum(5, 1)` (con `i > n`) rompe algo? No — es exactamente el caso base, representa una sumatoria sobre un **rango vacío**, que por convención matemática vale `0` (y `1` para productoria).
- ¿Por qué `Integral` necesita una tolerancia en el assert y `Sum`/`Prod` no? Porque `Integral` es una **aproximación numérica** (el resultado exacto solo se alcanza en el límite de infinitas particiones); `Sum`/`Prod` son exactos porque operan sobre enteros con una fórmula cerrada.
- ¿Por qué `f` se pasa como `double (*f)(double)` y no como `double f(double)`? Porque en C++ una función no puede pasarse "por valor" como parámetro — al escribirla como parámetro, el compilador la trata automáticamente como puntero a función; escribir el `*` explícito dejar claro que es un puntero.
- ¿Podría `Xor` implementarse con el operador `!=` sobre `bool` y por qué es válido? Sí — comparar dos `bool` con `!=` da `true` exactamente cuando difieren, que es la definición exacta de Xor; es más corto que escribir `(a || b) && !(a && b)`.

---
