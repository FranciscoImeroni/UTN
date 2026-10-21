⬅️ [[00 - Índice y Requisitos Generales]]

> Requisitos previos: tené fresco [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Punteros y Listas Enlazadas.md|Punteros y Listas Enlazadas]] (nodo, `new`/`delete`, recorrido con puntero) y [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Call Stack y Heap.md|Call Stack y Heap]] (stack vs heap, memory leaks). Los capítulos 32 a 38 son, en esencia, aplicar esos dos temas para construir **estructuras dinámicas con nombre propio** (Secuencia, Stack, Queue, Árbol).

## ⚠️ Nota sobre estas páginas del libro

A diferencia de otros capítulos, en las páginas 105-121 del PDF (capítulos 32 a 38) el libro **no da Objetivos, Temas ni Problema en prosa, ni transcribe la interfaz (operaciones) del TDA**. Solo da, por cada trabajo: `Restricciones`, `Tareas` y `Productos` (el árbol de archivos a entregar). Esto es intencional: la primera tarea de cada trabajo es literalmente **"Especificar tipo"** — diseñar vos mismo la interfaz del TDA y documentarla en el archivo `*.md // Especificación.` que pide el árbol de productos, antes de programar nada.

Por eso, en la sección **Restricciones / Operaciones** de cada capítulo transcribo las restricciones tal cual el PDF, y agrego con criterio propio (marcado como tal) la interfaz estándar que se espera de cada TDA — son las operaciones clásicas de Secuencia/Stack/Queue/BST que vas a encontrar en cualquier bibliografía de la materia, y que la cátedra evalúa en el oral aunque el enunciado no las liste letra por letra.

## 🆚 Contigua vs. Enlazada — la comparación que atraviesa todo este capítulo

El libro pide implementar **la misma interfaz** dos veces: una con almacenamiento contiguo (`array` redimensionable con `new`/`delete`) y otra con almacenamiento enlazado (nodos con `new`, sin límite fijo de capacidad). Es el mismo ejercicio de "un problema, dos herramientas" que ya viste con `Dim0..Dim3` en `26-053-01/18-Arreglos & Dimensiones - Total de Ventas/Guia-Examen-Oral.md`, aplicado ahora a TDAs en vez de a dimensiones.

| Aspecto | Implementación **Contigua** (array) | Implementación **Enlazada** (nodos) |
|---|---|---|
| Almacenamiento interno | Un bloque `T*` reservado con `new T[capacidad]` | Nodos sueltos (`struct Nodo{T dato; Nodo* siguiente;}`) reservados uno por uno con `new` |
| Capacidad | **Finita**, hay que trackear `capacidad` y `cantidad`, y **redimensionar** (reservar más grande, copiar, liberar lo viejo) cuando se llena | **No hay límite fijo** — cada elemento nuevo es un `new Nodo` independiente |
| Acceso por posición (`Obtener(i)`) | **O(1)** — [[Punteros y Listas Enlazadas#Aritmética de Punteros\|aritmética de punteros]], `datos[i]` | **O(n)** — hay que recorrer nodo por nodo desde el primero |
| Insertar/eliminar al **final** | O(1) amortizado (O(n) el día que toca redimensionar) | O(1) si hay puntero al último nodo; O(n) si solo hay puntero al primero |
| Insertar/eliminar al **principio** | O(n) — hay que correr todos los elementos un lugar | **O(1)** — se reacomoda un solo puntero |
| Insertar/eliminar en el **medio** | O(n) — correr elementos | O(n) — hay que llegar caminando hasta la posición, pero la escritura en sí es O(1) |
| Uso de memoria | Compacto, sin overhead por elemento, pero puede haber espacio **reservado y sin usar** (capacidad > cantidad) | Un poco más pesado por elemento (cada nodo carga el puntero `siguiente` aparte), pero **usa exactamente lo que necesita**, nunca de más |
| Riesgo típico | Desbordar el array sin redimensionar (UB); no liberar el array viejo al redimensionar (leak) | Memory leak si se hace `Eliminar`/`Pop` sin `delete` del nodo; punteros colgantes si se usa un nodo después de borrarlo |
| Localidad de memoria (caché) | Buena — los elementos están contiguos | Mala — cada nodo puede estar en cualquier lado del heap |

> 💡 Para el oral: la pregunta trampa es "¿cuál es mejor?". Respuesta correcta: **depende del patrón de uso**. Si accedés mucho por índice al azar → contigua. Si insertás/eliminás mucho al principio o no sabés de antemano cuántos elementos vas a tener → enlazada.

---

## 32. Secuencia Dinámica — Implementación Contigua

**Objetivos:** diseñar e implementar el TDA **Secuencia Dinámica** — una colección **ordenada** de elementos cuyo tamaño **crece y decrece en tiempo de ejecución** (a diferencia de `std::array<T,N>`, que vimos en `[[04 - Abstracción con Tipos]]` con tamaño fijo en compilación) — usando un `array` como estructura de almacenamiento interna, separando interfaz pública de implementación privada (como en `[[06 - Estructuras Dinámicas - Geometría II]]`, capítulo 27).

**Temas** *(inferido; el PDF no los lista en esta sección para este capítulo)*:
- TDA Secuencia Dinámica (interfaz abstracta, independiente de cómo se implemente por dentro).
- Reserva dinámica con `array` + redimensionamiento (`new`/`delete[]`).
- Separación interfaz/implementación (`.h` declara, `.cpp` implementa).
- Diseño de pruebas (`assert`) y de un programa de aplicación que use el TDA.

**Problema:** implementar una secuencia que se comporte como una lista ordenada indexable (podés `Obtener`/`Modificar` el elemento en la posición `i`, `Insertar`/`Eliminar` en cualquier posición, `Agregar` al final) pero que, a diferencia de un array común, **no tenga un tamaño fijo conocido de antemano**: el usuario del TDA no debería tener que preocuparse por la capacidad interna — el propio TDA se encarga de redimensionar el array cuando se queda sin lugar.

**Restricciones / Operaciones:**
- *(del PDF, textual)* La implementación debe basarse en `array`, por lo tanto tienen una capacidad máxima (que hay que manejar/crecer vos mismo — el `array` de la biblioteca estándar, `std::array`, NO sirve acá porque su tamaño es fijo en compilación; hay que usar un puntero `T*` reservado con `new[]`).
- *(interfaz propuesta, con criterio propio — no viene listada en el PDF)*:

```
Secuencia<T> = <colección ordenada de elementos de tipo T, tamaño variable>

Crear()                       -> Secuencia vacía
Destruir(s)                   -> libera toda la memoria reservada
EsVacia(s)                    -> bool
Tamanio(s)                    -> int  (cantidad de elementos actuales)
Agregar(s, valor)             -> agrega "valor" al final
Insertar(s, pos, valor)       -> inserta "valor" en la posición pos (0 <= pos <= Tamanio(s))
Eliminar(s, pos)              -> quita el elemento en la posición pos (0 <= pos < Tamanio(s))
Obtener(s, pos)               -> devuelve el valor en la posición pos
Modificar(s, pos, valor)      -> reemplaza el valor en la posición pos
```

**Tareas** *(textual del PDF)*:
- Especificar tipo.
- Diseñar pruebas.
- Implementar parte pública.
- Implementar parte privada.
- Probar.
- Diseñar un programa de aplicación.

**Productos:**
```
DD-SecDinCont
 |-- readme.md
 |-- SecDin.md // Especificación.
 |-- SecDinTest.cpp
 |-- SecDin.h
 |-- SecDinCont.cpp
 `-- SecDinApp.cpp
```

### 📘 Teoría necesaria

**TDA Secuencia (abstracto), antes de pensar en "cómo" se guarda:** es un conjunto ordenado de elementos, identificado por su posición (índice `0..Tamanio-1`), con las operaciones de arriba. La interfaz **no dice nada** sobre si por dentro hay un array o una lista enlazada — eso es exactamente lo que separa "especificar" (capítulo 32.2, tarea 1) de "implementar" (tareas 3-4), y es el mismo principio de interfaz/implementación de `[[06 - Estructuras Dinámicas - Geometría II]]`.

**Estructura de datos interna concreta — array dinámico redimensionable:**

```cpp
template<typename T>
class SecDin{
    // ...
private:
    T*  datos;       // bloque reservado con new T[capacidad]
    int capacidad;    // cuántas celdas hay reservadas (pueden estar vacías)
    int cantidad;     // cuántas celdas están realmente usadas (cantidad <= capacidad)
};
```

La clave: **`cantidad` (lo lógico, lo que ve el usuario) es distinto de `capacidad` (lo físico, lo reservado)**. Cuando `cantidad == capacidad` y hay que agregar un elemento más, no hay lugar: toca **redimensionar**.

**Redimensionar, paso a paso:**
1. Reservar un bloque nuevo, más grande (típicamente el doble: `nuevaCapacidad = capacidad == 0 ? 1 : capacidad * 2`).
2. Copiar los `cantidad` elementos viejos al bloque nuevo.
3. `delete[]` el bloque viejo.
4. El puntero `datos` pasa a apuntar al bloque nuevo; actualizar `capacidad`.

```cpp
// ejemplo mínimo, no la solución completa
void Redimensionar(int nuevaCapacidad){
    T* nuevo = new T[nuevaCapacidad];
    for(int i = 0; i < cantidad; ++i)
        nuevo[i] = datos[i];
    delete[] datos;
    datos = nuevo;
    capacidad = nuevaCapacidad;
}
```

> 💡 Lo que implementás acá a mano es lo que hace `std::vector` por dentro: ver [[Vectores - std vector]].

**Complejidad:** `Obtener`/`Modificar` son O(1) (aritmética de punteros, ver [[Punteros y Listas Enlazadas#Aritmética de Punteros]]). `Agregar` al final es O(1) *amortizado* (cada tanto toca redimensionar y copiar todo, pero eso pasa cada vez con menos frecuencia relativa). `Insertar`/`Eliminar` en una posición cualquiera son O(n) porque hay que correr los elementos posteriores un lugar para abrir/cerrar el hueco.

### 🧭 Cómo encararlo

1. Escribí primero la especificación (`SecDin.md`): la interfaz de arriba, con pre/postcondiciones de cada operación (ej. `Eliminar` requiere `0 <= pos < Tamanio(s)`).
2. Definí la clase en `SecDin.h` con los tres campos privados (`datos`, `capacidad`, `cantidad`) y la interfaz pública declarada (sin cuerpo).
3. Implementá primero lo más simple: `Crear` (constructor, todo en 0/`nullptr`), `EsVacia`, `Tamanio`.
4. Después `Obtener`/`Modificar` — son directos, `datos[pos]`, sin tocar tamaño.
5. Después `Agregar` (caso particular de `Insertar` al final) — incluí la lógica de redimensionar acá primero, en el caso más simple.
6. Recién después `Insertar`/`Eliminar` en una posición arbitraria — pensalos como "abrir/cerrar un hueco": `Insertar` corre todo un lugar a la derecha *desde el final hacia la posición* (para no pisarse), `Eliminar` corre todo un lugar a la izquierda *desde la posición hacia el final*.
7. Casos borde a probar con `assert`: secuencia vacía, `Insertar`/`Agregar` que dispara redimensionar, `Eliminar` del único elemento, `pos == 0` y `pos == Tamanio()-1`.
8. El destructor (`Destruir`) tiene que hacer `delete[] datos` — si no, memory leak.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // ===================== archivo: SecDin.h =====================
> // Con templates, declaración e implementación deben terminar juntas para
> // que el compilador pueda instanciar SecDin<T> con cualquier T. Por eso
> // este .h declara la clase e incluye SecDinCont.cpp al final (que sigue
> // existiendo como archivo aparte, para respetar la separación pedida por
> // la consigna, pero el compilador lo procesa como si fuera parte del .h).
> #ifndef SECDIN_H
> #define SECDIN_H
>
> template<typename T>
> class SecDin{
> public:
>     SecDin();
>     ~SecDin();
>
>     bool EsVacia() const;
>     int  Tamanio() const;
>
>     void Agregar(const T& valor);
>     void Insertar(int pos, const T& valor);
>     void Eliminar(int pos);
>     T    Obtener(int pos) const;
>     void Modificar(int pos, const T& valor);
>
> private:
>     T*  datos;
>     int capacidad;
>     int cantidad;
>
>     void Redimensionar(int nuevaCapacidad);
> };
>
> #include "SecDinCont.cpp"
> #endif
>
> // ===================== archivo: SecDinCont.cpp =====================
> #include "SecDin.h"
> #include <cassert>
>
> template<typename T>
> SecDin<T>::SecDin() : datos(nullptr), capacidad(0), cantidad(0) {}
>
> template<typename T>
> SecDin<T>::~SecDin(){
>     delete[] datos;
> }
>
> template<typename T>
> bool SecDin<T>::EsVacia() const{
>     return cantidad == 0;
> }
>
> template<typename T>
> int SecDin<T>::Tamanio() const{
>     return cantidad;
> }
>
> template<typename T>
> void SecDin<T>::Redimensionar(int nuevaCapacidad){
>     T* nuevo = new T[nuevaCapacidad];
>     for(int i = 0; i < cantidad; ++i)
>         nuevo[i] = datos[i];
>     delete[] datos;
>     datos = nuevo;
>     capacidad = nuevaCapacidad;
> }
>
> template<typename T>
> void SecDin<T>::Insertar(int pos, const T& valor){
>     assert(pos >= 0 && pos <= cantidad);
>     if(cantidad == capacidad)
>         Redimensionar(capacidad == 0 ? 1 : capacidad * 2);
>     for(int i = cantidad; i > pos; --i)   // corre a la derecha, desde el final
>         datos[i] = datos[i-1];
>     datos[pos] = valor;
>     ++cantidad;
> }
>
> template<typename T>
> void SecDin<T>::Agregar(const T& valor){
>     Insertar(cantidad, valor);
> }
>
> template<typename T>
> void SecDin<T>::Eliminar(int pos){
>     assert(pos >= 0 && pos < cantidad);
>     for(int i = pos; i < cantidad - 1; ++i)  // corre a la izquierda
>         datos[i] = datos[i+1];
>     --cantidad;
> }
>
> template<typename T>
> T SecDin<T>::Obtener(int pos) const{
>     assert(pos >= 0 && pos < cantidad);
>     return datos[pos];
> }
>
> template<typename T>
> void SecDin<T>::Modificar(int pos, const T& valor){
>     assert(pos >= 0 && pos < cantidad);
>     datos[pos] = valor;
> }
>
> // ===================== archivo: SecDinTest.cpp =====================
> #include "SecDin.h"
> #include <cassert>
>
> int main(){
>     SecDin<int> s;
>     assert(s.EsVacia());
>     s.Agregar(10);
>     s.Agregar(20);
>     s.Agregar(30);              // fuerza al menos una redimensión
>     assert(s.Tamanio() == 3);
>     assert(s.Obtener(1) == 20);
>
>     s.Insertar(1, 99);          // 10, 99, 20, 30
>     assert(s.Obtener(1) == 99 && s.Obtener(2) == 20);
>
>     s.Eliminar(0);              // 99, 20, 30
>     assert(s.Obtener(0) == 99 && s.Tamanio() == 3);
>
>     s.Modificar(2, 111);
>     assert(s.Obtener(2) == 111);
>     return 0;
> }
>
> // ===================== archivo: SecDinApp.cpp =====================
> #include "SecDin.h"
> #include <iostream>
>
> int main(){
>     SecDin<int> notas;
>     for(int n; std::cin >> n; )
>         notas.Agregar(n);
>
>     std::cout << "Cargaste " << notas.Tamanio() << " notas:\n";
>     for(int i = 0; i < notas.Tamanio(); ++i)
>         std::cout << i << ": " << notas.Obtener(i) << '\n';
>     return 0;
> }
> ```
> **Explicación de lo no obvio:** `Insertar` recorre desde el final hacia `pos` (`for i = cantidad; i > pos; --i`) y no al revés, porque si copiaras de adelante hacia atrás pisarías datos que todavía no copiaste. `Eliminar` hace lo simétrico, de `pos` hacia el final. El `#include "SecDinCont.cpp"` al final del `.h` es el truco estándar para poder tener templates "separados" en dos archivos sin errores de enlazado (linker), manteniendo la separación de archivos que pide la consigna.

### 💡 Trampas típicas / posibles preguntas

- Redimensionar y **olvidarse el `delete[]` del bloque viejo** → memory leak en cada redimensión.
- Copiar con `for(i=0; i<capacidad; ++i)` en vez de `i<cantidad` en `Redimensionar` → copia basura (celdas nunca inicializadas) de más.
- `Insertar`/`Eliminar` corriendo los elementos **en el sentido equivocado** → pisa datos antes de copiarlos.
- Con templates, **linkear `.h` y `.cpp` por separado sin el `#include` al final** → error de linker ("undefined reference") porque el compilador nunca ve el cuerpo de las funciones al compilar `SecDinTest.cpp`.
- Confundir `capacidad` (reservado) con `cantidad` (usado) en las condiciones de los `assert` o los bucles.

---

## 33. Stack — Implementación Contigua

**Objetivos:** implementar el TDA **Stack** (Pila), la estructura **LIFO** (*Last In, First Out* — el último que entra es el primero que sale) más simple de todas, usando un `array` redimensionable como almacenamiento interno.

**Temas** *(inferido)*:
- TDA Stack: interfaz LIFO, invariante (solo se puede tocar la **cima**).
- Reutilización del patrón de redimensionamiento visto en la Secuencia Dinámica (cap. 32).
- Relación con el **call stack** real de la CPU — ver [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Call Stack y Heap.md|Call Stack y Heap]], que ya explica push/pop/top a nivel de invocación de funciones; acá se construye el mismo concepto como TDA de biblioteca, para guardar cualquier tipo de dato, no solo stack frames.

**Problema:** implementar una pila: una colección donde solo se puede **agregar** (`Push`) y **quitar** (`Pop`) por un único extremo, la **cima** (*top*); y solo se puede **consultar** el elemento de la cima (`Top`), nunca el resto directamente.

**Restricciones / Operaciones:**
- *(del PDF, textual)* La implementación debe basarse en `array`, por lo tanto tienen una capacidad máxima (a redimensionar dinámicamente).
- *(interfaz propuesta, con criterio propio)*:

```
Stack<T> = <colección LIFO de elementos de tipo T>

Crear()          -> Stack vacío
Destruir(p)       -> libera toda la memoria
EsVacia(p)        -> bool
Tamanio(p)        -> int
Push(p, valor)    -> apila "valor" en la cima
Pop(p)            -> desapila el elemento de la cima (requiere !EsVacia(p))
Top(p)            -> devuelve (sin quitar) el elemento de la cima (requiere !EsVacia(p))
```

**Tareas** *(textual)*: Especificar tipo. Diseñar pruebas. Implementar parte pública. Implementar parte privada. Probar. Diseñar un programa de aplicación.

**Productos:**
```
DD-StackCont
 |-- readme.md
 |-- Stack.md // Especificación.
 |-- StackTest.cpp
 |-- Stack.h
 |-- StackCont.cpp
 `-- StackApp.cpp
```

### 📘 Teoría necesaria

**TDA Stack (abstracto):** LIFO. Invariante clave: **nunca podés tocar nada que no sea la cima**. No hay `Obtener(pos)` como en la Secuencia — esa es justo la diferencia de interfaz entre ambos TDA, aunque por dentro se puedan implementar de manera muy parecida.

| Operación | Complejidad esperada (en cualquier implementación razonable) |
|---|---|
| `Push` | O(1) amortizado |
| `Pop` | O(1) |
| `Top` | O(1) |
| `EsVacia` / `Tamanio` | O(1) |

**Implementación contigua:** reutiliza exactamente la misma idea de `SecDin` (array + capacidad + cantidad + redimensionar), pero la interfaz pública es más angosta: la "cima" es siempre `datos[cantidad-1]`.

```cpp
// idea mínima
template<typename T>
class Stack{
    T*  datos;
    int capacidad;
    int cantidad;   // cantidad-1 es el índice de la cima
};
```

`Push` = "agregar al final" (igual que `SecDin::Agregar`, con la misma lógica de redimensionar). `Pop` = simplemente `--cantidad` (¡no hace falta correr nada, a diferencia de `SecDin::Eliminar` en el medio!). `Top` = `datos[cantidad-1]`.

> 💡 Notá que `Push`/`Pop` en el array son **más simples y más baratos** que el `Insertar`/`Eliminar` en el medio de la Secuencia genérica: como siempre tocan el mismo extremo (el final), nunca hay que correr elementos.

### 🧭 Cómo encararlo

1. Especificá primero (`Stack.md`): la interfaz de arriba, dejando explícito que `Pop`/`Top` requieren `!EsVacia()` (precondición — qué pasa si no se cumple es decisión de diseño: `assert` y aborta, o excepción; en este TP usamos `assert`).
2. Empezá por `Crear`/`EsVacia`/`Tamanio` (triviales).
3. `Push`: igual lógica que `SecDin::Agregar` — si `cantidad == capacidad`, redimensionar antes.
4. `Pop`: solo `assert(!EsVacia()); --cantidad;` — no hace falta destruir ni mover nada.
5. `Top`: `assert(!EsVacia()); return datos[cantidad-1];`
6. Probá casos borde: `Pop`/`Top` sobre pila vacía (debe fallar el `assert`, no explotar silenciosamente), apilar más elementos de los que entran en la capacidad inicial (fuerza redimensionar), apilar y desapilar todo hasta volver a vaciar.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // ===================== archivo: Stack.h =====================
> #ifndef STACK_H
> #define STACK_H
>
> template<typename T>
> class Stack{
> public:
>     Stack();
>     ~Stack();
>
>     bool EsVacia() const;
>     int  Tamanio() const;
>     void Push(const T& valor);
>     void Pop();
>     T    Top() const;
>
> private:
>     T*  datos;
>     int capacidad;
>     int cantidad;
>
>     void Redimensionar(int nuevaCapacidad);
> };
>
> #include "StackCont.cpp"
> #endif
>
> // ===================== archivo: StackCont.cpp =====================
> #include "Stack.h"
> #include <cassert>
>
> template<typename T>
> Stack<T>::Stack() : datos(nullptr), capacidad(0), cantidad(0) {}
>
> template<typename T>
> Stack<T>::~Stack(){
>     delete[] datos;
> }
>
> template<typename T>
> bool Stack<T>::EsVacia() const{ return cantidad == 0; }
>
> template<typename T>
> int Stack<T>::Tamanio() const{ return cantidad; }
>
> template<typename T>
> void Stack<T>::Redimensionar(int nuevaCapacidad){
>     T* nuevo = new T[nuevaCapacidad];
>     for(int i = 0; i < cantidad; ++i)
>         nuevo[i] = datos[i];
>     delete[] datos;
>     datos = nuevo;
>     capacidad = nuevaCapacidad;
> }
>
> template<typename T>
> void Stack<T>::Push(const T& valor){
>     if(cantidad == capacidad)
>         Redimensionar(capacidad == 0 ? 1 : capacidad * 2);
>     datos[cantidad] = valor;
>     ++cantidad;
> }
>
> template<typename T>
> void Stack<T>::Pop(){
>     assert(!EsVacia());
>     --cantidad;     // no hace falta "borrar" el valor viejo, solo dejar de contarlo
> }
>
> template<typename T>
> T Stack<T>::Top() const{
>     assert(!EsVacia());
>     return datos[cantidad - 1];
> }
>
> // ===================== archivo: StackTest.cpp =====================
> #include "Stack.h"
> #include <cassert>
>
> int main(){
>     Stack<int> p;
>     assert(p.EsVacia());
>     p.Push(1); p.Push(2); p.Push(3);
>     assert(p.Tamanio() == 3);
>     assert(p.Top() == 3);
>     p.Pop();
>     assert(p.Top() == 2);
>     p.Pop(); p.Pop();
>     assert(p.EsVacia());
>     return 0;
> }
>
> // ===================== archivo: StackApp.cpp =====================
> // Aplicación clásica de Stack: verificar que los paréntesis de una expresión
> // estén balanceados.
> #include "Stack.h"
> #include <iostream>
> #include <string>
>
> int main(){
>     std::string linea;
>     std::getline(std::cin, linea);
>
>     Stack<char> pila;
>     bool balanceado = true;
>     for(char c : linea){
>         if(c == '(')
>             pila.Push(c);
>         else if(c == ')'){
>             if(pila.EsVacia()){ balanceado = false; break; }
>             pila.Pop();
>         }
>     }
>     balanceado = balanceado && pila.EsVacia();
>     std::cout << (balanceado ? "Balanceado" : "NO balanceado") << '\n';
>     return 0;
> }
> ```
> **Explicación de lo no obvio:** `Pop` no necesita "limpiar" `datos[cantidad-1]` — con solo decrementar `cantidad` ese valor queda lógicamente fuera de la pila, aunque físicamente siga en el array (se va a sobreescribir en el próximo `Push`). `StackApp.cpp` es el ejemplo de libro de texto de "para qué sirve un Stack": cada `(` se apila, cada `)` desapila uno; si en algún momento hay que desapilar y la pila está vacía, o si al final queda algo sin desapilar, no está balanceado.

### 💡 Trampas típicas / posibles preguntas

- Hacer `Pop()` sobre una pila vacía sin chequear `EsVacia()` antes → UB (accede a `datos[-1]`).
- Pensar que `Pop` tiene que "hacer algo" con el valor desapilado (como liberarlo) — en la versión contigua **no hace falta**, a diferencia de la versión enlazada del capítulo 36, donde sí hay que hacer `delete` del nodo.
- Confundir `Top` (consulta, no modifica) con `Pop` (modifica, no devuelve nada en este diseño) — algunas bibliografías combinan ambas en una sola operación; acá están separadas a propósito.
- Redimensionar mal la capacidad inicial (arrancar en 0 y duplicar desde 0 se queda trabado si no se contempla el caso `capacidad == 0 ? 1 : capacidad*2`).
- Relacionar mal este Stack con el *call stack* real: son el mismo concepto LIFO, pero el call stack lo maneja el hardware/SO automáticamente (ver [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Call Stack y Heap.md|Call Stack y Heap]]), mientras que este `Stack<T>` es una estructura de datos de biblioteca que programás vos.

---

## 34. Queue — Implementación Contigua

**Objetivos:** implementar el TDA **Queue** (Cola), la estructura **FIFO** (*First In, First Out* — el primero que entra es el primero que sale) usando un `array` **circular** como almacenamiento interno.

**Temas** *(inferido)*:
- TDA Queue: interfaz FIFO.
- Array circular con aritmética módulo N (evita tener que correr elementos al desencolar).

**Problema:** implementar una cola: se agrega por un extremo (**fondo/back**) y se quita por el otro (**frente/front**). Si se implementara igual que el Stack (con un solo índice de "tope" que crece hacia la derecha), desencolar por el frente obligaría a **correr todos los elementos restantes un lugar hacia la izquierda** en cada `Pop` — O(n) por operación. La consigna evita ese problema exigiendo un **array circular**: los índices de frente y fondo "dan la vuelta" con aritmética módulo `capacidad`, así que tanto encolar como desencolar quedan en O(1).

**Restricciones / Operaciones:**
- *(del PDF, textual)*:
  - La implementación basarse en `array`, por lo tanto tienen una capacidad máxima.
  - **El `array` debe utilizarse como un array circular con aritmética módulo N.**
- *(interfaz propuesta, con criterio propio)*:

```
Queue<T> = <colección FIFO de elementos de tipo T>

Crear()          -> Queue vacía
Destruir(c)       -> libera toda la memoria
EsVacia(c)        -> bool
Tamanio(c)        -> int
Push(c, valor)    -> encola "valor" al fondo
Pop(c)            -> desencola el elemento del frente (requiere !EsVacia(c))
Front(c)          -> devuelve (sin quitar) el elemento del frente (requiere !EsVacia(c))
```

**Tareas** *(textual)*: Especificar tipo. Diseñar pruebas. Implementar parte pública. Implementar parte privada. Probar. Diseñar un programa de aplicación.

**Productos:**
```
DD-QueueCont
 |-- readme.md
 |-- Queue.md // Especificación.
 |-- QueueTest.cpp
 |-- Queue.h
 |-- QueueCont.cpp
 `-- QueueApp.cpp
```

### 📘 Teoría necesaria

**TDA Queue (abstracto):** FIFO. Se toca por **dos** extremos distintos: se agrega por uno (`Push`, fondo) y se quita/consulta por el otro (`Pop`/`Front`, frente) — a diferencia del Stack, que solo toca un extremo.

**Array circular — la idea:** en vez de mover físicamente los elementos cuando desencolás, movés el **índice lógico del frente**. Cuando un índice llega al final del array, "da la vuelta" al principio con el operador módulo (`%`).

```cpp
template<typename T>
class Queue{
    T*  datos;
    int capacidad;
    int frente;     // índice del primer elemento lógico
    int cantidad;   // cuántos elementos hay (el "fondo" se calcula: (frente+cantidad) % capacidad)
};
```

```cpp
// ejemplo mínimo de la aritmética circular (no la solución completa)
int indice_fondo = (frente + cantidad) % capacidad;
datos[indice_fondo] = valor;   // Push: escribe en el fondo
++cantidad;

// Pop:
frente = (frente + 1) % capacidad;   // el frente "avanza", dando la vuelta si hace falta
--cantidad;
```

Ejemplo visual con `capacidad = 4`: si `frente = 2` y `cantidad = 3`, los elementos lógicos ocupan los índices físicos `2, 3, 0` (dio la vuelta) — el fondo real es el índice `0`, calculado como `(2+3) % 4 = 1`... hay que tener cuidado con el índice exacto del "próximo lugar libre" vs. "último ocupado" (por eso conviene razonar en términos de `frente` y `cantidad`, no de "frente y fondo" como dos índices independientes — es más fácil de mantener consistente).

**¿Por qué no alcanza con un índice de frente y otro de fondo sin más?** Porque cuando el array está completamente lleno (`cantidad == capacidad`), el índice del "próximo lugar libre" coincide numéricamente con el del "frente" — ambigüedad clásica entre "lleno" y "vacío" si solo usás dos índices. Por eso el diseño de arriba guarda `cantidad` como variable aparte: elimina la ambigüedad.

**Complejidad:** `Push`, `Pop`, `Front` son O(1) (más el costo amortizado de redimensionar en `Push`).

### 🧭 Cómo encararlo

1. Especificá primero la interfaz (`Queue.md`).
2. Pensá el array circular ANTES de escribir código: dibujá un array de capacidad 4, encolá 3 elementos, desencolá 2, encolá 3 más — vas a ver que el frente "da la vuelta". Solo después de entender el dibujo, escribí la aritmética.
3. Implementá `Crear`/`EsVacia`/`Tamanio` primero.
4. `Push`: calculá el índice físico del fondo con `(frente + cantidad) % capacidad`, escribí ahí, `++cantidad` (redimensionando antes si `cantidad == capacidad`).
5. `Pop`: `frente = (frente + 1) % capacidad; --cantidad;`
6. `Front`: `return datos[frente];`
7. **Redimensionar es el paso más delicado acá**: al copiar al array nuevo, tenés que copiar **en orden lógico** empezando desde `frente` (no simplemente `datos[i]` para `i` de `0` a `cantidad`, porque los elementos lógicos pueden estar "partidos" en dos tramos del array viejo). Y el nuevo `frente` pasa a ser `0`.
8. Probá: encolar/desencolar hasta que el frente dé la vuelta al menos una vez; encolar hasta forzar redimensionar **justo cuando el frente no está en 0** (el caso más propenso a bugs).

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // ===================== archivo: Queue.h =====================
> #ifndef QUEUE_H
> #define QUEUE_H
>
> template<typename T>
> class Queue{
> public:
>     Queue();
>     ~Queue();
>
>     bool EsVacia() const;
>     int  Tamanio() const;
>     void Push(const T& valor);
>     void Pop();
>     T    Front() const;
>
> private:
>     T*  datos;
>     int capacidad;
>     int frente;
>     int cantidad;
>
>     void Redimensionar(int nuevaCapacidad);
> };
>
> #include "QueueCont.cpp"
> #endif
>
> // ===================== archivo: QueueCont.cpp =====================
> #include "Queue.h"
> #include <cassert>
>
> template<typename T>
> Queue<T>::Queue() : datos(nullptr), capacidad(0), frente(0), cantidad(0) {}
>
> template<typename T>
> Queue<T>::~Queue(){
>     delete[] datos;
> }
>
> template<typename T>
> bool Queue<T>::EsVacia() const{ return cantidad == 0; }
>
> template<typename T>
> int Queue<T>::Tamanio() const{ return cantidad; }
>
> template<typename T>
> void Queue<T>::Redimensionar(int nuevaCapacidad){
>     T* nuevo = new T[nuevaCapacidad];
>     // Copio en ORDEN LÓGICO, arrancando desde "frente" (que puede no ser 0).
>     for(int i = 0; i < cantidad; ++i)
>         nuevo[i] = datos[(frente + i) % capacidad];
>     delete[] datos;
>     datos = nuevo;
>     capacidad = nuevaCapacidad;
>     frente = 0;   // en el array nuevo, el primer elemento lógico arranca en 0
> }
>
> template<typename T>
> void Queue<T>::Push(const T& valor){
>     if(cantidad == capacidad)
>         Redimensionar(capacidad == 0 ? 1 : capacidad * 2);
>     int indiceFondo = (frente + cantidad) % capacidad;
>     datos[indiceFondo] = valor;
>     ++cantidad;
> }
>
> template<typename T>
> void Queue<T>::Pop(){
>     assert(!EsVacia());
>     frente = (frente + 1) % capacidad;
>     --cantidad;
> }
>
> template<typename T>
> T Queue<T>::Front() const{
>     assert(!EsVacia());
>     return datos[frente];
> }
>
> // ===================== archivo: QueueTest.cpp =====================
> #include "Queue.h"
> #include <cassert>
>
> int main(){
>     Queue<int> c;
>     assert(c.EsVacia());
>     c.Push(1); c.Push(2); c.Push(3);
>     assert(c.Front() == 1);     // el primero que entró
>     c.Pop();
>     assert(c.Front() == 2);
>
>     // Fuerzo que el frente dé la vuelta y encima redimensione
>     c.Push(4); c.Push(5); c.Push(6); c.Push(7);
>     assert(c.Tamanio() == 5);
>     while(!c.EsVacia()) c.Pop();
>     assert(c.EsVacia());
>     return 0;
> }
>
> // ===================== archivo: QueueApp.cpp =====================
> // Aplicación clásica: simular una fila de atención (ej. caja de banco).
> #include "Queue.h"
> #include <iostream>
> #include <string>
>
> int main(){
>     Queue<std::string> fila;
>     std::string comando, nombre;
>     while(std::cin >> comando){
>         if(comando == "llega"){
>             std::cin >> nombre;
>             fila.Push(nombre);
>         } else if(comando == "atiende"){
>             if(fila.EsVacia())
>                 std::cout << "No hay nadie esperando\n";
>             else{
>                 std::cout << "Atendiendo a " << fila.Front() << '\n';
>                 fila.Pop();
>             }
>         }
>     }
>     return 0;
> }
> ```
> **Explicación de lo no obvio:** en `Redimensionar`, el `for` copia `datos[(frente + i) % capacidad]` y no `datos[i]` — si copiaras posición física por posición física, un elemento "partido" por el borde del array circular quedaría en el orden equivocado en el array nuevo. Después de redimensionar, el `frente` del array nuevo siempre es `0` porque ahí los elementos ya quedaron en orden lógico desde el principio.

### 💡 Trampas típicas / posibles preguntas

- Usar dos índices (`frente` y `fondo`) **sin** una variable `cantidad` aparte → ambigüedad entre "cola llena" y "cola vacía" cuando `frente == fondo`.
- Redimensionar copiando `datos[i]` en vez de `datos[(frente+i) % capacidad]` → corrompe el orden si el frente no estaba en 0 al momento de redimensionar.
- Olvidarse el `% capacidad` en `Pop` (`frente = frente + 1;` sin módulo) → en algún momento `frente` se va fuera de los límites del array.
- Confundir `Push`/`Pop` de esta `Queue` con los de `Stack` — en `Queue` tocan **extremos distintos** (fondo y frente), en `Stack` tocan **el mismo extremo** (la cima). Es un error común de nomenclatura llamar "Top" al frente de una cola.
- No liberar `datos` en el destructor → memory leak (igual que en Secuencia y Stack contiguos).

---

## 35. Secuencia Dinámica — Implementación Enlazada

**Objetivos:** implementar el **mismo TDA Secuencia** del capítulo 32 (idéntica interfaz, idéntica especificación `SecDin.md`), pero ahora con una **lista enlazada** de nodos como almacenamiento interno, en vez de un array. Es el ejercicio central del contraste contigua/enlazada: la interfaz pública no cambia una coma, solo cambia lo que hay "adentro de la caja negra".

**Temas:** *(inferido)* nodo enlazado + puntero al primero, reserva con `new` por nodo (sin capacidad fija), recorrido secuencial para llegar a una posición.

**Problema:** el mismo de 32 (colección ordenada, tamaño variable, indexable por posición), resuelto sin array: la secuencia es una cadena de nodos, cada uno con su dato y un puntero al siguiente.

**Restricciones / Operaciones:**
- *(del PDF, textual)*:
  - La implementación debe basarse en una `struct` con un puntero al primer nodo.
  - La reserva de memoria para los nodos debe realizarse dinámicamente con el operador `new`.
- La interfaz pública es **exactamente la misma** que en el capítulo 32 (`Crear`, `Destruir`, `EsVacia`, `Tamanio`, `Agregar`, `Insertar`, `Eliminar`, `Obtener`, `Modificar`) — no se repite acá, ver 32.

**Tareas** *(textual)*: Especificar tipo. Diseñar pruebas. Implementar parte pública. Implementar parte privada. Probar. Diseñar un programa de aplicación.

**Productos:**
```
DD-SecDinLink
 |-- readme.md
 |-- SecDin.md // Especificación.
 |-- SecDinTest.cpp
 |-- SecDin.h
 |-- SecDinLink.cpp
 `-- SecDinApp.cpp
```

### 📘 Teoría necesaria

**Nodo y puntero al siguiente:** esto ya está cubierto en detalle en [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Punteros y Listas Enlazadas.md|Punteros y Listas Enlazadas]] (qué es un nodo, `struct Node{T value; Node* next;}`, `new`/`delete`, recorrido con `for(Node* p=first; p; p=p->next)`). Repaso de 3 líneas: un nodo es un par `(dato, puntero al siguiente)`; la secuencia entera se identifica con **un solo puntero** al primer nodo (`primero`); recorrer la lista es seguir el `siguiente` hasta `nullptr`. Si tenés dudas de esa mecánica básica, andá primero a esa nota.

**Lo nuevo acá** (no cubierto en esa nota): cómo usar esa mecánica para implementar la **misma interfaz indexada por posición** que en la versión contigua.

```cpp
template<typename T>
class SecDin{
    struct Nodo{
        T     dato;
        Nodo* siguiente;
    };
    Nodo* primero;
    int   cantidad;
};
```

No hay `capacidad` ni redimensionar: cada `Agregar`/`Insertar` reserva **un nodo nuevo** con `new`, cada `Eliminar` libera **un nodo** con `delete`.

**El costo que aparece acá y no en la versión contigua:** `Obtener(pos)`/`Modificar(pos)` en la versión contigua eran O(1) (`datos[pos]`). Acá, para llegar a la posición `pos` hay que **caminar** nodo por nodo desde `primero`:

```cpp
// ejemplo mínimo: llegar al nodo en la posición pos
Nodo* p = primero;
for(int i = 0; i < pos; ++i)
    p = p->siguiente;
// p apunta ahora al nodo de la posición pos
```

Eso hace que `Obtener`/`Modificar`/`Insertar`/`Eliminar` en una posición arbitraria sean **O(n)** en la versión enlazada (contra O(1) de acceso, pero O(n) de correr elementos, en la contigua — ver la tabla comparativa al principio de este archivo). Donde la enlazada gana es en `Insertar`/`Eliminar` en la posición 0: es O(1) real (solo reacomodar `primero`), sin correr nada.

### 🧭 Cómo encararlo

1. Primero el **nodo**: definí `struct Nodo{T dato; Nodo* siguiente;};` como tipo anidado privado de `SecDin`.
2. Después las operaciones que solo tocan la **cabeza** de la lista: `Insertar(0, valor)` (crear un nodo nuevo que apunte a lo que era `primero`, y `primero` pasa a ser el nuevo nodo) y `Eliminar(0)` (guardar el puntero al viejo `primero`, mover `primero` a `primero->siguiente`, `delete` el viejo).
3. Recién después, la versión general de `Insertar(pos, valor)`/`Eliminar(pos)`: caminar hasta el nodo **anterior** a la posición `pos` (no hasta `pos` mismo — necesitás el anterior para poder reengancharlo), y ahí aplicar la misma lógica que en el paso 2.
4. Considerá los casos borde por separado: lista vacía (`primero == nullptr`), un solo elemento, insertar/eliminar en la última posición.
5. El destructor tiene que recorrer y hacer `delete` de **todos** los nodos, uno por uno — no alcanza con "olvidarse" del puntero `primero` como en un tipo con destructor automático.
6. `Agregar` (al final) reutiliza `Insertar(cantidad, valor)`.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // ===================== archivo: SecDin.h =====================
> #ifndef SECDIN_H
> #define SECDIN_H
>
> template<typename T>
> class SecDin{
> public:
>     SecDin();
>     ~SecDin();
>
>     bool EsVacia() const;
>     int  Tamanio() const;
>
>     void Agregar(const T& valor);
>     void Insertar(int pos, const T& valor);
>     void Eliminar(int pos);
>     T    Obtener(int pos) const;
>     void Modificar(int pos, const T& valor);
>
> private:
>     struct Nodo{
>         T     dato;
>         Nodo* siguiente;
>     };
>     Nodo* primero;
>     int   cantidad;
>
>     Nodo* ObtenerNodo(int pos) const;   // helper: camina hasta el nodo en "pos"
> };
>
> #include "SecDinLink.cpp"
> #endif
>
> // ===================== archivo: SecDinLink.cpp =====================
> #include "SecDin.h"
> #include <cassert>
>
> template<typename T>
> SecDin<T>::SecDin() : primero(nullptr), cantidad(0) {}
>
> template<typename T>
> SecDin<T>::~SecDin(){
>     Nodo* actual = primero;
>     while(actual != nullptr){
>         Nodo* siguiente = actual->siguiente;   // guardo el siguiente ANTES de borrar
>         delete actual;
>         actual = siguiente;
>     }
> }
>
> template<typename T>
> bool SecDin<T>::EsVacia() const{ return cantidad == 0; }
>
> template<typename T>
> int SecDin<T>::Tamanio() const{ return cantidad; }
>
> template<typename T>
> typename SecDin<T>::Nodo* SecDin<T>::ObtenerNodo(int pos) const{
>     assert(pos >= 0 && pos < cantidad);
>     Nodo* p = primero;
>     for(int i = 0; i < pos; ++i)
>         p = p->siguiente;
>     return p;
> }
>
> template<typename T>
> void SecDin<T>::Insertar(int pos, const T& valor){
>     assert(pos >= 0 && pos <= cantidad);
>     Nodo* nuevo = new Nodo{valor, nullptr};
>
>     if(pos == 0){
>         nuevo->siguiente = primero;
>         primero = nuevo;
>     } else {
>         Nodo* anterior = ObtenerNodo(pos - 1);
>         nuevo->siguiente = anterior->siguiente;
>         anterior->siguiente = nuevo;
>     }
>     ++cantidad;
> }
>
> template<typename T>
> void SecDin<T>::Agregar(const T& valor){
>     Insertar(cantidad, valor);
> }
>
> template<typename T>
> void SecDin<T>::Eliminar(int pos){
>     assert(pos >= 0 && pos < cantidad);
>     Nodo* aBorrar;
>
>     if(pos == 0){
>         aBorrar = primero;
>         primero = primero->siguiente;
>     } else {
>         Nodo* anterior = ObtenerNodo(pos - 1);
>         aBorrar = anterior->siguiente;
>         anterior->siguiente = aBorrar->siguiente;
>     }
>     delete aBorrar;
>     --cantidad;
> }
>
> template<typename T>
> T SecDin<T>::Obtener(int pos) const{
>     return ObtenerNodo(pos)->dato;
> }
>
> template<typename T>
> void SecDin<T>::Modificar(int pos, const T& valor){
>     ObtenerNodo(pos)->dato = valor;
> }
>
> // ===================== archivo: SecDinTest.cpp =====================
> // MISMO test que en 32 — la interfaz es idéntica, y por eso el mismo
> // archivo de test sirve para las dos implementaciones sin cambiar una línea.
> #include "SecDin.h"
> #include <cassert>
>
> int main(){
>     SecDin<int> s;
>     assert(s.EsVacia());
>     s.Agregar(10); s.Agregar(20); s.Agregar(30);
>     assert(s.Tamanio() == 3 && s.Obtener(1) == 20);
>     s.Insertar(1, 99);
>     assert(s.Obtener(1) == 99 && s.Obtener(2) == 20);
>     s.Eliminar(0);
>     assert(s.Obtener(0) == 99 && s.Tamanio() == 3);
>     s.Modificar(2, 111);
>     assert(s.Obtener(2) == 111);
>     return 0;
> }
>
> // ===================== archivo: SecDinApp.cpp =====================
> // MISMA app que en 32 — de nuevo, no necesita cambios: no le importa
> // cómo está implementada SecDin por dentro.
> #include "SecDin.h"
> #include <iostream>
>
> int main(){
>     SecDin<int> notas;
>     for(int n; std::cin >> n; )
>         notas.Agregar(n);
>     std::cout << "Cargaste " << notas.Tamanio() << " notas:\n";
>     for(int i = 0; i < notas.Tamanio(); ++i)
>         std::cout << i << ": " << notas.Obtener(i) << '\n';
>     return 0;
> }
> ```
> **Explicación de lo no obvio:** `Insertar`/`Eliminar` distinguen el caso `pos == 0` del general porque insertar/eliminar el primer nodo mueve el puntero `primero` (una variable miembro), mientras que en cualquier otra posición se reengancha el `siguiente` de otro nodo (`anterior`) — son dos operaciones distintas aunque el efecto lógico ("agregar/quitar en esa posición") sea el mismo. `ObtenerNodo(pos-1)` en vez de `ObtenerNodo(pos)` es la clave: para insertar/eliminar necesitás el nodo **anterior**, porque es el que hay que reenganchar.

### 💡 Trampas típicas / posibles preguntas

- `Eliminar` sin `delete aBorrar` → **memory leak**: el nodo queda reservado en el heap sin que nadie lo referencie nunca más.
- En el destructor, hacer `delete actual` y **recién después** leer `actual->siguiente` → puntero colgante (estás leyendo memoria ya liberada). Por eso hay que guardar `siguiente` **antes** de borrar.
- Actualizar `primero` en el caso `pos==0` de `Eliminar` **después** de hacer `delete` (en vez de antes) → usás un puntero que ya no es válido.
- Llamar `ObtenerNodo(pos)` en vez de `ObtenerNodo(pos-1)` dentro de `Insertar`/`Eliminar` → terminás con el nodo equivocado y el enlace se corrompe.
- Olvidarse de actualizar `cantidad` (`++`/`--`) → `Tamanio()` queda desincronizado con la lista real.
- Confundir esta implementación con la de 32 al ojo: la interfaz pública (`SecDin.h`, salvo el detalle interno de `Nodo`) es la misma — si te preguntan "¿qué cambia?", la respuesta es "solo lo privado".

---

## 36. Stack — Implementación Enlazada

**Objetivos:** implementar el **mismo TDA Stack** del capítulo 33 con una lista enlazada de nodos en vez de un array — sin capacidad máxima, sin redimensionar.

**Temas:** *(inferido)* nodo enlazado con puntero a la cima; push/pop como operaciones que solo tocan la **cabeza** de la lista (el caso más simple de todos los de esta guía, porque el Stack solo toca un extremo y ese extremo coincide justo con la cabeza, que es la posición más barata de tocar en una lista enlazada).

**Problema:** el mismo de 33 (LIFO: `Push`, `Pop`, `Top`), resuelto con nodos.

**Restricciones / Operaciones:**
- *(del PDF, textual)*:
  - La implementación basarse en un `struct` con un puntero al nodo de la cima.
  - La reserva de memoria para los nodos debe realizarse dinámicamente con el operador `new`.
- Interfaz pública: idéntica a la del capítulo 33 (`Crear`, `Destruir`, `EsVacia`, `Tamanio`, `Push`, `Pop`, `Top`).

**Tareas** *(textual)*: Especificar tipo. Diseñar pruebas. Implementar parte pública. Implementar parte privada. Probar. Diseñar un programa de aplicación.

**Productos** *(el PDF repite `DD-StackCont` como nombre de carpeta en esta página — es una errata del libro; el nombre correcto, coherente con el resto de los capítulos enlazados, es `DD-StackLink`)*:
```
DD-StackLink
 |-- readme.md
 |-- Stack.md // Especificación.
 |-- StackTest.cpp
 |-- Stack.h
 |-- StackLink.cpp
 `-- StackApp.cpp
```

### 📘 Teoría necesaria

Nodo y `new`/`delete`: ver [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Punteros y Listas Enlazadas.md|Punteros y Listas Enlazadas]]. Lo nuevo acá es que el Stack enlazado es el caso **más directo** de todo este capítulo: la cima del stack **es** el primer nodo de la lista, así que `Push`/`Pop` son simplemente "agregar/quitar de la cabeza" — sin recorrer nada.

```cpp
template<typename T>
class Stack{
    struct Nodo{
        T     dato;
        Nodo* siguiente;
    };
    Nodo* cima;    // el nodo de la cima ES el primer nodo de la lista
    int   cantidad;
};
```

`Push(valor)`: crear un nodo nuevo cuyo `siguiente` sea la cima actual, y la cima pasa a ser el nodo nuevo — **exactamente** `SecDin::Insertar(0, valor)` del capítulo 35, sin el caso general. `Pop()`: guardar el puntero a la cima actual, mover `cima` a `cima->siguiente`, `delete` el nodo viejo — exactamente `SecDin::Eliminar(0)`.

**Complejidad:** `Push`, `Pop`, `Top` son **O(1) real** (no amortizado, no hay redimensionar posible porque no hay capacidad fija) — en este sentido, la versión enlazada del Stack es incluso más simple conceptualmente que la contigua, aunque tenga el costo extra de memoria por nodo.

### 🧭 Cómo encararlo

1. `struct Nodo{T dato; Nodo* siguiente;};` primero, como en 35.
2. `Push`: nodo nuevo → `nuevo->siguiente = cima;` → `cima = nuevo;` → `++cantidad;`. Un solo caso, sin distinguir "lista vacía" de "lista con elementos" (¡funciona igual para ambos! si `cima` era `nullptr`, el nodo nuevo simplemente apunta a `nullptr`, que es correcto).
3. `Pop`: `assert(!EsVacia());` guardar `Nodo* viejo = cima;` → `cima = cima->siguiente;` → `delete viejo;` → `--cantidad;`.
4. `Top`: `assert(!EsVacia()); return cima->dato;`
5. Destructor: recorrer desde `cima` haciendo `delete` de cada nodo, igual que en 35.
6. Probá: pila vacía, un solo `Push`+`Pop` (queda vacía de nuevo, `cima` vuelve a `nullptr`), varios `Push` seguidos de varios `Pop` intercalados.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // ===================== archivo: Stack.h =====================
> #ifndef STACK_H
> #define STACK_H
>
> template<typename T>
> class Stack{
> public:
>     Stack();
>     ~Stack();
>
>     bool EsVacia() const;
>     int  Tamanio() const;
>     void Push(const T& valor);
>     void Pop();
>     T    Top() const;
>
> private:
>     struct Nodo{
>         T     dato;
>         Nodo* siguiente;
>     };
>     Nodo* cima;
>     int   cantidad;
> };
>
> #include "StackLink.cpp"
> #endif
>
> // ===================== archivo: StackLink.cpp =====================
> #include "Stack.h"
> #include <cassert>
>
> template<typename T>
> Stack<T>::Stack() : cima(nullptr), cantidad(0) {}
>
> template<typename T>
> Stack<T>::~Stack(){
>     while(cima != nullptr){
>         Nodo* siguiente = cima->siguiente;
>         delete cima;
>         cima = siguiente;
>     }
> }
>
> template<typename T>
> bool Stack<T>::EsVacia() const{ return cantidad == 0; }
>
> template<typename T>
> int Stack<T>::Tamanio() const{ return cantidad; }
>
> template<typename T>
> void Stack<T>::Push(const T& valor){
>     Nodo* nuevo = new Nodo{valor, cima};   // el nuevo nodo apunta a la vieja cima
>     cima = nuevo;
>     ++cantidad;
> }
>
> template<typename T>
> void Stack<T>::Pop(){
>     assert(!EsVacia());
>     Nodo* viejo = cima;
>     cima = cima->siguiente;
>     delete viejo;
>     --cantidad;
> }
>
> template<typename T>
> T Stack<T>::Top() const{
>     assert(!EsVacia());
>     return cima->dato;
> }
>
> // ===================== archivo: StackTest.cpp =====================
> // MISMO test que en 33 — interfaz idéntica.
> #include "Stack.h"
> #include <cassert>
>
> int main(){
>     Stack<int> p;
>     assert(p.EsVacia());
>     p.Push(1); p.Push(2); p.Push(3);
>     assert(p.Tamanio() == 3 && p.Top() == 3);
>     p.Pop();
>     assert(p.Top() == 2);
>     p.Pop(); p.Pop();
>     assert(p.EsVacia());
>     return 0;
> }
>
> // ===================== archivo: StackApp.cpp =====================
> // MISMA app que en 33 (verificador de paréntesis) — no cambia nada.
> #include "Stack.h"
> #include <iostream>
> #include <string>
>
> int main(){
>     std::string linea;
>     std::getline(std::cin, linea);
>     Stack<char> pila;
>     bool balanceado = true;
>     for(char c : linea){
>         if(c == '(')
>             pila.Push(c);
>         else if(c == ')'){
>             if(pila.EsVacia()){ balanceado = false; break; }
>             pila.Pop();
>         }
>     }
>     balanceado = balanceado && pila.EsVacia();
>     std::cout << (balanceado ? "Balanceado" : "NO balanceado") << '\n';
>     return 0;
> }
> ```
> **Explicación de lo no obvio:** `new Nodo{valor, cima}` usa la sintaxis de inicialización agregada de `struct` (llena `dato` y `siguiente` en el orden en que se declararon los campos) para armar el nodo **y** engancharlo a la vieja cima en una sola línea, antes de reasignar `cima`. Es importante leer `cima` (para guardarla dentro del nodo nuevo) **antes** de sobreescribirla.

### 💡 Trampas típicas / posibles preguntas

- `Pop` sin chequear `EsVacia()` → intenta desreferenciar `cima` cuando es `nullptr`.
- Hacer `delete cima;` y **después** `cima = cima->siguiente;` (orden invertido) → lee memoria ya liberada (dangling pointer), comportamiento indefinido.
- Olvidarse el `delete` en `Pop` → memory leak, uno de los puntos que más se pregunta en el oral comparando esta versión con la contigua del capítulo 33 (que no necesita `delete` en `Pop`).
- Pensar que hace falta distinguir "pila vacía" como caso especial en `Push` — **no** hace falta: si `cima` es `nullptr`, el nodo nuevo simplemente queda con `siguiente = nullptr`, que es exactamente lo que se espera de un stack de un solo elemento.
- Confundir el destructor de este Stack con el de la Secuencia enlazada (35): la lógica de recorrer y liberar nodo por nodo es la misma idea general, pero acá arranca desde `cima` en vez de `primero`.

---

## 37. Queue — Implementación Enlazada

**Objetivos:** implementar el **mismo TDA Queue** del capítulo 34 con una lista enlazada, esta vez con **dos** punteros (uno al primero, otro al último nodo), en vez de un array circular.

**Temas:** *(inferido)* lista enlazada con puntero al primero Y al último (para que `Push` al fondo sea O(1) sin tener que recorrer toda la lista cada vez).

**Problema:** el mismo de 34 (FIFO: `Push` al fondo, `Pop`/`Front` del frente), resuelto con nodos.

**Restricciones / Operaciones:**
- *(del PDF, textual)*:
  - La implementación basarse en un `struct` con un puntero al primer nodo **y otro al último**.
  - La reserva de memoria para los nodos debe realizarse dinámicamente con el operador `new`.
- Interfaz pública: idéntica a la del capítulo 34 (`Crear`, `Destruir`, `EsVacia`, `Tamanio`, `Push`, `Pop`, `Front`).

**Tareas** *(textual)*: Especificar tipo. Diseñar pruebas. Implementar parte pública. Implementar parte privada. Probar. Diseñar un programa de aplicación.

**Productos:**
```
DD-QueueLink
 |-- readme.md
 |-- Queue.md // Especificación.
 |-- QueueTest.cpp
 |-- Queue.h
 |-- QueueLink.cpp
 `-- QueueApp.cpp
```

### 📘 Teoría necesaria

**Por qué hace falta un segundo puntero (`ultimo`):** en la Secuencia y el Stack enlazados alcanzaba con `primero`, porque las operaciones "caras" siempre tocaban la cabeza. Acá, `Push` tiene que agregar al **fondo** (el otro extremo). Si solo tuvieras `primero`, agregar al fondo obligaría a recorrer **toda** la lista para encontrar el último nodo — O(n) por `Push`, perdiendo toda la ventaja de la lista enlazada. Guardando también un puntero `ultimo`, `Push` queda en O(1): enganchás el nodo nuevo directamente después de `ultimo`, sin recorrer nada.

```cpp
template<typename T>
class Queue{
    struct Nodo{
        T     dato;
        Nodo* siguiente;
    };
    Nodo* primero;   // frente: de acá se desencola
    Nodo* ultimo;    // fondo: acá se engancha lo nuevo
    int   cantidad;
};
```

`Push(valor)`: crear nodo nuevo con `siguiente = nullptr`; si la cola estaba vacía, `primero` **y** `ultimo` pasan a ser el nodo nuevo; si no, `ultimo->siguiente = nuevo;` y después `ultimo = nuevo;`. `Pop()`: guardar `primero` viejo, mover `primero = primero->siguiente`, `delete` el viejo; **caso especial**: si la lista queda vacía (`primero == nullptr` después de mover), hay que poner también `ultimo = nullptr` — si no, `ultimo` queda apuntando a un nodo ya liberado (dangling).

**Complejidad:** `Push`, `Pop`, `Front` son O(1) real, gracias al segundo puntero.

### 🧭 Cómo encararlo

1. `struct Nodo` primero, igual que en 35/36.
2. Pensá **primero el caso "cola vacía → un elemento"** en `Push`, por separado del caso general: es el único momento en que `primero` y `ultimo` cambian **los dos** a la vez.
3. Después el caso general de `Push` (cola con ≥1 elemento): solo se toca `ultimo` y el `siguiente` del viejo `ultimo`; `primero` no se toca.
4. Para `Pop`, pensá primero el caso general (queda al menos un elemento) y **después** el caso borde "me quedo sin elementos" (hay que resetear `ultimo` también, si no queda dangling).
5. `Front`: trivial, `primero->dato`.
6. Destructor: igual patrón que 35/36, recorrer desde `primero` liberando.
7. Probá especialmente: encolar 1 solo elemento y desencolarlo (¿queda `primero == ultimo == nullptr`?); encolar varios, desencolar todos, volver a encolar (¿el `Queue` "revive" bien después de vaciarse?).

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // ===================== archivo: Queue.h =====================
> #ifndef QUEUE_H
> #define QUEUE_H
>
> template<typename T>
> class Queue{
> public:
>     Queue();
>     ~Queue();
>
>     bool EsVacia() const;
>     int  Tamanio() const;
>     void Push(const T& valor);
>     void Pop();
>     T    Front() const;
>
> private:
>     struct Nodo{
>         T     dato;
>         Nodo* siguiente;
>     };
>     Nodo* primero;
>     Nodo* ultimo;
>     int   cantidad;
> };
>
> #include "QueueLink.cpp"
> #endif
>
> // ===================== archivo: QueueLink.cpp =====================
> #include "Queue.h"
> #include <cassert>
>
> template<typename T>
> Queue<T>::Queue() : primero(nullptr), ultimo(nullptr), cantidad(0) {}
>
> template<typename T>
> Queue<T>::~Queue(){
>     while(primero != nullptr){
>         Nodo* siguiente = primero->siguiente;
>         delete primero;
>         primero = siguiente;
>     }
> }
>
> template<typename T>
> bool Queue<T>::EsVacia() const{ return cantidad == 0; }
>
> template<typename T>
> int Queue<T>::Tamanio() const{ return cantidad; }
>
> template<typename T>
> void Queue<T>::Push(const T& valor){
>     Nodo* nuevo = new Nodo{valor, nullptr};
>     if(EsVacia()){
>         primero = nuevo;
>         ultimo  = nuevo;
>     } else {
>         ultimo->siguiente = nuevo;
>         ultimo = nuevo;
>     }
>     ++cantidad;
> }
>
> template<typename T>
> void Queue<T>::Pop(){
>     assert(!EsVacia());
>     Nodo* viejo = primero;
>     primero = primero->siguiente;
>     if(primero == nullptr)      // me quedé sin elementos
>         ultimo = nullptr;       // si no, "ultimo" quedaría dangling
>     delete viejo;
>     --cantidad;
> }
>
> template<typename T>
> T Queue<T>::Front() const{
>     assert(!EsVacia());
>     return primero->dato;
> }
>
> // ===================== archivo: QueueTest.cpp =====================
> // MISMO test que en 34 — interfaz idéntica.
> #include "Queue.h"
> #include <cassert>
>
> int main(){
>     Queue<int> c;
>     assert(c.EsVacia());
>     c.Push(1); c.Push(2); c.Push(3);
>     assert(c.Front() == 1);
>     c.Pop();
>     assert(c.Front() == 2);
>
>     while(!c.EsVacia()) c.Pop();
>     assert(c.EsVacia());
>
>     c.Push(99);                 // la cola "revive" bien tras vaciarse
>     assert(c.Front() == 99 && c.Tamanio() == 1);
>     return 0;
> }
>
> // ===================== archivo: QueueApp.cpp =====================
> // MISMA app que en 34 (fila de atención) — no cambia nada.
> #include "Queue.h"
> #include <iostream>
> #include <string>
>
> int main(){
>     Queue<std::string> fila;
>     std::string comando, nombre;
>     while(std::cin >> comando){
>         if(comando == "llega"){
>             std::cin >> nombre;
>             fila.Push(nombre);
>         } else if(comando == "atiende"){
>             if(fila.EsVacia())
>                 std::cout << "No hay nadie esperando\n";
>             else{
>                 std::cout << "Atendiendo a " << fila.Front() << '\n';
>                 fila.Pop();
>             }
>         }
>     }
>     return 0;
> }
> ```
> **Explicación de lo no obvio:** el `if(primero == nullptr) ultimo = nullptr;` dentro de `Pop` es el detalle que más se pasa por alto. Sin esa línea, al desencolar el último elemento de la cola, `ultimo` seguiría apuntando al nodo que se acaba de liberar con `delete` — un dangling pointer que recién explota (quizás mucho después) en el próximo `Push`, cuando se intente escribir `ultimo->siguiente`.

### 💡 Trampas típicas / posibles preguntas

- Olvidarse de actualizar `ultimo` al vaciar la cola con `Pop` → dangling pointer, bug que puede tardar en manifestarse (aparece recién en el próximo `Push`, lo que lo hace difícil de debuggear).
- Olvidarse de poner `primero = ultimo = nuevo;` (los dos) cuando `Push` se hace sobre una cola vacía → si solo actualizás uno de los dos, la cola queda en un estado inconsistente.
- `Eliminar`/`Pop` sin `delete viejo` → memory leak, igual que en 35 y 36.
- Confundir cuál extremo es cuál: se **encola por `ultimo`/fondo**, se **desencola por `primero`/frente** — es fácil mezclarlos porque los nombres "primero" y "último" no dicen explícitamente "frente" o "fondo".
- Pensar que hace falta un puntero **doblemente enlazado** (con `anterior` además de `siguiente`) para resolver esto — no hace falta: como `Pop` siempre saca por `primero` y `Push` siempre entra por `ultimo`, alcanza con enlace simple hacia adelante.

---

## 38. Árbol de Búsqueda Binaria

> ⚠️ El PDF solo trae el título del capítulo (página 117) y páginas en blanco a continuación — no da Restricciones, Tareas ni Productos explícitos para este trabajo (a diferencia de los capítulos 32-37). Completo el resto con el mismo criterio de diseño usado en toda la guía, y con la teoría de BST que ya aparece resumida en [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Templates, Arreglos Asociativos y Arboles.md|Templates, Arreglos Asociativos y Arboles]] (sección "Introducción a Árboles — BST").

**Objetivos:** implementar el TDA **Árbol de Búsqueda Binaria** (BST), una estructura enlazada no lineal que permite **buscar, insertar y recorrer** un conjunto ordenado de elementos en tiempo logarítmico (en el caso promedio/balanceado), a diferencia del tiempo lineal de una Secuencia.

**Temas:** *(con criterio propio)*:
- Nodo con **dos** punteros (`izquierda`, `derecha`) en vez de uno solo (`siguiente`).
- Invariante BST: en cualquier nodo, todo el subárbol izquierdo es "menor" y todo el subárbol derecho es "mayor" que el dato de ese nodo.
- Recursión sobre estructuras enlazadas (insertar, buscar y recorrer un árbol se definen naturalmente de forma recursiva).
- Recorridos: in-order, pre-order, post-order.

**Problema:** organizar un conjunto de elementos comparables (`<`) de forma que buscar, insertar y recorrerlos en orden sea eficiente. Ya se vio la motivación en [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Templates, Arreglos Asociativos y Arboles.md|Templates, Arreglos Asociativos y Arboles]]: un array/lista sin ordenar necesita O(n) para buscar; un BST bien armado necesita O(log n).

**Restricciones / Operaciones** *(propuestas con criterio propio, no vienen del PDF)*:

```
ArbolBB<T> = <conjunto ordenado de elementos de tipo T, comparables con <>

Crear()              -> Árbol vacío
Destruir(a)           -> libera todos los nodos
EsVacio(a)             -> bool
Cantidad(a)            -> int (cantidad total de nodos)
Insertar(a, valor)     -> agrega "valor" respetando el invariante BST (sin duplicados)
Existe(a, valor)       -> bool, ¿"valor" está en el árbol?
Eliminar(a, valor)     -> quita "valor" del árbol si existe, reacomodando para mantener el invariante
InOrder(a)             -> Secuencia<T> con los elementos en orden ascendente
PreOrder(a)            -> Secuencia<T> con los elementos en orden (nodo, izquierda, derecha)
PostOrder(a)           -> Secuencia<T> con los elementos en orden (izquierda, derecha, nodo)
```

**Tareas** *(criterio propio, siguiendo el patrón del resto del capítulo)*: Especificar tipo. Diseñar pruebas (`assert`, incluyendo casos de árbol vacío, un solo nodo, y árbol degenerado tipo lista). Implementar parte pública. Implementar parte privada (recursiva, sobre punteros a nodo). Probar. Diseñar un programa de aplicación.

**Productos** *(criterio propio, mismo patrón `DD-` del resto del capítulo)*:
```
DD-ArbolBB
 |-- readme.md
 |-- ArbolBB.md // Especificación.
 |-- ArbolBBTest.cpp
 |-- ArbolBB.h
 |-- ArbolBB.cpp
 `-- ArbolBBApp.cpp
```

### 📘 Teoría necesaria

**TDA BST (abstracto):** conjunto ordenado, no una secuencia lineal — no hay "posición i", hay **estructura jerárquica**. La operación central es la comparación (`<`) contra el dato de cada nodo para decidir "¿voy a la izquierda o a la derecha?".

**Invariante BST** (el corazón de todo el capítulo): parado en cualquier nodo `n` del árbol,
- todos los valores del subárbol izquierdo de `n` son **menores** que `n->dato`,
- todos los valores del subárbol derecho de `n` son **mayores** que `n->dato`,
- y esto vale **recursivamente** para cada subárbol, no solo para la raíz.

```
                Darkside
                 /      \
              Bruce    Superman
              /   \      /    \
          Batman Diana Lois  Wonder Woman
```
(mismo ejemplo que en [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Templates, Arreglos Asociativos y Arboles.md|Templates, Arreglos Asociativos y Arboles]], acá con foco en cómo se implementa en vez de en el concepto).

**Nodo con dos punteros:**

```cpp
template<typename T>
class ArbolBB{
    struct Nodo{
        T     dato;
        Nodo* izquierda;
        Nodo* derecha;
    };
    Nodo* raiz;
    int   cantidad;
};
```

**Insertar (idea recursiva):** si el árbol (o subárbol) está vacío, ahí va el nodo nuevo. Si no, comparo contra la raíz: si es menor, insertar recursivamente en el subárbol izquierdo; si es mayor, en el derecho; si es igual, no hago nada (sin duplicados).

**Buscar (`Existe`, idea recursiva):** misma lógica de comparación, pero en vez de insertar, devuelvo `true` si encuentro el valor, `false` si llego a un subárbol vacío sin encontrarlo.

**Los tres recorridos** — la diferencia es **cuándo** se visita el nodo actual en relación a sus hijos:

| Recorrido | Orden de visita | Para qué sirve típicamente |
|---|---|---|
| **In-order** | izquierda → **nodo** → derecha | Devuelve los elementos **ordenados** ascendentemente — la razón de ser del BST |
| **Pre-order** | **nodo** → izquierda → derecha | Reconstruir el árbol / copiarlo (el primer elemento visitado es siempre la raíz) |
| **Post-order** | izquierda → derecha → **nodo** | Destruir/liberar el árbol de forma segura (los hijos se procesan antes que el padre) |

```cpp
// ejemplo mínimo de recorrido in-order (no la solución completa)
void InOrder(Nodo* n){
    if(n == nullptr) return;
    InOrder(n->izquierda);
    cout << n->dato << ' ';
    InOrder(n->derecha);
}
```

**Complejidad:** en un árbol **balanceado** de `n` elementos, `Insertar`/`Existe`/`Eliminar` son O(log n) (la altura del árbol). En el **peor caso** (árbol degenerado — ej. insertar elementos ya ordenados, cada uno se cuelga siempre del mismo lado) la altura es `n` y todo pasa a ser O(n), igual que una lista enlazada. Los recorridos completos siempre son O(n) (hay que visitar los `n` nodos sí o sí).

### 🧭 Cómo encararlo

1. Definí primero el `Nodo` con `izquierda`/`derecha` (no `siguiente` — es la diferencia estructural con todo lo anterior del capítulo).
2. Escribí `Insertar` de forma **recursiva**, pensando primero el caso base (subárbol vacío → ahí se crea el nodo) y después el caso recursivo (comparar y bajar a la izquierda o a la derecha). Necesitás un helper privado recursivo, por ejemplo `void Insertar(Nodo*& n, const T& valor)` que reciba el puntero **por referencia** (para poder modificarlo cuando es `nullptr` y hay que crear el nodo ahí).
3. `Existe` es casi calcado a `Insertar` pero sin modificar nada — solo comparar y bajar, devolviendo `bool`.
4. Los recorridos (`InOrder`/`PreOrder`/`PostOrder`) son los tres casi idénticos entre sí: solo cambia el **orden** en que aparecen las tres líneas (visitar nodo, recursar izquierda, recursar derecha). Escribilos usando la `SecDin<T>` de los capítulos 32/35 para devolver los resultados — reutilizás el TDA que ya hiciste.
5. `Eliminar` es la operación más delicada — dejala para el final, y pensá primero los 3 casos por separado: (a) nodo sin hijos → simplemente se borra; (b) nodo con un solo hijo → el hijo "sube" a ocupar el lugar del padre; (c) nodo con dos hijos → hay que buscar el **sucesor** (el menor del subárbol derecho, o simétricamente el mayor del izquierdo), copiar su valor al nodo a eliminar, y eliminar el sucesor (que por construcción tiene a lo sumo un hijo, cayendo en el caso (a) o (b)).
6. El destructor: recorrido **post-order** (liberar hijos antes que el padre), recursivo.
7. Probá con `assert`: árbol vacío, insertar un solo valor, insertar valores ya ordenados (para ver el caso degenerado), buscar un valor presente y uno ausente, y los tres recorridos sobre un árbol conocido (comparar contra el resultado esperado a mano).

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> // ===================== archivo: ArbolBB.h =====================
> #ifndef ARBOLBB_H
> #define ARBOLBB_H
> #include "SecDin.h"   // reutilizamos la Secuencia Dinámica de los capítulos 32/35
>
> template<typename T>
> class ArbolBB{
> public:
>     ArbolBB();
>     ~ArbolBB();
>
>     bool EsVacio() const;
>     int  Cantidad() const;
>     void Insertar(const T& valor);
>     bool Existe(const T& valor) const;
>     void Eliminar(const T& valor);
>
>     SecDin<T> InOrder() const;
>     SecDin<T> PreOrder() const;
>     SecDin<T> PostOrder() const;
>
> private:
>     struct Nodo{
>         T     dato;
>         Nodo* izquierda;
>         Nodo* derecha;
>     };
>     Nodo* raiz;
>     int   cantidad;
>
>     void Insertar(Nodo*& n, const T& valor);
>     bool Existe(Nodo* n, const T& valor) const;
>     void Eliminar(Nodo*& n, const T& valor);
>     Nodo* MinimoNodo(Nodo* n) const;        // el menor de un subárbol (baja siempre a la izquierda)
>     void Destruir(Nodo* n);                 // post-order

>     void InOrder(Nodo* n, SecDin<T>& resultado) const;
>     void PreOrder(Nodo* n, SecDin<T>& resultado) const;
>     void PostOrder(Nodo* n, SecDin<T>& resultado) const;
> };
>
> #include "ArbolBB.cpp"
> #endif
>
> // ===================== archivo: ArbolBB.cpp =====================
> #include "ArbolBB.h"
> #include <cassert>
>
> template<typename T>
> ArbolBB<T>::ArbolBB() : raiz(nullptr), cantidad(0) {}
>
> template<typename T>
> ArbolBB<T>::~ArbolBB(){
>     Destruir(raiz);
> }
>
> template<typename T>
> void ArbolBB<T>::Destruir(Nodo* n){
>     if(n == nullptr) return;
>     Destruir(n->izquierda);   // post-order: primero los hijos...
>     Destruir(n->derecha);
>     delete n;                  // ...recién después el propio nodo
> }
>
> template<typename T>
> bool ArbolBB<T>::EsVacio() const{ return raiz == nullptr; }
>
> template<typename T>
> int ArbolBB<T>::Cantidad() const{ return cantidad; }
>
> template<typename T>
> void ArbolBB<T>::Insertar(const T& valor){
>     Insertar(raiz, valor);
> }
>
> template<typename T>
> void ArbolBB<T>::Insertar(Nodo*& n, const T& valor){
>     if(n == nullptr){                      // caso base: acá va el nodo nuevo
>         n = new Nodo{valor, nullptr, nullptr};
>         ++cantidad;
>     } else if(valor < n->dato){
>         Insertar(n->izquierda, valor);
>     } else if(n->dato < valor){
>         Insertar(n->derecha, valor);
>     }
>     // si valor == n->dato, no se hace nada (sin duplicados)
> }
>
> template<typename T>
> bool ArbolBB<T>::Existe(const T& valor) const{
>     return Existe(raiz, valor);
> }
>
> template<typename T>
> bool ArbolBB<T>::Existe(Nodo* n, const T& valor) const{
>     if(n == nullptr) return false;
>     if(valor < n->dato) return Existe(n->izquierda, valor);
>     if(n->dato < valor) return Existe(n->derecha, valor);
>     return true;   // ni menor ni mayor -> es igual
> }
>
> template<typename T>
> typename ArbolBB<T>::Nodo* ArbolBB<T>::MinimoNodo(Nodo* n) const{
>     while(n->izquierda != nullptr)
>         n = n->izquierda;
>     return n;
> }
>
> template<typename T>
> void ArbolBB<T>::Eliminar(const T& valor){
>     Eliminar(raiz, valor);
> }
>
> template<typename T>
> void ArbolBB<T>::Eliminar(Nodo*& n, const T& valor){
>     if(n == nullptr) return;                 // no está, no hay nada que hacer
>
>     if(valor < n->dato){
>         Eliminar(n->izquierda, valor);
>     } else if(n->dato < valor){
>         Eliminar(n->derecha, valor);
>     } else {
>         // encontramos el nodo a eliminar
>         if(n->izquierda == nullptr && n->derecha == nullptr){
>             // caso (a): sin hijos
>             delete n;
>             n = nullptr;
>             --cantidad;
>         } else if(n->izquierda == nullptr){
>             // caso (b): un solo hijo (derecho) -> el hijo sube
>             Nodo* viejo = n;
>             n = n->derecha;
>             delete viejo;
>             --cantidad;
>         } else if(n->derecha == nullptr){
>             // caso (b): un solo hijo (izquierdo) -> el hijo sube
>             Nodo* viejo = n;
>             n = n->izquierda;
>             delete viejo;
>             --cantidad;
>         } else {
>             // caso (c): dos hijos -> copio el sucesor (mínimo del subárbol derecho)
>             // y elimino el sucesor de su lugar original (ahí cae en el caso (a) o (b))
>             Nodo* sucesor = MinimoNodo(n->derecha);
>             n->dato = sucesor->dato;
>             Eliminar(n->derecha, sucesor->dato);
>         }
>     }
> }
>
> template<typename T>
> void ArbolBB<T>::InOrder(Nodo* n, SecDin<T>& resultado) const{
>     if(n == nullptr) return;
>     InOrder(n->izquierda, resultado);
>     resultado.Agregar(n->dato);
>     InOrder(n->derecha, resultado);
> }
>
> template<typename T>
> SecDin<T> ArbolBB<T>::InOrder() const{
>     SecDin<T> resultado;
>     InOrder(raiz, resultado);
>     return resultado;
> }
>
> template<typename T>
> void ArbolBB<T>::PreOrder(Nodo* n, SecDin<T>& resultado) const{
>     if(n == nullptr) return;
>     resultado.Agregar(n->dato);
>     PreOrder(n->izquierda, resultado);
>     PreOrder(n->derecha, resultado);
> }
>
> template<typename T>
> SecDin<T> ArbolBB<T>::PreOrder() const{
>     SecDin<T> resultado;
>     PreOrder(raiz, resultado);
>     return resultado;
> }
>
> template<typename T>
> void ArbolBB<T>::PostOrder(Nodo* n, SecDin<T>& resultado) const{
>     if(n == nullptr) return;
>     PostOrder(n->izquierda, resultado);
>     PostOrder(n->derecha, resultado);
>     resultado.Agregar(n->dato);
> }
>
> template<typename T>
> SecDin<T> ArbolBB<T>::PostOrder() const{
>     SecDin<T> resultado;
>     PostOrder(raiz, resultado);
>     return resultado;
> }
>
> // ===================== archivo: ArbolBBTest.cpp =====================
> #include "ArbolBB.h"
> #include <cassert>
>
> int main(){
>     ArbolBB<int> a;
>     assert(a.EsVacio());
>
>     for(int v : {50, 30, 70, 20, 40, 60, 80})
>         a.Insertar(v);
>     assert(a.Cantidad() == 7);
>
>     assert(a.Existe(40));
>     assert(!a.Existe(999));
>
>     SecDin<int> orden = a.InOrder();
>     assert(orden.Tamanio() == 7);
>     for(int i = 0; i + 1 < orden.Tamanio(); ++i)
>         assert(orden.Obtener(i) < orden.Obtener(i+1));   // in-order siempre ascendente
>
>     a.Eliminar(30);           // nodo con dos hijos (20 y 40)
>     assert(!a.Existe(30));
>     assert(a.Existe(20) && a.Existe(40));   // los hijos siguen ahí, reacomodados
>     assert(a.Cantidad() == 6);
>
>     return 0;
> }
>
> // ===================== archivo: ArbolBBApp.cpp =====================
> // Aplicación: cargar palabras y mostrarlas ordenadas alfabéticamente (in-order).
> #include "ArbolBB.h"
> #include <iostream>
> #include <string>
>
> int main(){
>     ArbolBB<std::string> palabras;
>     for(std::string p; std::cin >> p; )
>         palabras.Insertar(p);
>
>     std::cout << "Palabras distintas: " << palabras.Cantidad() << "\nOrdenadas:\n";
>     SecDin<std::string> orden = palabras.InOrder();
>     for(int i = 0; i < orden.Tamanio(); ++i)
>         std::cout << orden.Obtener(i) << '\n';
>     return 0;
> }
> ```
> **Explicación de lo no obvio:** el helper `Insertar(Nodo*& n, ...)` recibe el puntero **por referencia** (`Nodo*&`) a propósito: cuando `n` es `nullptr` y hacés `n = new Nodo{...}`, esa asignación tiene que "verse" en el nodo padre real del árbol (ya sea `raiz` o el `izquierda`/`derecha` de otro nodo) — si `n` fuera un puntero común (por valor), estarías modificando una copia local y el nuevo nodo quedaría "perdido", sin engancharse a nada. El mismo truco se usa en `Eliminar` para poder reemplazar el nodo por su hijo (caso b) o por `nullptr` (caso a). El caso (c) de `Eliminar` reutiliza la propia función `Eliminar` de forma recursiva sobre el sucesor, en vez de duplicar la lógica de los otros dos casos.

### 💡 Trampas típicas / posibles preguntas

- Usar `Nodo*` (por valor) en vez de `Nodo*&` (por referencia) en los helpers de `Insertar`/`Eliminar` → el nodo nuevo/la reestructuración no queda enganchada al árbol real, se pierde silenciosamente.
- En `Eliminar` con dos hijos, copiar el dato del sucesor pero **olvidarse de eliminarlo** de su lugar original → el valor queda **duplicado** en el árbol, rompiendo el invariante BST.
- Confundir el orden de las tres líneas en los recorridos (visita/izquierda/derecha) → `PreOrder` con las líneas en el orden de `InOrder` no es un error de compilación, compila igual, pero da un resultado **semánticamente equivocado** — es un error puramente lógico, hay que revisarlo a mano.
- Insertar valores **ya ordenados** (`1,2,3,4,5...`) sin darse cuenta de que eso genera el peor caso: el árbol degenera en una lista enlazada (cada nodo cuelga siempre del mismo lado), perdiendo el O(log n). Es una pregunta clásica de oral: "¿este árbol está balanceado? ¿por qué no?".
- Destructor que no recorre en post-order (por ejemplo, hacer `delete n;` antes de liberar los hijos) → se pierden los punteros a los hijos antes de poder liberarlos → memory leak de subárboles enteros.
- Comparar con `==` en vez de con dos `<` (`valor < n->dato` / `n->dato < valor`) cuando `T` no tiene `operator==` definido pero sí `operator<` — el patrón de "menor y mayor, si ninguno, son iguales" es más general y es el que se usa en la biblioteca estándar de C++ (`std::set`, `std::map`).
