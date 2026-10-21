> Requisito: [[Call Stack y Heap]] (heap, `new[]`/`delete[]`) y [[Punteros y Listas Enlazadas#Aritmética de Punteros|Aritmética de Punteros]]. Un `std::vector` es, por dentro, un arreglo dinámico que te maneja el `new[]`/`delete[]` solo.

# `std::vector<T>` — el arreglo que crece

`std::vector<T>` es una **secuencia contigua de `T` cuyo tamaño se decide y cambia en tiempo de ejecución**. Está en `<vector>`.

| | `T a[N]` | `std::array<T,N>` | `std::vector<T>` |
|---|---|---|---|
| Tamaño | fijo, en compilación | fijo, en compilación | **variable, en ejecución** |
| Dónde viven los datos | stack (si es local) | stack (si es local) | **heap** |
| Sabe su tamaño | no | sí (`size()`) | sí (`size()`) |
| Se puede copiar con `=` | no | sí | sí (copia profunda) |
| Libera solo la memoria | no aplica | no aplica | **sí** (destructor) |

Es un **template** (ver [[Templates, Arreglos Asociativos y Arboles]]): `vector<int>`, `vector<string>` y `vector<vector<int>>` son tipos distintos generados desde el mismo molde.

## Declarar e inicializar

```cpp
#include <vector>
using namespace std;

vector<int> v;            // vacío, size() == 0
vector<int> w(5);         // 5 elementos, todos en 0 (los int se inicializan en 0)
vector<int> x(5, 7);      // 5 elementos, todos valen 7
vector<int> y{1, 2, 3};   // 3 elementos: 1, 2, 3
vector<int> z = y;        // COPIA: z es un vector independiente
```

> 💡 **Trampa de llaves vs paréntesis:** `vector<int> a(3, 3)` son **3 elementos** valiendo 3. `vector<int> b{3, 3}` son **2 elementos**: `3` y `3`. Con llaves manda la lista de valores. Si querés `n` copias de `x`, usá paréntesis.

## Operaciones principales

| Operación | Qué hace | Costo |
|---|---|---|
| `v.size()` | cantidad de elementos | O(1) |
| `v.empty()` | `true` si `size() == 0` | O(1) |
| `v.push_back(x)` | agrega `x` al final | O(1) amortizado |
| `v.pop_back()` | saca el último (**no lo devuelve**) | O(1) |
| `v.at(i)` | elemento `i`, **con chequeo** | O(1) |
| `v[i]` | elemento `i`, **sin chequeo** | O(1) |
| `v.front()`, `v.back()` | primero / último | O(1) |
| `v.clear()` | borra todos los elementos (`size()` queda en 0) | O(n) |
| `v.insert(v.begin() + i, x)` | inserta en la posición `i` | **O(n)** (corre los de la derecha) |
| `v.erase(v.begin() + i)` | borra el de la posición `i` | **O(n)** (corre los de la derecha) |
| `v.resize(n)` | cambia `size()` a `n` | O(n) |
| `v.reserve(n)` | reserva capacidad para `n`, **no cambia `size()`** | O(n) |

```cpp
vector<int> v;
v.push_back(10);
v.push_back(20);
v.push_back(30);      // v = {10, 20, 30}
v.pop_back();         // v = {10, 20}
v.at(1);              // 20
v.size();             // 2
```

## `v.at(10)` vs `v[10]`

Es la misma diferencia que en `std::array` (ver la sección de `.at()` vs `[]` en la guía del TP18).

- `v.at(i)`: si `i >= size()` **lanza `std::out_of_range`**. El error es visible.
- `v[i]`: no chequea nada. Fuera de rango es **comportamiento indefinido**.

```cpp
vector<int> v{1, 2, 3};
v.at(10) = 5;    // lanza std::out_of_range -> el programa aborta con un mensaje claro
v[10]    = 5;    // UB: puede "andar", pisar memoria ajena o romper más adelante
```

> 💡 **Trampa clásica:** `vector<int> v; v[0] = 5;` también es UB. Un vector vacío no tiene ninguna posición válida. `push_back` **agrega**; `[]` y `at` solo **acceden a lo que ya existe**. Si querés un vector de 10 posiciones usables desde el principio, declaralo como `vector<int> v(10);`.

## `size()` vs `capacity()`

Un vector reserva **más espacio del que usa** para no pedir memoria en cada `push_back`.

- `size()`: cuántos elementos tiene.
- `capacity()`: cuántos le caben antes de tener que reservar de nuevo. Siempre `capacity() >= size()`.

Cuando `push_back` encuentra `size() == capacity()`, el vector hace lo mismo que hacías a mano en el cap. 26 de la guía:

1. Reserva (con `new[]`) un bloque más grande (en general el doble).
2. Copia (o mueve) los elementos al bloque nuevo.
3. Libera (`delete[]`) el bloque viejo.

```
size=3 cap=3:  [10][20][30]
push_back(40) -> reserva cap=6 -> copia -> libera el viejo
size=4 cap=6:  [10][20][30][40][ ][ ]
```

Como el crecimiento es geométrico (cada vez se duplica), el costo de las copias, repartido entre todos los `push_back`, da **O(1) amortizado** por inserción. Si sabés cuántos elementos vas a meter, `reserve(n)` evita las reasignaciones intermedias.

```cpp
vector<int> v;
v.reserve(1000);          // capacity() >= 1000, size() sigue en 0
for (int i = 0; i < 1000; ++i) v.push_back(i);   // sin reasignar
```

## Recorrer un vector

```cpp
vector<int> v{1, 2, 3};

for (int x : v)        s += x;         // range-for, x es una copia
for (int& x : v)       x *= 2;         // x es referencia: modifica el vector
for (const auto& x : v) cout << x;     // solo lectura, sin copiar (ideal con elementos grandes)

for (size_t i = 0; i < v.size(); ++i)  // clásico, cuando necesitás el índice
    cout << v.at(i);
```

## Vector de vectores (matriz)

```cpp
vector<vector<int>> m(3, vector<int>(4, 0));   // 3 filas, 4 columnas, todo en 0
m.at(2).at(3) = 5;                              // fila 2, columna 3
```

A diferencia de `array<array<int,4>,3>`, las filas pueden tener **largos distintos** y cambiar en ejecución. El costo es que las filas no están contiguas entre sí.

## Un vector es un arreglo dinámico "con modales"

Por dentro tiene aproximadamente: un puntero al bloque del heap, `size` y `capacity`.

```cpp
// Idea de lo que hay adentro (simplificado, no es el código real de la STL)
template <typename T>
struct Vector {
    T*     datos;       // puntero al arreglo en el heap
    size_t tam;         // size()
    size_t cap;         // capacity()
};
```

- Por eso `v[i]` es **aritmética de punteros**: `*(datos + i)`, O(1).
- Por eso `insert`/`erase` en el medio son O(n): hay que correr los elementos de a uno.
- Por eso `v.begin()` y `v.end()` se comportan como punteros (`v.begin() + 2`, `v.end() - v.begin()`).
- El destructor del vector hace el `delete[]`: **no hay memory leaks** si lo usás normalmente. Por eso en C++ moderno se prefiere `vector` antes que `new T[n]` a mano.

Y es lo que implementás vos en los caps. 26 y 32 de la guía ([[06 - Estructuras Dinámicas - Geometría II]], [[07 - Secuencias, Pilas y Colas]]): un arreglo dinámico que se redimensiona.

## Invalidación (trampa de parcial)

Si `push_back` provoca una **reasignación**, el bloque viejo se libera: todo puntero, referencia o iterador a elementos del vector queda **colgando** (dangling).

```cpp
vector<int> v{1, 2, 3};
int* p = &v.at(0);
v.push_back(4);      // puede reasignar...
*p = 10;             // ...y p apunta a memoria ya liberada: UB
```

Es la misma idea de puntero colgante de [[Call Stack y Heap]], pero el que libera la memoria sos vos sin darte cuenta. Regla: **después de `push_back`, `insert`, `erase` o `resize`, no reutilices punteros ni iteradores que sacaste antes.**

## Otras trampas

- **`v.size() - 1` con vector vacío:** `size()` devuelve `size_t` (sin signo). `0 - 1` da un número enorme, no `-1`. `for (size_t i = 0; i <= v.size() - 1; ++i)` con un vector vacío itera "para siempre". Usá `i < v.size()`.
- **`pop_back()` no devuelve nada.** Para leer el último, primero `back()`, después `pop_back()`. `pop_back()` en un vector vacío es UB: chequeá `empty()`.
- **Pasar por valor copia todo.** `void f(vector<int> v)` copia el contenido entero. Para sólo leer: `const vector<int>&`; para modificar: `vector<int>&`.
- **`erase` dentro de un range-for** invalida el recorrido.
- **`vector<bool>`** es una especialización rara (guarda bits); no se comporta como los demás.

> **Ejercicio resuelto:** ¿Qué imprime?
> ```cpp
> vector<int> v(3, 1);   // {1, 1, 1}
> v.push_back(5);        // {1, 1, 1, 5}
> v.pop_back();          // {1, 1, 1}
> v.at(1) = 9;           // {1, 9, 1}
> cout << v.size() << v.back();
> ```
> `31`. `size()` es 3 y `back()` es `1`: la `9` quedó en el medio.

## ¿Cuándo usar cada uno? (en esta materia)

| Situación | Conviene |
|---|---|
| Tamaño conocido en compilación (12 meses, 3 vendedores, 4 regiones) | `std::array` |
| Tamaño que se conoce en ejecución o que va creciendo | `std::vector` |
| Te piden implementar vos el arreglo dinámico (caps. 26, 32) | `new[]`/`delete[]` **a mano**, no `vector` |
| Pila o cola para recorrer o mostrar (cap. 40, Historial del Browser) | `vector` con `push_back`/`pop_back` |

La consigna manda: si el trabajo dice `std::array`, o pide implementar el contenedor a mano, usar `vector` es romper la restricción.

# 🧪 Práctica

1. ¿Cuántos elementos tienen `vector<int> a(4, 2)` y `vector<int> b{4, 2}`? ¿Qué valen?
2. ¿Qué diferencia hay entre `v.at(5)` y `v[5]` si `v.size() == 3`?
3. ¿Qué hace `vector<int> v; v[0] = 1;` y cómo se corrige?
4. Un vector tiene `size() == 8` y `capacity() == 8`. ¿Qué pasa por dentro cuando hacés `push_back`?
5. ¿Por qué `push_back` es O(1) *amortizado* y no O(1) a secas? ¿Por qué `insert` en la posición 0 es O(n)?
6. ¿Qué tiene de malo `int* p = &v[0]; v.push_back(1); *p = 3;`?
7. Escribí una función que reciba un vector de enteros **sin copiarlo** y devuelva la suma. Después otra que lo modifique duplicando cada elemento.
8. Dada `for (size_t i = 0; i <= v.size() - 1; ++i)`, ¿qué falla si `v` está vacío?
9. Declarás una matriz de 3 filas × 4 columnas con `vector`. ¿En qué se diferencia de `array<array<int,4>,3>`?
10. ¿Por qué conviene `vector<string>` y no `stack<string>` para el historial del browser si hay que mostrarlo?
