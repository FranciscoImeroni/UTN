⬅️ [[00 - Índice y Requisitos Generales]]

# 06 — Estructuras Dinámicas · Geometría II

Este bloque es el más importante del cuatrimestre: es donde `new`/`delete`, punteros y separación interfaz/implementación dejan de ser teoría suelta y se usan para resolver un problema real (un polígono, que por definición tiene una cantidad de vértices que **no se sabe en compilación**). Antes de arrancar, conviene tener frescas estas tres notas — se citan puntualmente en cada capítulo, no hace falta releerlas enteras ahora:

- [[Call Stack y Heap]] — stack vs. heap, `new`/`delete`, punteros colgantes.
- [[Punteros y Listas Enlazadas]] — nodos, `->`, recorrido de listas.
- [[Modulos]] — separación `.h`/`.cpp`, cohesión/acoplamiento.

---

## 26. Simulación de Estructuras Dinámicas — Polígonos

**Objetivos:** demostrar capacidad de reconocer estructuras dinámicas y de construirlas.
**Temas:**
- Tipo de dato definido por el usuario (programador).
- Estructura dinámica.
- Reserva manual explícita de memoria y *heap*.
- Operadores `new` y `delete`.
- Punteros.

**Problema:** determinar el perímetro de un polígono en el plano. La cantidad de vértices puede diferir para cada polígono (no hay un tamaño fijo compartido por todos los polígonos posibles).

**Análisis (tal como lo plantea el libro):**
- Un polígono se define, en su forma más básica, como una **secuencia de vértices** (puntos en el plano). El orden de la secuencia determina las aristas (lados) del polígono.
- Para representar en memoria secuencias de longitud variable hay dos familias de solución:
  - **Solución #1 — representación contigua mediante arreglo.**
    - **1a:** indicar el fin de la secuencia repitiendo el primer o el último punto.
    - **1b:** indicar el fin de la secuencia con un natural que indica la cantidad de vértices.
  - **Solución #2 — representación enlazada mediante nodos y punteros al siguiente nodo.**
  - El libro pide explícitamente comparar ventajas/desventajas de 1a vs. 1b, y de la contigua vs. la enlazada (ver tabla en 🧭 más abajo).

**Restricciones / Operaciones (la interfaz del TDA `Poligono`):**
- `AddVértice`
- `GetVértice`
- `SetVértice`
- `RemoveVértice`
- `GetCantidadLados`
- `GetPerímetro`
- Las pruebas (en particular los `assert`) **deben ser iguales para ambas implementaciones** — es la forma que tiene la cátedra de verificar que dos representaciones internas distintas cumplen exactamente el mismo contrato.

**Tareas:**
1. Especificar matemáticamente el tipo en `Polígono.md`: (a) el conjunto de operaciones, (b) el conjunto de valores.
2. Diseñar y codificar las pruebas en `main`.
3. Declarar los prototipos de las operaciones antes de `main`.
4. Declarar los tipos antes de los prototipos de las operaciones.
5. Compilar. En este punto se espera error de **linkeo** (faltan definiciones), no de compilación.
6. Codificar las definiciones de las operaciones, luego de `main`.
7. Probar: compilación y linkeo sin errores, ejecutable sin errores.

**Productos:**
```
DD-Plano
 |-- readme.md
 |-- Polígono.md       // Especificación
 |-- PolígonoCont.cpp  // Implementación y pruebas (contigua)
 `-- PolígonoLink.cpp  // Implementación y pruebas (enlazada)
```

### 📘 Teoría necesaria

> [!info] Ya tenés una nota completa sobre esto
> Ver [[Call Stack y Heap]] — repasá la sección "Reserva Dinámica de Memoria — Heap" antes de este ejercicio: acá es exactamente donde se empieza a usar `new`/`delete` en un caso real. Repasá también [[Punteros y Listas Enlazadas]] para la representación enlazada (nodo = `value` + `next`).

**Lo nuevo de este capítulo es *aplicar* esa teoría a un arreglo cuyo tamaño se decide en runtime.**

#### Arreglo dinámico (`new T[n]`) vs. `std::array<T,N>`

`std::array<T,N>` (el que usaste en Geometría I y en Arreglos & Dimensiones) exige que `N` sea una **constante de compilación**: el compilador tiene que saber el tamaño antes de generar el binario. Un polígono no cumple eso — un triángulo tiene 3 vértices, un hexágono 6, y ambos son "polígonos" del mismo tipo. Para esto se necesita reservar el arreglo **en el heap**, con un tamaño que es una **variable**, no una constante:

```cpp
unsigned n;
std::cin >> n;
Punto* vertices = new Punto[n];   // n es una VARIABLE — esto es imposible con std::array
...
delete[] vertices;
```

| | `std::array<T,N>` | `new T[n]` |
|---|---|---|
| Tamaño | Constante, fijo en compilación | Variable, decidido en runtime |
| Dónde vive | Stack (o donde viva el objeto contenedor) | Heap |
| Quién libera | El compilador, automáticamente | El programador, con `delete[]` |
| Uso en este TP | Círculo/Triángulo (ch. 22-25): cantidad fija de puntos | Polígono: cantidad de vértices variable |

#### Redimensionar un arreglo dinámico "a mano"

`new` no tiene un "resize". Para "agregar un vértice" cuando el arreglo ya está lleno, el patrón es siempre el mismo: reservar uno **nuevo** más grande, **copiar** lo que había, **liberar** el viejo.

```cpp
Punto* viejo = vertices;
Punto* nuevo = new Punto[nuevaCapacidad];
for (unsigned i = 0; i < cantidad; ++i) nuevo[i] = viejo[i];
delete[] viejo;
vertices = nuevo;
```

Esto es exactamente lo que hace `AddVertice` en la solución de abajo.

#### Constructor/destructor informal para un tipo con memoria dinámica adentro

Regla de oro (RAII informal, sin necesidad de ir a fondo con smart pointers): **todo lo que reservás con `new` al "crear" el tipo, lo liberás con `delete` al "destruirlo"**. Como acá no usamos `class` con constructores reales (el libro trabaja con `struct` + funciones libres), esa regla se traduce en un par de funciones `Crear`/`Destruir` que hay que invocar a mano:

```cpp
void Crear(Poligono& p);     // reserva el arreglo inicial (equivalente a un constructor)
void Destruir(Poligono& p);  // libera el arreglo (equivalente a un destructor)
```

> 💡 Si te olvidás de llamar `Destruir`, no hay error de compilación ni de ejecución visible: es un **memory leak silencioso**. Es el error #1 de este bloque de capítulos.

### 🧭 Cómo encararlo

1. Pensá primero la **representación interna**, no las operaciones. Para la versión contigua necesitás como mínimo: un puntero al arreglo de `Punto` en el heap, un contador de vértices usados, y (para no redimensionar en cada `AddVertice`) un contador de capacidad reservada.
2. Recién con eso definido, las operaciones básicas: `Crear` (reserva inicial), `Destruir` (libera), `AddVertice` (redimensiona si hace falta, copia, agrega).
3. Después las que acceden por índice: `GetVertice`, `SetVertice`, `RemoveVertice` — todas deben validar `i < cantidad` con `assert` (no hay `cin`/`cout` de por medio, así que no hace falta `.at()`, pero sí chequeo explícito).
4. Al final, las derivadas: `GetCantidadLados` (es simplemente `cantidad`, un polígono de *n* vértices tiene *n* lados) y `GetPerimetro` (sumar la distancia entre cada vértice y el siguiente, **cerrando el ciclo** con módulo: el último vértice se conecta con el primero).
5. Para la versión enlazada, repetí el mismo análisis pero con `Nodo{ Punto valor; Nodo* siguiente; }` en vez de arreglo — `AddVertice` recorre hasta el último nodo y engancha uno nuevo; `GetVertice` recorre `i` pasos desde el primero.
6. Compará ventajas/desventajas antes de escribir código (pregunta explícita del enunciado):

| | 1a (fin = repetir 1er/último punto) | 1b (fin = contador `unsigned`) | Enlazada |
|---|---|---|---|
| Ventaja | No hace falta variable extra | `GetCantidadLados` es O(1); simple de razonar | Insertar/borrar en el medio es barato (no hay que correr elementos) |
| Desventaja | Ambiguo si el polígono real repite un vértice; frágil | Hay que mantener el contador sincronizado con el arreglo | `GetVertice(i)` es O(i), no O(1); overhead de un puntero por nodo |

El libro pide **1b** en la práctica (es la que se generaliza mejor), así que la solución de abajo usa un contador `unsigned cantidad`.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // archivo: PoligonoCont.cpp  (Solución #1b — arreglo contiguo dinámico)
> #include <cassert>
> #include <cmath>
>
> struct Punto { double x, y; };
>
> struct Poligono {
>     Punto* vertices;     // puntero al arreglo, EN EL HEAP
>     unsigned cantidad;   // vértices realmente usados
>     unsigned capacidad;  // tamaño reservado (cantidad <= capacidad)
> };
>
> void Crear(Poligono& p);
> void Destruir(Poligono& p);
> void AddVertice(Poligono& p, Punto v);
> Punto GetVertice(const Poligono& p, unsigned i);
> void SetVertice(Poligono& p, unsigned i, Punto v);
> void RemoveVertice(Poligono& p, unsigned i);
> unsigned GetCantidadLados(const Poligono& p);
> double GetPerimetro(const Poligono& p);
>
> int main() {
>     Poligono p;
>     Crear(p);
>
>     AddVertice(p, {0, 0});
>     AddVertice(p, {4, 0});
>     AddVertice(p, {4, 3});
>     AddVertice(p, {0, 3});   // rectángulo 4x3 -> perímetro 14
>
>     assert(GetCantidadLados(p) == 4);
>     assert(GetVertice(p, 1).x == 4);
>     assert(GetPerimetro(p) == 14);
>
>     SetVertice(p, 0, {-1, -1});
>     assert(GetVertice(p, 0).x == -1);
>
>     RemoveVertice(p, 0);
>     assert(GetCantidadLados(p) == 3);   // triángulo (4,0)(4,3)(0,3)
>
>     Destruir(p);   // fundamental: libera el arreglo del heap
>     return 0;
> }
>
> void Crear(Poligono& p) {
>     p.capacidad = 4;
>     p.cantidad = 0;
>     p.vertices = new Punto[p.capacidad];
> }
>
> void Destruir(Poligono& p) {
>     delete[] p.vertices;   // arreglo -> delete CON corchetes
>     p.vertices = nullptr;  // evita dangling pointer si alguien lo vuelve a usar
>     p.cantidad = p.capacidad = 0;
> }
>
> void AddVertice(Poligono& p, Punto v) {
>     if (p.cantidad == p.capacidad) {
>         // redimensionar "a mano": nuevo arreglo más grande, copiar, liberar el viejo
>         unsigned nuevaCapacidad = p.capacidad * 2;
>         Punto* nuevo = new Punto[nuevaCapacidad];
>         for (unsigned i = 0; i < p.cantidad; ++i) nuevo[i] = p.vertices[i];
>         delete[] p.vertices;
>         p.vertices = nuevo;
>         p.capacidad = nuevaCapacidad;
>     }
>     p.vertices[p.cantidad] = v;
>     ++p.cantidad;
> }
>
> Punto GetVertice(const Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     return p.vertices[i];
> }
>
> void SetVertice(Poligono& p, unsigned i, Punto v) {
>     assert(i < p.cantidad);
>     p.vertices[i] = v;
> }
>
> void RemoveVertice(Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     for (unsigned j = i; j + 1 < p.cantidad; ++j) p.vertices[j] = p.vertices[j + 1];
>     --p.cantidad;
> }
>
> unsigned GetCantidadLados(const Poligono& p) {
>     return p.cantidad;   // n vértices -> n lados (se cierra el polígono)
> }
>
> double GetPerimetro(const Poligono& p) {
>     assert(p.cantidad >= 3);
>     double per = 0;
>     for (unsigned i = 0; i < p.cantidad; ++i) {
>         Punto a = p.vertices[i];
>         Punto b = p.vertices[(i + 1) % p.cantidad];   // % para cerrar el ciclo
>         double dx = b.x - a.x, dy = b.y - a.y;
>         per += std::sqrt(dx * dx + dy * dy);
>     }
>     return per;
> }
> ```
>
> ```cpp
> // archivo: PoligonoLink.cpp  (Solución #2 — lista enlazada)
> #include <cassert>
> #include <cmath>
>
> struct Punto { double x, y; };
>
> struct Nodo {
>     Punto valor;
>     Nodo* siguiente;
> };
>
> struct Poligono {
>     Nodo* primero;
>     unsigned cantidad;
> };
>
> void Crear(Poligono& p);
> void Destruir(Poligono& p);
> void AddVertice(Poligono& p, Punto v);
> Punto GetVertice(const Poligono& p, unsigned i);
> void SetVertice(Poligono& p, unsigned i, Punto v);
> void RemoveVertice(Poligono& p, unsigned i);
> unsigned GetCantidadLados(const Poligono& p);
> double GetPerimetro(const Poligono& p);
>
> int main() {
>     // MISMAS pruebas que PoligonoCont.cpp — es un requisito del enunciado
>     Poligono p;
>     Crear(p);
>
>     AddVertice(p, {0, 0});
>     AddVertice(p, {4, 0});
>     AddVertice(p, {4, 3});
>     AddVertice(p, {0, 3});
>
>     assert(GetCantidadLados(p) == 4);
>     assert(GetVertice(p, 1).x == 4);
>     assert(GetPerimetro(p) == 14);
>
>     SetVertice(p, 0, {-1, -1});
>     assert(GetVertice(p, 0).x == -1);
>
>     RemoveVertice(p, 0);
>     assert(GetCantidadLados(p) == 3);
>
>     Destruir(p);
>     return 0;
> }
>
> static Nodo* NodoEn(const Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     Nodo* actual = p.primero;
>     for (unsigned j = 0; j < i; ++j) actual = actual->siguiente;
>     return actual;
> }
>
> void Crear(Poligono& p) {
>     p.primero = nullptr;
>     p.cantidad = 0;
> }
>
> void Destruir(Poligono& p) {
>     Nodo* actual = p.primero;
>     while (actual != nullptr) {
>         Nodo* siguiente = actual->siguiente;
>         delete actual;          // un solo objeto -> delete SIN corchetes
>         actual = siguiente;
>     }
>     p.primero = nullptr;
>     p.cantidad = 0;
> }
>
> void AddVertice(Poligono& p, Punto v) {
>     Nodo* nuevo = new Nodo{v, nullptr};
>     if (p.primero == nullptr) {
>         p.primero = nuevo;
>     } else {
>         Nodo* ultimo = NodoEn(p, p.cantidad - 1);
>         ultimo->siguiente = nuevo;
>     }
>     ++p.cantidad;
> }
>
> Punto GetVertice(const Poligono& p, unsigned i) {
>     return NodoEn(p, i)->valor;
> }
>
> void SetVertice(Poligono& p, unsigned i, Punto v) {
>     NodoEn(p, i)->valor = v;
> }
>
> void RemoveVertice(Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     Nodo* aBorrar;
>     if (i == 0) {
>         aBorrar = p.primero;
>         p.primero = p.primero->siguiente;
>     } else {
>         Nodo* anterior = NodoEn(p, i - 1);
>         aBorrar = anterior->siguiente;
>         anterior->siguiente = aBorrar->siguiente;
>     }
>     delete aBorrar;
>     --p.cantidad;
> }
>
> unsigned GetCantidadLados(const Poligono& p) { return p.cantidad; }
>
> double GetPerimetro(const Poligono& p) {
>     assert(p.cantidad >= 3);
>     double per = 0;
>     for (unsigned i = 0; i < p.cantidad; ++i) {
>         Punto a = GetVertice(p, i);
>         Punto b = GetVertice(p, (i + 1) % p.cantidad);
>         double dx = b.x - a.x, dy = b.y - a.y;
>         per += std::sqrt(dx * dx + dy * dy);
>     }
>     return per;
> }
> ```
> **Dónde está el `new`/`delete`:** en `PoligonoCont.cpp`, en `Crear`/`Destruir`/`AddVertice` (arreglo completo → `new[]`/`delete[]`). En `PoligonoLink.cpp`, en `AddVertice`/`Destruir`/`RemoveVertice` (un nodo a la vez → `new`/`delete` sin corchetes). Notá que **el `main` es idéntico** en ambos archivos: ese es justo el punto del ejercicio — la interfaz no cambia, solo la representación interna.

### 💡 Trampas típicas / posibles preguntas

- Olvidarse de llamar `Destruir` al final → memory leak. No falla en ejecución, no se nota sin una herramienta como `valgrind`.
- Usar `delete` en vez de `delete[]` sobre el arreglo (o viceversa) → comportamiento indefinido.
- En la versión enlazada, `RemoveVertice(0)` es un caso especial: hay que mover `primero`, no solo "desenganchar" un nodo intermedio. Olvidarse de ese caso rompe la lista.
- `GetVertice`/`SetVertice` sin `assert(i < cantidad)` → en la versión contigua es lectura fuera de arreglo (UB); en la enlazada, `NodoEn` desreferenciaría `nullptr` al llegar al final de la lista.
- ¿Por qué las pruebas tienen que ser **iguales** en ambas implementaciones? Porque el objetivo es demostrar que dos representaciones internas distintas cumplen el mismo contrato — si las pruebas fueran distintas, no se estaría verificando lo mismo.

---

## 27. Interfaces & Implementaciones — Modularización

**Objetivos:** reestructurar los tipos construidos en los trabajos anteriores para que tengan separación física en archivos.
**Temas:**
- Separación de especificación.
- Separación de pruebas.
- Separación de la parte pública de la implementación.
- Separación de la parte privada de la implementación.
- Separación de construcción (`Makefile`).

**Problema:** tomar los tipos ya construidos (`Punto`, `ParteDelPlano`, `Círculo`, `Triángulo`, `Cuadrado`, `Cuadrilátero`, `Polígono`) y reorganizarlos en archivos separados, en vez de tenerlos todos mezclados en un único `.cpp`. Cada tipo debe tener su propia subcarpeta.

**Tareas / Restricciones:**
- Escribir un `Makefile` por cada tipo (que lo construya y pruebe).
- Escribir un `Makefile` raíz que construya todo.
- *(Crédito extra, opcional)* lo referido a `Makefile`s.

**Productos:**
```
DD-Modularización
 |-- readme.md
 |-- Makefile          // Construye todo usando los Makefiles de las subcarpetas
 |-- Punto
 |    |-- Makefile     // Construye y prueba Punto
 |    |-- Punto.md     // Especificación de tipo
 |    |-- Punto.h      // Implementación: Parte Pública, Interfaz o Contrato
 |    |-- PuntoTest.cpp // Pruebas
 |    `-- Punto.cpp    // Implementación: Parte Privada
 |-- ParteDelPlano/...
 |-- Círculo/...
 |-- Triángulo/...
 |-- Cuadrado/...
 |-- Cuadrilátero/...
 `-- Polígono/...
```

### 📘 Teoría necesaria

> [!info] Ya tenés una nota completa sobre esto
> Ver [[Modulos]] — es literalmente el tema de este capítulo: la analogía célula/módulo, la diferencia entre `.h` (membrana, parte pública) y `.cpp` (núcleo, parte privada), y los cuatro atributos de calidad (cohesión, acoplamiento, ocultamiento, encapsulamiento). Repasala entera antes de escribir un solo `.h`.

Lo que agrega este capítulo puntual es la mecánica concreta para que **varios `.cpp` compilen juntos sin errores de "doble definición"**, y el `Makefile` para no tener que escribir el comando de compilación a mano cada vez.

#### Include guards

Si dos `.cpp` distintos incluyen el mismo `.h`, y ese `.h` no está protegido, el preprocesador pega el contenido dos veces en la misma unidad de traducción (por ejemplo si `Punto.h` incluye a `Color.h` y `Poligono.h` también incluye a `Color.h` y a `Punto.h`) → el compilador ve el `struct Color` declarado dos veces → error. La solución es un **include guard**:

```cpp
// archivo: Punto.h
#ifndef PUNTO_H     // "si PUNTO_H todavía no está definido..."
#define PUNTO_H     // "...definilo ahora (para la próxima vez)"

struct Punto { double x, y; };
// declaraciones...

#endif              // PUNTO_H
```

La primera vez que se incluye `Punto.h`, `PUNTO_H` no existe, así que se procesa todo el contenido y se define `PUNTO_H`. La segunda vez que algo intenta incluirlo (directa o indirectamente), `PUNTO_H` ya existe, el `#ifndef` da falso, y el contenido se saltea entero. Alternativa moderna, una sola línea, mismo efecto, soportada por todos los compiladores relevantes:

```cpp
#pragma once
```

#### Qué va en `.h` y qué va en `.cpp`

```cpp
// Punto.h — SOLO declaraciones: qué existe, no cómo funciona
#ifndef PUNTO_H
#define PUNTO_H

struct Punto { double x, y; };
double GetDistancia(const Punto& a, const Punto& b);   // prototipo, sin cuerpo

#endif
```
```cpp
// Punto.cpp — la definición: el cuerpo real
#include "Punto.h"   // "" para headers propios, <> para headers del sistema
#include <cmath>

double GetDistancia(const Punto& a, const Punto& b) {
    double dx = b.x - a.x, dy = b.y - a.y;
    return std::sqrt(dx * dx + dy * dy);
}
```

Cualquier otro `.cpp` que necesite usar `Punto` hace `#include "Punto.h"` — ve el prototipo (puede compilar sus propias llamadas a `GetDistancia`), pero no necesita ver el `.cpp`. Recién al **linkear** (unir todos los `.o`) el linker busca dónde está el cuerpo real de `GetDistancia` y lo conecta. Por eso en el Capítulo 26 el enunciado dice explícitamente *"deberíamos obtener error de linkeo pero no de compilación"*: separar prototipo (en `.h`) de cuerpo (en `.cpp`) es justo lo que permite que el `.cpp` que llama a la función **compile igual**, aunque el cuerpo todavía no exista.

#### `Makefile` mínimo por tipo

Un `Makefile` describe **reglas**: `objetivo: dependencias` seguido de la receta (con **tab**, no espacios, al principio de la línea — error clásico de `make`).

```makefile
CXX = g++
CXXFLAGS = -std=c++17 -Wall -Wextra

PuntoTest: Punto.o PuntoTest.o
	$(CXX) $(CXXFLAGS) -o PuntoTest Punto.o PuntoTest.o

Punto.o: Punto.cpp Punto.h
	$(CXX) $(CXXFLAGS) -c Punto.cpp

PuntoTest.o: PuntoTest.cpp Punto.h
	$(CXX) $(CXXFLAGS) -c PuntoTest.cpp

test: PuntoTest
	./PuntoTest

clean:
	rm -f *.o PuntoTest
```

`make` compara fechas de modificación: si `Punto.cpp` no cambió desde la última vez, no lo vuelve a compilar. Eso es lo que gana el proyecto al modularizar: recompilar solo lo que cambió, no todo junto.

### 🧭 Cómo encararlo

1. Elegí un tipo (empezá por el más simple, `Punto`) y creale su carpeta.
2. Separá lo que YA tenías (de los capítulos 22-26) en cuatro archivos: `Tipo.md` (lo que ya tenías como especificación matemática), `Tipo.h` (`struct` + prototipos, con include guard), `Tipo.cpp` (cuerpos de las funciones), `TipoTest.cpp` (el `main` con los `assert`, que ahora incluye `Tipo.h` en vez de tener todo junto).
3. Repetí el patrón para los demás tipos. Ojo con las **dependencias entre tipos**: `Poligono.h` va a necesitar `#include "Punto.h"` (usa `Punto` como vértice) y `#include "Color.h"` si el polígono tiene color (ver Capítulo 28) — esos `#include` en un `.h` son los que Módulos llama "include que importa".
4. Escribí el `Makefile` de cada carpeta primero, probalo (`make test` debería compilar y correr los `assert` sin tocar nada a mano), y recién después escribí el `Makefile` raíz que invoca a todos los de las subcarpetas.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir) — ejemplo con `Punto`, el patrón se repite igual para los demás tipos
> ```cpp
> // archivo: Punto/Punto.h
> #ifndef PUNTO_H
> #define PUNTO_H
>
> struct Punto {
>     double x, y;
> };
>
> bool IsIgual(const Punto& a, const Punto& b);
> double GetDistancia(const Punto& a, const Punto& b);
> double GetDistanciaAlOrigen(const Punto& p);
>
> #endif
> ```
> ```cpp
> // archivo: Punto/Punto.cpp
> #include "Punto.h"
> #include <cmath>
>
> bool IsIgual(const Punto& a, const Punto& b) {
>     return a.x == b.x && a.y == b.y;
> }
>
> double GetDistancia(const Punto& a, const Punto& b) {
>     double dx = b.x - a.x, dy = b.y - a.y;
>     return std::sqrt(dx * dx + dy * dy);
> }
>
> double GetDistanciaAlOrigen(const Punto& p) {
>     return GetDistancia(p, Punto{0, 0});
> }
> ```
> ```cpp
> // archivo: Punto/PuntoTest.cpp
> #include "Punto.h"
> #include <cassert>
>
> int main() {
>     Punto a{0, 0}, b{3, 4};
>     assert(GetDistancia(a, b) == 5);
>     assert(GetDistanciaAlOrigen(b) == 5);
>     assert(IsIgual(a, Punto{0, 0}));
>     assert(!IsIgual(a, b));
>     return 0;
> }
> ```
> ```makefile
> # archivo: Punto/Makefile
> CXX = g++
> CXXFLAGS = -std=c++17 -Wall -Wextra
>
> PuntoTest: Punto.o PuntoTest.o
> 	$(CXX) $(CXXFLAGS) -o PuntoTest Punto.o PuntoTest.o
>
> Punto.o: Punto.cpp Punto.h
> 	$(CXX) $(CXXFLAGS) -c Punto.cpp
>
> PuntoTest.o: PuntoTest.cpp Punto.h
> 	$(CXX) $(CXXFLAGS) -c PuntoTest.cpp
>
> test: PuntoTest
> 	./PuntoTest
>
> clean:
> 	rm -f *.o PuntoTest
> ```
> ```makefile
> # archivo: Makefile (raíz de DD-Modularización)
> SUBDIRS = Punto ParteDelPlano Círculo Triángulo Cuadrado Cuadrilátero Polígono
>
> all:
> 	for dir in $(SUBDIRS); do $(MAKE) -C $$dir; done
>
> test:
> 	for dir in $(SUBDIRS); do $(MAKE) -C $$dir test; done
>
> clean:
> 	for dir in $(SUBDIRS); do $(MAKE) -C $$dir clean; done
> ```
> Los demás tipos (`Círculo`, `Triángulo`, `Cuadrado`, `Cuadrilátero`, `Polígono`) se arman con exactamente el mismo esquema de 4 archivos + `Makefile`; `Polígono` reutiliza el `.h`/`.cpp` del Capítulo 26 (o 30, si ya lo enlazaste), y su `Makefile` agrega `Punto.o` como dependencia porque `Poligono.cpp` usa `Punto`.

### 💡 Trampas típicas / posibles preguntas

- Indentar la receta del `Makefile` con espacios en vez de **tab** → `make` tira `*** missing separator`.
- Olvidar el include guard (o repetirlo con el mismo nombre en dos archivos distintos por copy-paste) → errores de redefinición dificiles de leer.
- Poner el `struct` completo con sus campos en el `.h` pero el **cuerpo** de una función también en el `.h` (en vez de solo el prototipo) → si dos `.cpp` incluyen ese `.h`, el linker se queja de "múltiples definiciones" de esa función.
- Confundir "el `.cpp` incluye a su propio `.h`" (exporta, normal y esperado) con "un módulo incluye el `.h` de otro módulo" (importa, es una dependencia real) — ver [[Modulos]].
- ¿Por qué compilar da error de **linkeo** y no de compilación cuando falta el cuerpo de una función? → Porque el compilador solo necesita el prototipo (`.h`) para generar el código que la *llama*; el *cuerpo* real lo busca recién el linker al unir todos los `.o`.

---

## 28. Geometría — Desarrollo de Tipos

**Introducción:** este trabajo usa el `Color` construido en Geometría I. Es el primero de una secuencia de trabajos que aplican tipos para resolver problemas de geometría. El tema central es la construcción de tipos mediante **producto cartesiano**.

**Objetivos:** demostrar capacidad de construcción de tipos compuestos basados en otros tipos, simples o compuestos, existentes o nuevos.
**Temas:**
- Tipo de dato definido por el usuario.
- Tipo Abstracto de Datos (TDA).
- Especificación. Implementación.
- Definición de conjunto de valores con `struct`.
- Definición de conjunto de operaciones con funciones y pasaje de argumentos por referencia.
- Estructura dinámica con capacidad máxima.

**Problema:** construir el tipo `Polígono` con color. Un polígono tiene una cantidad **dinámica** de vértices, y el tipo debe incluir operaciones para agregar, remover, acceder y modificar esos vértices.

**Tabla de tipos y operaciones** (los marcados *opcional* son crédito extra):

| Tipo | Valores | Operaciones |
|---|---|---|
| Punto | Coordenadas cartesianas | `IsIgual`, `GetDistancia`, `GetDistanciaAlOrigen`, `GetRho`*, `GetPhi`*, `GetCuadrante`*, `GetEje`*, `GetSemiplano`*, `Mover`* |
| Círculo* | Círculo con color en el plano | `GetCircunferencia`, `GetÁrea`, `Mover` |
| Triángulo* | Triángulo con color, descripto por 3 puntos | `GetPerímetro`, `GetÁrea`, `IsEscaleno`, `IsEquilátero`, `IsIsósceles`, `GetTipo`*, `GetCentro`* |
| Rectángulo* | Rectángulo con color, lados paralelos a los ejes | `GetBase`, `GetAltura`, `GetPerímetro`, `GetÁrea`, `GetLongitudDiagonal`, `IsCuadrado`, `GetVértice`* (retorna el punto de cada uno de los 4 vértices: `SuperiorIzquierdo`, `SuperiorDerecho`, `InferiorIzquierdo`, `InferiorDerecho`) |
| **Polígono** | Polígono con color en el plano | `AddVértice`, `GetVértice`, `SetVértice`, `RemoveVértice`, `GetCantidadLados`, `GetPerímetro` |

(`*` = opcional / crédito extra.)

**Restricciones:**
- Usar el `Color` construido previamente (Capítulo 20).
- Los vértices deben ser del tipo `Punto`.
- La secuencia dinámica de vértices debe implementarse con **un array que contenga los elementos** y **un `unsigned` que indique cuántos vértices tiene realmente**. Ese `unsigned` es menor o igual al tamaño del array.
- Las pruebas deben realizarse con `assert`, sin usar `cin` ni `cout`.

**Tareas** (por cada tipo de dato): 1. especificar el tipo matemáticamente, 2. diseñar y codificar las pruebas en `main`, 3. implementar el tipo.

**Productos:**
```
DD-Geometría
 |-- readme.md
 |-- Geometría.md   // Especificación de todos los tipos
 `-- Geometría.cpp  // Implementación y pruebas de todos los tipos
```
*(Crédito extra: repetir la separación en archivos del Capítulo 27 — `.md`/`.h`/`Test.cpp`/`.cpp` por tipo, más un `makefile` que construya y pruebe la solución.)*

### 📘 Teoría necesaria

**Diferencia clave con el Capítulo 26:** ahí el arreglo era **heap puro** (`new`/`delete`, crecía sin límite predefinido). Acá la restricción pide algo más simple: un array de **capacidad máxima fija** (pensalo como `Punto vertices[MAX_VERTICES];`, tamaño constante conocido de antemano) + un `unsigned` contador de cuántas casillas están realmente usadas. Es la misma idea de "secuencia dinámica" que ya viste en `std::array` + contador (ver [[26-053-01/18-Arreglos & Dimensiones - Total de Ventas/Guia-Examen-Oral|TP 18]]), aplicada ahora a un `struct` propio en vez de a un `array` de la librería estándar. No hace falta `new`/`delete` para esta versión — eso se retoma explícitamente recién en el Capítulo 30, cuando se pide pasar a estructuras enlazadas.

> 💡 No confundas los dos "polígonos dinámicos" del bloque: el del Capítulo 26 es un ejercicio *sobre* reserva dinámica en sí misma (por eso usa el heap). El de este capítulo es un ejercicio *sobre* construcción de tipos por producto cartesiano, y usa la técnica más simple (capacidad máxima) para no distraer del tema central. Van a converger en el Capítulo 30.

#### Producto cartesiano aplicado a `struct`

Un `struct` con varios campos **es** un producto cartesiano de los tipos de esos campos. `Poligono` es `(array de Punto) × unsigned × Color`:

```cpp
const unsigned MAX_VERTICES = 100;

struct Poligono {
    Punto vertices[MAX_VERTICES];  // componente 1: hasta MAX_VERTICES puntos
    unsigned cantidad;             // componente 2: cuántos están realmente en uso
    Color color;                   // componente 3: reutiliza el Color de Geometría I
};
```

#### Pasaje de argumentos por referencia

Ya lo veías con `Ventas&` en el TP 18. Acá se vuelve indispensable porque `Poligono` incluye un array de hasta 100 `Punto` — pasarlo **por valor** copiaría toda esa memoria en cada llamada. Regla práctica para este capítulo: si la función **modifica** el polígono (`AddVertice`, `SetVertice`, `RemoveVertice`), recibe `Poligono&`; si solo lo **consulta** (`GetVertice`, `GetPerimetro`), puede recibir `const Poligono&` (referencia, sin copia, pero el `const` impide que la función lo modifique por error).

### 🧭 Cómo encararlo

1. Confirmá que ya tenés `Color` (Capítulo 20) y `Punto` (Capítulo 22) disponibles — este tipo se construye **sobre** ellos, no desde cero.
2. Definí `Poligono` como el producto cartesiano de arreglo-con-capacidad-máxima + contador + color.
3. Escribí primero `CrearPoligono` (inicializa `cantidad = 0` y guarda el color) — sin esto, cualquier prueba posterior arranca con basura.
4. `AddVertice`: debe fallar de alguna forma controlada si `cantidad == MAX_VERTICES` (con `assert`, ya que la restricción prohíbe `cin`/`cout` para señalar el error).
5. `GetVertice`/`SetVertice`/`RemoveVertice`: mismo patrón de validación con `assert(i < cantidad)` que en el Capítulo 26.
6. `GetCantidadLados`/`GetPerimetro`: idénticos en fórmula a los del Capítulo 26 (son la misma matemática), solo cambia que ahora leen de un array de tamaño fijo en vez de uno reservado con `new`.
7. Si vas por el crédito extra de `Punto`/`Círculo`/`Triángulo`/`Rectángulo`, empezalos recién después de tener `Polígono` funcionando — son independientes entre sí y el enunciado los marca opcionales.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // archivo: Geometría.h  (fragmento: Color, Punto y Poligono — lo obligatorio del capítulo)
> #ifndef GEOMETRIA_H
> #define GEOMETRIA_H
>
> #include <cassert>
> #include <cmath>
>
> // Color: ya construido en el Capítulo 20, se asume esta forma simple
> enum class Color { Rojo, Verde, Azul, Amarillo, Blanco, Negro };
>
> // Punto: ya construido en el Capítulo 22
> struct Punto {
>     double x, y;
> };
>
> bool IsIgual(const Punto& a, const Punto& b);
> double GetDistancia(const Punto& a, const Punto& b);
> double GetDistanciaAlOrigen(const Punto& p);
>
> // Polígono: lo nuevo de este capítulo
> const unsigned MAX_VERTICES = 100;
>
> struct Poligono {
>     Punto vertices[MAX_VERTICES];
>     unsigned cantidad;
>     Color color;
> };
>
> void CrearPoligono(Poligono& p, Color c);
> void AddVertice(Poligono& p, Punto v);
> Punto GetVertice(const Poligono& p, unsigned i);
> void SetVertice(Poligono& p, unsigned i, Punto v);
> void RemoveVertice(Poligono& p, unsigned i);
> unsigned GetCantidadLados(const Poligono& p);
> double GetPerimetro(const Poligono& p);
>
> #endif
> ```
> ```cpp
> // archivo: Geometría.cpp
> #include "Geometría.h"
>
> bool IsIgual(const Punto& a, const Punto& b) {
>     return a.x == b.x && a.y == b.y;
> }
>
> double GetDistancia(const Punto& a, const Punto& b) {
>     double dx = b.x - a.x, dy = b.y - a.y;
>     return std::sqrt(dx * dx + dy * dy);
> }
>
> double GetDistanciaAlOrigen(const Punto& p) {
>     return GetDistancia(p, Punto{0, 0});
> }
>
> void CrearPoligono(Poligono& p, Color c) {
>     p.cantidad = 0;
>     p.color = c;
> }
>
> void AddVertice(Poligono& p, Punto v) {
>     assert(p.cantidad < MAX_VERTICES);   // capacidad máxima: no hay redimensión posible acá
>     p.vertices[p.cantidad] = v;
>     ++p.cantidad;
> }
>
> Punto GetVertice(const Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     return p.vertices[i];
> }
>
> void SetVertice(Poligono& p, unsigned i, Punto v) {
>     assert(i < p.cantidad);
>     p.vertices[i] = v;
> }
>
> void RemoveVertice(Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     for (unsigned j = i; j + 1 < p.cantidad; ++j) p.vertices[j] = p.vertices[j + 1];
>     --p.cantidad;
> }
>
> unsigned GetCantidadLados(const Poligono& p) {
>     return p.cantidad;
> }
>
> double GetPerimetro(const Poligono& p) {
>     assert(p.cantidad >= 3);
>     double per = 0;
>     for (unsigned i = 0; i < p.cantidad; ++i) {
>         Punto a = p.vertices[i];
>         Punto b = p.vertices[(i + 1) % p.cantidad];
>         double dx = b.x - a.x, dy = b.y - a.y;
>         per += std::sqrt(dx * dx + dy * dy);
>     }
>     return per;
> }
>
> int main() {
>     Poligono cuadrado;
>     CrearPoligono(cuadrado, Color::Rojo);
>
>     AddVertice(cuadrado, {0, 0});
>     AddVertice(cuadrado, {2, 0});
>     AddVertice(cuadrado, {2, 2});
>     AddVertice(cuadrado, {0, 2});
>
>     assert(GetCantidadLados(cuadrado) == 4);
>     assert(GetPerimetro(cuadrado) == 8);
>     assert(cuadrado.color == Color::Rojo);
>
>     Punto a{0, 0}, b{3, 4};
>     assert(GetDistancia(a, b) == 5);
>     assert(GetDistanciaAlOrigen(b) == 5);
>     assert(IsIgual(a, Punto{0, 0}));
>
>     return 0;
> }
> ```
> Los tipos opcionales (`Círculo`, `Triángulo`, `Rectángulo`) siguen el mismo patrón: `struct` con `Color` + los `Punto` que hagan falta, funciones libres que reciben `const Tipo&` para consultar y `Tipo&` para modificar (`Mover`). Quedan como ejercicio de crédito extra — no aportan nada nuevo en términos de memoria dinámica, que es el eje de este bloque.

### 💡 Trampas típicas / posibles preguntas

- Confundir este `Poligono` (array de tamaño **fijo**, sin `new`) con el del Capítulo 26 (heap, crecimiento real). Si te preguntan la diferencia en el oral: acá la "dinámica" está en el `unsigned` contador (cuántas casillas se usan), no en el tamaño de la memoria reservada.
- Pasar `Poligono` por **valor** a una función que solo lee (`GetPerimetro(Poligono p)` en vez de `const Poligono&`) — compila y anda, pero copia el array de 100 `Punto` en cada llamada.
- Olvidar `CrearPoligono` (o no inicializar `cantidad = 0`) → `AddVertice` empieza a escribir en `vertices[basura]`.
- `AddVertice` sin `assert(cantidad < MAX_VERTICES)` → si el polígono real necesita más de `MAX_VERTICES` vértices, se escribe fuera del array (UB), silenciosamente.
- ¿Por qué el enunciado exige `assert` y prohíbe `cin`/`cout` en este capítulo? Porque las pruebas tienen que ser reproducibles y automáticas (correr el binario sin intervención humana debe alcanzar para saber si algo está mal), no inspeccionadas a ojo.

---

## 29. Geometría Parte II — Input/Output

**Introducción:** segundo trabajo de la serie que aplica tipos para resolver problemas de geometría; utiliza los tipos de la primera parte (Capítulo 28). Tema central: construcción de tipos mediante producto cartesiano.

**Problema:** dado un archivo con polígonos, copiar a un nuevo archivo los polígonos que tienen un perímetro **menor** a un valor `x`.

**Restricciones:**
- La conexión a los archivos debe ser mediante `streams`.
- Si fuese necesario, usar `in.clear()` para limpiar el estado erróneo de un stream y volver a leer.
- A los tipos deben agregarse operaciones de extracción e inserción según estos prototipos (`T` es el nombre del tipo):
  ```cpp
  bool ExtraerT(istream& in, T& v);
  bool InsertarT(ostream& out, const T& v);
  ```
- *(Crédito extra)* usar interfaz fluida ([[26-053-01/18-Arreglos & Dimensiones - Total de Ventas/Guia-Examen-Oral|FLUENT]]) con estos prototipos en su lugar:
  ```cpp
  istream& ExtraerT(istream& in, T& v);
  ostream& InsertarT(ostream& out, const T& v);
  ```
- La solución debe desarrollarse en una función:
  ```cpp
  void CopiarPolígonosConPerímetrosMayoresA(double x, string nombreArchivoIn, string nombreArchivoOut);
  ```

**Tareas:**
1. Diseñar la representación que cada tipo va a tener en los flujos.
2. *(Opcional)* especificar matemáticamente la operación inserción para cada tipo — la especificación de extracción es simplemente *"la operación Extraer debe poder extraer un valor insertado por la operación Insertar"*.
3. Agregar las pruebas del par de operaciones para cada tipo.

**Productos:** modifica los productos del trabajo anterior. Si hiciste un solo `Geometría.cpp`, su `main` invoca a `CopiarPolígonosConPerímetrosMayoresA` al final. Si separaste archivos (Capítulo 27), creá `Filtrar.cpp` con un `main` que invoque esa función, y con su implementación.

### 📘 Teoría necesaria

#### `operator<<`/`operator>>` sobrecargados, o funciones `Leer`/`Mostrar`, para tipos propios

El patrón `ExtraerT`/`InsertarT` que pide el enunciado es exactamente la misma idea que `operator>>`/`operator<<` sobrecargados, pero con nombre de función en vez de operador — así el libro evita entrar en la sintaxis de sobrecarga de operadores, que no es tema de este capítulo. Fíjate el paralelismo con lo que ya usás sin pensarlo:

```cpp
std::cin  >> x;     // "built-in" para int, double, etc. — azúcar de operator>>(istream&, int&)
ExtraerPunto(cin, p);  // lo mismo, para un tipo propio, sin azúcar de operador
```

#### La versión con interfaz fluida (crédito extra)

> [!info] Repaso rápido — interfaz fluida
> Ya la viste en [[26-053-01/18-Arreglos & Dimensiones - Total de Ventas/Guia-Examen-Oral|TP 18]] (tema 10): cada operación devuelve una referencia al propio objeto, para poder encadenar. `cin >> a >> b` funciona porque `operator>>` devuelve `istream&`.

```cpp
istream& ExtraerPunto(istream& in, Punto& v) {
    return in >> v.x >> v.y;   // devuelve el propio stream -> se puede encadenar
}
```
Con la versión fluida, `ExtraerPoligono` puede escribirse como una cadena de llamadas que corta apenas alguna falla (el stream se vuelve falsy), en vez de tener que chequear un `bool` después de cada paso — es más prolijo, pero funcionalmente equivalente a la versión con `bool`.

#### `in.clear()`

Cuando una extracción falla (por ejemplo, `in >> x` sobre texto que no es un número), el stream queda en estado de error: **cualquier operación posterior sobre ese mismo stream también va a fallar**, hasta que se lo "resetee" explícitamente con `in.clear()`. Esto solo tiene sentido si el error fue por un dato mal formado (no por llegar al fin de archivo real) y el programa quiere intentar recuperarse (por ejemplo, saltar la línea con el dato malo y seguir con la siguiente):

```cpp
if (!(in >> x)) {
    in.clear();               // "olvidate" del error
    in.ignore(1000, '\n');    // descartá el resto de la línea rota
}
```

### 🧭 Cómo encararlo

1. Diseñá primero el **formato de texto** de un polígono en el archivo — algo simple y no ambiguo, por ejemplo: `<color> <cantidad> x1 y1 x2 y2 ... xn yn` en una línea.
2. Escribí `ExtraerPunto`/`InsertarPunto` (dos `double`, trivial) y `ExtraerColor`/`InsertarColor` (mapear el `enum` a/desde una palabra).
3. Con esos dos ya andando, armá `ExtraerPoligono`/`InsertarPoligono` en términos de ellos: extraer color, extraer cantidad, hacer un bucle de `ExtraerPunto` + `AddVertice` tantas veces como indique la cantidad.
4. Recién ahí escribí `CopiarPolígonosConPerímetrosMayoresA`: abrir los dos archivos con `ifstream`/`ofstream`, bucle de lectura con `while (ExtraerPoligono(in, p))` (mismo patrón que `while (cin >> x)` del TP 18), filtrar por `GetPerimetro`, e insertar los que cumplen.
5. Probá con un archivo de texto armado a mano, no solo con `assert` en memoria — es la única forma de verificar que el formato de ida y vuelta (`Insertar` seguido de `Extraer`) es consistente.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir) — versión con interfaz fluida (crédito extra), usa los tipos de la Parte I
> ```cpp
> // agregar a Geometría.h
> #include <iostream>
> #include <fstream>
> #include <string>
>
> std::istream& ExtraerPunto(std::istream& in, Punto& v);
> std::ostream& InsertarPunto(std::ostream& out, const Punto& v);
> std::istream& ExtraerColor(std::istream& in, Color& c);
> std::ostream& InsertarColor(std::ostream& out, const Color& c);
> std::istream& ExtraerPoligono(std::istream& in, Poligono& p);
> std::ostream& InsertarPoligono(std::ostream& out, const Poligono& p);
>
> void CopiarPoligonosConPerimetrosMayoresA(double x, std::string nombreArchivoIn,
>                                            std::string nombreArchivoOut);
> ```
> ```cpp
> // agregar a Geometría.cpp
> std::istream& ExtraerPunto(std::istream& in, Punto& v) {
>     return in >> v.x >> v.y;
> }
>
> std::ostream& InsertarPunto(std::ostream& out, const Punto& v) {
>     return out << v.x << ' ' << v.y;
> }
>
> std::istream& ExtraerColor(std::istream& in, Color& c) {
>     std::string s;
>     if (!(in >> s)) return in;
>     if      (s == "Rojo")     c = Color::Rojo;
>     else if (s == "Verde")    c = Color::Verde;
>     else if (s == "Azul")     c = Color::Azul;
>     else if (s == "Amarillo") c = Color::Amarillo;
>     else if (s == "Blanco")   c = Color::Blanco;
>     else if (s == "Negro")    c = Color::Negro;
>     else in.setstate(std::ios::failbit);   // palabra desconocida -> marcar error a mano
>     return in;
> }
>
> std::ostream& InsertarColor(std::ostream& out, const Color& c) {
>     switch (c) {
>         case Color::Rojo:     return out << "Rojo";
>         case Color::Verde:    return out << "Verde";
>         case Color::Azul:     return out << "Azul";
>         case Color::Amarillo: return out << "Amarillo";
>         case Color::Blanco:   return out << "Blanco";
>         case Color::Negro:    return out << "Negro";
>     }
>     return out;
> }
>
> std::istream& ExtraerPoligono(std::istream& in, Poligono& p) {
>     Color c;
>     unsigned n;
>     if (!ExtraerColor(in, c)) return in;
>     if (!(in >> n)) return in;
>
>     CrearPoligono(p, c);
>     for (unsigned i = 0; i < n; ++i) {
>         Punto v;
>         if (!ExtraerPunto(in, v)) {
>             in.setstate(std::ios::failbit);
>             return in;
>         }
>         AddVertice(p, v);
>     }
>     return in;
> }
>
> std::ostream& InsertarPoligono(std::ostream& out, const Poligono& p) {
>     InsertarColor(out, p.color) << ' ' << GetCantidadLados(p);
>     for (unsigned i = 0; i < GetCantidadLados(p); ++i) {
>         out << ' ';
>         InsertarPunto(out, GetVertice(p, i));
>     }
>     return out;
> }
>
> void CopiarPoligonosConPerimetrosMayoresA(double x, std::string nombreArchivoIn,
>                                            std::string nombreArchivoOut) {
>     std::ifstream in(nombreArchivoIn);
>     std::ofstream out(nombreArchivoOut);
>     assert(in.is_open() && out.is_open());
>
>     Poligono p;
>     while (ExtraerPoligono(in, p)) {
>         if (GetPerimetro(p) > x) {          // ojo: el nombre de la función usa "Mayores" (ver trampas)
>             InsertarPoligono(out, p) << '\n';
>         }
>     }
>     if (in.fail() && !in.eof()) {
>         in.clear();   // uso explícito pedido por la restricción, para el caso de línea mal formada
>     }
> }
>
> int main() {
>     // ejemplo de archivo de entrada, armado con InsertarPoligono para probar ida y vuelta
>     {
>         std::ofstream out("polígonos_in.txt");
>         Poligono chico, grande;
>         CrearPoligono(chico, Color::Rojo);
>         AddVertice(chico, {0, 0}); AddVertice(chico, {1, 0}); AddVertice(chico, {1, 1});
>         CrearPoligono(grande, Color::Azul);
>         AddVertice(grande, {0, 0}); AddVertice(grande, {10, 0});
>         AddVertice(grande, {10, 10}); AddVertice(grande, {0, 10});
>         InsertarPoligono(out, chico) << '\n';
>         InsertarPoligono(out, grande) << '\n';
>     }
>
>     CopiarPoligonosConPerimetrosMayoresA(10, "polígonos_in.txt", "polígonos_out.txt");
>
>     std::ifstream verificacion("polígonos_out.txt");
>     Poligono leido;
>     assert(ExtraerPoligono(verificacion, leido));   // solo debería quedar el "grande" (perímetro 40)
>     assert(leido.color == Color::Azul);
>     assert(!ExtraerPoligono(verificacion, leido));  // no debería haber un segundo polígono
>     return 0;
> }
> ```

### 💡 Trampas típicas / posibles preguntas

- **El enunciado tiene una inconsistencia real:** el texto del problema dice *"perímetro menor a un valor x"*, pero el prototipo pedido se llama `CopiarPolígonosConPerímetrosMayoresA`. Es una trampa legítima del enunciado (no tuya) — en el oral, señalalo y aclará qué interpretación elegiste (la solución de arriba respeta el **nombre de la función**, que es lo que la cátedra probablemente corrige automáticamente).
- Olvidar `in.setstate(std::ios::failbit)` cuando `ExtraerColor` recibe una palabra desconocida: sin eso, la función "falla silenciosamente" (deja `c` sin actualizar pero el stream sigue en estado OK) y el llamador no se entera.
- No chequear `in.is_open()`/`out.is_open()` antes de usar los archivos — si el archivo de entrada no existe, todas las extracciones fallan inmediatamente y el bucle simplemente no copia nada, sin avisar por qué.
- Mezclar `Extraer`/`Insertar` con `bool` en algunos tipos y con interfaz fluida en otros dentro del mismo trabajo — elegí un estilo y sé consistente, porque `ExtraerPoligono` necesita poder encadenar las llamadas a `ExtraerPunto`/`ExtraerColor` de la misma forma en todos los casos.
- ¿Qué pasa si `ExtraerPoligono` falla **a la mitad** de leer los vértices (por ejemplo, el archivo dice `cantidad=5` pero solo hay 3 puntos escritos)? En la solución de arriba, el polígono queda parcialmente cargado y el stream en estado de error — el `while` del llamador corta ahí, que es el comportamiento esperado (no copiar un polígono corrupto).

---

## 30. Geometría Parte III — Estructuras Enlazadas

**Problema:** resuelve el **mismo problema** que la Parte II (Capítulo 29: copiar a un archivo nuevo los polígonos con perímetro mayor/menor a `x`), pero la representación interna del tipo `Polígono` deja de ser contigua (array de capacidad máxima) y pasa a ser **enlazada**.

**Restricciones:** los cambios deben estar acotados a la declaración de los `struct` y a la implementación de las operaciones — **el prototipo de las operaciones no debe cambiar**. Es decir, `AddVertice`, `GetVertice`, `SetVertice`, `RemoveVertice`, `GetCantidadLados`, `GetPerimetro`, `ExtraerPoligono`, `InsertarPoligono` siguen llamándose igual y recibiendo los mismos tipos — solo cambia qué hay *adentro* de `Poligono` y cómo están escritos los cuerpos.

**Productos:** los mismos del trabajo anterior, con `Poligono` reimplementado sobre una lista enlazada.

### 📘 Teoría necesaria

> [!info] Ya tenés una nota completa sobre esto
> Ver [[Punteros y Listas Enlazadas]] — repasá especialmente el recorrido con `for(Node* p = first; p; p = p->next)` y la construcción de nodos con `new Node`. Este capítulo es la aplicación directa de esa nota al `Polígono` de los dos capítulos anteriores.

Lo único genuinamente nuevo acá es **la restricción de diseño**: cambiar la representación sin tocar la interfaz pública. Esto es el mismo principio de [[Modulos]] (bajo acoplamiento, ocultamiento de información) llevado a la práctica: cualquier código que ya use `Poligono` (por ejemplo, `CopiarPoligonosConPerimetrosMayoresA` del Capítulo 29) **no debería tener que cambiar ni una línea** para seguir funcionando con la nueva representación interna. Si tenés que tocar algo fuera de la declaración del `struct` y de los cuerpos de las operaciones, algo del diseño anterior estaba mal encapsulado.

**Diferencia práctica frente al Capítulo 28:** ahora `GetVertice(i)` deja de ser O(1) (acceso directo al array) y pasa a ser O(i) (hay que recorrer `i` nodos desde el primero) — es el costo típico de cambiar arreglo por lista enlazada, y vale la pena poder explicarlo en el oral.

### 🧭 Cómo encararlo

1. Reemplazá el array de capacidad máxima por `Nodo{ Punto valor; Nodo* siguiente; }` + puntero al primer nodo + contador (igual patrón que `PoligonoLink.cpp` del Capítulo 26, pero ahora con `Color` incluido).
2. Escribí una función auxiliar privada `NodoEn(i)` que recorra hasta el nodo `i` — la van a usar `GetVertice`, `SetVertice` y `RemoveVertice`, evita repetir el recorrido tres veces.
3. `AddVertice` ahora es "enganchar un nodo nuevo al final" en vez de "escribir en la siguiente casilla libre" — y ya no hay límite de capacidad, así que el `assert` de "está lleno" desaparece.
4. Agregá una función `DestruirPoligono` (no existía en la versión de array, porque ahí no había nada que liberar explícitamente) y acordate de invocarla al final de cada prueba.
5. Verificá que `ExtraerPoligono`/`InsertarPoligono`/`CopiarPoligonosConPerimetrosMayoresA` del Capítulo 29 **no necesiten ningún cambio** — si necesitan cambio, revisá el punto 1: probablemente estén accediendo a campos del `struct` directamente en vez de a través de las operaciones.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // archivo: Geometría.h — reemplaza SOLO la declaración de Poligono del Capítulo 28
> struct Nodo {
>     Punto valor;
>     Nodo* siguiente;
> };
>
> struct Poligono {
>     Nodo* primero;
>     unsigned cantidad;
>     Color color;
> };
>
> // Prototipos: IDÉNTICOS a los del Capítulo 28/29 — esto es lo que no debe cambiar.
> void CrearPoligono(Poligono& p, Color c);
> void DestruirPoligono(Poligono& p);   // nueva: hace falta porque ahora SÍ hay memoria que liberar
> void AddVertice(Poligono& p, Punto v);
> Punto GetVertice(const Poligono& p, unsigned i);
> void SetVertice(Poligono& p, unsigned i, Punto v);
> void RemoveVertice(Poligono& p, unsigned i);
> unsigned GetCantidadLados(const Poligono& p);
> double GetPerimetro(const Poligono& p);
> ```
> ```cpp
> // archivo: Geometría.cpp — reemplaza SOLO los cuerpos de las operaciones de Poligono
> static Nodo* NodoEn(const Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     Nodo* actual = p.primero;
>     for (unsigned j = 0; j < i; ++j) actual = actual->siguiente;
>     return actual;
> }
>
> void CrearPoligono(Poligono& p, Color c) {
>     p.primero = nullptr;
>     p.cantidad = 0;
>     p.color = c;
> }
>
> void DestruirPoligono(Poligono& p) {
>     Nodo* actual = p.primero;
>     while (actual != nullptr) {
>         Nodo* siguiente = actual->siguiente;
>         delete actual;
>         actual = siguiente;
>     }
>     p.primero = nullptr;
>     p.cantidad = 0;
> }
>
> void AddVertice(Poligono& p, Punto v) {
>     Nodo* nuevo = new Nodo{v, nullptr};   // ya no hay límite de capacidad que chequear
>     if (p.primero == nullptr) {
>         p.primero = nuevo;
>     } else {
>         NodoEn(p, p.cantidad - 1)->siguiente = nuevo;
>     }
>     ++p.cantidad;
> }
>
> Punto GetVertice(const Poligono& p, unsigned i) {
>     return NodoEn(p, i)->valor;
> }
>
> void SetVertice(Poligono& p, unsigned i, Punto v) {
>     NodoEn(p, i)->valor = v;
> }
>
> void RemoveVertice(Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     Nodo* aBorrar;
>     if (i == 0) {
>         aBorrar = p.primero;
>         p.primero = p.primero->siguiente;
>     } else {
>         Nodo* anterior = NodoEn(p, i - 1);
>         aBorrar = anterior->siguiente;
>         anterior->siguiente = aBorrar->siguiente;
>     }
>     delete aBorrar;
>     --p.cantidad;
> }
>
> unsigned GetCantidadLados(const Poligono& p) { return p.cantidad; }
>
> double GetPerimetro(const Poligono& p) {
>     assert(p.cantidad >= 3);
>     double per = 0;
>     for (unsigned i = 0; i < p.cantidad; ++i) {
>         Punto a = GetVertice(p, i);
>         Punto b = GetVertice(p, (i + 1) % p.cantidad);
>         double dx = b.x - a.x, dy = b.y - a.y;
>         per += std::sqrt(dx * dx + dy * dy);
>     }
>     return per;
> }
>
> int main() {
>     // MISMAS pruebas de contenido que en el Capítulo 28 — el comportamiento observable no cambió
>     Poligono cuadrado;
>     CrearPoligono(cuadrado, Color::Rojo);
>     AddVertice(cuadrado, {0, 0});
>     AddVertice(cuadrado, {2, 0});
>     AddVertice(cuadrado, {2, 2});
>     AddVertice(cuadrado, {0, 2});
>
>     assert(GetCantidadLados(cuadrado) == 4);
>     assert(GetPerimetro(cuadrado) == 8);
>
>     RemoveVertice(cuadrado, 0);
>     assert(GetCantidadLados(cuadrado) == 3);
>
>     DestruirPoligono(cuadrado);   // nuevo respecto del Capítulo 28: acá SÍ hace falta
>     return 0;
> }
> ```
> `ExtraerPoligono`, `InsertarPoligono` y `CopiarPoligonosConPerimetrosMayoresA` del Capítulo 29 se pegan tal cual, sin modificar una sola línea — solo hay que acordarse de llamar `DestruirPoligono` después de usar cada `Poligono` leído dentro de `CopiarPoligonosConPerimetrosMayoresA`, cosa que no hacía falta con la versión de array.

### 💡 Trampas típicas / posibles preguntas

- Agregar `DestruirPoligono` y **no llamarlo** en los lugares donde antes no hacía falta (por ejemplo, dentro del `while` de `CopiarPoligonosConPerimetrosMayoresA`) → memory leak que no existía en la versión del Capítulo 28/29, porque ahí no había nada que liberar.
- Tocar la firma de alguna operación "para que sea más cómoda" con la lista enlazada (por ejemplo, agregarle un parámetro a `GetVertice`) → viola la restricción explícita del capítulo.
- Volver a cometer el error del Capítulo 26: `RemoveVertice(0)` sin el caso especial de mover `p.primero`.
- ¿Por qué `GetVertice` es más lento acá que en el Capítulo 28? Porque hay que recorrer nodo por nodo desde el principio (O(i)) en vez de calcular una dirección de memoria directa (O(1)).
- ¿Qué gana la versión enlazada a cambio de ese costo? `RemoveVertice`/`AddVertice` en el medio de la secuencia no requieren correr elementos (no hay `for` de desplazamiento como en la versión de array), y no hay límite de capacidad fijado de antemano.

---

## 31. Geometría Parte IV — Renderizar (SVG)

### 31.1. Polígonos en SVG

**Objetivos:** demostrar la implementación de tipos secuenciales dinámicos y aplicar lo visto en el trabajo anterior.

**Tareas:**
- Especificación del tipo polígono, que tiene cantidad dinámica de puntos y cuenta con las operaciones `GetCantidadDePuntos`, `GetPunto`, `SetPunto`, `InsertarPunto`, `RemoverPunto`.
- Implementación **contigua**.
- Implementación **enlazada**.
- Prueba con `assert` y generación del polígono en `SVG`.

**Productos:**
```
DD-PoligonosSvg
 |-- readme.md
 |-- Polígono.md            // Especificación del tipo polígono
 |-- PolígonoContigua.cpp   // Implementación y pruebas
 `-- PolígonoEnlazada.cpp   // Implementación y pruebas
```

> 💡 Notá que la interfaz de este capítulo **no es la misma** que la de los capítulos 26/28/30: acá no hay color ni `AddVertice`/`RemoveVértice`, sino una secuencia genérica de puntos con `InsertarPunto`/`RemoverPunto` — y a diferencia de `AddVertice` (que siempre agregaba al final), `InsertarPunto` debe poder insertar **en cualquier posición**. Es un tipo nuevo, más simple, pensado específicamente para practicar de nuevo contigua-vs-enlazada y para tener algo que dibujar.

### 📘 Teoría necesaria

#### `InsertarPunto`/`RemoverPunto` en una posición arbitraria

A diferencia de `AddVertice` (que siempre agrega al final), acá hay que **desplazar** elementos para abrir o cerrar un hueco en el medio de la secuencia:

- Contigua: para insertar en la posición `i`, primero correr todo lo que está en `[i, cantidad)` una casilla hacia la derecha (empezando por el final, para no pisar datos), y recién ahí escribir en `i`.
- Enlazada: para insertar en la posición `i`, hay que llegar al nodo `i-1` y enganchar el nodo nuevo entre ese y el que estaba en `i` — no hace falta mover nada, solo redirigir dos punteros.

```cpp
// Contigua — abrir un hueco en la posición i (versión con arreglo dinámico, como en el Cap. 26)
for (unsigned j = cantidad; j > i; --j) puntos[j] = puntos[j - 1];
puntos[i] = v;
++cantidad;
```

#### Renderizar a SVG — no es un tema nuevo, es formatear texto

SVG es un formato de imagen vectorial basado en **texto plano** (XML). "Renderizar un polígono" acá es, en la práctica, armar un `string`/`ostream` con las etiquetas `<svg>...</svg>` y una lista de coordenadas separadas por espacios — exactamente el mismo `cout <<`/concatenación de strings que ya usaste en el TP 18 para formatear una tabla, aplicado a un formato de salida distinto:

```xml
<svg xmlns="http://www.w3.org/2000/svg" width="400" height="400">
  <polygon points="0,0 100,0 100,100 0,100" fill="none" stroke="black" stroke-width="2" />
</svg>
```

`points="x1,y1 x2,y2 ..."` es simplemente la lista de vértices del polígono, en el mismo orden en que están guardados — que es justo lo que ya sabés recorrer con un `for` sobre `GetCantidadDePuntos`/ `GetPunto`.

### 🧭 Cómo encararlo

1. Definí la interfaz nueva (`GetCantidadDePuntos`, `GetPunto`, `SetPunto`, `InsertarPunto`, `RemoverPunto`) sobre un `struct Poligono` simple (sin color esta vez, solo puntos).
2. Implementación contigua: arreglo dinámico en el heap (mismo patrón de redimensión del Capítulo 26), pero con `InsertarPunto`/`RemoverPunto` que desplazan elementos en vez de solo agregar/quitar al final.
3. Implementación enlazada: lista de nodos (mismo patrón del Capítulo 30), con `InsertarPunto`/ `RemoverPunto` reenganchando punteros.
4. Recién con las dos implementaciones probadas con `assert`, escribí una función `PoligonoASvg(const Poligono&) -> string` (o que reciba un `ostream&`) que arme el texto SVG recorriendo los puntos con `GetCantidadDePuntos`/`GetPunto` — esa función puede ser **una sola**, compartida entre ambas implementaciones, si ambas exponen la misma interfaz pública (de nuevo, el principio de bajo acoplamiento de [[Modulos]]).
5. Guardá la salida con un `ofstream` a un archivo `.svg` y abrilo en un navegador para verificar visualmente — es un buen chequeo adicional a los `assert`, aunque el enunciado solo pida `assert` para la lógica del tipo en sí.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // archivo: PolígonoContigua.cpp
> #include <cassert>
> #include <fstream>
> #include <sstream>
> #include <string>
>
> struct Punto { double x, y; };
>
> struct Poligono {
>     Punto* puntos;
>     unsigned cantidad;
>     unsigned capacidad;
> };
>
> void CrearPoligono(Poligono& p);
> void DestruirPoligono(Poligono& p);
> unsigned GetCantidadDePuntos(const Poligono& p);
> Punto GetPunto(const Poligono& p, unsigned i);
> void SetPunto(Poligono& p, unsigned i, Punto v);
> void InsertarPunto(Poligono& p, unsigned i, Punto v);
> void RemoverPunto(Poligono& p, unsigned i);
> std::string PoligonoASvg(const Poligono& p, int ancho = 400, int alto = 400);
>
> static void Redimensionar(Poligono& p, unsigned nuevaCapacidad) {
>     Punto* nuevo = new Punto[nuevaCapacidad];
>     for (unsigned i = 0; i < p.cantidad; ++i) nuevo[i] = p.puntos[i];
>     delete[] p.puntos;
>     p.puntos = nuevo;
>     p.capacidad = nuevaCapacidad;
> }
>
> void CrearPoligono(Poligono& p) {
>     p.capacidad = 4;
>     p.cantidad = 0;
>     p.puntos = new Punto[p.capacidad];
> }
>
> void DestruirPoligono(Poligono& p) {
>     delete[] p.puntos;
>     p.puntos = nullptr;
>     p.cantidad = p.capacidad = 0;
> }
>
> unsigned GetCantidadDePuntos(const Poligono& p) { return p.cantidad; }
>
> Punto GetPunto(const Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     return p.puntos[i];
> }
>
> void SetPunto(Poligono& p, unsigned i, Punto v) {
>     assert(i < p.cantidad);
>     p.puntos[i] = v;
> }
>
> void InsertarPunto(Poligono& p, unsigned i, Punto v) {
>     assert(i <= p.cantidad);   // <= porque insertar en "cantidad" es agregar al final
>     if (p.cantidad == p.capacidad) Redimensionar(p, p.capacidad * 2);
>     for (unsigned j = p.cantidad; j > i; --j) p.puntos[j] = p.puntos[j - 1];
>     p.puntos[i] = v;
>     ++p.cantidad;
> }
>
> void RemoverPunto(Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     for (unsigned j = i; j + 1 < p.cantidad; ++j) p.puntos[j] = p.puntos[j + 1];
>     --p.cantidad;
> }
>
> std::string PoligonoASvg(const Poligono& p, int ancho, int alto) {
>     std::ostringstream svg;
>     svg << "<svg xmlns=\"http://www.w3.org/2000/svg\" width=\"" << ancho
>         << "\" height=\"" << alto << "\">\n  <polygon points=\"";
>     for (unsigned i = 0; i < GetCantidadDePuntos(p); ++i) {
>         Punto v = GetPunto(p, i);
>         if (i > 0) svg << ' ';
>         svg << v.x << ',' << v.y;
>     }
>     svg << "\" fill=\"none\" stroke=\"black\" stroke-width=\"2\" />\n</svg>\n";
>     return svg.str();
> }
>
> int main() {
>     Poligono p;
>     CrearPoligono(p);
>
>     InsertarPunto(p, 0, {0, 0});
>     InsertarPunto(p, 1, {100, 0});
>     InsertarPunto(p, 2, {100, 100});   // 0,0 -> 100,0 -> 100,100
>     assert(GetCantidadDePuntos(p) == 3);
>
>     InsertarPunto(p, 3, {0, 100});     // agregar al final vía insertar en "cantidad"
>     assert(GetCantidadDePuntos(p) == 4);
>     assert(GetPunto(p, 3).y == 100);
>
>     InsertarPunto(p, 1, {50, -20});    // insertar en el MEDIO
>     assert(GetCantidadDePuntos(p) == 5);
>     assert(GetPunto(p, 1).x == 50);
>     assert(GetPunto(p, 2).x == 100);   // lo que antes estaba en 1 se corrió a 2
>
>     RemoverPunto(p, 1);
>     assert(GetCantidadDePuntos(p) == 4);
>     assert(GetPunto(p, 1).x == 100);   // volvió a acomodarse
>
>     std::ofstream archivo("poligono.svg");
>     archivo << PoligonoASvg(p);
>
>     DestruirPoligono(p);
>     return 0;
> }
> ```
> ```cpp
> // archivo: PolígonoEnlazada.cpp — mismo patrón que PoligonoLink.cpp (Cap. 26) + inserción/remoción
> // en posición arbitraria, en vez de solo al final
> #include <cassert>
> #include <fstream>
> #include <sstream>
> #include <string>
>
> struct Punto { double x, y; };
> struct Nodo { Punto valor; Nodo* siguiente; };
> struct Poligono { Nodo* primero; unsigned cantidad; };
>
> void CrearPoligono(Poligono& p);
> void DestruirPoligono(Poligono& p);
> unsigned GetCantidadDePuntos(const Poligono& p);
> Punto GetPunto(const Poligono& p, unsigned i);
> void SetPunto(Poligono& p, unsigned i, Punto v);
> void InsertarPunto(Poligono& p, unsigned i, Punto v);
> void RemoverPunto(Poligono& p, unsigned i);
> std::string PoligonoASvg(const Poligono& p, int ancho = 400, int alto = 400);
>
> static Nodo* NodoEn(const Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     Nodo* actual = p.primero;
>     for (unsigned j = 0; j < i; ++j) actual = actual->siguiente;
>     return actual;
> }
>
> void CrearPoligono(Poligono& p) { p.primero = nullptr; p.cantidad = 0; }
>
> void DestruirPoligono(Poligono& p) {
>     Nodo* actual = p.primero;
>     while (actual != nullptr) {
>         Nodo* siguiente = actual->siguiente;
>         delete actual;
>         actual = siguiente;
>     }
>     p.primero = nullptr;
>     p.cantidad = 0;
> }
>
> unsigned GetCantidadDePuntos(const Poligono& p) { return p.cantidad; }
> Punto GetPunto(const Poligono& p, unsigned i) { return NodoEn(p, i)->valor; }
> void SetPunto(Poligono& p, unsigned i, Punto v) { NodoEn(p, i)->valor = v; }
>
> void InsertarPunto(Poligono& p, unsigned i, Punto v) {
>     assert(i <= p.cantidad);
>     Nodo* nuevo = new Nodo{v, nullptr};
>     if (i == 0) {
>         nuevo->siguiente = p.primero;
>         p.primero = nuevo;
>     } else {
>         Nodo* anterior = NodoEn(p, i - 1);
>         nuevo->siguiente = anterior->siguiente;
>         anterior->siguiente = nuevo;
>     }
>     ++p.cantidad;
> }
>
> void RemoverPunto(Poligono& p, unsigned i) {
>     assert(i < p.cantidad);
>     Nodo* aBorrar;
>     if (i == 0) {
>         aBorrar = p.primero;
>         p.primero = p.primero->siguiente;
>     } else {
>         Nodo* anterior = NodoEn(p, i - 1);
>         aBorrar = anterior->siguiente;
>         anterior->siguiente = aBorrar->siguiente;
>     }
>     delete aBorrar;
>     --p.cantidad;
> }
>
> std::string PoligonoASvg(const Poligono& p, int ancho, int alto) {
>     std::ostringstream svg;
>     svg << "<svg xmlns=\"http://www.w3.org/2000/svg\" width=\"" << ancho
>         << "\" height=\"" << alto << "\">\n  <polygon points=\"";
>     for (unsigned i = 0; i < GetCantidadDePuntos(p); ++i) {
>         Punto v = GetPunto(p, i);
>         if (i > 0) svg << ' ';
>         svg << v.x << ',' << v.y;
>     }
>     svg << "\" fill=\"none\" stroke=\"black\" stroke-width=\"2\" />\n</svg>\n";
>     return svg.str();
> }
>
> int main() {
>     // MISMAS pruebas que PolígonoContigua.cpp
>     Poligono p;
>     CrearPoligono(p);
>
>     InsertarPunto(p, 0, {0, 0});
>     InsertarPunto(p, 1, {100, 0});
>     InsertarPunto(p, 2, {100, 100});
>     InsertarPunto(p, 3, {0, 100});
>     InsertarPunto(p, 1, {50, -20});
>     assert(GetCantidadDePuntos(p) == 5);
>     assert(GetPunto(p, 1).x == 50);
>
>     RemoverPunto(p, 1);
>     assert(GetCantidadDePuntos(p) == 4);
>     assert(GetPunto(p, 1).x == 100);
>
>     std::ofstream archivo("poligono_enlazado.svg");
>     archivo << PoligonoASvg(p);
>
>     DestruirPoligono(p);
>     return 0;
> }
> ```
> **Dónde está lo nuevo respecto de los capítulos anteriores:** `InsertarPunto(i)`/`RemoverPunto(i)` con `i` arbitrario (no solo al final) — en la contigua, el `for` de desplazamiento corre en direcciones opuestas según sea inserción (de atrás para adelante, para no pisar datos) o remoción (de adelante para atrás); en la enlazada, es pura redirección de dos punteros, sin desplazar nada. `PoligonoASvg` no toca la representación interna en ningún momento — solo usa `GetCantidadDePuntos`/`GetPunto`, así que la misma función serviría para ambos archivos sin cambiar una línea.

### 💡 Trampas típicas / posibles preguntas

- En la implementación contigua, desplazar en el sentido equivocado al insertar (de adelante para atrás en vez de atrás para adelante) pisa datos antes de haberlos copiado — hay que ir del final hacia la posición `i`.
- `InsertarPunto` con `assert(i < cantidad)` en vez de `assert(i <= cantidad)` — insertar exactamente en la posición `cantidad` (agregar al final) es un caso válido y común, no debería fallar.
- Volver a olvidar `DestruirPoligono` (memory leak) — a esta altura del bloque, es el error que la cátedra más espera que hayas dejado de cometer.
- Generar el SVG accediendo directamente a `p.puntos[i]` (implementación contigua) en vez de pasar por `GetPunto` — funciona, pero acopla `PoligonoASvg` a una sola de las dos representaciones, en vez de ser una función que sirve para cualquier implementación que respete la interfaz.
- ¿Qué pasa si `ancho`/`alto` del SVG son más chicos que las coordenadas de los puntos? El polígono se dibuja igual, pero queda cortado fuera del `viewport` — no es un bug de la lógica del tipo, es una decisión de escala que quedaría para una versión más completa del renderizador (fuera del alcance de este capítulo).
