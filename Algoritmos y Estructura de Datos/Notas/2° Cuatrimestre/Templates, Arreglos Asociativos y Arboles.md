## [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Assets/AED 2018 K1051 Clase #27.pdf|PowerPoint]]

# Metaprogramación y Programación Genérica

**Programación genérica** = implementar tipos y funciones **parametrizados**, que sirven para una familia entera de casos en vez de uno solo. Cuando implementás una función genérica, en realidad estás implementando una *familia de funciones* (lo mismo aplica a tipos: implementás una *familia de tipos*).

**Metaprogramación** = programar un constructo que le indica al **compilador** que programe código fuente por vos (en tiempo de compilación, no de ejecución). En C++ el constructo para esto es `template` ("molde").

- Lo que escribís con `template` no es en sí mismo un programa que corre: es un programa **para que el compilador genere el programa real**, instanciando el molde con los tipos/valores concretos que necesites.
- Comparación entre lenguajes:
  - **Java**: no tiene metaprogramación, pero sí programación genérica (con impacto en tiempo de ejecución — usa type erasure/reflexión — pero código fuente más corto).
  - **C# y D**: siguen un modelo más parecido a C++.
  - **C**: logra algo similar con métodos no seguros como `void*` (sin chequeo de tipos en compilación).

## De la función específica a la función genérica — evolución paso a paso

Este es el ejemplo clásico de la cátedra para entender **por qué** existen los templates (no memorizarlos de una, sino ver el problema que resuelven).

Partimos de una función que imprime un `array<int,5>` específico:

```cpp
void PrintArrayInt5(const array<int,5>& x){
    for(auto e : x)
        cout << e << '\t';
    cout << '\n';
}
PrintArrayInt5(a);
```

**Problema:** solo sirve para arreglos de `int` de tamaño exactamente 5. Si necesito otro tamaño, tengo que copiar y pegar la función cambiando el `5`. Primer paso: generalizar el **tamaño** con un template (`size_t n` es un parámetro de template que es un **valor**, no un tipo):

```cpp
template <size_t n>
void PrintArrayInt(const array<int, n>& x){
    for(auto e : x)
        cout << e << '\t';
    cout << '\n';
}
PrintArrayInt(a);
```

Ahora sirve para `array<int, n>` de cualquier tamaño `n`, pero sigue atado a `int`. Segundo paso: generalizar también el **tipo** (`typename T` es un parámetro de template que es un **tipo**):

```cpp
template <typename T, size_t n>
void PrintArray(const array<T, n>& x){
    for(auto e : x)
        cout << e << '\t';
    cout << '\n';
}
PrintArray(a);
```

Ahora `PrintArray` sirve para un `array` de cualquier tipo y cualquier tamaño. **Una sola implementación** cubre infinitas combinaciones (T, n) — eso es "implementar una familia de funciones", el concepto de arriba, en carne propia.

> 💡 Para el parcial: si te dan una función con `template<typename T>` o `template<size_t n>`, identificá primero **qué se está generalizando** (¿el tipo de los datos? ¿un tamaño/cantidad que antes era fijo?). Esa es la pregunta que casi siempre te hacen: "¿qué gana este código al agregarle el template respecto a la versión anterior sin template?"

## Familia de tipos: `Matrix` con `using` + template + valores por *default*

Los templates no son solo para funciones — también para **tipos**. Y, al igual que los parámetros de función, pueden tener **valores por default**.

```cpp
template <typename T = int, std::size_t rows = 1, std::size_t columns = rows>
using Matrix = std::array<std::array<T, columns>, rows>;
```

Leído de adentro hacia afuera: `Matrix<T, rows, columns>` es "un array de `rows` elementos, donde cada elemento es un array de `columns` elementos de tipo `T`" — es decir, una matriz de `rows` × `columns`.

Ejemplos de uso, mostrando cómo se completan los *defaults* que no especificás:

```cpp
array<array<int,3>,5>  aai35;   // la versión "sin azúcar", escrita a mano
Matrix<int,5,3>         mi53;   // T=int, rows=5, columns=3 -> matriz 5x3 de int
Matrix<>                mi11;   // todo por default: T=int, rows=1, columns=rows=1 -> 1x1 de int
Matrix<double>           md11;   // T=double, rows=1 (default), columns=rows=1 -> 1x1 de double
Matrix<char,2>            mc22;   // T=char, rows=2, columns=rows=2 (toma el valor de rows!) -> 2x2 de char
```

> 💡 Trampa de parcial: fijate que el default de `columns` es `rows` (no un número fijo). Por eso `Matrix<char,2>` da una matriz **cuadrada** 2×2: como no especificaste `columns`, toma el valor que sí especificaste para `rows`.

---

# Arreglo Asociativo

También llamado *Lookup Table*, Tabla de Búsqueda, Tabla de Símbolos, o **Mapa**. La idea: guardar pares `(clave, valor)` y poder buscar el valor a partir de la clave — a diferencia de un array común, donde buscás por posición numérica, acá buscás por **clave**.

## Ejemplo: Agenda Telefónica como Tipo de Dato Abstracto (TDA)

```
Agenda = {(nombre,teléfono) / nombre in Σ*, nombre no vacío, teléfono no vacío}
```

Operaciones:
- `Add(a, nombre, telefono)` — agrega o actualiza un par
- `Get(a, nombre)` — devuelve el teléfono asociado a ese nombre
- `Set(a, nombre, telefono)` — modifica el teléfono de un nombre existente
- `Remove(a, nombre)` — borra el par

```cpp
Agenda a;
Add(a, "Superman",     "555-4567");
Add(a, "Batman",       "555-5555");
Add(a, "Wonder Woman", "555-1234");
Add(a, "Lois",         "555-4567");
Add(a, "Bruce",        "555-5555");
Add(a, "Diana",        "555-1234");
Add(a, "Darkside",     "555-6666");

cout << Get(a, "Lois"); // 555-4567
```

## ¿Cómo se implementa por dentro? Arreglo vs. Lista enlazada vs. Árbol

| Representación | Espacio | Tiempo de búsqueda |
|---|---|---|
| Arreglo (sin ordenar) o Lista enlazada | O(n) | O(n) — hay que recorrer todo en el peor caso |
| **Árbol** (bien balanceado) | O(n) | **O(log n)** |

Esta comparación de complejidad es exactamente la motivación para meterse con árboles: un arreglo/lista te obliga a mirar, en el peor caso, **todos** los elementos para encontrar uno. Un árbol bien organizado te permite descartar la mitad de los candidatos en cada paso.

---

# Introducción a Árboles — BST (Árbol de Búsqueda Binario)

Un **BST** organiza los datos de forma que, parado en cualquier nodo, todo lo que está en la rama de la **izquierda** es "menor" y todo lo que está en la rama de la **derecha** es "mayor" (según algún criterio de orden — en el ejemplo, orden alfabético del nombre).

```
                      root
                        |
                    Darkside
                     /      \
                  Bruce    Superman
                  /   \      /    \
              Batman Diana Lois  Wonder Woman
```

Leyendo el diagrama: desde `root` bajás a `Darkside`. Como `Bruce < Darkside`, va a la izquierda; como `Superman > Darkside`, va a la derecha. Y así sucesivamente en cada nivel.

Con esta organización, buscar un nombre es **logarítmico**: en cada nodo comparás y descartás toda una mitad del árbol, en vez de tener que recorrer elemento por elemento como en una lista.

Un árbol también sirve para implementar el TDA **Conjunto Finito** (no solo Agenda/Mapa), con las operaciones típicas:
- `Add` — agregar un elemento
- `IsMember` — ¿está el elemento?
- `Remove` — sacar el elemento

**Pregunta abierta que deja la cátedra para pensar:** ¿se puede implementar con este mismo esquema un conjunto **infinito**? ¿una secuencia infinita? (Pista para pensarlo vos: un árbol/estructura finita en memoria no puede *contener* infinitos elementos a la vez, pero sí podría *generar* o *representar* una secuencia infinita bajo demanda — es la puerta de entrada a ideas de evaluación perezosa/streams, que en general se ve más adelante).

---

# 🧪 Práctica

1. Te dan esta función ya con template. Explicá qué generaliza respecto a una versión sin template, y escribí un ejemplo de instanciación concreta:
   ```cpp
   template <typename T>
   T Maximo(const T& a, const T& b){
       return a > b ? a : b;
   }
   ```

2. Dado
   ```cpp
   template <typename T = double, std::size_t n = 3>
   using Vector = std::array<T, n>;
   ```
   ¿Qué tipo concreto genera `Vector<>`? ¿Y `Vector<int>`? ¿Y `Vector<int, 5>`?

3. Explicá con tus palabras por qué buscar en un array desordenado es O(n) pero buscar en un BST bien armado es O(log n). ¿Qué pasa si el BST queda muy desbalanceado (por ejemplo, insertás los elementos ya ordenados)?

4. Dibujá el BST que resulta de insertar, en este orden, las claves: `50, 30, 70, 20, 40, 60, 80` (menor a la izquierda, mayor a la derecha).

5. ¿Por qué se dice que implementar una función con `template<typename T>` es "implementar una familia de funciones" en vez de una sola función? Relacionalo con el ejemplo de `PrintArrayInt5` → `PrintArrayInt` → `PrintArray` de arriba.
