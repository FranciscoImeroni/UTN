⬅️ [[00 - Índice y Requisitos Generales]]

# 05 - Geometría (Caso de Estudio, Parte V — capítulos 22 a 25)

Estos cuatro trabajos son el mismo dominio (figuras en el plano) resuelto **todavía con tipos simples**: `struct` para agrupar datos y `enum struct` para taxonomías cerradas, sin punteros ni memoria dinámica. Es la preparación directa para `[[06 - Estructuras Dinámicas - Geometría II]]`, donde se vuelve a resolver lo mismo (Polígonos) pero con `.h`/`.cpp` separados, interfaz vs. implementación real, y estructuras enlazadas.

---

## 22. Enumeraciones & Estructuras — Plano y Punto

**Objetivos:** Demostrar capacidad básica de construcción de tipos compuestos a partir de tipos existentes y simples (no compuestos).

**Temas:**
- Tipo de dato definido por el usuario (programador).
- Definición de conjunto de valores con `struct`.
- Definición de conjunto de valores con `enum struct`.

**Problema:** Dado un punto, determinar en qué parte del plano está: Cuadrante I, II, III o IV; sobre el Eje X o el Eje Y; o en el Origen (intersección de ambos ejes). En criollo: hay que escribir una función que reciba un `Punto` (un par de coordenadas `x`, `y`) y devuelva **un único valor** de un conjunto cerrado de 7 posibilidades (`CuadranteI`, `CuadranteII`, `CuadranteIII`, `CuadranteIV`, `EjeX`, `EjeY`, `Origen`). El caso `Origen` tiene prioridad sobre "está en los dos ejes"; un punto con `x == 0` y `y == 0` es `Origen`, no `EjeX` ni `EjeY`.

**Restricciones:**
- La solución debe ser una función que reciba un valor del tipo `Punto` y retorne un valor del tipo `ParteDelPlano`. Nombres posibles: `DóndeEstá`, `GetParteDelPlano`, `DóndeEnElPlanoEstá`.
- `Punto` debe implementarse con `struct`.
- `ParteDelPlano` debe implementarse con `enum struct`.
- **Crédito extra:** especificar e implementar *distancia entre dos puntos* y *distancia al origen*, y analizar ventajas/desventajas de definir una en función de la otra (spoiler: definir "distancia al origen" como "distancia entre el punto y `{0,0}`" evita repetir la fórmula, a costa de una llamada de función extra — trade-off habitual entre reutilización y micro-rendimiento).

**Tareas:** Debatir formas matemáticas de definir un punto y de representarlo en memoria → especificar `ParteDelPlano` y `Punto` en sendos `.md` (conjunto de operaciones + conjunto de valores) → diseñar y codificar las pruebas en `main` → declarar prototipos y tipos antes de `main` → compilar (debería dar error de *linkeo*, no de compilación) → codificar las definiciones después de `main` → probar de nuevo.

**Productos:** carpeta `DD-Plano` con `readme.md`, `ParteDelPlano.md` (especificación), `Punto.md` (especificación) y `Plano.cpp` (implementación y pruebas).

### 📘 Teoría necesaria

**`struct` en C++.** Un `struct` agrupa varios datos relacionados bajo un solo nombre de tipo. Cada dato interno se llama **miembro** (o campo). Se inicializa con `{}` (inicialización agregada: lista los valores en el orden en que se declararon los miembros).

```cpp
struct Punto {
    double x;
    double y;
};

Punto p{3.0, 4.0};   // p.x == 3.0, p.y == 4.0
Punto origen{};      // agregado con {} vacío: x=0.0, y=0.0 (value-initialization)
```

Diferencia con `class`: en un `struct` los miembros son **públicos por default**; en una `class` son **privados por default**. Es la única diferencia real del lenguaje — todo lo demás (métodos, constructores, herencia) funciona igual en ambos. En esta etapa del curso se usa `struct` porque el foco es "agrupar datos + funciones libres que operan sobre ellos", no encapsular estado.

**`enum struct` (enumeración con ámbito).** Define un tipo con un conjunto **cerrado y nombrado** de valores posibles. `enum struct` (equivalente a `enum class`, son sinónimos intercambiables en C++11+) crea un enum **con ámbito propio** (*scoped enum*): hay que escribir el nombre del tipo antes del valor (`ParteDelPlano::EjeX`, no `EjeX` a secas), y **no** convierte implícitamente a `int`. Esto evita choques de nombres y comparaciones sin sentido entre enums distintos — más seguro que el `enum` viejo de C.

```cpp
enum struct ParteDelPlano {
    CuadranteI, CuadranteII, CuadranteIII, CuadranteIV,
    EjeX, EjeY, Origen
};

ParteDelPlano parte = ParteDelPlano::Origen;
if (parte == ParteDelPlano::Origen) { /* ... */ }
// int x = parte;              // ❌ no compila: scoped enum no convierte implícito a int
int x = int(parte);            // ✅ conversión explícita si hace falta
```

**Diseño de un Tipo de Dato Abstracto (TDA): interfaz vs. implementación.** Diseñar un tipo en dos pasos separados:
- **Interfaz (especificación):** qué operaciones ofrece el tipo, qué reciben y qué devuelven — *sin decir cómo están hechas por dentro*. Es lo que va en `Punto.md` / `ParteDelPlano.md`: el "contrato" que cualquiera que use el tipo puede leer sin mirar el `.cpp`.
- **Implementación:** cómo están representados los datos (los campos del `struct`) y cómo está escrito el cuerpo de cada función. Es lo que va en `Plano.cpp`.

Esta separación es la misma idea de "declarar prototipos antes, definir después" que ya venís aplicando: el prototipo **es** la interfaz, el cuerpo de la función **es** la implementación. Acá todavía viven ambas cosas en un solo `.cpp`; en la Parte VI se separan de verdad en `.h` (interfaz) y `.cpp` (implementación).

**Funciones libres vs. métodos.** Con `struct` + funciones libres, una operación se escribe como `ParteDelPlano DóndeEstá(Punto p)` — una función normal, afuera del tipo, que recibe el `Punto` como parámetro. La alternativa (que se usa con `class`, más adelante en la materia) sería un **método**: `p.DóndeEstá()`, definido *dentro* del tipo. Acá se usan funciones libres a propósito: todavía no se vio encapsulamiento ni métodos: el tipo es solo datos, y el comportamiento vive aparte.

**Fórmula de distancia entre dos puntos** (crédito extra de este capítulo, y base de varios cálculos de los capítulos siguientes):

$$d(p, q) = \sqrt{(p.x - q.x)^2 + (p.y - q.y)^2}$$

Distancia al origen = distancia entre el punto y `Punto{0, 0}`.

### 🧭 Cómo encararlo

1. Escribí primero el `struct Punto` (dos `double`) y el `enum struct ParteDelPlano` (7 valores).
2. Pensá `DóndeEstá` como una cadena de casos, del más específico al más general: primero `Origen` (`x==0 && y==0`), después los ejes (`y==0` → `EjeX`; `x==0` → `EjeY`), y recién ahí los 4 cuadrantes según el signo de `x` e `y`. Si probás los cuadrantes primero te vas a comer casos borde mal clasificados.
3. Escribí los `assert` de `main` **antes** de la implementación: un punto por cuadrante, uno por eje, y el origen. Son 7 casos mínimos (uno por cada valor del enum).
4. Recién ahí escribí el cuerpo de `DóndeEstá`.
5. Como crédito extra, agregá `DistanciaEntre(Punto, Punto)` y expresá `DistanciaAlOrigen(Punto)` en términos de esa función (reutilización).

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* Plano.cpp
>  * TP22 - Enumeraciones & Estructuras: Plano y Punto
>  * Determina en qué parte del plano cartesiano está un punto dado.
>  * Quiénes: <Apellido, Nombre> (Legajo)
>  * Cuando: 2026-09-17
>  */
> #include <iostream>
> #include <cassert>
> #include <cmath>
> using namespace std;
>
> struct Punto {
>     double x;
>     double y;
> };
>
> enum struct ParteDelPlano {
>     CuadranteI, CuadranteII, CuadranteIII, CuadranteIV,
>     EjeX, EjeY, Origen
> };
>
> // Prototipos
> ParteDelPlano DóndeEstá(Punto p);
> double DistanciaEntre(Punto a, Punto b);
> double DistanciaAlOrigen(Punto p);
>
> int main() {
>     assert(DóndeEstá(Punto{1, 1}) == ParteDelPlano::CuadranteI);
>     assert(DóndeEstá(Punto{-1, 1}) == ParteDelPlano::CuadranteII);
>     assert(DóndeEstá(Punto{-1, -1}) == ParteDelPlano::CuadranteIII);
>     assert(DóndeEstá(Punto{1, -1}) == ParteDelPlano::CuadranteIV);
>     assert(DóndeEstá(Punto{5, 0}) == ParteDelPlano::EjeX);
>     assert(DóndeEstá(Punto{0, 5}) == ParteDelPlano::EjeY);
>     assert(DóndeEstá(Punto{0, 0}) == ParteDelPlano::Origen);
>
>     assert(DistanciaEntre(Punto{0, 0}, Punto{3, 4}) == 5.0);
>     assert(DistanciaAlOrigen(Punto{3, 4}) == 5.0);
>
>     cout << "Todos los tests pasaron." << endl;
>     return 0;
> }
>
> ParteDelPlano DóndeEstá(Punto p) {
>     if (p.x == 0 && p.y == 0) return ParteDelPlano::Origen;
>     if (p.y == 0) return ParteDelPlano::EjeX;
>     if (p.x == 0) return ParteDelPlano::EjeY;
>     if (p.x > 0 && p.y > 0) return ParteDelPlano::CuadranteI;
>     if (p.x < 0 && p.y > 0) return ParteDelPlano::CuadranteII;
>     if (p.x < 0 && p.y < 0) return ParteDelPlano::CuadranteIII;
>     return ParteDelPlano::CuadranteIV;
> }
>
> double DistanciaEntre(Punto a, Punto b) {
>     double dx = a.x - b.x;
>     double dy = a.y - b.y;
>     return sqrt(dx * dx + dy * dy);
> }
>
> double DistanciaAlOrigen(Punto p) {
>     return DistanciaEntre(p, Punto{0, 0});
> }
> ```
> El orden de los `if` en `DóndeEstá` es lo único no obvio: se evalúa `Origen` primero, después los ejes, y recién al final los cuadrantes, porque son condiciones que se van "descartando" — un punto sobre un eje nunca puede caer en un cuadrante.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué `Origen` se comprueba antes que los ejes? Porque `(0,0)` cumple *también* `y==0` y `x==0` por separado: si no lo chequeás primero, un punto en el origen te queda clasificado como `EjeX`.
- ¿Qué diferencia hay entre `enum` (viejo) y `enum struct`? El segundo tiene ámbito propio (hay que calificar con `Tipo::Valor`) y no convierte implícitamente a `int`; el primero mete todos los valores en el espacio de nombres circundante y sí convierte.
- ¿Por qué `struct` y no `class` acá? Porque todo es público (son solo datos) y todavía no hay invariantes que proteger con encapsulamiento.
- Comparar `double`s con `==` es en general peligroso por errores de redondeo; acá `x == 0` funciona porque los datos de prueba son exactos, pero en un caso real conviene comparar contra un épsilon (`abs(x) < 1e-9`) — tenerlo presente para funciones con más cálculo (ver capítulos 23-24).
- ¿Qué significa que la interfaz esté en un `.md` aparte? Que la especificación (qué hace cada operación) se puede leer y evaluar sin mirar el código — es la costumbre que se vuelve obligatoria en la Parte VI con archivos `.h`.

---

## 23. Tipo Círculo

**Objetivos:** Demostrar capacidad de construcción de tipos compuestos basados en otros tipos existentes o creados por el programador.

**Temas:**
- Tipo de dato definido por el usuario (programador).
- Tipo Abstracto de Datos.
- Especificación.
- Implementación.
- Definición de conjunto de valores con `struct`.

**Problema:** Diseñar un tipo `Circulo` en el plano, con las operaciones:
- `GetCircunferencia`: devuelve el **perímetro** del círculo (la palabra "circunferencia" se usa acá en el sentido de "longitud del contorno", no "el conjunto de puntos que forman el borde").
- `GetÁrea`: devuelve el área encerrada.
- `IsDentro`: dado un `Punto`, determina si está **dentro** del círculo (incluyendo el borde: un punto a distancia exactamente igual al radio se considera dentro).
- `Mover`: traslada el círculo a otro lugar del plano (cambia su centro; el radio no cambia).

**Restricciones (análisis pedido por el libro):**
- Analizar las diferentes formas matemáticas de definir un círculo (centro + radio, tres puntos del borde, etc.) y las diferentes formas de representarlo en memoria.
- ¿Podría representarse con un `array` y con `using`? (Sí: por ejemplo `using Circulo = array<double,3>` con `{x, y, radio}` — pero se pierde el nombre de cada campo y hay que acordarse el orden por convención; por eso `struct` es preferible: cada dato tiene nombre propio.)

**Productos:** carpeta `DD-Círculo` con `readme.md`, `Círculo.md` (especificación) y `Círculo.cpp` (implementación y pruebas).

### 📘 Teoría necesaria

*(struct, enum struct, TDA, funciones libres: ver arriba, capítulo 22.)*

**Composición de tipos.** Un `struct` puede tener como miembro **a otro struct**. Es la forma natural de expresar "esto está hecho de aquello": un círculo *tiene* un centro, y ese centro *es* un `Punto`.

```cpp
struct Circulo {
    Punto centro;   // struct dentro de struct
    double radio;
};

Circulo c{{0, 0}, 5};   // agregado anidado: centro = {0,0}, radio = 5
c.centro.x;              // se accede en cadena: primero el miembro compuesto, después el suyo
```

Esto es distinto de "un círculo tiene 3 `double` sueltos (`cx`, `cy`, `radio`)": con composición, el compilador entiende que `centro` es *un* `Punto`, y podés reutilizar directamente las funciones que ya escribiste para `Punto` (como `DistanciaEntre`) en vez de reimplementarlas.

**Fórmulas del círculo**, con centro `c` y radio `r`:

$$\text{Circunferencia} = 2 \pi r \qquad \text{Área} = \pi r^2$$

`IsDentro(p, circulo)` se resuelve reutilizando la distancia del capítulo anterior: un punto está dentro (o en el borde) si `DistanciaEntre(p, circulo.centro) <= circulo.radio`.

### 🧭 Cómo encararlo

1. Definí `struct Circulo { Punto centro; double radio; };` (reutilizá el `Punto` del capítulo 22).
2. Empezá por lo más simple: `GetCircunferencia` y `GetÁrea` son una línea cada una con la fórmula. Probalas primero con un círculo de radio conocido (ej. radio 5 → circunferencia `10π`).
3. Después `IsDentro`: reutilizá (o reescribí) `DistanciaEntre`. Probá tres casos: un punto claramente adentro, uno claramente afuera, y uno **justo sobre el borde** (a distancia == radio) — ese caso borde es el que valida si entendiste bien "dentro incluye el borde".
4. Por último `Mover`: decidí la firma. Como modifica el círculo, conviene que reciba el `Circulo` **por referencia** (`Circulo&`) para que el cambio se vea afuera de la función, en vez de devolver una copia nueva. Probá que mover cambie el resultado de `IsDentro` para el mismo punto.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* Circulo.cpp
>  * TP23 - Tipo Círculo
>  * Círculo definido por centro + radio: circunferencia, área, pertenencia y traslación.
>  * Quiénes: <Apellido, Nombre> (Legajo)
>  * Cuando: 2026-09-17
>  */
> #include <iostream>
> #include <cassert>
> #include <cmath>
> using namespace std;
>
> const double PI = 3.14159265358979323846;
>
> struct Punto {
>     double x;
>     double y;
> };
>
> struct Circulo {
>     Punto centro;
>     double radio;
> };
>
> // Prototipos
> double DistanciaEntre(Punto a, Punto b);
> double GetCircunferencia(Circulo c);
> double GetÁrea(Circulo c);
> bool IsDentro(Punto p, Circulo c);
> void Mover(Circulo& c, Punto nuevoCentro);
>
> int main() {
>     Circulo c{{0, 0}, 5};
>
>     assert(abs(GetCircunferencia(c) - 2 * PI * 5) < 1e-9);
>     assert(abs(GetÁrea(c) - PI * 5 * 5) < 1e-9);
>
>     assert(IsDentro(Punto{0, 0}, c) == true);   // centro
>     assert(IsDentro(Punto{3, 4}, c) == true);   // dist == 5, sobre el borde -> dentro
>     assert(IsDentro(Punto{4, 4}, c) == false);  // dist == sqrt(32) ~= 5.66 -> afuera
>
>     Mover(c, Punto{10, 10});
>     assert(c.centro.x == 10 && c.centro.y == 10);
>     assert(IsDentro(Punto{3, 4}, c) == false);  // ya no está dentro tras mover
>     assert(IsDentro(Punto{10, 10}, c) == true); // el nuevo centro sí
>
>     cout << "Todos los tests pasaron." << endl;
>     return 0;
> }
>
> double DistanciaEntre(Punto a, Punto b) {
>     double dx = a.x - b.x;
>     double dy = a.y - b.y;
>     return sqrt(dx * dx + dy * dy);
> }
>
> double GetCircunferencia(Circulo c) {
>     return 2 * PI * c.radio;
> }
>
> double GetÁrea(Circulo c) {
>     return PI * c.radio * c.radio;
> }
>
> bool IsDentro(Punto p, Circulo c) {
>     return DistanciaEntre(p, c.centro) <= c.radio;
> }
>
> void Mover(Circulo& c, Punto nuevoCentro) {
>     c.centro = nuevoCentro;
> }
> ```
> `Mover` recibe `Circulo&` (referencia) porque su trabajo es modificar el objeto original, no crear uno nuevo; todas las demás funciones reciben `Circulo`/`Punto` **por valor** porque solo necesitan leer. `c.centro = nuevoCentro;` funciona porque `struct` soporta asignación miembro a miembro automáticamente (copia `x` e `y` de un `Punto` al otro).
>
> Nota: en `PI * c.radio * c.radio` la comparación con `abs(... - ...) < 1e-9` es necesaria porque `PI` es una aproximación de punto flotante — nunca compares resultados de cálculos con `π` usando `==`.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué `Mover` recibe el círculo por referencia y las demás funciones no? Porque `Mover` necesita modificar el objeto original; el resto solo lee, así que una copia (paso por valor) alcanza y es más simple.
- ¿"Dentro" incluye el borde? Según este enunciado sí (`<=`); es una decisión de diseño que hay que dejar explícita en `Círculo.md` — con `<` estricto un punto justo sobre la circunferencia quedaría "afuera", lo cual también sería una interpretación válida si el enunciado lo pidiera así.
- ¿Por qué no comparar `GetÁrea(c) == PI * 25` directamente? Por precisión de punto flotante: `π` es irracional, cualquier cálculo con él acumula error de redondeo; siempre se compara con una tolerancia (épsilon).
- Ventaja de que `Circulo` tenga un `Punto` adentro (composición) en vez de `double cx, cy`: se reutiliza toda la lógica de `Punto` (como `DistanciaEntre`) sin reescribirla.
- ¿Se podría implementar `Circulo` con un `array<double,3>`? Sí, pero se pierde legibilidad: `c.at(0)` no dice si es `x`, `y` o el radio, mientras que `c.centro.x` sí.

---

## 24. Tipo Triángulo

**Objetivos:** Demostrar capacidad de construcción de tipos compuestos basados en otros tipos existentes o creados por el programador.

**Temas:** Tipo de dato definido por el usuario, Tipo Abstracto de Datos, Especificación, Implementación, definición de conjunto de valores con `struct`.

**Problema:** Diseñar un tipo `Triangulo` en el plano (tres vértices), con las operaciones:
- `GetPerímetro`: suma de las longitudes de los tres lados.
- `GetÁrea`: área encerrada.
- `IsEscaleno` / `IsEquilátero` / `IsIsósceles`: clasificación **por longitud de lados**. Un triángulo equilátero también cumple la definición matemática de isósceles (tiene *al menos* dos lados iguales); escaleno es "ningún par de lados iguales".
- `GetTipoPorLados`: versión que devuelve **un único valor enumerado** (`Escaleno`, `Isósceles`, `Equilátero`) en vez de tres booleanos sueltos — para esta clasificación exclusiva, un equilátero cuenta como `Equilátero`, no como `Isósceles` (aunque el predicado `IsIsósceles` de arriba sí le dé `true`).
- `GetTipoPorÁngulos`: clasifica el triángulo como `Acutángulo` (los 3 ángulos < 90°), `Rectángulo` (un ángulo == 90°) u `Obtusángulo` (un ángulo > 90°), usando solo las longitudes de los lados (sin calcular los ángulos explícitamente, vía el recíproco del Teorema de Pitágoras).
- `GetCentro`: el centroide (baricentro) del triángulo.

**Restricciones:**
- Los dos conjuntos de tipos (`TipoPorLados` y `TipoPorÁngulos`) se deben implementar con `enum struct`.

**Análisis pedido:** las diferentes formas matemáticas de definir un triángulo (3 vértices; o 3 longitudes de lado; o 2 lados + ángulo entre ellos) y las formas de representarlo en memoria; ¿se podría representar con un `array` y con `using`?

**Productos:** carpeta `DD-Triángulo` con `readme.md`, `Triángulo.md` (especificación) y `Triángulo.cpp` (implementación y pruebas).

### 📘 Teoría necesaria

*(struct, enum struct, TDA: ver arriba, capítulo 22. Composición de tipos: ver arriba, capítulo 23 — acá se repite el mismo patrón con tres `Punto` en vez de uno.)*

**Fórmula de Herón (área de un triángulo a partir de sus 3 lados).** Con lados de longitud `a`, `b`, `c`, y semiperímetro `s = (a + b + c) / 2`:

$$\text{Área} = \sqrt{s (s-a)(s-b)(s-c)}$$

Sirve exactamente porque acá **no se conocen los ángulos**, solo las coordenadas de los 3 vértices; a partir de ellas se calculan las 3 longitudes de lado (con la `DistanciaEntre` de los capítulos anteriores) y se aplica la fórmula.

**Clasificar por ángulos sin calcular ángulos.** El recíproco del Teorema de Pitágoras dice que, si `m` es el lado más largo y `p`, `q` los otros dos, comparando **los cuadrados**:
- `m² == p² + q²` → el ángulo opuesto a `m` es recto (90°) → `Rectángulo`.
- `m² > p² + q²` → ese ángulo es obtuso (> 90°) → `Obtusángulo`.
- `m² < p² + q²` → los tres ángulos son agudos → `Acutángulo`.

Evita usar funciones trigonométricas (`acos`, etc.) para algo que se resuelve con aritmética simple.

### 🧭 Cómo encararlo

1. `struct Triangulo { Punto a; Punto b; Punto c; };` — reutilizá el `Punto` de los capítulos previos.
2. Definí primero un helper interno para las 3 longitudes de lado (podés escribir una función que devuelva las tres, o simplemente calcularlas al principio de cada función que las necesite). `GetPerímetro` es la suma de esas tres longitudes: escribila y probala primero con un triángulo fácil (ej. 3-4-5).
3. `GetÁrea` con Herón, reutilizando el mismo cálculo de lados. Probala con el mismo triángulo 3-4-5 (área conocida: 6).
4. Los predicados (`IsEscaleno`, `IsEquilátero`, `IsIsósceles`): compará longitudes con una tolerancia (`abs(a - b) < 1e-9`), nunca con `==` directo entre `double`. Probá un caso de cada tipo.
5. `GetTipoPorLados`: combina los predicados anteriores, con `Equilátero` chequeado primero (porque un equilátero también "pasa" el predicado de isósceles).
6. `GetTipoPorÁngulos`: identificá el lado más largo (o su cuadrado) y comparalo contra la suma de los cuadrados de los otros dos. Probá los tres casos con triángulos conocidos (uno rectángulo tipo 3-4-5 es el más fácil de verificar a mano).
7. `GetCentro`: es el único cálculo "de una línea" (promedio de las tres coordenadas x, de las tres y) — dejalo para el final aunque sea simple, para no mezclarlo con la lógica de clasificación mientras la probás.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* Triangulo.cpp
>  * TP24 - Tipo Triángulo
>  * Triángulo definido por 3 vértices: perímetro, área, clasificación por lados y por
>  * ángulos, y centroide.
>  * Quiénes: <Apellido, Nombre> (Legajo)
>  * Cuando: 2026-09-17
>  */
> #include <iostream>
> #include <cassert>
> #include <cmath>
> #include <algorithm>
> using namespace std;
>
> const double EPS = 1e-9;
>
> struct Punto {
>     double x;
>     double y;
> };
>
> struct Triangulo {
>     Punto a;
>     Punto b;
>     Punto c;
> };
>
> enum struct TipoPorLados { Escaleno, Isósceles, Equilátero };
> enum struct TipoPorÁngulos { Acutángulo, Rectángulo, Obtusángulo };
>
> // Prototipos
> double DistanciaEntre(Punto p, Punto q);
> bool Igual(double x, double y);
> double GetPerímetro(Triangulo t);
> double GetÁrea(Triangulo t);
> bool IsEscaleno(Triangulo t);
> bool IsEquilátero(Triangulo t);
> bool IsIsósceles(Triangulo t);
> TipoPorLados GetTipoPorLados(Triangulo t);
> TipoPorÁngulos GetTipoPorÁngulos(Triangulo t);
> Punto GetCentro(Triangulo t);
>
> int main() {
>     Triangulo equilatero{{0, 0}, {4, 0}, {2, 2 * sqrt(3.0)}};
>     Triangulo isosceles{{0, 0}, {4, 0}, {2, 3}};
>     Triangulo escaleno{{0, 0}, {4, 0}, {1, 2}};
>     Triangulo rectangulo{{0, 0}, {4, 0}, {0, 3}};  // clásico 3-4-5
>
>     assert(IsEquilátero(equilatero));
>     assert(GetTipoPorLados(equilatero) == TipoPorLados::Equilátero);
>
>     assert(IsIsósceles(isosceles) && !IsEquilátero(isosceles));
>     assert(GetTipoPorLados(isosceles) == TipoPorLados::Isósceles);
>
>     assert(IsEscaleno(escaleno));
>     assert(GetTipoPorLados(escaleno) == TipoPorLados::Escaleno);
>
>     assert(abs(GetPerímetro(rectangulo) - 12.0) < EPS);
>     assert(abs(GetÁrea(rectangulo) - 6.0) < EPS);
>     assert(GetTipoPorÁngulos(rectangulo) == TipoPorÁngulos::Rectángulo);
>     assert(GetTipoPorÁngulos(equilatero) == TipoPorÁngulos::Acutángulo);
>
>     Punto centro = GetCentro(rectangulo);
>     assert(abs(centro.x - 4.0 / 3) < EPS);
>     assert(abs(centro.y - 1.0) < EPS);
>
>     cout << "Todos los tests pasaron." << endl;
>     return 0;
> }
>
> double DistanciaEntre(Punto p, Punto q) {
>     double dx = p.x - q.x;
>     double dy = p.y - q.y;
>     return sqrt(dx * dx + dy * dy);
> }
>
> bool Igual(double x, double y) {
>     return abs(x - y) < EPS;
> }
>
> double GetPerímetro(Triangulo t) {
>     return DistanciaEntre(t.a, t.b) + DistanciaEntre(t.b, t.c) + DistanciaEntre(t.c, t.a);
> }
>
> double GetÁrea(Triangulo t) {
>     double ab = DistanciaEntre(t.a, t.b);
>     double bc = DistanciaEntre(t.b, t.c);
>     double ca = DistanciaEntre(t.c, t.a);
>     double s = (ab + bc + ca) / 2;
>     return sqrt(s * (s - ab) * (s - bc) * (s - ca));
> }
>
> bool IsEquilátero(Triangulo t) {
>     double ab = DistanciaEntre(t.a, t.b);
>     double bc = DistanciaEntre(t.b, t.c);
>     double ca = DistanciaEntre(t.c, t.a);
>     return Igual(ab, bc) && Igual(bc, ca);
> }
>
> bool IsIsósceles(Triangulo t) {
>     double ab = DistanciaEntre(t.a, t.b);
>     double bc = DistanciaEntre(t.b, t.c);
>     double ca = DistanciaEntre(t.c, t.a);
>     return Igual(ab, bc) || Igual(bc, ca) || Igual(ca, ab);
> }
>
> bool IsEscaleno(Triangulo t) {
>     return !IsIsósceles(t);
> }
>
> TipoPorLados GetTipoPorLados(Triangulo t) {
>     if (IsEquilátero(t)) return TipoPorLados::Equilátero;
>     if (IsIsósceles(t)) return TipoPorLados::Isósceles;
>     return TipoPorLados::Escaleno;
> }
>
> TipoPorÁngulos GetTipoPorÁngulos(Triangulo t) {
>     double ab2 = pow(DistanciaEntre(t.a, t.b), 2);
>     double bc2 = pow(DistanciaEntre(t.b, t.c), 2);
>     double ca2 = pow(DistanciaEntre(t.c, t.a), 2);
>     double mayor = max({ab2, bc2, ca2});
>     double sumaOtros = ab2 + bc2 + ca2 - mayor;
>     if (Igual(mayor, sumaOtros)) return TipoPorÁngulos::Rectángulo;
>     if (mayor > sumaOtros) return TipoPorÁngulos::Obtusángulo;
>     return TipoPorÁngulos::Acutángulo;
> }
>
> Punto GetCentro(Triangulo t) {
>     return Punto{(t.a.x + t.b.x + t.c.x) / 3, (t.a.y + t.b.y + t.c.y) / 3};
> }
> ```
> `GetTipoPorLados` chequea `Equilátero` **antes** que `Isósceles`: como todo triángulo equilátero también cumple el predicado `IsIsósceles`, si se invirtiera el orden ningún triángulo llegaría nunca a clasificarse como `Equilátero`. `GetTipoPorÁngulos` evita calcular ángulos: solo compara el cuadrado del lado más largo contra la suma de los cuadrados de los otros dos (recíproco de Pitágoras).

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué comparar con `Igual(x, y)` (épsilon) y no `x == y`? Las longitudes salen de `sqrt`, que casi nunca da un resultado exacto en punto flotante; comparar con `==` puede fallar por errores de redondeo minúsculos aunque matemáticamente los valores sean iguales.
- ¿Por qué el orden `Equilátero` → `Isósceles` → `Escaleno` en `GetTipoPorLados` y no al revés? Porque un equilátero cumple "al menos dos lados iguales" (la definición de isósceles) — hay que descartar primero el caso más específico.
- ¿`GetTipoPorÁngulos` funciona para cualquier terna de 3 puntos? Solo si forman un triángulo válido (no colineales); con 3 puntos alineados, el "área" de Herón da 0 y la clasificación por ángulos no tiene sentido geométrico — sería una precondición a documentar en `Triángulo.md`.
- ¿Por qué `GetCentro` es simplemente el promedio de las coordenadas? Porque el centroide de un triángulo (punto de intersección de las medianas) tiene esa propiedad geométrica exacta — no hace falta ningún cálculo más complejo.
- Identificadores con tilde (`GetÁrea`, `IsEquilátero`) compilan en GCC/Clang modernos porque aceptan UTF-8 en identificadores, pero no es universal: si el compilador de la cátedra da error de encoding, la salida es renombrar a ASCII (`GetArea`, `IsEquilatero`) sin cambiar nada de la lógica.

---

## 25. Estructuras & Arreglos — Cuadriláteros y Rectángulos

**Objetivos:** Demostrar capacidad de construcción de tipos compuestos basados en otros tipos existentes o creados por el programador, y capacidad de selección de criterios de representación de tipos en memoria.

**Temas:**
- Tipo de dato definido por el usuario.
- Definición de conjunto de valores con `struct`.
- Definición de conjunto de valores con `using`.

**Problema:** Todas las figuras tienen ubicación en el plano. Los cuadrados y rectángulos tienen, además, sus **lados paralelos a los ejes** (no están "rotados"); los cuadriláteros son la figura general (4 vértices cualesquiera, no necesariamente con lados paralelos a los ejes ni ángulos rectos). Hay que resolver:
1. Determinar el **perímetro** de un cuadrado.
2. Determinar si un **punto** está dentro de un cuadrado.
3. Determinar el **perímetro** de un rectángulo.
4. Determinar si un **cuadrado** está dentro de un rectángulo.
5. Determinar si un **rectángulo es cuadrado** (sus dos dimensiones son iguales).
6. Determinar el **perímetro** de un cuadrilátero (general, no necesariamente axis-aligned).
7. Determinar si un **cuadrilátero** está dentro de un rectángulo.

**Restricciones:**
- Las soluciones deben implementarse con funciones que reciban valores del tipo `Punto`, `Cuadrado`, `Rectángulo` o `Cuadrilátero`. Nombres posibles de las funciones: `IsDentro`, `GetPerímetro`, `IsCuadrado`. En la práctica esto pide **sobrecarga de funciones**: varias versiones de `IsDentro` y de `GetPerímetro`, una por cada combinación de tipos que tiene sentido, distinguidas por los tipos de sus parámetros.
- **Crédito extra:** especificar e implementar `AsCuadrado`, que recibe un `Rectángulo` y devuelve el `Cuadrado` equivalente. ¿Qué precondición hace falta? Que el rectángulo sea efectivamente un cuadrado (`ancho == alto`); si no se cumple, no hay un "cuadrado equivalente" bien definido.

**Tareas:** especificar matemáticamente cada tipo (`Cuadrado.md`, `Rectángulo.md`, `Cuadrilátero.md`: conjunto de operaciones + conjunto de valores) → diseñar y codificar las pruebas en `main` → declarar prototipos antes de `main` → declarar los tipos antes de los prototipos → compilar (error de linkeo esperado, no de compilación) → codificar las definiciones → probar de nuevo.

**Productos:** carpeta `DD-Cuadrilátero` con `readme.md`, `Cuadrado.md`, `Rectángulo.md`, `Cuadrilátero.md` (especificaciones) y `Cuadriláteros.cpp` (implementación y pruebas).

### 📘 Teoría necesaria

*(struct, enum struct, TDA, composición: ver arriba, capítulos 22-24.)*

**Arreglos de structs / structs con un arreglo adentro.** Un `Cuadrilátero` general (4 vértices arbitrarios) no se puede describir con "una esquina + una medida" como sí se puede con un cuadrado o rectángulo axis-aligned: hacen falta los 4 puntos completos. En vez de escribir `struct Cuadrilátero { Punto v1; Punto v2; Punto v3; Punto v4; };` (cuatro campos sueltos, tedioso de recorrer), conviene usar un **arreglo de 4 `Punto`** como único miembro — o directamente un alias de tipo con `using`:

```cpp
using Cuadrilátero = std::array<Punto, 4>;   // "Cuadrilátero" es nombre corto para este array

Cuadrilátero q{ Punto{0,0}, Punto{2,0}, Punto{2,2}, Punto{0,2} };
for (Punto vertice : q) { /* recorre los 4 vértices con range-for */ }
```

Ventaja sobre 4 campos sueltos: se puede **recorrer con un loop** (`for` o range-for) en vez de repetir código 4 veces a mano, y la cantidad de vértices queda en un solo lugar (el `4` del `array`).

`using Nombre = TipoLargo;` es un **alias de tipo**: le da un nombre corto a un tipo existente (acá, `array<Punto,4>`). No crea un tipo nuevo distinto — es sinónimo total del tipo original —, a diferencia de `struct`, que sí crea un tipo nuevo. Es la misma herramienta que ya se vio para simplificar tipos anidados largos (ej. `using Ventas = array<array<array<int,12>,3>,4>;` del TP de Arreglos & Dimensiones).

**Fórmulas de perímetro:**

$$P_{\text{cuadrado}} = 4 \cdot \text{lado} \qquad P_{\text{rectángulo}} = 2 \cdot (\text{ancho} + \text{alto})$$

$$P_{\text{cuadrilátero}} = \sum_{i=0}^{3} d(v_i,\ v_{(i+1) \bmod 4})$$

(la suma de las distancias entre cada vértice y el siguiente, "dando la vuelta": el último vértice se conecta de nuevo con el primero — de ahí el `% 4`).

**"Dentro" para figuras axis-aligned.** Como cuadrado y rectángulo tienen lados paralelos a los ejes, un punto está dentro si sus coordenadas caen dentro de un rango en `x` y un rango en `y` (no hace falta trigonometría ni productos vectoriales, que sí harían falta para un cuadrilátero rotado cualquiera). Por la misma razón, "¿un cuadrado está dentro de un rectángulo?" se reduce a comprobar que sus esquinas opuestas (mínima y máxima) caigan dentro del rectángulo.

### 🧭 Cómo encararlo

1. Elegí la representación de cada tipo:
   - `Cuadrado`: una esquina (por convención, inferior izquierda) + un `lado`.
   - `Rectángulo`: una esquina + `ancho` + `alto`.
   - `Cuadrilátero`: `using Cuadrilátero = array<Punto, 4>;` (4 vértices, en orden).
2. Empezá por lo más simple: `GetPerímetro(Cuadrado)` y `GetPerímetro(Rectángulo)` son una fórmula directa. Probalas con valores fáciles.
3. `IsDentro(Punto, Cuadrado)` e `IsDentro(Punto, Rectángulo)`: son comparaciones de rango en `x` e `y`. Probá un punto adentro, uno afuera, y uno justo sobre el borde (documentá si "dentro" incluye el borde, igual que en el capítulo 23).
4. `IsCuadrado(Rectángulo)`: una comparación (`ancho == alto`).
5. `GetPerímetro(Cuadrilátero)`: recorré los 4 vértices con un `for` (índice `i` y `(i+1) % 4`) sumando distancias — reutilizá `DistanciaEntre` de los capítulos anteriores.
6. `IsDentro(Cuadrado, Rectángulo)`: aprovechá que ya tenés `IsDentro(Punto, Rectángulo)` — alcanza con comprobar las dos esquinas opuestas del cuadrado.
7. `IsDentro(Cuadrilátero, Rectángulo)`: recorré los 4 vértices con range-for y comprobá cada uno con `IsDentro(Punto, Rectángulo)`; si **alguno** falla, el cuadrilátero no está dentro.
8. Por último, como crédito extra, `AsCuadrado(Rectángulo)`: agregá un `assert` (o mecanismo de precondición) que valide `ancho == alto` antes de construir el `Cuadrado`.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* Cuadrilateros.cpp
>  * TP25 - Estructuras & Arreglos: Cuadriláteros y Rectángulos
>  * Cuadrado y Rectángulo (lados paralelos a los ejes) + Cuadrilátero general (array
>  * de 4 vértices): perímetros, pertenencia de puntos/figuras y AsCuadrado.
>  * Quiénes: <Apellido, Nombre> (Legajo)
>  * Cuando: 2026-09-17
>  */
> #include <iostream>
> #include <array>
> #include <cassert>
> #include <cmath>
> using namespace std;
>
> struct Punto {
>     double x;
>     double y;
> };
>
> struct Cuadrado {
>     Punto esquina;  // inferior izquierda
>     double lado;
> };
>
> struct Rectángulo {
>     Punto esquina;  // inferior izquierda
>     double ancho;   // extensión en x
>     double alto;    // extensión en y
> };
>
> // Cuadrilátero general: 4 vértices en orden (no necesariamente axis-aligned).
> using Cuadrilátero = array<Punto, 4>;
>
> // Prototipos
> double DistanciaEntre(Punto p, Punto q);
> double GetPerímetro(Cuadrado c);
> double GetPerímetro(Rectángulo r);
> double GetPerímetro(Cuadrilátero q);
> bool IsDentro(Punto p, Cuadrado c);
> bool IsDentro(Punto p, Rectángulo r);
> bool IsDentro(Cuadrado c, Rectángulo r);
> bool IsDentro(Cuadrilátero q, Rectángulo r);
> bool IsCuadrado(Rectángulo r);
> Cuadrado AsCuadrado(Rectángulo r);  // crédito extra
>
> int main() {
>     Cuadrado cuad{{0, 0}, 4};
>     Rectángulo rect{{-1, -1}, 10, 6};
>     Cuadrilátero cuadri{ Punto{0, 0}, Punto{2, 0}, Punto{2, 2}, Punto{0, 2} };
>
>     // 1. Perímetro de un cuadrado
>     assert(GetPerímetro(cuad) == 16);
>
>     // 2. Punto dentro de un cuadrado
>     assert(IsDentro(Punto{2, 2}, cuad) == true);
>     assert(IsDentro(Punto{5, 5}, cuad) == false);
>
>     // 3. Perímetro de un rectángulo
>     assert(GetPerímetro(rect) == 32);
>
>     // 4. Cuadrado dentro de un rectángulo
>     assert(IsDentro(cuad, rect) == true);
>     Cuadrado cuadGrande{{5, 5}, 10};
>     assert(IsDentro(cuadGrande, rect) == false);
>
>     // 5. Rectángulo es cuadrado
>     assert(IsCuadrado(rect) == false);
>     Rectángulo rectCuadrado{{0, 0}, 5, 5};
>     assert(IsCuadrado(rectCuadrado) == true);
>
>     // 6. Perímetro de un cuadrilátero
>     assert(GetPerímetro(cuadri) == 8);
>
>     // 7. Cuadrilátero dentro de un rectángulo
>     assert(IsDentro(cuadri, rect) == true);
>     Cuadrilátero cuadriGrande{ Punto{-5, -5}, Punto{20, -5}, Punto{20, 20}, Punto{-5, 20} };
>     assert(IsDentro(cuadriGrande, rect) == false);
>
>     // Crédito extra: AsCuadrado
>     Cuadrado equivalente = AsCuadrado(rectCuadrado);
>     assert(equivalente.lado == 5);
>
>     cout << "Todos los tests pasaron." << endl;
>     return 0;
> }
>
> double DistanciaEntre(Punto p, Punto q) {
>     double dx = p.x - q.x;
>     double dy = p.y - q.y;
>     return sqrt(dx * dx + dy * dy);
> }
>
> double GetPerímetro(Cuadrado c) {
>     return 4 * c.lado;
> }
>
> double GetPerímetro(Rectángulo r) {
>     return 2 * (r.ancho + r.alto);
> }
>
> double GetPerímetro(Cuadrilátero q) {
>     double total = 0;
>     for (int i = 0; i < 4; ++i) {
>         total += DistanciaEntre(q.at(i), q.at((i + 1) % 4));
>     }
>     return total;
> }
>
> bool IsDentro(Punto p, Cuadrado c) {
>     return p.x >= c.esquina.x && p.x <= c.esquina.x + c.lado &&
>            p.y >= c.esquina.y && p.y <= c.esquina.y + c.lado;
> }
>
> bool IsDentro(Punto p, Rectángulo r) {
>     return p.x >= r.esquina.x && p.x <= r.esquina.x + r.ancho &&
>            p.y >= r.esquina.y && p.y <= r.esquina.y + r.alto;
> }
>
> bool IsDentro(Cuadrado c, Rectángulo r) {
>     // Como ambos son axis-aligned, alcanza con chequear las dos esquinas
>     // opuestas (mínima y máxima) del cuadrado.
>     Punto esquinaOpuesta{c.esquina.x + c.lado, c.esquina.y + c.lado};
>     return IsDentro(c.esquina, r) && IsDentro(esquinaOpuesta, r);
> }
>
> bool IsDentro(Cuadrilátero q, Rectángulo r) {
>     for (Punto vertice : q) {
>         if (!IsDentro(vertice, r)) return false;
>     }
>     return true;
> }
>
> bool IsCuadrado(Rectángulo r) {
>     return r.ancho == r.alto;
> }
>
> Cuadrado AsCuadrado(Rectángulo r) {
>     assert(IsCuadrado(r));  // precondición: si no es cuadrado, no hay equivalente válido
>     return Cuadrado{r.esquina, r.ancho};
> }
> ```
> `IsDentro` está **sobrecargada** cuatro veces (mismo nombre, distintos tipos de parámetro): el compilador elige la versión correcta según los tipos de los argumentos en cada llamada. `IsDentro (Cuadrado, Rectángulo)` y `IsDentro(Cuadrilátero, Rectángulo)` no reimplementan la lógica de rangos: **reutilizan** `IsDentro(Punto, Rectángulo)` sobre las esquinas/vértices relevantes.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué alcanza con comprobar 2 esquinas del cuadrado (no las 4) para `IsDentro(Cuadrado, Rectángulo)`? Porque ambas figuras son axis-aligned: si la esquina mínima y la máxima del cuadrado caen dentro del rectángulo, las otras dos esquinas (que combinan las mismas coordenadas x/y) también caen adentro necesariamente. Esto **no** vale para un cuadrilátero rotado cualquiera — por eso ese caso sí recorre los 4 vértices.
- ¿Por qué `Cuadrilátero` usa `array<Punto,4>` y no una esquina + medidas como `Cuadrado`/`Rectángulo`? Porque un cuadrilátero general no tiene por qué tener lados paralelos a los ejes ni ángulos rectos: hacen falta los 4 vértices completos, no alcanza con "una esquina + una medida".
- ¿`% 4` en `GetPerímetro(Cuadrilátero)` es obligatorio? Sí: sin el módulo, el último lado (el que conecta el vértice 3 con el vértice 0, "cerrando" la figura) nunca se sumaría.
- ¿Qué pasaría si a `AsCuadrado` se le pasa un rectángulo con `ancho != alto`? El `assert` aborta el programa — es la forma de documentar en código que **no** está definido el comportamiento para ese caso; en la especificación (`Cuadrado.md`) hay que anotar explícitamente esa precondición.
- ¿`using Cuadrilátero = array<Punto,4>;` crea un tipo nuevo? No: es solo un alias, `Cuadrilátero` y `array<Punto,4>` son intercambiables en todo sentido — a diferencia de un `struct` nuevo, que sí crea un tipo distinto aunque tenga los mismos datos adentro.

---
