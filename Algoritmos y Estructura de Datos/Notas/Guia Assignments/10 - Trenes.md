⬅️ [[00 - Índice y Requisitos Generales]]

# 10. Trenes (versión contigua y enlazada)

> Enunciado de una revisión del libro más nueva que la del PDF que usamos (Rev. 7.2.0-rc.1): ahí aparece numerado como 42, pero en nuestro PDF el 42 es la Bibliografía. Por eso va como nota 10 de la guía y no en la numeración del libro. El problema de dimensionar recursos (cuántas formaciones hacen falta) está aparte, en [[09 - Línea AyED - Viajes de Pasajeros y Cargas]]; sirve como contexto para armar los datos de prueba de este.

## Enunciado resumido

**Objetivos:**
- Diseñar e implementar tipos.
- Aplicar composición de tipos.
- Demostrar capacidad de simular estructuras dinámicas.
- Estructuras enlazadas.

**Temas:** Tipos · Estructuras · Flujos.

**Problema:** dada una necesidad de servicios de trenes, armar las **formaciones** que permiten satisfacer los servicios de carga y de pasajeros.

**Restricciones:**
- Flujos estándar: `cin` y `cout`.
- **Dos versiones** de la solución: **contigua** y **enlazada**.
- Los programas se llaman `trenescont` y `treneslink`.
- Se ejecutan así: `trenescont < comandos.txt > resultados.txt`.
- Los **comandos** indican cómo crear las formaciones.
- Se deben especificar e implementar estos tipos con sus operaciones:

| Tipo | Operaciones del enunciado |
|---|---|
| `Formación` | `Crear : Recurso → Formación` · `AddLocomotora : Formación × Recurso → Formación` · `AddVagón : Formación × Recurso → Formación` · `GetCapacidad : Formación → ℕ` · `…` |
| `PoolDeRecursos` | `Agregar : …` · `Sacar : …` · `…` |

**Código de partida (tal cual):**

```cpp
#include<array>
#include<string>
using namespace std::literals;
struct Recurso{
    static const unsigned CapacidadCarga{17};
    static const unsigned CapacidadPasaje{21};
    enum struct Tipo{Locomotora, Pasajero, Comedor, Cocina, Carga};
    Tipo tipo;
    std::string id;
    unsigned capacidad;//despues lo vemos
};
struct Formación{
    static const unsigned LongitudMáxima{10};
    std::string id;
    std::array<Recurso, LongitudMáxima> links;
};
int main() {
    Recurso primero{ Recurso::Tipo::Locomotora, "L001"s, 2};
}
```

**Productos:** carpeta `DD-Trenes` con `readme.md`, `trenes.md` (la **especificación** de los tipos), `trenescont.cpp` y `treneslink.cpp`.

> ⚠️ **Erratas del enunciado:** la carpeta dice `DD-Polígonos` (copy-paste, debería ser `DD-Trenes`) y la lista de productos está cortada en `trenesl…` (es `treneslink.cpp`).

## Cómo sacar de la consigna qué hay que hacer

Es un método mecánico: leé el enunciado tres veces, buscando una cosa distinta cada vez.

| Qué buscás | Qué te dice | En este enunciado |
|---|---|---|
| **Sustantivos** | Los **tipos** | Recurso, Formación, PoolDeRecursos (y servicio de carga / de pasajeros) |
| **Verbos** | Las **operaciones** | crear una formación, agregar locomotora, agregar vagón, saber la capacidad, agregar un recurso al pool, sacar un recurso del pool |
| **Restricciones** | Los **entregables** | dos programas (`trenescont`, `treneslink`), leer de `cin` y escribir en `cout`, un archivo `trenes.md` con la especificación, un `readme.md` |
| **Lo que no está definido** | Las **hipótesis** que tenés que declarar | el formato de los comandos, cuántos recursos entran en el pool, qué pasa cuando falta un recurso |
| **Los `…`** | Operaciones que **vos** agregás | las que vayas necesitando al pensar cómo armar una formación (por ejemplo contar cuántos vagones de un tipo quedan en el pool) |

La frase clave del problema es **"dada una necesidad de servicios a satisfacer, armar las formaciones"**. La necesidad es un dato de entrada (cuánta capacidad hay que transportar y de qué tipo, carga o pasajeros), y el programa tiene que armar formaciones que la cubran sacando recursos del pool. Los **comandos** son esa necesidad escrita en el archivo de entrada.

## Qué NO dice el enunciado (hipótesis a declarar en el `readme.md`)

| # | Falta definir | Hipótesis que tomé |
|---|---|---|
| H1 | Qué es una "necesidad" | Un comando `CF <pasajero\|carga> <capacidad>`: cuánta capacidad hay que transportar y de qué servicio |
| H2 | Cargar el pool | Un comando `agregar <tipo> <id>` por cada recurso disponible (locomotoras y vagones) |
| H3 | Cuánto transporta un vagón | `Recurso::CapacidadPasaje` (21) o `Recurso::CapacidadCarga` (17); locomotora, comedor y cocina: 0 (es el "después lo vemos") |
| H4 | Largo de una formación | `LongitudMáxima = 10` cuenta **todos** los links; con 1 locomotora quedan **9 vagones** como máximo |
| H5 | Cuántos vagones hacen falta | `ceil(capacidad pedida / capacidad del vagón)`; si no entran en una formación se arman varias (`ceil(vagones / 9)`) |
| H6 | Crear una formación | `Crear` recibe la **locomotora** inicial y un `id` (`F1`, `F2`, ...) |
| H7 | Qué pasa si faltan recursos | El comando **no se ejecuta**: se informa `error: ...` y el pool queda como estaba (se verifica todo **antes** de sacar nada) |
| H8 | Recursos que se sacan del pool | El **primero** que llegó de ese tipo (orden de llegada) |
| H9 | Tamaños máximos | Contigua: pool de 64 recursos; 32 formaciones como máximo en las dos versiones |

> 💡 Si la cátedra pasa un `comandos.txt` con otro formato, solo cambia el intérprete; los tipos y sus operaciones no se tocan. Por eso conviene mantenerlos separados.

## 📘 Teoría necesaria

### a) Tipos compuestos y composición de tipos
Un `Recurso` agrupa `Tipo` + `id` + `capacidad`. Una `Formación` **contiene** recursos (una secuencia de `Recurso`). Eso es **composición**: un tipo armado con otros. Es el mismo mecanismo que `Triángulo` = 3 `Punto` en [[05 - Geometría]].

### b) `enum struct`
`enum struct Tipo { Locomotora, ... }` es un enumerado **de ámbito cerrado** (igual que `enum class`): se escribe `Recurso::Tipo::Locomotora`, no hay conversión implícita a `int` y los nombres no ensucian el espacio global. Ver [[04 - Abstracción con Tipos]].

### c) Miembros `static const` dentro de un `struct`
`static const unsigned CapacidadCarga{17};` es una constante **de la clase**, no de cada objeto: existe una sola copia y se usa como `Recurso::CapacidadCarga`. No ocupa lugar en cada `Recurso`. `Formación::LongitudMáxima` también se usa como tamaño del `std::array`, que exige una constante de compilación.

### d) Especificar un tipo: las operaciones como funciones
En `trenes.md` cada operación se escribe como `Nombre : lo que recibe → lo que devuelve`, con `×` para separar varios argumentos. Para escribirla te preguntás dos cosas: **qué necesita** para trabajar y **qué entrega**.
- `GetCapacidad : Formación → ℕ` recibe una formación y devuelve un natural.
- `AddVagón : Formación × Recurso → Formación` recibe una formación y un recurso, y **devuelve la formación nueva**.

En esta notación una función **no modifica** lo que recibe: devuelve el resultado. Por eso `Sacar` del pool devuelve **dos cosas**: `Sacar : PoolDeRecursos × Tipo → PoolDeRecursos × Recurso` (el pool sin el recurso y el recurso sacado). En C++ eso se puede traducir de dos maneras (ver el punto f).

### e) Representación contigua vs. enlazada
Mismo tipo, misma interfaz, dos representaciones:

| | Contigua | Enlazada |
|---|---|---|
| `Formación` | `std::array<Recurso, 10>` + contador `longitud` | `Nodo*` al primer recurso + `longitud` |
| `PoolDeRecursos` | `std::array<Recurso, 64>` + contador | `Nodo*` al primero |
| Tope | `LongitudMáxima` / `Capacidad` fijos | El pool no tiene tope; la formación lo respeta por regla |
| `Sacar` | busca y **corre** los de la derecha un lugar | busca y **reengancha** (`anterior->siguiente = actual->siguiente`) |
| Memoria | todo vive dentro del objeto | `new` por nodo, hay que `delete` (ver [[Punteros y Listas Enlazadas]]) |

La idea central del trabajo: **el código que usa los tipos no cambia**. Por eso el intérprete de comandos es idéntico en las dos versiones. Mismo concepto que en [[07 - Secuencias, Pilas y Colas]] (Stack contiguo vs enlazado).

### f) Pasar por valor vs. por referencia
- **Por valor** (`Formación AddVagón(Formación f, Recurso r)`): la función recibe una copia y devuelve la nueva. Es fiel a la notación matemática. Se usa reasignando: `f = AddVagón(f, v);`.
- **Por referencia** (`bool Sacar(PoolDeRecursos& p, Recurso::Tipo t, Recurso& salida)`): la función modifica el original, y lo que "devuelve" sale por un parámetro. Evita copiar un pool grande (64 recursos) en cada llamada.

### g) Copiar un tipo con punteros
En la versión contigua, `Formación f2 = f1;` copia todo el arreglo (copia **profunda**, por defecto). En la enlazada copia solo el **puntero** `primero`: `f1` y `f2` apuntan a los **mismos nodos**. Con las firmas "por valor" eso obliga a una regla: después de `f = AddVagón(f, r)` la formación vieja no se vuelve a usar, y los nodos se liberan **una sola vez** (`Destruir`). Ver [[Call Stack y Heap]] (doble `delete`).

### h) División hacia arriba (ceil) con enteros
`n / c` entre enteros trunca hacia abajo. Para saber cuántos vagones hacen falta hay que redondear **hacia arriba**: `(n + c - 1) / c`. Con `n = 100` y `c = 21`: `(100 + 20) / 21 = 5` (cinco vagones, capacidad 105). Con `n = 105` da exactamente 5, no 6. Es la misma fórmula de [[09 - Línea AyED - Viajes de Pasajeros y Cargas]].

### i) Flujos estándar y redirección
`cin >> comando` dentro de un `while` lee hasta el fin de archivo; la redirección `< comandos.txt > resultados.txt` no cambia el código. Es el patrón del TP18 (ver la guía `Guia-Examen-Oral.md`, sección de streams y redirección). Cada comando se lee como una palabra, y cada rama del `if` lee **sus** argumentos con `cin >> ...`.

## 📐 Cómo se arma `trenes.md` (la especificación)

Es un documento **sin código** que explica los tipos y el lenguaje de comandos. Se escribe **antes** de programar. Tiene cuatro partes.

**1. Firmas.** Las del enunciado más las que agregues donde dice `…`. Una por línea, en el formato `Nombre : entrada → salida`.

**2. Comandos.** El lenguaje del archivo de entrada. Por cada comando: nombre, parámetros y qué hace.

**3. Salida.** Qué imprime el programa por cada comando, con el formato exacto (un error, una formación armada, etc.).

**4. Descripción de cada operación.** Para cada firma: qué hace en una frase, cuándo no se puede usar y qué cambia al terminar.

Un ejemplo con las partes 1 a 3 resueltas (completá vos la 4 para las que faltan, con el modelo de `AddVagón`):

```text
# Trenes — especificación

## Formación
Crear         : String × Recurso → Formación
AddLocomotora : Formación × Recurso → Formación
AddVagón      : Formación × Recurso → Formación
GetCapacidad  : Formación → ℕ
GetLongitud   : Formación → ℕ

## PoolDeRecursos
Agregar       : PoolDeRecursos × Recurso → PoolDeRecursos
Sacar         : PoolDeRecursos × Tipo → PoolDeRecursos × Recurso
GetCantidad   : PoolDeRecursos × Tipo → ℕ

## Comandos
agregar <tipo> <id>          carga un recurso en el pool (tipo: locomotora, pasajero, carga, comedor, cocina)
CF <pasajero|carga> <N>      arma las formaciones que transportan al menos N
mostrar                      lista todas las formaciones armadas

## Salida
agregar  ->  "pool + <tipo> <id>"
CF       ->  una línea por formación: "CF <tipo> <N> -> F1: L001 P001 ... (capacidad C)"
             y una última línea: "   capacidad pedida <N>, armada <total>"
errores  ->  "error: <motivo>" (el pool queda sin cambios)

## Operaciones
AddVagón : Formación × Recurso → Formación
  Qué hace:   agrega el vagón al final de la formación.
  No se puede: si la formación ya tiene LongitudMáxima recursos, o si el recurso es una locomotora.
  Qué cambia: la longitud sube en 1 y la capacidad suma la del vagón.
```

## 🧭 Cómo encararlo

1. **Declarar las hipótesis H1–H9** en el `readme.md` y escribir `trenes.md` con las cuatro partes. Sin código todavía.
2. **Hacer la cuenta a mano** con 2 o 3 pedidos: `CF pasajero 100` → `ceil(100/21) = 5` vagones, 1 formación; `CF pasajero 300` → 15 vagones, 2 formaciones (9 + 6). Si la cuenta te da distinto al programa, uno de los dos está mal.
3. **Tipos primero, sin comandos.** Escribir `Recurso` (ya está) y un `main` de prueba con `assert`s: crear una formación con una locomotora, agregar dos vagones, `assert(GetCapacidad(f) == ...)`.
4. **Agregar el contador `longitud`.** El código de partida no lo trae: sin él no sabés qué posiciones del `std::array` están en uso.
5. **Decidir qué pasa al llenarse** (10 links): la operación no debe aceptar el 11.º. Pensá si lo controla la función (`assert`) o quien la llama (el intérprete, para poder informar un `error`).
6. **`PoolDeRecursos`:** `Agregar`, `Sacar` y `GetCantidad`. Probalos con `assert`s antes de conectar nada más.
7. **Intérprete:** un `while (cin >> comando)` con un `if` por comando. En `CF` el orden es: calcular cuántos vagones y formaciones hacen falta, **verificar que el pool alcance**, y recién entonces sacar y armar.
8. **Armar `comandos.txt`** con casos felices y de error: pool sin locomotoras, vagones insuficientes, capacidad 0, tipo inválido, un pedido que necesite dos formaciones.
9. **Versión enlazada:** cambiar **solo** los dos `struct` y sus operaciones. Si tenés que tocar el intérprete, el diseño no separó bien interfaz de implementación.
10. **Verificar:** ejecutar las dos versiones con el mismo `comandos.txt` y comparar con `diff`: tienen que dar exactamente el mismo `resultados.txt`.
11. **Memoria (enlazada):** correr con `-fsanitize=address` para ver que no quedan nodos sin liberar.

## ✅ Solución completa

> Estructura de archivos (el intérprete y `Recurso` se comparten; si la cátedra exige **solo** los dos `.cpp`, pegá el contenido de `Recurso.h` e `Interprete.inc` dentro de cada uno).
> ```
> DD-Trenes/
>  |-- readme.md
>  |-- trenes.md        // especificación
>  |-- Recurso.h
>  |-- Interprete.inc
>  |-- trenescont.cpp
>  |-- treneslink.cpp
>  `-- comandos.txt
> ```

> [!example]- Recurso.h (común)
> ```cpp
> /* Recurso.h
>  * Tipo Recurso (código de partida) y utilidades comunes a trenescont y treneslink
>  * Equipo 1
>  * 20261021
>  */
> #ifndef RECURSO_H
> #define RECURSO_H
> #include <string>
> using namespace std::literals;
> 
> struct Recurso{
>     static const unsigned CapacidadCarga{17};
>     static const unsigned CapacidadPasaje{21};
>     enum struct Tipo{Locomotora, Pasajero, Comedor, Cocina, Carga};
>     Tipo tipo;
>     std::string id;
>     unsigned capacidad;  // despues lo vemos
> };
> 
> // Capacidad que le corresponde a cada tipo (la locomotora no transporta)
> inline unsigned CapacidadDe(Recurso::Tipo t){
>     switch(t){
>         case Recurso::Tipo::Carga:   return Recurso::CapacidadCarga;
>         case Recurso::Tipo::Pasajero:return Recurso::CapacidadPasaje;
>         default:                     return 0;
>     }
> }
> inline bool LeerTipo(const std::string& s, Recurso::Tipo& t){
>     if(s=="locomotora") t=Recurso::Tipo::Locomotora;
>     else if(s=="pasajero") t=Recurso::Tipo::Pasajero;
>     else if(s=="comedor")  t=Recurso::Tipo::Comedor;
>     else if(s=="cocina")   t=Recurso::Tipo::Cocina;
>     else if(s=="carga")    t=Recurso::Tipo::Carga;
>     else return false;
>     return true;
> }
> inline const char* NombreTipo(Recurso::Tipo t){
>     switch(t){
>         case Recurso::Tipo::Locomotora: return "locomotora";
>         case Recurso::Tipo::Pasajero:   return "pasajero";
>         case Recurso::Tipo::Comedor:    return "comedor";
>         case Recurso::Tipo::Cocina:     return "cocina";
>         case Recurso::Tipo::Carga:      return "carga";
>     }
>     return "?";
> }
> #endif
> ```

> [!example]- Interprete.inc (común)
> ```cpp
> /* Interprete.inc
>  * Lee comandos de cin y arma formaciones. Idéntico para trenescont y treneslink:
>  * usa sólo la INTERFAZ (Crear, AddVagón, GetCantidad, Agregar, Sacar...).
>  * Se incluye al final de cada .cpp, después de definir Formación y PoolDeRecursos.
>  * Equipo 1
>  * 20261021
>  */
> #include <array>
> #include <iostream>
> 
> int main(){
>     PoolDeRecursos pool{};
>     std::array<Formación, 32> formaciones{};
>     unsigned cantFormaciones{0};
> 
>     std::string comando;
>     while(std::cin >> comando){
>         if(comando == "agregar"){                       // agregar <tipo> <id>
>             std::string tipoStr, id;
>             std::cin >> tipoStr >> id;
>             Recurso::Tipo t;
>             if(!LeerTipo(tipoStr, t))      std::cout << "error: tipo desconocido " << tipoStr << '\n';
>             else if(!Agregar(pool, Recurso{t, id, CapacidadDe(t)}))
>                                            std::cout << "error: pool lleno\n";
>             else                           std::cout << "pool + " << tipoStr << ' ' << id << '\n';
>         }
>         else if(comando == "CF"){                       // CF <pasajero|carga> <capacidad>
>             std::string tipoStr;
>             unsigned n{0};
>             std::cin >> tipoStr >> n;
>             Recurso::Tipo t;
>             if(!LeerTipo(tipoStr, t) || (t != Recurso::Tipo::Pasajero && t != Recurso::Tipo::Carga)){
>                 std::cout << "error: CF necesita pasajero o carga, no " << tipoStr << '\n';
>                 continue;
>             }
>             if(n == 0){ std::cout << "error: la capacidad debe ser mayor que 0\n"; continue; }
> 
>             // 1) Cuenta: cuántos vagones y cuántas formaciones hacen falta
>             unsigned vagones = (n + CapacidadDe(t) - 1) / CapacidadDe(t);        // ceil(n / capacidadVagon)
>             unsigned porFormacion = Formación::LongitudMáxima - 1;               // 1 lugar es de la locomotora
>             unsigned necesarias = (vagones + porFormacion - 1) / porFormacion;   // ceil(vagones / porFormacion)
> 
>             // 2) Verifico TODO antes de sacar nada, así un CF fallido no deja el pool a medias
>             if(cantFormaciones + necesarias > formaciones.size())
>                 std::cout << "error: no hay lugar para " << necesarias << " formacion(es) mas\n";
>             else if(GetCantidad(pool, Recurso::Tipo::Locomotora) < necesarias)
>                 std::cout << "error: faltan locomotoras (hacen falta " << necesarias << ")\n";
>             else if(GetCantidad(pool, t) < vagones)
>                 std::cout << "error: faltan vagones de " << tipoStr << " (hacen falta " << vagones << ")\n";
>             else{
>                 // 3) Armo las formaciones
>                 unsigned capacidadTotal{0};
>                 for(unsigned k = 0; k < necesarias; ++k){
>                     Recurso loc, vag;
>                     Sacar(pool, Recurso::Tipo::Locomotora, loc);
>                     Formación f = Crear("F" + std::to_string(cantFormaciones + 1), loc);
>                     for(unsigned i = 0; i < porFormacion && vagones > 0; ++i, --vagones){
>                         Sacar(pool, t, vag);
>                         f = AddVagón(f, vag);
>                     }
>                     capacidadTotal += GetCapacidad(f);
>                     formaciones.at(cantFormaciones++) = f;
>                     std::cout << "CF " << tipoStr << ' ' << n << " -> " << Mostrar(f) << '\n';
>                 }
>                 std::cout << "   capacidad pedida " << n << ", armada " << capacidadTotal << '\n';
>             }
>         }
>         else if(comando == "mostrar"){
>             for(unsigned i = 0; i < cantFormaciones; ++i)
>                 std::cout << Mostrar(formaciones.at(i)) << '\n';
>         }
>         else std::cout << "error: comando desconocido " << comando << '\n';
>     }
>     for(unsigned i = 0; i < cantFormaciones; ++i) Destruir(formaciones.at(i));
>     Destruir(pool);
> }
> ```

> [!example]- trenescont.cpp (contigua)
> ```cpp
> /* trenescont.cpp
>  * Trenes - versión CONTIGUA: Formación y PoolDeRecursos sobre std::array.
>  * Equipo 1
>  * 20261021
>  */
> #include <array>
> #include <cassert>
> #include <string>
> #include "Recurso.h"
> 
> struct Formación{
>     static const unsigned LongitudMáxima{10};
>     std::string id;
>     std::array<Recurso, LongitudMáxima> links;
>     unsigned longitud{0};              // cuántos links están en uso
> };
> 
> // ----- Formación -----
> Formación Crear(const std::string& id, Recurso locomotora){
>     assert(locomotora.tipo == Recurso::Tipo::Locomotora);
>     Formación f{};
>     f.id = id;
>     f.links.at(0) = locomotora;
>     f.longitud = 1;
>     return f;
> }
> unsigned GetLongitud(const Formación& f){ return f.longitud; }
> Formación AddLocomotora(Formación f, Recurso r){
>     assert(r.tipo == Recurso::Tipo::Locomotora && f.longitud < Formación::LongitudMáxima);
>     f.links.at(f.longitud++) = r;
>     return f;
> }
> Formación AddVagón(Formación f, Recurso r){
>     assert(r.tipo != Recurso::Tipo::Locomotora && f.longitud < Formación::LongitudMáxima);
>     f.links.at(f.longitud++) = r;
>     return f;
> }
> unsigned GetCapacidad(const Formación& f){
>     unsigned total{0};
>     for(unsigned i = 0; i < f.longitud; ++i) total += f.links.at(i).capacidad;
>     return total;
> }
> std::string Mostrar(const Formación& f){
>     std::string s = f.id + ":";
>     for(unsigned i = 0; i < f.longitud; ++i) s += " " + f.links.at(i).id;
>     return s + " (capacidad " + std::to_string(GetCapacidad(f)) + ")";
> }
> void Destruir(Formación&){}            // nada que liberar: todo vive en el objeto
> 
> // ----- PoolDeRecursos -----
> struct PoolDeRecursos{
>     static const unsigned Capacidad{64};
>     std::array<Recurso, Capacidad> recursos;
>     unsigned cantidad{0};
> };
> bool Agregar(PoolDeRecursos& p, Recurso r){
>     if(p.cantidad == PoolDeRecursos::Capacidad) return false;
>     p.recursos.at(p.cantidad++) = r;
>     return true;
> }
> bool Sacar(PoolDeRecursos& p, Recurso::Tipo t, Recurso& salida){
>     for(unsigned i = 0; i < p.cantidad; ++i){
>         if(p.recursos.at(i).tipo == t){
>             salida = p.recursos.at(i);
>             for(unsigned j = i + 1; j < p.cantidad; ++j)   // cierro el hueco
>                 p.recursos.at(j - 1) = p.recursos.at(j);
>             --p.cantidad;
>             return true;
>         }
>     }
>     return false;
> }
> unsigned GetCantidad(const PoolDeRecursos& p, Recurso::Tipo t){
>     unsigned n{0};
>     for(unsigned i = 0; i < p.cantidad; ++i) if(p.recursos.at(i).tipo == t) ++n;
>     return n;
> }
> void Destruir(PoolDeRecursos&){}
> 
> #include "Interprete.inc"
> ```

> [!example]- treneslink.cpp (enlazada)
> ```cpp
> /* treneslink.cpp
>  * Trenes - versión ENLAZADA: Formación y PoolDeRecursos como listas de nodos.
>  * Equipo 1
>  * 20261021
>  */
> #include <cassert>
> #include <string>
> #include "Recurso.h"
> 
> struct Nodo{
>     Recurso valor;
>     Nodo* siguiente;
> };
> // Agrega un nodo al FINAL de la lista que empieza en 'primero'
> void AgregarAlFinal(Nodo*& primero, Recurso r){
>     Nodo* nuevo = new Nodo{r, nullptr};
>     if(!primero){ primero = nuevo; return; }
>     Nodo* p = primero;
>     while(p->siguiente) p = p->siguiente;
>     p->siguiente = nuevo;
> }
> void Liberar(Nodo*& primero){
>     while(primero){
>         Nodo* sig = primero->siguiente;
>         delete primero;
>         primero = sig;
>     }
> }
> 
> struct Formación{
>     static const unsigned LongitudMáxima{10};
>     std::string id;
>     Nodo* primero{nullptr};
>     unsigned longitud{0};
> };
> 
> // ----- Formación -----
> // OJO: copiar una Formación copia el PUNTERO, no los nodos. Al hacer f = AddVagón(f, r)
> // la Formación vieja deja de usarse (se "transfiere" a la nueva).
> Formación Crear(const std::string& id, Recurso locomotora){
>     assert(locomotora.tipo == Recurso::Tipo::Locomotora);
>     Formación f{};
>     f.id = id;
>     AgregarAlFinal(f.primero, locomotora);
>     f.longitud = 1;
>     return f;
> }
> unsigned GetLongitud(const Formación& f){ return f.longitud; }
> Formación AddLocomotora(Formación f, Recurso r){
>     assert(r.tipo == Recurso::Tipo::Locomotora && f.longitud < Formación::LongitudMáxima);
>     AgregarAlFinal(f.primero, r);
>     ++f.longitud;
>     return f;
> }
> Formación AddVagón(Formación f, Recurso r){
>     assert(r.tipo != Recurso::Tipo::Locomotora && f.longitud < Formación::LongitudMáxima);
>     AgregarAlFinal(f.primero, r);
>     ++f.longitud;
>     return f;
> }
> unsigned GetCapacidad(const Formación& f){
>     unsigned total{0};
>     for(const Nodo* p = f.primero; p; p = p->siguiente) total += p->valor.capacidad;
>     return total;
> }
> std::string Mostrar(const Formación& f){
>     std::string s = f.id + ":";
>     for(const Nodo* p = f.primero; p; p = p->siguiente) s += " " + p->valor.id;
>     return s + " (capacidad " + std::to_string(GetCapacidad(f)) + ")";
> }
> void Destruir(Formación& f){ Liberar(f.primero); f.longitud = 0; }
> 
> // ----- PoolDeRecursos -----
> struct PoolDeRecursos{
>     Nodo* primero{nullptr};
> };
> bool Agregar(PoolDeRecursos& p, Recurso r){
>     AgregarAlFinal(p.primero, r);       // no tiene tope: siempre puede agregar
>     return true;
> }
> bool Sacar(PoolDeRecursos& p, Recurso::Tipo t, Recurso& salida){
>     Nodo* anterior{nullptr};
>     for(Nodo* actual = p.primero; actual; anterior = actual, actual = actual->siguiente){
>         if(actual->valor.tipo == t){
>             salida = actual->valor;
>             if(anterior) anterior->siguiente = actual->siguiente;   // desengancho en el medio/final
>             else         p.primero = actual->siguiente;             // era el primero
>             delete actual;
>             return true;
>         }
>     }
>     return false;
> }
> unsigned GetCantidad(const PoolDeRecursos& p, Recurso::Tipo t){
>     unsigned n{0};
>     for(const Nodo* q = p.primero; q; q = q->siguiente) if(q->valor.tipo == t) ++n;
>     return n;
> }
> void Destruir(PoolDeRecursos& p){ Liberar(p.primero); }
> 
> #include "Interprete.inc"
> ```

> [!example]- comandos.txt y resultados.txt
> ```
> (antes: 4 locomotoras L001-L004, 20 vagones de pasajeros P001-P020 y 7 de carga G001-G007, cargados con "agregar")
> CF pasajero 100
> CF carga 100
> CF pasajero 300
> CF pasajero 21
> CF comedor 10
> CF carga 0
> foo
> mostrar
> ```
> Salida (idéntica en las dos versiones, sin las líneas "pool + ..."):
> ```
> CF pasajero 100 -> F1: L001 P001 P002 P003 P004 P005 (capacidad 105)
>    capacidad pedida 100, armada 105
> CF carga 100 -> F2: L002 G001 G002 G003 G004 G005 G006 (capacidad 102)
>    capacidad pedida 100, armada 102
> CF pasajero 300 -> F3: L003 P006 P007 P008 P009 P010 P011 P012 P013 P014 (capacidad 189)
> CF pasajero 300 -> F4: L004 P015 P016 P017 P018 P019 P020 (capacidad 126)
>    capacidad pedida 300, armada 315
> error: faltan locomotoras (hacen falta 1)
> error: CF necesita pasajero o carga, no comedor
> error: la capacidad debe ser mayor que 0
> error: comando desconocido foo
> F1: L001 P001 P002 P003 P004 P005 (capacidad 105)
> F2: L002 G001 G002 G003 G004 G005 G006 (capacidad 102)
> F3: L003 P006 P007 P008 P009 P010 P011 P012 P013 P014 (capacidad 189)
> F4: L004 P015 P016 P017 P018 P019 P020 (capacidad 126)
> ```

### Cómo se explica la solución

- **Mismo intérprete, dos representaciones.** `Interprete.inc` solo llama a `Crear`, `AddVagón`, `Sacar`, `GetCantidad`, etc. No sabe si hay un `std::array` o nodos. Verificado: las dos versiones dan el mismo resultado con `diff`.
- **`CF` en tres pasos.** Primero la cuenta (`vagones = ceil(N / capacidad del vagón)`, `formaciones = ceil(vagones / 9)`), después se verifica que el pool alcance con `GetCantidad`, y recién entonces se saca y se arma. Por eso un `CF` fallido no deja el pool a medias.
- **Por qué 9 y no 10.** `LongitudMáxima` cuenta la locomotora: queda un lugar menos para vagones.
- **Reparto de vagones.** Las formaciones se llenan hasta 9 vagones y la última lleva lo que sobra (en `CF pasajero 300`: 9 + 6).
- **`Sacar` contiguo vs enlazado.** En el arreglo hay que cerrar el hueco corriendo los de la derecha; en la lista alcanza con redirigir un puntero (`anterior->siguiente`) y hacer `delete` del nodo.
- **Orden de llegada.** `AgregarAlFinal` en la lista y `cantidad++` en el arreglo hacen que `Sacar` devuelva siempre "el primero que llegó" de ese tipo, igual en las dos versiones.
- **`Destruir`.** En la contigua no hace nada; en la enlazada libera todos los nodos. Está en la interfaz de las dos para que el intérprete no cambie.
- Compilado con `g++ -std=c++17 -Wall -Wextra -fsanitize=address,undefined`, sin errores ni fugas.

## 💡 Trampas típicas / posibles preguntas

- **Redondear para abajo:** `100 / 21` en enteros da 4, y 4 vagones transportan 84, menos de lo pedido. Siempre `ceil`.
- **Contar 10 vagones por formación:** uno de los 10 lugares es la locomotora.
- **Sacar recursos antes de verificar:** si el pool no alcanza a mitad de camino, quedan locomotoras o vagones sacados de un pedido que falló.
- **Olvidar el contador `longitud`.** El código de partida solo trae el `std::array`; sin saber cuántos links usa, `GetCapacidad` suma basura de posiciones sin inicializar.
- **Aceptar un 11.º link.** `.at(10)` lanza `out_of_range`; mejor controlarlo antes y informarlo.
- **Pasar una `Formación` enlazada por valor y usar la vieja** después del `AddVagón`: dos objetos comparten nodos y un `delete` doble rompe el programa.
- **Fugas en la enlazada:** reservar un nodo con `new` y no liberarlo al `Sacar` o al terminar.
- **Dejar la lista sin enganchar** al sacar el primer nodo (`p.primero = actual->siguiente`) o uno del medio.
- **Tipos mezclados:** `AddLocomotora` con un vagón, o `AddVagón` con una locomotora. Las operaciones validan el tipo.
- **`const` en `static const unsigned`:** si lo usás como argumento por referencia (por ejemplo `std::min`), puede pedir una definición fuera de la clase; compararlo o usarlo como tamaño de array no lo necesita.
- Pregunta de oral probable: *"¿qué cambió entre las dos versiones y qué no?"* → cambian los dos `struct` y sus operaciones; no cambian la interfaz, el intérprete ni el formato de los comandos.
- Pregunta de oral probable: *"¿qué costo tiene `Agregar` en cada una?"* → contigua O(1); en la enlazada O(n) porque recorre hasta el final. Se podría guardar un puntero al último nodo para que sea O(1).

## 🔁 Cómo se relaciona con el resto de la guía

| Si venís de... | Qué reutilizás |
|---|---|
| [[05 - Geometría]] | Composición de tipos y especificar operaciones antes de implementar |
| [[06 - Estructuras Dinámicas - Geometría II]] (caps. 28–30) | Misma idea: versión con arreglo y versión con nodos con la misma interfaz |
| [[07 - Secuencias, Pilas y Colas]] | Contigua vs enlazada, `Destruir` y reenganchar punteros |
| [[04 - Abstracción con Tipos]] | `enum struct`, `std::array` |
| [[09 - Línea AyED - Viajes de Pasajeros y Cargas]] | La cuenta de cuántas formaciones hacen falta (`ceil`), y datos para dimensionar `comandos.txt` |
