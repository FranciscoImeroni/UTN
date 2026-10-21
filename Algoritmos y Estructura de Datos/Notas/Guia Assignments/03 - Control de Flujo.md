⬅️ [[00 - Índice y Requisitos Generales]]

# 03 - Control de Flujo

Capítulos 13 y 14 del libro. Son los dos trabajos más cortos de todo el libro, pero son la base de todo lo que viene después: acá se fijan las herramientas de **decisión** (`if`/`else`, operador ternario) y de **repetición** (`goto`, `while`, `do while`, `for`) que en capítulos posteriores se combinan con funciones, arreglos y tipos propios.

---

## 13. Mayor de dos Números

**Objetivos:** Aplicar una estructura de control condicional para comparar dos valores y decidir cuál es el mayor.
**Temas:** Comparación de valores, sentencia condicional (`if`/`else`), operador condicional ternario.
**Problema:** *"Dado dos números informar cuál es el mayor."* (textual del libro)
**Restricciones:** El libro no impone restricciones adicionales para este trabajo puntual.
**Productos:**
- Sufijo del nombre de la carpeta: `Mayor`
- `readme.md`
- `Mayor.cpp`

### 📘 Teoría necesaria

#### `if` / `else` — sentencia condicional
Ejecuta un bloque de código **solo si** una condición (una expresión que se evalúa como `bool`) es verdadera; opcionalmente, `else` cubre el caso contrario.

```cpp
if (condición) {
    // se ejecuta si condición == true
} else {
    // se ejecuta si condición == false
}
```

Ejemplo mínimo:
```cpp
int a = 3, b = 7;
int mayor;
if (a > b) {
    mayor = a;
} else {
    mayor = b;
}
```

#### Operador condicional ternario `?:`
Es una **expresión** (no una sentencia) que vale una cosa u otra según una condición. Es la forma compacta de un `if`/`else` que solo asigna o retorna un valor.

```cpp
resultado = (condición) ? valor_si_true : valor_si_false;
```

Ejemplo mínimo:
```cpp
int a = 3, b = 7;
int mayor = (a > b) ? a : b;
```

> 💡 `if`/`else` es una **sentencia** (controla flujo, no "vale" nada). El ternario es una **expresión** (vale un valor, se puede usar directamente en una asignación, un `return`, o dentro de otra expresión). Por eso en `10-FuncionesRecursivasConOperadorCondicional/repetirFrase.cpp` se usa el ternario: la función necesita *devolver un valor*, no solo tomar una decisión.

#### Cómo aparece en este trabajo
El problema es exactamente "elegir uno de dos valores según una condición" — el caso de uso de libro de texto para el ternario. La restricción de forma general del curso (ver [[00 - Índice y Requisitos Generales]]) pide separar prototipo, `main` con `assert` y definición.

### 🧭 Cómo encararlo

1. Definí una función `Mayor(int a, int b)` que devuelva el mayor de los dos. Decidí antes qué hacer si son iguales (lo razonable: devolver cualquiera de los dos, ya que son iguales).
2. Escribí el prototipo antes de `main`.
3. En `main`, escribí varios `assert` que cubran: `a > b`, `a < b`, `a == b`, y números negativos.
4. Implementá la función debajo de `main`, con el operador ternario (o `if`/`else`, ambas formas son válidas — el ternario es más corto para este caso).

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* Mayor.cpp
>  * Dado dos números, informa cuál es el mayor.
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <cassert>
>
> using namespace std;
>
> int Mayor(int a, int b); // Prototipo
>
> int main() {
>     // Pruebas
>     assert(Mayor(3, 5) == 5);
>     assert(Mayor(5, 3) == 5);
>     assert(Mayor(4, 4) == 4);
>     assert(Mayor(-1, -5) == -1);
>     assert(Mayor(0, 0) == 0);
>
>     return 0;
> }
>
> // Definición
> int Mayor(int a, int b) {
>     return (a > b) ? a : b;
> }
> ```
> `Mayor(4, 4) == 4` prueba el caso de empate: como `a > b` es `false`, el ternario devuelve `b`, que en ese caso vale lo mismo que `a`. No hace falta un tercer caso para "iguales".

### 💡 Trampas típicas / posibles preguntas

- ¿Qué pasa si `a == b`? → No hace falta un caso especial: cualquiera de los dos sirve porque valen lo mismo.
- ¿Por qué usar el ternario y no `if`/`else` acá? → Porque la función solo tiene que *devolver* un valor; el ternario expresa eso en una línea sin variables intermedias.
- Diferencia entre sentencia y expresión: `if` no "vale" nada, el ternario sí.
- ¿Se puede anidar el ternario? → Sí (`a > b ? a : (b > c ? b : c)` para el mayor de tres), pero anidar demasiado ternarios perjudica la legibilidad — a partir de 2-3 casos conviene volver a `if`/`else`.

---

## 14. Repetición de Frase

**Objetivos:** Comparar dos estrategias de control de repetición (salto condicional vs. iteración estructurada) resolviendo el mismo problema con ambas.
**Temas:** Salto condicional (`goto`), iteración estructurada (`while`, `do while`, `for`), funciones que retornan un `string` construido incrementalmente.
**Problema:** *"Enviar una frase a la salida estándar muchas veces."* (textual del libro)
**Restricciones:** Realizar **dos versiones** del algoritmo, cada una con su propia implementación:
- Salto condicional.
- Iterativa estructurada.
- La función debe **retornar** un `string` largo (no imprimir directamente adentro del loop).
- Debe usarse alguna de: `do while`, `while`, `for`, `goto` (una para cada versión).

**Productos:**
- Sufijo del nombre de la carpeta: `Repetición`
- `readme.md` con los dos algoritmos.
- `Saltos.cpp`.
- `Iteración.cpp`.

> [!info] Ya hay un intento de este problema en el repo, pero con un enfoque distinto
> `26-053-01/10-FuncionesRecursivasConOperadorCondicional/repetirFrase.cpp` resuelve *el mismo problema* ("repetir una frase N veces y devolver el string resultante") pero con una **tercera estrategia**: recursión con operador ternario. No es ni "salto condicional" ni "iterativa estructurada", así que no reemplaza a `Saltos.cpp` ni a `Iteración.cpp` — pero es un buen punto de partida porque ya define la firma correcta (`string repetirFrase(string frase, int n)`) y el mismo set de `assert` que conviene reusar acá:
> ```cpp
> string repetirFrase(string frase, int n) {
>     string resultado = (n == 0) ? "" : (frase + repetirFrase(frase, n - 1));
>     return resultado;
> }
> ```
> Para este TP hay que adaptar la firma a las dos formas que pide la consigna (goto e iteración), sin recursión.

### 📘 Teoría necesaria

#### `goto` y etiquetas — salto condicional
`goto etiqueta;` transfiere el control de ejecución directamente a la línea marcada con `etiqueta:`, sin pasar por ninguna estructura de bucle. Combinado con un `if`, se puede armar un bucle "a mano":

```cpp
int i = 0;
inicio:
    if (i >= n) goto fin;
    // cuerpo del "bucle"
    ++i;
    goto inicio;
fin:
    // sigue acá
```

Esto es, literalmente, **cómo el compilador traduce un `while` a código de máquina** por debajo: todo bucle estructurado se puede reescribir como comparación + salto condicional + salto incondicional. La diferencia es que `goto` expone ese mecanismo en el código fuente, mientras que `while`/`for` lo esconden detrás de una sintaxis que el compilador garantiza que está bien formada (no te podés "escapar" del medio de un `for` a un lugar arbitrario del programa).

> 💡 `goto` tiene mala fama (["Goto Considered Harmful", Dijkstra, 1968]) porque permite saltos arbitrarios que hacen que el flujo de un programa grande sea imposible de seguir ("código spaghetti"). Acá se usa **a propósito**, como ejercicio didáctico, para entender qué esconden las estructuras de control "de verdad".

#### Iteración estructurada: `while`, `do while`, `for`
Las tres son formas de repetir un bloque de código; la diferencia está en **cuándo** se evalúa la condición y qué partes son "obligatorias" en la sintaxis.

```cpp
// while: evalúa la condición ANTES de cada vuelta (0 o más ejecuciones)
while (condición) {
    // cuerpo
}

// do while: evalúa la condición DESPUÉS de cada vuelta (siempre al menos 1 ejecución)
do {
    // cuerpo
} while (condición);

// for: agrupa inicialización + condición + avance en un solo lugar
for (inicialización; condición; avance) {
    // cuerpo
}
```

| | ¿Cuándo chequea la condición? | ¿Mínimo de ejecuciones? | Uso típico |
|---|---|---|---|
| `while` | Antes de cada vuelta | 0 | Repetir mientras algo sea cierto, sin contador fijo |
| `do while` | Después de cada vuelta | 1 | Cuando el cuerpo *siempre* tiene que correr al menos una vez (p. ej. pedir un dato y validar) |
| `for` | Antes de cada vuelta (como `while`) | 0 | Cuando se conoce de antemano *cuántas* veces se repite (contador) |

Un `for` con contador es exactamente equivalente a este `while`:
```cpp
for (int i = 0; i < n; ++i) { /* cuerpo */ }

// es lo mismo que:
int i = 0;
while (i < n) { /* cuerpo */ ++i; }
```

#### Construir un `string` largo dentro de un bucle
`std::string` soporta `+=` para concatenar (agregar al final):

```cpp
string resultado = "";
resultado += "Hola";   // resultado == "Hola"
resultado += "Hola";   // resultado == "HolaHola"
```

Esto es lo que reemplaza a "imprimir la frase muchas veces": en vez de `cout << frase` dentro del bucle (que ya no cumpliría la restricción de "retornar un string"), se **acumula** en una variable `string` que se devuelve al final.

#### Cómo aparecen en este trabajo
- `Saltos.cpp` implementa el bucle con `goto` + `if` (salto condicional), acumulando en un `string`.
- `Iteración.cpp` implementa el mismo bucle con `for` (o `while`/`do while`), acumulando en un `string`.
- Ambas exponen la misma firma `string repetirFrase(string frase, int n)` y pasan los mismos `assert`.

### 🧭 Cómo encararlo

1. Fijá la firma: `string repetirFrase(string frase, int n)`. Casos borde a cubrir con `assert`: `n == 0` (debe devolver `""`), `n == 1` (debe devolver la frase tal cual), `n > 1` (frase repetida `n` veces, sin separadores).
2. Para `Saltos.cpp`: declarás un acumulador `string resultado = ""` y un contador `int i = 0`; armá una etiqueta de inicio, un `if` que salte a una etiqueta de fin cuando `i >= n`, el cuerpo que hace `resultado += frase; ++i;`, y un `goto` de vuelta al inicio.
3. Para `Iteración.cpp`: mismo acumulador, pero con un `for` (o `while`) clásico que recorra `0..n-1` sumando `frase` en cada vuelta.
4. Reusá el mismo bloque de `assert` en ambos `main`, para demostrar que las dos implementaciones son observacionalmente equivalentes (mismo resultado para las mismas entradas, aunque el mecanismo de control interno sea distinto).

### ✅ Solución completa

> [!example]- `Saltos.cpp` — salto condicional con `goto` (click para expandir)
> ```cpp
> /* Saltos.cpp
>  * Repite una frase N veces y devuelve el string resultante, usando salto condicional (goto).
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <string>
> #include <cassert>
>
> using namespace std;
>
> string repetirFrase(string frase, int n); // Prototipo
>
> int main() {
>     // Pruebas
>     assert(repetirFrase("Hola", 0) == "");
>     assert(repetirFrase("UTN", 1) == "UTN");
>     assert(repetirFrase("Hola! como estas?", 2) == "Hola! como estas?Hola! como estas?");
>     assert(repetirFrase("Ab", 3) == "AbAbAb");
>
>     return 0;
> }
>
> // Definición
> string repetirFrase(string frase, int n) {
>     string resultado = "";
>     int i = 0;
>
> inicio:
>     if (i >= n) goto fin;
>     resultado += frase;
>     ++i;
>     goto inicio;
>
> fin:
>     return resultado;
> }
> ```
> El `if (i >= n) goto fin;` es la condición de salida (equivalente a la condición de un `while` escrita "al revés"): si todavía no llegué a `n` vueltas, **no** salto y sigo de largo hacia el cuerpo; si ya llegué, salto directo al `return`.

> [!example]- `Iteración.cpp` — iterativa estructurada con `for` (click para expandir)
> ```cpp
> /* Iteración.cpp
>  * Repite una frase N veces y devuelve el string resultante, usando iteración estructurada (for).
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <string>
> #include <cassert>
>
> using namespace std;
>
> string repetirFrase(string frase, int n); // Prototipo
>
> int main() {
>     // Pruebas
>     assert(repetirFrase("Hola", 0) == "");
>     assert(repetirFrase("UTN", 1) == "UTN");
>     assert(repetirFrase("Hola! como estas?", 2) == "Hola! como estas?Hola! como estas?");
>     assert(repetirFrase("Ab", 3) == "AbAbAb");
>
>     return 0;
> }
>
> // Definición
> string repetirFrase(string frase, int n) {
>     string resultado = "";
>     for (int i = 0; i < n; ++i) {
>         resultado += frase;
>     }
>     return resultado;
> }
> ```
> Notá que es **el mismo algoritmo** que `Saltos.cpp` — el `for` esconde exactamente la inicialización (`int i = 0`), condición (`i < n`) y avance (`++i`) que en `Saltos.cpp` estaban escritos a mano con `goto`.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué la consigna prohíbe imprimir directo con `cout` dentro del loop? → Porque pide que la función **retorne** el string, separando el cálculo (dominio de la solución) de la salida por pantalla — quien llama a la función decide qué hacer con el resultado (imprimirlo, guardarlo, compararlo en un `assert`).
- ¿Qué pasa si `n` es negativo? → Con la implementación de arriba, tanto `goto` (`i >= n` ya es cierto en `i=0`) como `for` (`i < n` ya es falso en `i=0`) devuelven `""` sin entrar al cuerpo. Vale la pena un `assert(repetirFrase("x", -1) == "")` para dejarlo documentado.
- ¿Es lo mismo `goto` que un bucle? → Sí, semánticamente: cualquier bucle estructurado se puede reescribir con `goto` + `if`. La diferencia es de **legibilidad y seguridad**: el compilador no puede garantizar nada sobre un `goto` arbitrario, mientras que un `while`/`for` tiene una forma fija que es más fácil de razonar.
- ¿Por qué el ejemplo de `repetirFrase.cpp` (recursivo) no cumple la consigna? → Porque ni usa `goto` ni un bucle iterativo: usa **recursión** (la función se llama a sí misma). Es una tercera estrategia de repetición, válida para otros TPs, pero no es ninguna de las dos que pide este capítulo.
- Diferencia entre `while` y `do while`: si `n == 0`, un `while` no ejecuta el cuerpo ni una vez; un `do while` sí ejecutaría una vuelta antes de chequear — para este problema, `do while` metería un bug (repetiría la frase una vez aunque `n == 0`), a menos que se agregue una guarda extra.

---
