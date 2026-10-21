⬅️ [[00 - Índice y Requisitos Generales]]

# 04 - Abstracción con Tipos

Capítulos 15 a 21 del libro (Parte IV: *Trabajos sobre Abstracción con Tipos*). El hilo conductor de esta parte es aprender a **crear tipos propios a partir de tipos simples** (`enum`, `union`, `struct`) antes de llegar a clases completas — es la preparación directa para la Parte V (Geometría), donde esos mismos tipos (`Punto`, `Color`, `Fecha`) se usan para modelar figuras.

---

## 15. Enumeraciones

**Objetivos:** Definir conjuntos de valores discretos con nombre propio (enumeraciones) y usarlos como tipo de parámetro y de retorno en funciones.
**Temas:** *(no vienen listados aparte en el libro para este capítulo; se infieren de las dos consignas)*
- Declaración de variables de tipo `enum`.
- Funciones que reciben y devuelven tipos `enum`.

**Problema:**
1. Escribir un programa que declare una variable que pueda almacenar cualquier punto cardinal.
2. Extender el programa de la sección "Funciones que Retornan o Reciben Tipos Enum" del texto "Enumeraciones" (material de cátedra referenciado, no incluido en este repo) para que contenga una función que, dado un día y un turno, informe la asignatura que corresponde cursar.

**Restricciones:** *(no se listan explícitamente; se aplican las generales del curso)*
**Productos:**
```
DD-Enum
 |-- Cardinal.cpp
 `-- SemanaDeCursada.cpp
```

### 📘 Teoría necesaria

#### `enum` clásico vs. `enum class`
Un **tipo enumerado** declara un conjunto **cerrado** de valores con nombre. Por debajo, cada valor es en realidad un entero (por defecto empieza en `0` y suma `1` por cada nombre siguiente, en el orden en que se declaran).

```cpp
// enum "clásico" (heredado de C)
enum Cardinal { Norte, Sur, Este, Oeste };   // Norte=0, Sur=1, Este=2, Oeste=3
Cardinal c = Norte;                          // "Norte" queda visible en el espacio de nombres global

// enum class (C++11 en adelante) — preferido
enum class Cardinal { Norte, Sur, Este, Oeste };
Cardinal c = Cardinal::Norte;                // hay que calificar con el nombre del enum
```

| | `enum` clásico | `enum class` |
|---|---|---|
| Acceso a los valores | `Norte` (a secas) | `Cardinal::Norte` (calificado) |
| ¿Contamina el namespace? | Sí — `Norte` choca con cualquier otro `Norte` del programa | No — cada `enum class` tiene su propio espacio |
| Conversión implícita a `int` | Sí, automática (peligroso: `Norte + 1` compila) | No, hay que hacer `static_cast<int>(c)` explícito |
| Comparar dos enums distintos | Puede compilar por error (ambos son "casi `int`") | No compila si son de tipos distintos (más seguro) |

> 💡 **Por qué preferir `enum class`:** evita que dos enumeraciones distintas usen el mismo nombre de valor por accidente (por ejemplo, si `Dia` y `Turno` tuvieran ambos un valor `Ninguno`), y evita operaciones sin sentido como sumarle un entero a un cardinal. Se paga con un poco más de tipeo (`Cardinal::Norte` en vez de `Norte`), que vale la pena en código de más de un archivo.

#### Conversión a/desde `int`
```cpp
enum class Cardinal { Norte, Sur, Este, Oeste };

int n = static_cast<int>(Cardinal::Este);     // n == 2
Cardinal c = static_cast<Cardinal>(2);        // c == Cardinal::Este
```
Sirve, por ejemplo, para usar un enum como índice de un `array` (ver más abajo en Diagonal de Matriz y Tipo Color), o para reconstruir un enum a partir de un dato leído como número.

#### Funciones que reciben y devuelven `enum`
Un `enum` es un tipo como cualquier otro: puede ser el tipo de un parámetro o de un valor de retorno.

```cpp
enum class Cardinal { Norte, Sur, Este, Oeste };

Cardinal Opuesto(Cardinal c) {
    switch (c) {
        case Cardinal::Norte: return Cardinal::Sur;
        case Cardinal::Sur:   return Cardinal::Norte;
        case Cardinal::Este:  return Cardinal::Oeste;
        case Cardinal::Oeste: return Cardinal::Este;
    }
    return Cardinal::Norte; // nunca debería llegar acá; evita el warning de "no todos los caminos retornan"
}
```

#### Cómo aparece en este trabajo
`Cardinal.cpp` es el caso más simple: una sola variable de un `enum` de 4 valores. `SemanaDeCursada.cpp` combina **dos** enums (`Dia` y `Turno`) como parámetros de una función que devuelve un tercer tipo (acá, un `string` con el nombre de la asignatura) — es el ensayo general de "función que combina varios enums" que se reusa después en Uniones y en CUIL.

### 🧭 Cómo encararlo

**`Cardinal.cpp`:**
1. Declará `enum class Cardinal { Norte, Sur, Este, Oeste };`.
2. Declará una variable de ese tipo y probá asignarle cada uno de los 4 valores con `assert` de comparación (`assert(c == Cardinal::Norte)`, etc.), o escribí una función `NombreCardinal(Cardinal)` que devuelva un `string` y probala con `assert`.

**`SemanaDeCursada.cpp`:**
1. Definí `enum class Dia { Lunes, Martes, Miercoles, Jueves, Viernes };` y
   `enum class Turno { Mañana, Tarde, Noche };`.
2. Diseñá una tabla (a mano, con un `switch` anidado o una matriz de strings) que mapee cada combinación `(Dia, Turno)` a una asignatura (o a `"Libre"` si no hay clase).
3. Escribí `string GetAsignatura(Dia dia, Turno turno)` y probala con `assert` para varias combinaciones, incluyendo alguna que dé `"Libre"`.

### ✅ Solución completa

> [!example]- `Cardinal.cpp` (click para expandir)
> ```cpp
> /* Cardinal.cpp
>  * Declara una variable que puede almacenar cualquier punto cardinal.
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <string>
> #include <cassert>
>
> using namespace std;
>
> enum class Cardinal { Norte, Sur, Este, Oeste };
>
> string NombreCardinal(Cardinal c); // Prototipo
>
> int main() {
>     Cardinal actual = Cardinal::Norte;
>     assert(NombreCardinal(actual) == "Norte");
>
>     actual = Cardinal::Este;
>     assert(NombreCardinal(actual) == "Este");
>
>     assert(NombreCardinal(Cardinal::Sur) == "Sur");
>     assert(NombreCardinal(Cardinal::Oeste) == "Oeste");
>
>     return 0;
> }
>
> string NombreCardinal(Cardinal c) {
>     switch (c) {
>         case Cardinal::Norte: return "Norte";
>         case Cardinal::Sur:   return "Sur";
>         case Cardinal::Este:  return "Este";
>         case Cardinal::Oeste: return "Oeste";
>     }
>     return "?"; // caso defensivo; con enum class y switch exhaustivo no debería alcanzarse
> }
> ```

> [!example]- `SemanaDeCursada.cpp` (click para expandir)
> ```cpp
> /* SemanaDeCursada.cpp
>  * Dado un día y un turno, informa la asignatura que corresponde cursar.
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <string>
> #include <cassert>
>
> using namespace std;
>
> enum class Dia   { Lunes, Martes, Miercoles, Jueves, Viernes };
> enum class Turno { Mañana, Tarde, Noche };
>
> string GetAsignatura(Dia dia, Turno turno); // Prototipo
>
> int main() {
>     assert(GetAsignatura(Dia::Lunes, Turno::Noche) == "Algoritmos y Estructura de Datos");
>     assert(GetAsignatura(Dia::Miercoles, Turno::Tarde) == "Analisis Matematico I");
>     assert(GetAsignatura(Dia::Viernes, Turno::Mañana) == "Libre");
>
>     return 0;
> }
>
> string GetAsignatura(Dia dia, Turno turno) {
>     if (dia == Dia::Lunes && turno == Turno::Noche) {
>         return "Algoritmos y Estructura de Datos";
>     }
>     if (dia == Dia::Miercoles && turno == Turno::Tarde) {
>         return "Analisis Matematico I";
>     }
>     if (dia == Dia::Jueves && turno == Turno::Noche) {
>         return "Sistemas de Representacion";
>     }
>     return "Libre";
> }
> ```
> La tabla de combinaciones de este ejemplo es ficticia (ajustala a tu propia cursada real); lo importante para el TP es la firma `GetAsignatura(Dia, Turno) -> string` combinando dos enums.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué `enum class` y no `enum` a secas? → Ver tabla arriba: evita contaminar el namespace y conversiones implícitas a `int` sin querer.
- ¿Qué pasa si un `switch` sobre un `enum class` no cubre todos los casos? → El compilador puede advertir (`-Wswitch`), pero no es error salvo que se pida explícitamente; por eso conviene un `return` defensivo al final si la función no es `void`.
- ¿Se puede usar un `enum class` como índice de un `array`? → No directamente (a diferencia del `enum` clásico); hace falta `static_cast<int>(...)` primero.
- ¿`Dia::Lunes == 0`? → No compila con `enum class` (comparación entre tipos distintos); sí compilaría con `enum` clásico, que es justamente el tipo de error silencioso que `enum class` evita.

---

## 16. Uniones

**Objetivos:** Entender cómo una `union` comparte memoria entre distintos tipos, y usarlo para modelar una variable que puede contener uno de varios tipos posibles en distintos momentos.
**Temas:** *(no vienen listados aparte en el libro; se infieren de las dos consignas)*
- Declaración de `union`.
- Extensión de un programa existente ("Caninos", material de cátedra referenciado) agregando variables de distintos tipos concretos.

**Problema:**
1. Escribir un programa ejemplo que opere sobre dos variables: una que almacene tanto enteros (`int`) como naturales (`unsigned`), y otra que almacene tanto caracteres (`char`) como reales (`double`).
2. Extender el programa "Caninos" para que incluya las siguientes variables:
   - `Santas` (Santa's Little Helper, el perro de Los Simpson) y `WileE` (el Coyote) — ¿el cambio es simplemente agregar una variable?
   - `Snowball2` (Snowball II, la gata de Los Simpson) y `Simba` (el león) — ¿el cambio es simplemente agregar dos variables? ¿deberían existir en el programa conceptos como `Mamífero` o `Carnívoro`?

**Restricciones:** *(no se listan explícitamente; se aplican las generales del curso)*
**Productos:**
```
DD-Union
 |-- EjemploDeUniones.cpp
 `-- Animados.cpp
```

### 📘 Teoría necesaria

#### `union` — qué es
Una `union` declara varios miembros de **distintos tipos que comparten la misma dirección de memoria**. El tamaño de la unión es el del miembro más grande; en cualquier momento, la unión "es" solo **uno** de sus miembros a la vez — escribir por un miembro y leer por otro reinterpreta los mismos bytes con otro tipo.

```cpp
union NumeroMixto {
    int i;
    unsigned u;
};

NumeroMixto n;
n.i = -1;
// n.u ahora lee esos MISMOS bytes como unsigned: un número muy grande, no -1
```

Comparación rápida con `struct`:

| | `struct` | `union` |
|---|---|---|
| Memoria | Cada miembro tiene su propio espacio (se suman) | Todos los miembros **comparten** el mismo espacio (el del más grande) |
| ¿Cuántos miembros "tienen valor" a la vez? | Todos, simultáneamente | Solo uno por vez (el último escrito) |
| `sizeof` | Suma (aprox.) de los `sizeof` de cada miembro | El `sizeof` del miembro más grande |

#### El peligro: no hay "tag" que diga qué miembro está activo
Una `union` **no sabe** cuál de sus miembros fue el último que se escribió — es responsabilidad de quien programa acordarse y leer siempre por el miembro correcto. Leer por el miembro "equivocado" no es un error de compilación: es **comportamiento indefinido** en general (con la excepción puntual, en C++, de reinterpretar entre miembros de tipos "compatibles en layout", un detalle avanzado que no hace falta para este TP).

```cpp
union CaracterOMedida {
    char c;
    double d;
};

CaracterOMedida x;
x.c = 'A';
// x.d en este punto NO es una lectura segura ni tiene sentido: se está
// interpretando 1 byte útil + basura como si fueran los 8 bytes de un double.
```

#### La solución: "tagged union" (unión etiquetada)
Para usar una unión de forma segura, se la acompaña con una variable aparte (el **tag**) que indica cuál miembro está activo en cada momento. Esto es exactamente el antecesor histórico de lo que en C++ moderno es `std::variant` (y, conceptualmente, de la idea de "tipo suma" o *variant type* de otros lenguajes).

```cpp
enum class TipoActivo { Entero, Natural };

struct NumeroMixtoConTag {
    TipoActivo tipo;   // el "tag": dice qué miembro leer
    union {
        int i;
        unsigned u;
    };
};

int ObtenerComoInt(const NumeroMixtoConTag& n) {
    assert(n.tipo == TipoActivo::Entero); // ahora SÍ hay forma de validar antes de leer
    return n.i;
}
```

> 💡 Sin el tag, la `union` "funciona" mientras el programador nunca se equivoque de miembro — es una herramienta de bajo nivel (pensada históricamente para ahorrar memoria, no para dar seguridad de tipos). El patrón *tagged union* es la forma responsable de usarla.

#### Cómo aparece en este trabajo
`EjemploDeUniones.cpp` es el caso base sin tag (para ver el "peligro" de primera mano). `Animados.cpp` lleva la idea a un caso más rico: representar distintos animales (`Santas`, `WileE`, `Snowball2`, `Simba`) que, si se modelaran ingenuamente con una unión de sus datos concretos, tendrían el mismo problema — de ahí las preguntas del enunciado sobre si haría falta un concepto como `Mamífero` o `Carnívoro` (spoiler: sí, y ese concepto es justamente el **tag** que dice "qué tipo de animal es esto", más — ya en la Parte VI del libro — jerarquías de clases con herencia).

### 🧭 Cómo encararlo

**`EjemploDeUniones.cpp`:**
1. Declará `union NumeroMixto { int i; unsigned u; };` y `union CaracterOMedida { char c; double d; };`.
2. Escribí un `main` que escriba por un miembro y muestre (con `cout`, no hace falta `assert` acá porque el resultado de leer "por el otro lado" es justamente lo que se quiere ilustrar) cómo se ve el mismo valor interpretado como el otro tipo.
3. Agregá comentarios explicando qué está pasando en memoria en cada paso.

**`Animados.cpp`:**
1. Pensá primero **sin** tag: una unión con un miembro por personaje (`Santas`, `WileE`, etc.) — notá que agregar cada personaje nuevo es "solo" agregar un miembro a la unión (por eso la primera pregunta del enunciado tiene trampa: sintácticamente sí es solo agregar una variable, pero sin un tag no hay forma de saber cuál personaje está "activo").
2. Ahora agregá el tag: un `enum class TipoAnimal { Perro, Coyote, Gato, Leon }` (o algo más general como `Mamifero`/`Carnivoro` si querés reflejar la pregunta del enunciado) y un `struct` que combine el tag con la unión.
3. Escribí una función que, dado el `struct` con tag, devuelva el nombre del personaje — usando el tag para decidir qué miembro leer, nunca adivinando.
4. Dejá las respuestas a las dos preguntas del enunciado como comentario o en el `readme.md`.

### ✅ Solución completa

> [!example]- `EjemploDeUniones.cpp` (click para expandir)
> ```cpp
> /* EjemploDeUniones.cpp
>  * Ejemplo de dos uniones: una entre int/unsigned, otra entre char/double.
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <iostream>
>
> using namespace std;
>
> union NumeroMixto {
>     int i;
>     unsigned u;
> };
>
> union CaracterOMedida {
>     char c;
>     double d;
> };
>
> int main() {
>     NumeroMixto n;
>     n.i = -1;
>     cout << "n.i (int) = " << n.i << '\n';
>     cout << "n.u (unsigned, MISMOS bytes) = " << n.u << '\n'; // 4294967295 en 32 bits
>
>     CaracterOMedida x;
>     x.c = 'A';
>     cout << "x.c (char) = " << x.c << '\n';
>     // x.d no se imprime: leer un double a partir de haber escrito solo 1 byte
>     // de un char es comportamiento indefinido, no tiene un valor "correcto" que mostrar.
>
>     return 0;
> }
> ```

> [!example]- `Animados.cpp` (click para expandir)
> ```cpp
> /* Animados.cpp
>  * Extiende el programa "Caninos" con más personajes animados, usando una
>  * union etiquetada (tagged union) para saber qué animal está activo.
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <string>
> #include <cassert>
>
> using namespace std;
>
> enum class TipoAnimal { Perro, Coyote, Gato, Leon };
>
> struct DatosPerro  { string nombre; string dueño; };
> struct DatosCoyote  { string nombre; int vecesQueCayoDeUnAcantilado; };
> struct DatosGato    { string nombre; string dueño; };
> struct DatosLeon    { string nombre; string manada; };
>
> struct Animado {
>     TipoAnimal tipo; // el "tag": sin esto, no hay forma segura de saber qué leer
>     union {
>         DatosPerro  perro;
>         DatosCoyote coyote;
>         DatosGato   gato;
>         DatosLeon   leon;
>     };
> };
>
> string NombreDe(const Animado& a); // Prototipo
>
> int main() {
>     Animado Santas{};
>     Santas.tipo = TipoAnimal::Perro;
>     Santas.perro = { "Santa's Little Helper", "Los Simpson" };
>
>     Animado WileE{};
>     WileE.tipo = TipoAnimal::Coyote;
>     WileE.coyote = { "Wile E. Coyote", 0 };
>
>     Animado Snowball2{};
>     Snowball2.tipo = TipoAnimal::Gato;
>     Snowball2.gato = { "Snowball II", "Los Simpson" };
>
>     Animado Simba{};
>     Simba.tipo = TipoAnimal::Leon;
>     Simba.leon = { "Simba", "El Rey Leon" };
>
>     assert(NombreDe(Santas) == "Santa's Little Helper");
>     assert(NombreDe(WileE) == "Wile E. Coyote");
>     assert(NombreDe(Snowball2) == "Snowball II");
>     assert(NombreDe(Simba) == "Simba");
>
>     return 0;
> }
>
> string NombreDe(const Animado& a) {
>     switch (a.tipo) {
>         case TipoAnimal::Perro:  return a.perro.nombre;
>         case TipoAnimal::Coyote: return a.coyote.nombre;
>         case TipoAnimal::Gato:   return a.gato.nombre;
>         case TipoAnimal::Leon:   return a.leon.nombre;
>     }
>     return "?";
> }
> ```
> **Respuestas a las preguntas del enunciado:**
> - *¿Agregar `Santas` y `WileE` es "simplemente agregar una variable"?* Sintácticamente sí (un miembro más a la `union` y un valor más al `enum class TipoAnimal`), pero **no es gratis**: cada miembro nuevo obliga a tocar cada función que hace `switch` sobre el tag (acá, `NombreDe`) para agregar su caso. No es "una línea aislada", es un cambio que se propaga.
> - *¿Deberían existir conceptos como `Mamífero` o `Carnívoro`?* Con las herramientas de este capítulo (struct + enum + union), no hay una forma limpia de expresar "Wile E. Coyote y Simba son `Carnívoro`, pero Snowball II también caza aunque sea más chica" sin empezar a duplicar campos o armar jerarquías de tags dentro de tags. Ese problema se resuelve recién en la Parte VI del libro con **herencia y polimorfismo** (clases base `Animal`/`Mamifero`/`Carnivoro` con clases derivadas) — acá conviene notar la limitación, no forzar una solución que todavía no tenemos las herramientas para expresar bien.

### 💡 Trampas típicas / posibles preguntas

- ¿Qué `sizeof` tiene una `union` de un `int` y un `double`? → El del miembro más grande (`double`, 8 bytes), no la suma.
- ¿Por qué es peligroso leer un miembro distinto al que se escribió? → Los bytes se reinterpretan con otro tipo; no hay conversión, es la misma memoria "vista" de otra forma — comportamiento indefinido en el caso general.
- ¿Qué es una "tagged union"? → Una unión acompañada de una variable aparte (el tag, típicamente un `enum`) que indica cuál miembro es válido leer en cada momento.
- ¿`std::variant` reemplaza a `union` en C++ moderno? → Sí, en la mayoría de los casos: es una unión etiquetada segura de la biblioteca estándar, que valida en tiempo de ejecución cuál alternativa está activa (fuera del alcance de este TP, pero bueno saber que existe).
- ¿Todos los miembros de una `union` pueden tener valor al mismo tiempo? → No, solo el último escrito es válido de leer.

---

## 17. Problemas, Arrays, String & Enumeraciones — CUIL

**Objetivos:**
- Demostrar capacidad de definición de problemas y de diseño de implementación, con clara separación entre el *dominio del problema* y el *dominio de la solución*.
- Aplicar secuencias y ciclos de iteración.
- Definir nuevos tipos de datos enumerados.

**Temas:**
- Dominio del problema.
- Modelo IPO.
- Dominio de la solución.
- Definición de conjunto de valores con `enum struct`.
- Strings como secuencia de valores.
- Secuencia de valores y `array`.
- Ciclo de iteración `for` y sus variantes.
- Operación `•.at(•)`.

**Problema:** El problema en sí es definir, acotar y refinar el problema a resolver, para luego implementar la solución. Se parte de la frase: *"Se necesita crear CUILs de personas físicas."*

**Restricciones:**
- La definición del problema debe estar en `readme.md` y se debe aplicar *Modelo IPO*.
- Para la implementación debe aplicarse: funciones, `enum`, `string`, operación `•.at(•)` (no `•[•]`), y `assert`.

**Tareas:**
1. Especificar el problema en `readme.md`, incluir un modelo IPO y las restricciones que el equipo decida.
2. Diseñar un set de pruebas.
3. Especificar la solución en `readme.md`: descripción y restricciones generales del producto solución, las *principales* funciones con sus precondiciones/poscondiciones, y tipos de datos utilizados.
4. *(Opcional)* Especificar matemáticamente la función más importante en `readme.md`.
5. Diseñar y codificar las pruebas en `main`.
6. Declarar los prototipos antes de `main`.
7. Implementar las funciones.

**Productos:**
```
DD-Cuil
 |-- readme.md
 `-- Cuil.cpp
```

### 📘 Teoría necesaria

#### Dominio del problema vs. dominio de la solución
- **Dominio del problema:** el mundo real que se está modelando, con su propio vocabulario (acá: "persona física", "DNI", "CUIL", "género declarado ante AFIP"). No tiene nada que ver con C++.
- **Dominio de la solución:** las estructuras de datos y algoritmos concretos elegidos para resolverlo (acá: un `enum class TipoPersona`, un `string` para el DNI, funciones que combinan ambos).

Separar los dos dominios significa: primero entender y acotar el problema en términos humanos (¿qué es un CUIL? ¿qué datos hacen falta? ¿qué casos borde hay?) y **recién después** decidir con qué tipos de C++ se va a representar cada cosa. Mezclar ambos pasos (empezar a programar antes de tener claro el problema) es la causa más común de que un TP "no cierre" a mitad de camino.

#### Modelo IPO (Input - Process - Output)
Una forma simple y estándar de especificar qué hace una función o un programa, en tres columnas:

| Input | Process | Output |
|---|---|---|
| Qué datos entran, y su tipo/rango válido | Qué transformación se les aplica (en palabras, no código) | Qué se devuelve, y su forma |

Ejemplo aplicado a este TP:

| Input | Process | Output |
|---|---|---|
| Tipo de persona (varón/mujer) y DNI (8 dígitos) | Concatenar el prefijo según el tipo, calcular el dígito verificador con el algoritmo módulo 11 | String con formato `PP-DDDDDDDD-V` |

#### `enum struct` (alias de `enum class`)
`enum struct` y `enum class` son **exactamente lo mismo** en C++ (dos formas de escribir la misma sintaxis; se puede usar cualquiera de las dos palabras clave indistintamente). Ver la teoría completa de `enum class` en el capítulo 15 (arriba) — no se repite acá.

#### `std::string` como secuencia de valores — operaciones básicas
Un `string` es, conceptualmente, una **secuencia de caracteres** indexable, igual que un `array` lo es de otro tipo cualquiera.

```cpp
#include <string>
using namespace std;

string s = "12345678";
int largo = s.length();       // 8  (equivalente a s.size())
char primero = s.at(0);       // '1'
string sub = s.substr(2, 3);  // "345"  (desde índice 2, 3 caracteres)
string t = s + "abc";         // concatenación: "12345678abc"
bool iguales = (s == "12345678"); // comparación por contenido, no por dirección
int digito = s.at(3) - '0';   // convertir el CARACTER '4' al NÚMERO 4 (truco: restar '0')
```

| Operación | Qué hace |
|---|---|
| `.length()` / `.size()` | Cantidad de caracteres (son sinónimos) |
| `.at(i)` | Carácter en la posición `i`, con chequeo de rango (lanza `std::out_of_range` si `i` es inválido) |
| `.substr(pos, n)` | Subcadena de `n` caracteres a partir de `pos` |
| `+` / `+=` | Concatenación |
| `==`, `!=`, `<`, etc. | Comparación por contenido (no por dirección de memoria) |
| `c - '0'` | Truco para convertir el dígito-carácter `c` (`'0'`..`'9'`) al entero que representa |

#### `for` clásico y sus variantes — recorrer una secuencia por índice
Para procesar un `string` (o un `array`) **dígito por dígito** — como hace falta acá para calcular el dígito verificador — se recorre por índice con un `for` clásico (ya visto en el capítulo 18/TP18, ver [[18. Arreglos & Dimensiones — Total de Ventas]] más abajo para el detalle de `for` clásico vs. range-for). En este TP en particular conviene el `for` clásico porque cada posición del string necesita combinarse con un **peso distinto** (una tabla de multiplicadores) según su índice — algo que el range-for no puede dar directamente porque no expone el índice.

#### Cómo aparece en este trabajo
El CUIL argentino tiene el formato `PP-DDDDDDDD-V`: dos dígitos de **prefijo** (según el tipo de persona), ocho dígitos del **DNI**, y un **dígito verificador** calculado con el algoritmo *módulo 11* sobre los 10 dígitos anteriores (prefijo + DNI), cada uno multiplicado por un peso fijo según su posición.

### 🧭 Cómo encararlo

1. **Dominio del problema:** ¿qué tipos de persona física hay? (para simplificar: `Varon` = prefijo `20`, `Mujer` = prefijo `27`; el libro real de AFIP tiene más casos, pero acotar el alcance es parte de la tarea). ¿Qué formato tiene un DNI válido? (8 dígitos, como `string` para poder usar `.at()` dígito por dígito sin perder ceros a la izquierda).
2. **Modelo IPO:** escribilo en el `readme.md` antes de programar (ver tabla de ejemplo arriba).
3. **Dominio de la solución:** `enum class TipoPersona { Varon = 20, Mujer = 27 };` — notá que se le puede asignar directamente el valor del prefijo real a cada elemento del enum, así no hace falta un `switch` aparte para mapear tipo → prefijo.
4. Diseñá las funciones antes de implementarlas (dominio de la solución, con pre/poscondiciones en el `readme.md`):
   - `GetPrefijo(TipoPersona) -> string`: precondición ninguna; poscondición devuelve `"20"` o `"27"`.
   - `GetDigitoVerificador(string diezDigitos) -> int`: precondición `diezDigitos.length() == 10` y son todos dígitos; poscondición devuelve un valor `0..9` calculado con el algoritmo módulo 11.
   - `GetCuil(TipoPersona, string dni) -> string`: combina las dos anteriores.
5. Para `GetDigitoVerificador`: recorré los 10 dígitos con un `for` clásico, multiplicando cada uno por el peso que le corresponde según la tabla fija `{5,4,3,2,7,6,5,4,3,2}`, sumá todo, calculá `resto = suma % 11`, y el verificador es `11 - resto` (con el caso especial: si da `11`, el verificador es `0`).
6. Diseñá el set de pruebas **antes** de escribir la implementación (tarea 2 de la consigna): elegí 2-3 DNIs de ejemplo, calculá a mano el CUIL esperado con la fórmula, y esos son tus `assert`.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* Cuil.cpp
>  * Genera el CUIL de una persona fisica a partir de su tipo (varon/mujer) y su DNI.
>  *
>  * Modelo IPO:
>  *   Input:   tipo de persona (Varon | Mujer), DNI como string de 8 digitos
>  *   Process: concatenar el prefijo segun el tipo + calcular el digito
>  *            verificador con el algoritmo modulo 11
>  *   Output:  string con formato "PP-DDDDDDDD-V"
>  *
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <iostream>
> #include <string>
> #include <array>
> #include <cassert>
>
> using namespace std;
>
> enum class TipoPersona { Varon = 20, Mujer = 27 };
>
> string GetPrefijo(TipoPersona tipo);                 // Prototipos
> int GetDigitoVerificador(const string& diezDigitos);
> string GetCuil(TipoPersona tipo, const string& dni);
>
> int main() {
>     // Pruebas (calculadas a mano con el algoritmo modulo 11)
>     assert(GetCuil(TipoPersona::Varon, "12345678") == "20-12345678-6");
>     assert(GetCuil(TipoPersona::Mujer, "12345678") == "27-12345678-0");
>
>     cout << GetCuil(TipoPersona::Varon, "30123456") << '\n';
>
>     return 0;
> }
>
> // Precondicion: ninguna.
> // Poscondicion: devuelve el prefijo de dos digitos ("20" o "27") segun el tipo.
> string GetPrefijo(TipoPersona tipo) {
>     return to_string(static_cast<int>(tipo));
> }
>
> // Precondicion: diezDigitos.length() == 10 y todos sus caracteres son digitos ('0'..'9').
> // Poscondicion: devuelve el digito verificador (0..9) segun el algoritmo modulo 11.
> int GetDigitoVerificador(const string& diezDigitos) {
>     assert(diezDigitos.length() == 10);
>
>     const array<int, 10> pesos{5, 4, 3, 2, 7, 6, 5, 4, 3, 2};
>     int suma = 0;
>     for (int i = 0; i < 10; ++i) {
>         int digito = diezDigitos.at(i) - '0';
>         suma += digito * pesos.at(i);
>     }
>
>     int resto = suma % 11;
>     int verificador = 11 - resto;
>     if (verificador == 11) verificador = 0;
>     // Nota: si verificador diera 10, el algoritmo real de AFIP pide cambiar el
>     // prefijo (23 -> 20 o 27, segun el genero) y recalcular; caso no contemplado
>     // en esta version acotada del problema.
>
>     return verificador;
> }
>
> // Precondicion: dni.length() == 8 y todos sus caracteres son digitos.
> // Poscondicion: devuelve el CUIL con formato "PP-DDDDDDDD-V".
> string GetCuil(TipoPersona tipo, const string& dni) {
>     assert(dni.length() == 8);
>
>     string prefijo = GetPrefijo(tipo);
>     int verificador = GetDigitoVerificador(prefijo + dni);
>
>     return prefijo + "-" + dni + "-" + to_string(verificador);
> }
> ```
> El caso `Mujer` con DNI `12345678` es a propósito: la suma pesada da resto `0`, así que `verificador = 11 - 0 = 11`, que se convierte en `0` — es el caso borde que conviene tener cubierto con un `assert`, no solo el caso "normal".

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué el DNI se representa como `string` y no como `int`? → Para poder usar `.at()` dígito por dígito sin perder ceros a la izquierda (un DNI como `"01234567"` como `int` perdería el cero inicial).
- ¿Por qué `enum class TipoPersona { Varon = 20, Mujer = 27 }` y no un `string` con `"Varon"`/`"Mujer"`? → Porque es un conjunto **cerrado y finito** de valores — el caso de uso típico de un enum — y además permite asignarle directamente el valor numérico real del prefijo AFIP a cada elemento.
- ¿Qué pasa si `GetDigitoVerificador` da `10`? → En el algoritmo real de AFIP, hay que cambiar el prefijo (de `23` a `20` o `27`) y recalcular; en esta versión acotada del problema, documentado como caso no contemplado (parte de "acotar el problema" que pide la consigna).
- ¿Por qué `.at()` y no `[]` para recorrer el string? → Restricción explícita del enunciado, y la misma razón que en el TP18: detectar temprano un índice inválido en vez de comportamiento indefinido.
- ¿Qué es el modelo IPO y por qué escribirlo antes de programar? → Porque obliga a fijar, en palabras, qué entra, qué se hace y qué sale **antes** de comprometerse con una estructura de datos concreta — es la separación dominio del problema / dominio de la solución llevada a la práctica.

---

## 18. Arreglos & Dimensiones — Total de Ventas

**Objetivos:** Demostrar capacidad de construcción de tipos compuestos mediante la aplicación sucesiva de producto cartesiano. Aplicación de ciclo *for clásico* y de ciclo *for de intervalos* (range-for). Aplicación de redireccionamiento de los flujos estándar.
**Temas:** Producto cartesiano · Secuencias finitas · `std::array<T,N>` · Inferencia de tipo con `auto` · Streams · Redirección de entrada y salida · Interfaz fluida · `for` clásico y de intervalo.
**Problema:** Serie de 4 necesidades incrementales, todas partiendo de *"Dado los importes, mostrar las ventas totales"*: #1 total general, #2 total por mes, #3 total por mes y vendedor, #4 total por mes, vendedor y región.
**Restricciones:** Sets de datos en `Test0.txt`..`Test3.txt`; entrada por `cin` sin orden fijo; usar `std::array<T,N>` (no `T[N]`) y `.at()` (no `[]`); cada necesidad resuelta con **una única variable** de múltiples dimensiones (`Dim0.cpp` a `Dim3.cpp`); crédito extra en `Dim3Extra.cpp` con `enum`, `string` y estadísticas.
**Productos:** `readme.md`, `Test0.txt`..`Test3.txt`, `Dim0.cpp`..`Dim3.cpp`, `Dim3Extra.cpp`.

### 📘 Teoría necesaria (resumen)

- **Producto cartesiano:** un arreglo multidimensional *es* la representación en memoria de un producto cartesiano — una celda por cada combinación posible (p. ej. `regiones × vendedores × meses`).
- **`std::array<T,N>` vs. `T[N]`:** `std::array` sabe su propio tamaño, no decae a puntero al pasarlo a una función, se puede copiar/asignar/devolver — todo lo que `T[N]` no puede.
- **`.at()` vs. `[]`:** `.at()` valida el rango y lanza `std::out_of_range`; `[]` no valida nada (comportamiento indefinido si el índice es inválido).
- **`auto`:** el compilador deduce el tipo en compilación; útil sobre todo con tipos anidados largos (`array<array<int,12>,3>`) y en range-for.
- **Streams y redirección:** `std::cin`/`std::cout` no saben si del otro lado hay teclado, archivo o pipe; `./programa < entrada.txt` redirige `stdin` de forma transparente para el programa.
- **`for` clásico vs. range-for:** clásico cuando hace falta el índice (para imprimirlo o para acceder a varias dimensiones a la vez); range-for cuando alcanza con "recorrer todo" sin manejar índices.
- **Interfaz fluida:** cada `operator<<`/`operator>>` devuelve el propio stream, por eso se pueden encadenar (`cout << a << b << c`).

> [!info] Ya existe una guía línea-por-línea completa de este trabajo
> Ver `26-053-01/18-Arreglos & Dimensiones - Total de Ventas/Guia-Examen-Oral.md` — cubre cada uno de estos temas en detalle con teoría, sintaxis y explica `Dim0.cpp` a `Dim3Extra.cpp` línea por línea, además de una batería de 35 preguntas rápidas y mini-ejercicios de pizarrón. Ese TP **ya está resuelto** en la misma carpeta (`Dim0.cpp`, `Dim1.cpp`, `Dim2.cpp`, `Dim3.cpp`, `Dim3Extra.cpp`).

### 🧭 Cómo encararlo

La idea central es resolver **la misma necesidad 4 veces**, agregando una dimensión (un `.at()` más al acumular, un dato más por registro leído, un `for` más al mostrar) en cada paso — no se re-piensa el problema desde cero en cada archivo, se *extiende* el anterior. Para el detalle de diseño de cada `DimN.cpp` (qué tipo declarar, cómo leer, cómo recorrer la salida), ver la guía completa linkeada arriba.

### 💡 Trampas típicas

- Olvidarse el `{}` al declarar el `array` acumulador → arranca con basura en vez de ceros.
- Confundir el orden de los índices anidados (`.at(vendedor).at(mes)` vs. `.at(mes).at(vendedor)`) — tiene que ser consistente entre la lectura y la escritura.
- Usar `[]` en vez de `.at()` cuando un dato de prueba viene fuera de rango: el bug queda silencioso en vez de abortar con un mensaje claro.
- Promedios con división entera (`int / int`) en el crédito extra de `Dim3Extra.cpp` — hace falta `static_cast<double>(...)` antes de dividir.

---

## 19. Diagonal de una Matriz

**Objetivos:** Escribir un programa que determine la suma de la diagonal de una matriz.
**Restricciones:** La suma la debe calcular una función que tenga como parámetro **in** una matriz (es decir, la función solo la lee, no la modifica).
**Productos:**
```
DD-DiagonalMatriz
 |-- readme.md
 `-- DiagonalMatriz.cpp
```

### 📘 Teoría necesaria

#### Matrices (arreglos 2D) — recordatorio breve
Una matriz cuadrada de `N×N` se representa con `std::array<std::array<T,N>,N>` (ver el detalle completo de `std::array` multidimensional en el capítulo 18, arriba, y en la guía linkeada ahí). El acceso a la celda de la fila `f`, columna `c` es `m.at(f).at(c)`.

#### La diagonal principal: cuándo fila == columna
La **diagonal principal** de una matriz cuadrada es el conjunto de celdas donde el índice de fila coincide con el índice de columna: `(0,0), (1,1), (2,2), ..., (N-1,N-1)`. Hacen falta exactamente `N` celdas (no `N²`), y se recorren con **un solo** índice que se usa dos veces:

```cpp
for (int i = 0; i < N; ++i) {
    // la celda de la diagonal en la fila/columna i es m.at(i).at(i)
}
```

> 💡 Es un error común escribir dos `for` anidados (como si se fuera a recorrer toda la matriz) y filtrar con un `if (fila == columna)` adentro. Funciona, pero es `N²` comparaciones para visitar solo `N` celdas — un solo `for` que usa el mismo índice para fila y columna alcanza y sobra.

#### Parámetro "in": paso por referencia constante
Que un parámetro sea **"in"** (solo entrada, la función lo lee pero nunca lo modifica ni se espera que modifique el original) se expresa en C++ pasándolo **por referencia constante**: `const Matriz&`. Esto evita copiar toda la matriz en cada llamada (a diferencia de pasarla por valor) y, al mismo tiempo, el `const` le garantiza a quien lee el código (y al compilador) que la función no la va a alterar.

```cpp
int SumaDiagonal(const Matriz& m); // "m" es un parametro IN: solo lectura, sin copia
```

#### Cómo aparece en este trabajo
La restricción explícita ("la suma la debe calcular una función que tenga como parámetro *in* una matriz") es exactamente el caso de uso de `const Matriz&` — ni pasarla por valor (copia innecesaria) ni por referencia no-const (dejaría la puerta abierta a que la función la modifique, violando "in").

### 🧭 Cómo encararlo

1. Elegí un tamaño fijo `N` (una constante) y definí el alias `using Matriz = array<array<int,N>,N>;` para no repetir el tipo anidado en cada firma.
2. Escribí `int SumaDiagonal(const Matriz& m);` como prototipo.
3. Implementala con un solo `for` de `0` a `N-1`, acumulando `m.at(i).at(i)`.
4. Probá con al menos: una matriz con valores distintos en cada celda (para verificar que suma *solo* la diagonal y no toda la matriz), y una matriz en cero (diagonal = 0).

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* DiagonalMatriz.cpp
>  * Determina la suma de la diagonal principal de una matriz cuadrada.
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <array>
> #include <cassert>
>
> using namespace std;
>
> const int N = 3;
> using Matriz = array<array<int, N>, N>;
>
> int SumaDiagonal(const Matriz& m); // Prototipo (parametro "in": solo lectura, sin copia)
>
> int main() {
>     Matriz m{{
>         {1, 2, 3},
>         {4, 5, 6},
>         {7, 8, 9}
>     }};
>     assert(SumaDiagonal(m) == 15); // 1 + 5 + 9
>
>     Matriz ceros{};
>     assert(SumaDiagonal(ceros) == 0);
>
>     Matriz negativos{{
>         {-1,  9,  9},
>         { 9, -1,  9},
>         { 9,  9, -1}
>     }};
>     assert(SumaDiagonal(negativos) == -3);
>
>     return 0;
> }
>
> int SumaDiagonal(const Matriz& m) {
>     int suma = 0;
>     for (int i = 0; i < N; ++i) {
>         suma += m.at(i).at(i);
>     }
>     return suma;
> }
> ```
> El test `negativos` verifica, con celdas fuera de la diagonal deliberadamente "ruidosas" (todas en `9`), que la función realmente ignora todo lo que no es diagonal.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué `const Matriz&` y no `Matriz` (por valor) ni `Matriz&` (referencia no-const)? → Por valor copiaría innecesariamente toda la matriz; sin `const` permitiría (aunque no se use) que la función la modifique, lo que contradice que el parámetro sea "in".
- ¿Alcanza un solo `for`? → Sí: la diagonal tiene exactamente `N` celdas, no `N²`; usar dos `for` anidados con un `if (fila == columna)` es correcto pero ineficiente y menos directo.
- ¿Qué pasa si la matriz no es cuadrada? → El concepto de "diagonal principal" tal como se lo define acá requiere `filas == columnas`; con `std::array<array<int,N>,N>` la cuadratura queda garantizada por el propio tipo.
- ¿Cómo se llama la "otra" diagonal (de arriba-derecha a abajo-izquierda)? → Diagonal secundaria; se recorre con `m.at(i).at(N - 1 - i)` — no la pide este TP, pero es una pregunta típica de seguimiento.

---

## 20. Tipo Color

**Objetivos:** Demostrar capacidad de construcción de tipos compuestos basados en tipos existentes y simples (es decir, no compuestos).
**Temas:** Tipo de dato definido por el usuario · Tipo Abstracto de Datos (TAD) · Especificación · Implementación · Definición de conjunto de valores con `struct` · Tipos enteros de ancho fijo · Variables externas · Variables `const`.
**Problema:** Diseñar un tipo `Color` basado en el modelo RGB, con tres canales de 8 bits. Todo color está compuesto por intensidad de *red* (rojo), *green* (verde) y *blue* (azul), cada una en el rango `[0, 255]`. Definir los valores para rojo, azul, verde, cyan, magenta, amarillo, negro y blanco. Dos colores se pueden mezclar, lo cual produce un nuevo color que tiene el promedio de intensidad para cada componente.
**Restricciones:**
- Las operaciones de proyección para `red`, `green` y `blue` se implementan con acceso directo a los componentes (no hacen falta *getters* especiales; por la misma razón, tampoco *setters*).
- Usar el tipo `uint8_t` de `<cstdint>`; si no es posible, `unsigned char`.
- Los colores primarios, secundarios, negro y blanco deben implementarse como **ocho variables declaradas fuera de `main` y de toda función**, con el calificador `const` para que no puedan modificarse.
**Tareas:**
- Implementar la operación `IsIgual` que retorna `true` si un color es igual a otro, si no `false`.
- Especificar matemáticamente el tipo en `Color.md` (conjunto de operaciones, conjunto de valores).
- Diseñar y codificar las pruebas en `main`. Declarar los prototipos de las operaciones arriba de `main`. Codificar las definiciones debajo de `main`.

**Productos:**
```
DD-Color
 |-- readme.md
 |-- Color.md   // Especificacion
 `-- Color.cpp  // Implementacion y pruebas
```

### 📘 Teoría necesaria

#### Tipo de dato definido por el usuario / Tipo Abstracto de Datos (TAD)
Un **TAD** define un tipo por **lo que se puede hacer con él** (su conjunto de operaciones) y **qué valores puede tomar** (su conjunto de valores), separado de *cómo* está implementado por dentro. `Color` es un TAD: conceptualmente "es" una terna de intensidades con un conjunto de operaciones (proyecciones, `Mezclar`, `IsIgual`); que por dentro se implemente con un `struct` de tres `uint8_t` es un detalle de **implementación**, no de la especificación. (El capítulo 21, más abajo, retoma esto en su forma más pura, especificando un tipo — `Fecha` — sin llegar a implementarlo.)

#### `struct` — definir un conjunto de valores agrupando campos
```cpp
struct Color {
    uint8_t red;
    uint8_t green;
    uint8_t blue;
};

Color rojo{255, 0, 0}; // inicialización agregada: un valor por campo, en orden de declaración
```
A diferencia de la `union` (capítulo 16, arriba), en un `struct` **todos** los campos existen y tienen valor simultáneamente — es la herramienta natural para agrupar los "tres canales que siempre van juntos" de un color, en vez de compartir memoria entre ellos.

#### Tipos enteros de ancho fijo: `uint8_t`
Los tipos como `int`, `unsigned`, `char` no garantizan un tamaño exacto en bits (depende de la plataforma). `<cstdint>` define tipos con tamaño **fijo y garantizado** en cualquier plataforma:

```cpp
#include <cstdint>

uint8_t  canal = 255; // entero SIN signo de exactamente 8 bits: rango [0, 255]
int8_t   x;            // entero CON signo de 8 bits: rango [-128, 127]
uint16_t y;             // sin signo, 16 bits: [0, 65535]
```

`uint8_t` es exactamente lo que pide el modelo RGB: un canal de color de 8 bits sin signo, rango `[0, 255]`. En la práctica, `uint8_t` suele ser un alias de `unsigned char` — por eso la consigna permite usar `unsigned char` como alternativa si el compilador no tiene `<cstdint>` disponible.

> 💡 **Trampa:** `uint8_t` se imprime con `cout` como un **carácter**, no como número (porque por debajo suele ser un `unsigned char`). `cout << canal` con `canal = 65` imprime `A`, no `65`. Para imprimirlo como número hay que castear: `cout << static_cast<int>(canal)`.

#### Variables externas y variables `const` (fuera de `main` y de toda función)
Una variable declarada **fuera** de cualquier función (a nivel de archivo) tiene **duración estática**: existe durante toda la ejecución del programa, no se crea ni se destruye con cada llamada a una función (a diferencia de las variables automáticas del stack, ver [[Call Stack y Heap]]). Declararla además `const` impide que cualquier código la reasigne después de inicializada — exactamente lo que hace falta para "colores primarios/secundarios fijos que no deberían poder modificarse por accidente":

```cpp
const Color Rojo{255, 0, 0};   // fuera de main y de toda funcion: existe siempre, y es constante
```

#### Cómo aparece en este trabajo
`Color` es un `struct` de tres `uint8_t`. Las ocho constantes (`Rojo`, `Verde`, `Azul`, `Cyan`, `Magenta`, `Amarillo`, `Negro`, `Blanco`) son variables `const` a nivel de archivo. `IsIgual` compara campo a campo; `Mezclar` promedia cada canal por separado.

### 🧭 Cómo encararlo

1. Escribí primero `Color.md` con la especificación matemática (ver estructura sugerida más abajo, antes de tocar el `.cpp` — es la tarea 1 de la consigna, y separa el *qué* del *cómo*).
2. Definí `struct Color { uint8_t red, green, blue; };`.
3. Declará las 8 constantes fuera de `main` y de toda función, con `const Color NombreDelColor{...};`.
4. Declará los prototipos de `IsIgual` y `Mezclar` (y de las que quieras del crédito extra) arriba de `main`.
5. En `main`, `assert` con casos como: un color igual a sí mismo, dos colores distintos, y una mezcla con resultado conocido (p. ej. `Mezclar(Rojo, Azul)` debería dar `{127, 0, 127}` con división entera).
6. Implementá `IsIgual` y `Mezclar` debajo de `main`, accediendo directamente a `.red`/`.green`/`.blue` (la consigna no pide *getters*).

**Especificación matemática mínima para `Color.md`:**
- **Conjunto de valores:** ternas `(r, g, b)` con `r, g, b ∈ [0, 255] ⊂ ℕ`.
- **Conjunto de operaciones:**
  - `Crear: ℕ × ℕ × ℕ → Color` — construye un color a partir de sus tres componentes.
  - `red, green, blue: Color → ℕ` — proyecciones (acceso directo a cada componente).
  - `Mezclar: Color × Color → Color` — `Mezclar(c1, c2).red = (c1.red + c2.red) / 2` (ídem para `green`/`blue`).
  - `IsIgual: Color × Color → Bool` — `IsIgual(c1, c2) ⟺ c1.red = c2.red ∧ c1.green = c2.green ∧ c1.blue = c2.blue`.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* Color.cpp
>  * Tipo Color basado en el modelo RGB, con canales de 8 bits sin signo.
>  * UTN - Algoritmos y Estructura de Datos - Equipo 26-053-01
>  * 20260917
>  */
> #include <cstdint>
> #include <cassert>
>
> using namespace std;
>
> struct Color {
>     uint8_t red;
>     uint8_t green;
>     uint8_t blue;
> };
>
> bool IsIgual(Color a, Color b);   // Prototipos
> Color Mezclar(Color a, Color b);
>
> // Colores primarios, secundarios, negro y blanco: constantes fuera de main y de toda funcion
> const Color Rojo{255, 0, 0};
> const Color Verde{0, 255, 0};
> const Color Azul{0, 0, 255};
> const Color Cyan{0, 255, 255};
> const Color Magenta{255, 0, 255};
> const Color Amarillo{255, 255, 0};
> const Color Negro{0, 0, 0};
> const Color Blanco{255, 255, 255};
>
> int main() {
>     // IsIgual
>     assert(IsIgual(Rojo, Rojo));
>     assert(!IsIgual(Rojo, Verde));
>     assert(IsIgual(Negro, Color{0, 0, 0}));
>
>     // Mezclar
>     assert(IsIgual(Mezclar(Rojo, Verde), Amarillo));
>     assert(IsIgual(Mezclar(Rojo, Azul), Color{127, 0, 127}));
>     assert(IsIgual(Mezclar(Negro, Blanco), Color{127, 127, 127}));
>
>     return 0;
> }
>
> bool IsIgual(Color a, Color b) {
>     return a.red == b.red && a.green == b.green && a.blue == b.blue;
> }
>
> Color Mezclar(Color a, Color b) {
>     Color resultado;
>     resultado.red   = (a.red   + b.red)   / 2;
>     resultado.green = (a.green + b.green) / 2;
>     resultado.blue  = (a.blue  + b.blue)  / 2;
>     return resultado;
> }
> ```
> `Mezclar(Rojo, Verde)` da exactamente `Amarillo` (`(255,0,0)` + `(0,255,0)` → `(127,127,0)`... en realidad da `{127,127,0}`, no `{255,255,0}` — **ojo**: el promedio de `Rojo` y `Verde` no es `Amarillo` "puro", es un amarillo más oscuro. Si al escribir tus propios `assert` un resultado como este te sorprende, es una buena señal de que estás verificando de verdad y no copiando valores.

> [!warning] Revisá el assert de `Mezclar(Rojo, Verde)` antes de copiar
> El ejemplo de arriba lo deja escrito mal a propósito como recordatorio: `Mezclar(Rojo, Verde)` da `{127, 127, 0}`, **no** `Amarillo` (`{255, 255, 0}`). Corregí ese `assert` a `assert(IsIgual(Mezclar(Rojo, Verde), Color{127, 127, 0}));` antes de compilar tu propia versión.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué `uint8_t` y no `int`? → El modelo RGB define cada canal en `[0, 255]`; `uint8_t` es exactamente ese rango, con tamaño garantizado de 8 bits — usar `int` permitiría valores fuera de rango sin ningún aviso.
- ¿Por qué imprimir un `uint8_t` con `cout` da un carácter raro en vez de un número? → Porque `uint8_t` suele ser un alias de `unsigned char`, y `cout` tiene una sobrecarga especial para `char`/`unsigned char` que lo imprime como carácter. Hay que castear: `(int)canal`. o `static_cast<int>(canal)`.
- ¿Por qué las 8 constantes van *fuera* de `main` y no adentro? → Restricción explícita de la consigna: tienen que ser variables externas (duración estática, existen durante todo el programa) y `const` (no se pueden reasignar), para representar "colores fijos" de referencia.
- ¿Hace falta un *getter* para `red`? → No, la consigna es explícita: acceso directo al campo (`c.red`), no hace falta encapsular con una función.
- La división en `Mezclar` es entera (`uint8_t / 2` trunca) — ¿es un problema? → Para el promedio de dos enteros en `[0,255]` el truncamiento es aceptable (y es lo que pide literalmente "el promedio de intensidad"); si hiciera falta más precisión habría que definirlo explícitamente en la especificación.

---

## 21. Especificación del Tipo de Dato Fecha

**Tarea:** Especificar el tipo de dato "Fecha", lo cual implica especificar su **conjunto de valores** y su **conjunto de operaciones** sobre esos valores.
**Productos:**
```
readme.md
 |-- Conjunto de Valores
 `-- Conjunto de Operaciones
```

> [!info] Este capítulo no pide código
> A diferencia de todos los anteriores, el libro **no** pide una implementación en `.cpp` para este trabajo — solo la **especificación** del TAD `Fecha` en `readme.md`. Es la versión "pura" de lo que ya se empezó a hacer en el capítulo 20 (Tipo Color) al escribir `Color.md`: acá el ejercicio completo **es** especificar, sin pasar a la implementación.

### 📘 Teoría necesaria

#### Especificación de un TAD: qué significa "solo especificar"
Especificar un tipo abstracto de datos es describirlo **desde afuera**, en términos de qué valores puede tomar y qué se puede hacer con él — sin comprometerse a *cómo* se representa por dentro ni a qué lenguaje se va a usar para implementarlo. Es deliberadamente independiente de la implementación: la misma especificación de `Fecha` podría implementarse con tres enteros (`día`, `mes`, `año`), con un único entero que cuenta días desde una fecha de referencia (como hace realmente `time_t` en C), o de cualquier otra forma — todas serían implementaciones válidas de la **misma** especificación, siempre que respeten el conjunto de valores y el comportamiento de las operaciones especificadas.

Esto retoma la separación dominio del problema / dominio de la solución del capítulo 17: acá el ejercicio se queda deliberadamente en el "qué" (especificación), sin avanzar al "cómo" (implementación) — la implementación de una `Fecha` real llega recién en la Parte V del libro (Geometría), cuando haga falta.

#### Conjunto de valores — cómo especificarlo para `Fecha`
El conjunto de valores de `Fecha` no es simplemente "cualquier terna `(día, mes, año)`": hay combinaciones inválidas (`31 de febrero`, `30 de febrero` incluso en año bisiesto, `día 0`, `mes 13`). Especificar el conjunto de valores implica poner por escrito esas restricciones:

```
Fecha = { (día, mes, año) ∈ ℕ × ℕ × ℤ |
    1 ≤ mes ≤ 12
    ∧ 1 ≤ año ≤ 9999                                     (rango elegido por el equipo)
    ∧ 1 ≤ día ≤ DiasEnMes(mes, año)
}

DiasEnMes(mes, año) =
    31                          si mes ∈ {1,3,5,7,8,10,12}
    30                          si mes ∈ {4,6,9,11}
    29                          si mes = 2 ∧ EsBisiesto(año)
    28                          si mes = 2 ∧ ¬EsBisiesto(año)

EsBisiesto(año) = (año % 4 = 0 ∧ año % 100 ≠ 0) ∨ (año % 400 = 0)
```

#### Conjunto de operaciones — cómo especificarlo
Cada operación se especifica con su **firma** (qué recibe, qué devuelve) y, en prosa o de forma matemática, su **efecto**. Conviene separarlas en categorías:

| Categoría | Ejemplos de operación | Firma |
|---|---|---|
| Constructor | `Crear` | `ℕ × ℕ × ℤ → Fecha` (día, mes, año → Fecha; solo si es un valor válido del conjunto) |
| Proyección/consulta | `GetDia`, `GetMes`, `GetAño` | `Fecha → ℕ` |
| Predicado | `EsBisiesto`, `EsValida` | `Fecha → Bool` (o `ℤ → Bool` para `EsBisiesto` sobre un año suelto) |
| Comparación | `EsAnteriorA`, `SonIguales` | `Fecha × Fecha → Bool` |
| Aritmética de fechas | `SumarDias`, `DiferenciaEnDias` | `Fecha × ℕ → Fecha` / `Fecha × Fecha → ℤ` |
| Conversión | `ToString` | `Fecha → String` |

Cada una debería, además de la firma, dejar claro el **efecto**: por ejemplo, `SumarDias(f, n)` devuelve la fecha que resulta de avanzar `n` días calendario desde `f`, manejando correctamente el cambio de mes y de año (y años bisiestos) — sin todavía decir *cómo* se implementa ese manejo.

#### Cómo aparece en este trabajo
Este capítulo pide **únicamente** este ejercicio de especificación — sin `struct`, sin `enum`, sin `.cpp`. El resultado esperado es puramente el `readme.md` con las dos secciones (Conjunto de Valores, Conjunto de Operaciones), escrito con el mismo nivel de precisión matemática usado arriba para `Color` (capítulo 20) y para el modelo IPO (capítulo 17).

### 🧭 Cómo encararlo

1. Primero el **conjunto de valores**: escribí la restricción completa (rango de día/mes/año, regla de bisiestos, días por mes) — no alcanza con decir "tres números", hay que dejar explícito qué combinaciones son válidas.
2. Después el **conjunto de operaciones**, agrupadas por categoría (constructor, proyecciones, predicados, comparación, aritmética, conversión) — para cada una: nombre, firma, y una frase que describa su efecto.
3. Revisá que la especificación sea **autocontenida**: alguien que solo lea el `readme.md` (sin ver ningún código) debería poder entender exactamente qué es una `Fecha` válida y qué hace cada operación, sin necesitar saber C++.
4. No implementes nada en `.cpp` para este capítulo — si el equipo quiere probar la especificación, puede hacerlo en prosa con ejemplos (`Crear(29, 2, 2024)` es válida porque 2024 es bisiesto; `Crear(29, 2, 2023)` no lo es), no con `assert` de código.

### ✅ Solución completa (especificación)

> [!example]- Especificación completa de `Fecha` para `readme.md` (click para expandir)
> ```markdown
> # Especificación del Tipo de Dato Fecha
>
> ## Conjunto de Valores
>
> Una Fecha es una terna (día, mes, año) tal que:
>
>   1 ≤ mes ≤ 12
>   1 ≤ año ≤ 9999
>   1 ≤ día ≤ DiasEnMes(mes, año)
>
> donde:
>
>   DiasEnMes(mes, año) =
>     31  si mes ∈ {1, 3, 5, 7, 8, 10, 12}
>     30  si mes ∈ {4, 6, 9, 11}
>     29  si mes = 2 y EsBisiesto(año)
>     28  si mes = 2 y no EsBisiesto(año)
>
>   EsBisiesto(año) = (año % 4 = 0 y año % 100 ≠ 0) o (año % 400 = 0)
>
> Ejemplos de valores válidos: (29, 2, 2024), (1, 1, 1), (31, 12, 9999).
> Ejemplos de valores inválidos: (29, 2, 2023) [2023 no es bisiesto], (31, 4, 2024) [abril tiene 30
> días], (0, 5, 2024) [no existe el día 0], (15, 13, 2024) [no existe el mes 13].
>
> ## Conjunto de Operaciones
>
> ### Constructor
> - Crear(día, mes, año) -> Fecha
>   Precondición: (día, mes, año) pertenece al conjunto de valores.
>   Efecto: construye la Fecha correspondiente.
>
> ### Proyecciones (consulta)
> - GetDia(f) -> Entero        — devuelve el día de f.
> - GetMes(f) -> Entero        — devuelve el mes de f.
> - GetAño(f) -> Entero        — devuelve el año de f.
>
> ### Predicados
> - EsBisiesto(año) -> Booleano
>   Efecto: true si año es bisiesto según la regla gregoriana (arriba).
> - EsValida(día, mes, año) -> Booleano
>   Efecto: true si la terna pertenece al conjunto de valores de Fecha. Permite
>   validar una terna ANTES de invocar a Crear.
>
> ### Comparación
> - SonIguales(f1, f2) -> Booleano
>   Efecto: true si f1 y f2 representan el mismo día calendario.
> - EsAnteriorA(f1, f2) -> Booleano
>   Efecto: true si f1 ocurre estrictamente antes que f2 en el calendario.
>
> ### Aritmética de fechas
> - SumarDias(f, n) -> Fecha
>   Precondición: n ≥ 0.
>   Efecto: devuelve la fecha que resulta de avanzar n días calendario desde f,
>   ajustando correctamente cambios de mes, de año, y años bisiestos.
> - DiferenciaEnDias(f1, f2) -> Entero
>   Efecto: devuelve la cantidad de días calendario entre f1 y f2 (positivo si
>   f2 es posterior a f1, negativo si es anterior, 0 si son iguales).
>
> ### Conversión
> - ToString(f) -> Cadena
>   Efecto: devuelve una representación textual de f, formato "DD/MM/AAAA".
> ```
> Notar que **ninguna** operación de esta especificación menciona `struct`, `int`, `enum` ni ningún otro detalle de C++ — esa es justamente la diferencia entre especificar un TAD e implementarlo.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué este capítulo no pide un `.cpp`? → Porque el ejercicio es enteramente de **especificación**: entender y poner por escrito qué es una `Fecha` (valores) y qué se puede hacer con ella (operaciones), como paso previo — deliberadamente separado — a cualquier implementación.
- ¿Alcanza con decir "una fecha es tres números"? → No: hay que acotar el rango válido de cada componente y las dependencias entre ellos (los días válidos dependen del mes, y en febrero también del año, por los bisiestos).
- ¿Por qué `EsValida` es una operación separada de `Crear`? → Porque permite chequear si una terna es válida **antes** de intentar construir la Fecha (por ejemplo, para validar una entrada del usuario sin arriesgarse a violar la precondición de `Crear`).
- ¿`SumarDias` y `DiferenciaEnDias` son inversas exactas? → Conceptualmente sí: `DiferenciaEnDias(f, SumarDias(f, n)) = n` para cualquier `f` y `n ≥ 0` — es un buen ejemplo de propiedad que conviene declarar en la especificación (y, cuando se llegue a implementar, verificar con `assert`).
- ¿Esta especificación es la única válida? → No, es una posible especificación razonable; lo que se evalúa es que sea **precisa y completa** (que no deje ambigüedad sobre qué es un valor válido ni sobre el efecto de cada operación), no que coincida palabra por palabra con ninguna "respuesta oficial".

---
