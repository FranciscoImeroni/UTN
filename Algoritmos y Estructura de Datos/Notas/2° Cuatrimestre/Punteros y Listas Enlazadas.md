## [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Assets/AED 2018 K1051 Clase #24.pdf|PowerPoint]]

> Requisito: entender bien [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Call Stack y Heap.md|Call Stack y Heap]] antes de esta clase (acá se usa `new`/`delete` todo el tiempo).

# Punteros

Un puntero es una variable que **guarda una dirección de memoria**. Se dibuja como un círculo con una flecha hacia la caja (el dato) al que apunta.

- Un puntero puede apuntar a algo (flecha a una caja), a nada (`nullptr`, se dibuja con tierra ⏚), o dos punteros pueden apuntar a lo mismo.
- `*p` (asterisco) — **desreferenciar**: te da el valor al que apunta `p`, no la dirección.

### Asignación de punteros: `p = q` vs `*p = *q`

Esta es LA distinción que más se pregunta en el parcial.

```cpp
// Antes:
// p -> "Music"
// q -> "Calculus"

p = q;      // Hago que p apunte a lo MISMO que q.
            // "Music" queda huérfano (nadie apunta a él).
            // Ahora p y q apuntan los dos a "Calculus".

// En cambio:

*p = *q;    // Copio el CONTENIDO de lo que apunta q, adentro de lo que apunta p.
            // "Music" se sobreescribe y pasa a valer "Calculus".
            // p sigue apuntando a su misma caja de siempre (que ahora dice "Calculus"),
            // q sigue apuntando a la suya.
```

> 💡 Para el parcial: `p = q` cambia **a dónde apunta el puntero** (mueve la flecha). `*p = *q` cambia **el valor de la caja** (no toca las flechas). Son operaciones completamente distintas aunque se parezcan.

# Aritmética de Punteros

Un puntero guarda una dirección, y una dirección es un número. Se puede **sumarle y restarle enteros** para moverse por la memoria. Tiene sentido cuando los datos están **contiguos**, como en un arreglo.

## `p + n` avanza `n` elementos, no `n` bytes

El compilador multiplica por `sizeof(T)` automáticamente. Si `p` es `int*` y `sizeof(int)` es 4, entonces `p + 1` es la dirección de `p` más 4 bytes.

```cpp
int a[5] = {10, 20, 30, 40, 50};
int* p = a;          // p apunta al primer elemento: a[0]

*p          // 10
*(p + 2)    // 30   -> avanzo 2 ints (8 bytes) y desreferencio
p++;        // ahora p apunta a a[1] (se movió sizeof(int) bytes)
*p          // 20
```

```
Dirección:   1000   1004   1008   1012   1016
Contenido:   [10]   [20]   [30]   [40]   [50]
              a[0]   a[1]   a[2]   a[3]   a[4]
              p      p+1    p+2    p+3    p+4
```

Con un `double*` (8 bytes) el paso es 8; con un `char*` es 1. **El tipo del puntero define cuánto se mueve.** Por eso no existe aritmética sobre `void*`: no sabe `sizeof` de lo que apunta.

## `a[i]` es azúcar sintáctico de `*(a + i)`

El operador `[]` **es** aritmética de punteros más desreferencia.

```cpp
a[2] == *(a + 2)    // true, es literalmente lo mismo
p[2] == *(p + 2)    // también funciona con un puntero cualquiera
2[a] == a[2]        // true (la suma es conmutativa), compila pero no lo escribas nunca
```

Por esto el acceso a un elemento de un arreglo es O(1): una suma y un acceso a memoria, sin importar el índice. Una lista enlazada no puede hacerlo porque sus nodos no están contiguos. Para llegar al `i`-ésimo hay que seguir `i` punteros.

## Operaciones permitidas

| Operación | Resultado | Ejemplo |
|---|---|---|
| `p + n`, `p - n` | puntero | `p + 3` |
| `p++`, `++p`, `p--`, `p += n` | mueve `p` | `p += 2` |
| `p - q` | **entero** (`ptrdiff_t`): cantidad de **elementos** entre ambos | `&a[4] - &a[0] == 4` |
| `p < q`, `p == q`, `p != q`... | `bool` | `p < a + n` |
| `p + q`, `p * n`, `p / n` | **no existen** | no compila |

> 💡 `p - q` da la cantidad de **elementos** de distancia, no de bytes. Y solo está definido si `p` y `q` apuntan al **mismo arreglo**. Restar punteros de objetos distintos es comportamiento indefinido.

## Recorrer un arreglo con punteros

Es el patrón base. Los STL (`begin()`/`end()`) lo imitan.

```cpp
int suma(const int* a, size_t n) {
    int s = 0;
    for (const int* p = a; p != a + n; ++p)   // a + n: "uno pasado el final"
        s += *p;
    return s;
}
```

- `a + n` apunta **uno pasado el último elemento**. Es una dirección válida para **comparar**, pero **nunca** se puede desreferenciar (`*(a + n)` es comportamiento indefinido).
- Es el mismo criterio de `[begin, end)` de la biblioteca estándar: `v.begin()` es el primer elemento y `v.end()` es uno pasado el final. Sus iteradores soportan `+ n`, `- n` y `end() - begin()` igual que los punteros. Por eso `std::sort(v.begin(), v.begin() + cantidad)` ordena solo los primeros `cantidad` elementos.
- Cuidado con `<=`: `p <= a + n` hace una vuelta de más.

## Con memoria dinámica

Un `new T[n]` devuelve un puntero al primer elemento, y el resto sale por aritmética:

```cpp
int* h = new int[4]{1, 2, 3, 4};
int* fin = h + 4;                       // uno pasado el final
for (int* r = h; r != fin; ++r) *r *= 2;
delete[] h;                             // OJO: delete[] se hace sobre el puntero ORIGINAL
```

> 💡 Si movés `h` con `h++` y después hacés `delete[] h`, estás liberando una dirección que `new[]` nunca devolvió: comportamiento indefinido. Para recorrer, usá un puntero auxiliar (`r`) y dejá `h` intacto.

## Decay: el arreglo se convierte en puntero

En casi cualquier expresión, el nombre de un arreglo se convierte en un puntero a su primer elemento, y **se pierde el tamaño**.

```cpp
int a[5];
sizeof(a)            // 20  (5 * sizeof(int)): acá "a" todavía es el arreglo completo
int* p = a;          // decay: p apunta a a[0]
sizeof(p)            // 8 (tamaño de un puntero en 64 bits), no 20

size_t tam(int a[]) { return sizeof(a); }  // el parámetro "int a[]" ES "int* a"
tam(a)               // 8, no 20. El compilador avisa con -Wall
```

Por eso las funciones sobre arreglos de C reciben **puntero + cantidad** (`const int* a, size_t n`). `std::array<T, N>` no decae: sabe su tamaño y se puede pasar por valor o por referencia.

## Trampas de parcial

- **Salirse del arreglo:** `p = a + 7` en un `int a[5]` es indefinido, aunque no accedas a `*p`. Solo se permite hasta `a + 5` (uno pasado el final).
- **`p + 1` en `int*` no suma 1 byte.** Suma `sizeof(int)`. Si dicen "dirección 1000", el resultado es 1004.
- **Dangling por aritmética:** `delete[] h` después de mover `h`.
- **`sizeof` de un parámetro arreglo** devuelve el tamaño de un puntero.
- **Nodos de una lista:** `p + 1` **no** lleva al siguiente nodo. Los nodos no son contiguos, el siguiente se alcanza con `p->next`. La aritmética solo sirve en memoria contigua.

> **Ejercicio resuelto:** dado `int a[] = {5, 10, 15, 20}; int* p = a + 1;`, ¿cuánto valen `*p`, `*(p + 2)`, `p[1]`, `p - a` y `*(a + 3) - *p`?
> `*p` = 10 · `*(p + 2)` = 20 · `p[1]` = 15 · `p - a` = 1 · `*(a + 3) - *p` = 20 - 10 = 10.

### 🧪 Práctica

1. Si `int* p` vale 2000 y `sizeof(int)` es 4, ¿cuánto vale `p + 3`? ¿Y si fuera `double*` con `sizeof(double)` 8?
2. Reescribí `for (int i = 0; i < n; ++i) s += a[i];` usando solo punteros, sin `[]` ni `i`.
3. ¿Por qué `p - q` es válido pero `p + q` no? ¿Qué devuelve y en qué unidades?
4. ¿Qué pasa con `*(a + n)` en un arreglo de `n` elementos? ¿Y con la expresión `a + n` sola?
5. ¿Qué imprime `sizeof(a)` dentro de `void f(int a[])` si se la llama con `int x[10]`? ¿Por qué?
6. Hacé una función `int* Buscar(int* a, size_t n, int x)` que devuelva un puntero al primer `x` o `a + n` si no está. ¿Por qué conviene devolver `a + n` y no `nullptr`? (Pensá en `[begin, end)`).

> 💡 El arreglo dinámico que se redimensiona solo, sin que hagas `new[]`/`delete[]` a mano, es `std::vector`: ver [[Vectores - std vector]].

# Reserva Dinámica Manual — repaso rápido

```cpp
Type* p;          // declaro el puntero (todavía no apunta a nada válido)
p = nullptr;      // apunta a nada, explícito
p = new Type;     // reservo un Type en el heap; p apunta ahí
*p                // accedo/modifico el contenido
cin >> *p;        // leo un valor y lo guardo ahí
cout << *p;       // lo imprimo
delete p;         // libero la reserva. p queda "colgando" (dangling) — buena práctica: p = nullptr después.
```

**¿Qué crea `new`? ¿Qué borra `delete`?** `new` reserva espacio en el heap y devuelve la dirección (crea un objeto sin nombre, solo accesible vía puntero). `delete` libera ese espacio — el puntero sigue existiendo como variable, pero ya no es válido usarlo para leer/escribir (a menos que le asignes otra cosa).

# ¿Qué es un Nodo? `node = (data, link)`

Un **nodo** es la unidad básica de las estructuras enlazadas: un par compuesto por

- **data** (el dato en sí) — otros nombres que vas a ver en distinta bibliografía: *car*, *entry*, *payload*, *info*, *value*
- **link** (el puntero al siguiente) — otros nombres: *cdr*, *enlace*, **next** (el más común), *nxt*

```cpp
// Node de int
struct Node{
    int   value;
    Node* next;
};

// Node de un Type genérico
struct Node{
    Type  value;
    Node* next;
};

// Node con template (genérico de verdad)
template<typename T>
struct Node<T>{
    T      value;
    Node<T>* next;
};
```

Un nodo se puede dibujar de formas cada vez más abstractas: como tabla con nombres de campos → como caja partida en dos (value | next) → como círculo con flecha nombrada `next` → simplemente círculo→círculo → puntito→puntito. Todas representan lo mismo.

## Ejemplo: armar 3 nodos enlazados a mano

```cpp
Node first_node;
first_node.value = 'a';

Node* p0 = &first_node;
Node* p1 = new Node;
(*p1).value = 'b';     // (*p1).value  y  p1->value  son EXACTAMENTE lo mismo
p1->value  = 'b';      // el operador -> es azúcar sintáctica para (*p).campo

p0->next = p1;

Node* p2 = new Node;
p2->value = 'c';
p2->next  = p0;         // p2 apunta de vuelta al principio -> queda CIRCULAR

p1->next = p2;

// Recorrido (nótese que es do-while porque es circular: hay que parar
// cuando volvemos al punto de partida, no cuando lleguemos a nullptr)
Node* p = p0;
do{
    cout << p->value;
    p = p->next;
} while(p != p0);
```

> 💡 `p->campo` es siempre equivalente a `(*p).campo`. Si en el parcial ves una de las dos formas, es intercambiable — elegí la que te resulte más cómoda de escribir.

## Ejercicio resuelto — de código a diagrama

```cpp
Node* p0 = new Node;
p0->value = '0';

Node* p1 = p0->next = new Node;   // ojo: la asignación va de derecha a izquierda:
                                   // primero "new Node", eso se guarda en p0->next,
                                   // y ESE MISMO valor también se guarda en p1
p1->value = '1';

Node* p2 = p1->next = new Node;
p2->value = '2';
p2->next = p1;                     // acá p2 apunta "para atrás", a p1: queda un ciclo
```

Diagrama resultante: `p0 -> ['0'] -> ['1'] <-> ['2']` donde el nodo `'1'` y el nodo `'2'` quedan apuntándose mutuamente (`p1->next == p2` y `p2->next == p1`), formando un ciclo entre esos dos. `p0` sigue apuntando al nodo `'0'`, que apunta al `'1'`.

> 💡 Trampa: `Node* p1 = p0->next = new Node;` NO crea dos nodos. Crea **uno solo** con `new Node`, y esa misma dirección se la asigna tanto a `p0->next` como a `p1`. Es un error común pensar que son dos reservas distintas.

## Recorrer una lista enlazada (no circular, termina en `nullptr`)

```cpp
Node* first = ...;   // apunta al primer nodo, o es nullptr si la lista está vacía

for(Node* p = first; p; p = p->next)
    cout << p->value;
```

`p` (como condición del for) es `true` mientras no sea `nullptr`. Cuando `p->next` de un nodo vale `nullptr` (representado en los diagramas con un `0` o con tierra ⏚), ahí termina la lista.

---

# 🧪 Práctica

1. Dado:
   ```cpp
   Node a, b;
   Node* p = &a;
   Node* q = &b;
   ```
   ¿Qué diferencia hay entre ejecutar `p = q;` y ejecutar `*p = *q;`? Dibujá el "antes" y el "después" de cada uno.

2. Escribí el código C++ que arme esta lista (no circular, termina en `nullptr`): tres nodos con valores `'x'`, `'y'`, `'z'` en ese orden, apuntados desde una variable `first`.

3. ¿Qué imprime este recorrido, sabiendo que la lista es circular y `p0` es el primer nodo?
   ```cpp
   Node* p = p0;
   do{ cout << p->value; p = p->next; } while(p != p0);
   ```
   ¿Qué pasaría si en vez de `do-while` usaras un `for` como el de listas no circulares (`for(Node* p=p0; p; p=p->next)`)? Justificá.

4. ¿Por qué `p->campo` es lo mismo que `(*p).campo`? ¿Cuál preferís usar y por qué son intercambiables?

5. Si tenés `Node* p1 = p0->next = new Node;`, ¿cuántos nodos nuevos se crearon? ¿Qué apunta a qué al final?
