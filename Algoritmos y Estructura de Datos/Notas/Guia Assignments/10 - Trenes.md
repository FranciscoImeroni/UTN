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

## Qué NO dice el enunciado (hipótesis a declarar en el `readme.md`)

| # | Falta definir | Hipótesis que tomé |
|---|---|---|
| H1 | Formato de `comandos.txt` | 4 comandos: `agregar <tipo> <id>`, `formacion <id>`, `vagon <idFormacion> <tipo>`, `mostrar` |
| H2 | De dónde salen los recursos | Del `PoolDeRecursos`: los comandos `agregar` lo llenan y `formacion`/`vagon` **sacan** de ahí |
| H3 | `Crear : Recurso → Formación` | El recurso es la **locomotora** inicial; además se le da un `id` a la formación |
| H4 | `capacidad` del `Recurso` | Carga = 17, Pasajero = 21 (las constantes del código de partida); el resto 0. Es el "después lo vemos" |
| H5 | `GetCapacidad` | Suma de `capacidad` de todos los recursos de la formación |
| H6 | Largo máximo | `LongitudMáxima = 10` cuenta **todos** los links (locomotoras + vagones) |
| H7 | Operaciones `Agregar`, `Sacar` del pool | `bool Agregar(Pool&, Recurso)` y `bool Sacar(Pool&, Tipo, Recurso& salida)`: sacan el **primero** de ese tipo (orden de llegada) |
| H8 | Errores en los comandos | Se informan con una línea `error: ...` y se sigue con el siguiente comando |

> 💡 Si la cátedra pasa un `comandos.txt` con otro formato, sólo cambia el intérprete; los tipos y sus operaciones no se tocan. Por eso conviene mantenerlos separados.

## 📘 Teoría necesaria

### a) Tipos compuestos y composición de tipos
Un `Recurso` agrupa `Tipo` + `id` + `capacidad`. Una `Formación` **contiene** recursos (una secuencia de `Recurso`). Eso es **composición**: un tipo armado con otros. Es el mismo mecanismo que `Triángulo` = 3 `Punto` en [[05 - Geometría]].

### b) `enum struct`
`enum struct Tipo { Locomotora, ... }` es un enumerado **de ámbito cerrado** (igual que `enum class`): se escribe `Recurso::Tipo::Locomotora`, no hay conversión implícita a `int` y los nombres no ensucian el espacio global. Ver [[04 - Abstracción con Tipos]].

### c) Miembros `static const` dentro de un `struct`
`static const unsigned CapacidadCarga{17};` es una constante **de la clase**, no de cada objeto: existe una sola copia y se usa como `Recurso::CapacidadCarga`. No ocupa lugar en cada `Recurso`. `Formación::LongitudMáxima` también se usa como tamaño del `std::array`, que exige una constante de compilación.

### d) Especificar un tipo: operaciones como funciones
`AddVagón : Formación × Recurso → Formación` se lee "recibe una formación y un recurso y devuelve una formación". Es una función **pura** en estilo matemático: en vez de modificar la entrada, devuelve el resultado.

```cpp
Formación AddVagón(Formación f, Recurso r);   // f llega por VALOR (copia) y se devuelve la nueva
f = AddVagón(f, v);                           // se usa reasignando
```

`trenes.md` tiene que listar **esas firmas** y decir en una frase qué hace cada una, antes de implementar (es la tarea "Especificar tipo" que pide el libro en cada trabajo de estructuras).

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

### f) Copiar un tipo con punteros
En la versión contigua, `Formación f2 = f1;` copia todo el arreglo (copia **profunda**, por defecto). En la enlazada copia sólo el **puntero** `primero`: `f1` y `f2` apuntan a los **mismos nodos**. Con las firmas "por valor" del enunciado eso obliga a una regla: después de `f = AddVagón(f, r)` la formación vieja no se vuelve a usar, y los nodos se liberan **una sola vez** (`Destruir`). Ver [[Call Stack y Heap]] (doble `delete`).

### g) Flujos estándar y redirección
`cin >> comando` en un `while` lee hasta fin de archivo; la redirección `< comandos.txt > resultados.txt` no cambia el código. Es el patrón del TP18 (ver la guía `Guia-Examen-Oral.md`, sección de streams y redirección).

### h) Interpretar comandos
Leer una palabra (el comando) y ramificar por `if/else if`; cada rama lee **sus** argumentos con `cin >> ...`. El `while (cin >> comando)` corta solo en EOF.

## 🧭 Cómo encararlo

1. **Declarar las hipótesis H1–H8** en el `readme.md` y escribir `trenes.md`: tipos, firmas y una frase por operación. Sin código todavía.
2. **Tipos primero, sin comandos.** Escribir `Recurso` (ya está) y un `main` de prueba con `assert`s: crear una formación con una locomotora, agregar dos vagones, `assert(GetCapacidad(f) == ...)`.
3. **Agregar el contador `longitud`.** El código de partida no lo trae: sin él no sabés qué posiciones del `std::array` están en uso.
4. **Decidir qué pasa al llenarse** (10 links): la operación no debe aceptar el 11.º. Pensá si lo controla la función (`assert`) o quien la llama (el intérprete, para poder informar un `error`).
5. **`PoolDeRecursos`:** `Agregar` y `Sacar`. Probalos con `assert`s antes de conectar nada más.
6. **Intérprete:** un `while (cin >> comando)` con un `if` por comando. Imprimir una línea por acción.
7. **Armar `comandos.txt`** con casos felices y casos de error: pool sin locomotoras, formación llena, formación inexistente, tipo inválido.
8. **Versión enlazada:** cambiar **sólo** los dos `struct` y sus operaciones. Si tenés que tocar el intérprete, el diseño no separó bien interfaz de implementación.
9. **Verificar:** ejecutar las dos versiones con el mismo `comandos.txt` y comparar con `diff`: tienen que dar exactamente el mismo `resultados.txt`.
10. **Memoria (enlazada):** correr con `-fsanitize=address` para ver que no quedan nodos sin liberar.

## ✅ Solución completa

> Estructura de archivos (el intérprete y `Recurso` se comparten; si la cátedra exige **sólo** los dos `.cpp`, pegá el contenido de `Recurso.h` e `Interprete.inc` dentro de cada uno).
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
>  * 20261014
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
>  * usa sólo la INTERFAZ (Crear, AddLocomotora, AddVagón, GetCapacidad, Agregar, Sacar...).
>  * Se incluye al final de cada .cpp, después de definir Formación y PoolDeRecursos.
>  * Equipo 1
>  * 20261014
>  */
> #include <array>
> #include <iostream>
> 
> int main(){
>     PoolDeRecursos pool{};
>     std::array<Formación, 16> formaciones{};
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
>         else if(comando == "formacion"){                // formacion <idFormacion>
>             std::string id;
>             std::cin >> id;
>             Recurso loc;
>             if(cantFormaciones == formaciones.size())      std::cout << "error: no hay lugar para mas formaciones\n";
>             else if(!Sacar(pool, Recurso::Tipo::Locomotora, loc))
>                                                            std::cout << "error: no hay locomotoras en el pool\n";
>             else{
>                 formaciones.at(cantFormaciones++) = Crear(id, loc);
>                 std::cout << "formacion " << id << " creada con " << loc.id << '\n';
>             }
>         }
>         else if(comando == "vagon"){                    // vagon <idFormacion> <tipo>
>             std::string id, tipoStr;
>             std::cin >> id >> tipoStr;
>             Recurso::Tipo t;
>             Formación* f{nullptr};
>             for(unsigned i = 0; i < cantFormaciones; ++i)
>                 if(formaciones.at(i).id == id) f = &formaciones.at(i);
>             Recurso v;
>             if(!f)                                          std::cout << "error: no existe la formacion " << id << '\n';
>             else if(!LeerTipo(tipoStr, t) || t == Recurso::Tipo::Locomotora)
>                                                             std::cout << "error: " << tipoStr << " no es un vagon\n";
>             else if(GetLongitud(*f) == Formación::LongitudMáxima)
>                                                             std::cout << "error: " << id << " ya tiene la longitud maxima\n";
>             else if(!Sacar(pool, t, v))                     std::cout << "error: no hay " << tipoStr << " en el pool\n";
>             else{
>                 *f = AddVagón(*f, v);
>                 std::cout << id << " + " << v.id << '\n';
>             }
>         }
>         else if(comando == "mostrar"){
>             for(unsigned i = 0; i < cantFormaciones; ++i){
>                 std::cout << Mostrar(formaciones.at(i)) << '\n';
>             }
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
>  * 20261014
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
> void Destruir(PoolDeRecursos&){}
> 
> #include "Interprete.inc"
> ```

> [!example]- treneslink.cpp (enlazada)
> ```cpp
> /* treneslink.cpp
>  * Trenes - versión ENLAZADA: Formación y PoolDeRecursos como listas de nodos.
>  * Equipo 1
>  * 20261014
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
> void Destruir(PoolDeRecursos& p){ Liberar(p.primero); }
> 
> #include "Interprete.inc"
> ```

> [!example]- comandos.txt y resultados.txt
> ```
> agregar locomotora L001
> agregar locomotora L002
> agregar pasajero P001
> agregar pasajero P002
> agregar comedor C001
> agregar carga G001
> agregar carga G002
> formacion F1
> vagon F1 pasajero
> vagon F1 comedor
> vagon F1 pasajero
> vagon F1 cocina
> formacion F2
> vagon F2 carga
> vagon F2 carga
> vagon F2 carga
> formacion F3
> vagon F9 carga
> vagon F1 locomotora
> foo
> mostrar
> ```
> Salida (idéntica en las dos versiones):
> ```
> pool + locomotora L001
> pool + locomotora L002
> pool + pasajero P001
> pool + pasajero P002
> pool + comedor C001
> pool + carga G001
> pool + carga G002
> formacion F1 creada con L001
> F1 + P001
> F1 + C001
> F1 + P002
> error: no hay cocina en el pool
> formacion F2 creada con L002
> F2 + G001
> F2 + G002
> error: no hay carga en el pool
> error: no hay locomotoras en el pool
> error: no existe la formacion F9
> error: locomotora no es un vagon
> error: comando desconocido foo
> F1: L001 P001 C001 P002 (capacidad 42)
> F2: L002 G001 G002 (capacidad 34)
> ```

### Cómo se explica la solución

- **Mismo intérprete, dos representaciones.** `Interprete.inc` sólo llama a `Crear`, `AddVagón`, `Sacar`, etc. No sabe si hay un `std::array` o nodos. Verificado: las dos versiones dan el mismo resultado con `diff`.
- **`Sacar` contiguo vs enlazado.** En el arreglo hay que cerrar el hueco corriendo los de la derecha; en la lista alcanza con redirigir un puntero (`anterior->siguiente`) y hacer `delete` del nodo.
- **Orden de llegada.** `AgregarAlFinal` en la lista y `cantidad++` en el arreglo hacen que `Sacar` devuelva siempre "el primero que llegó" de ese tipo, igual en las dos versiones.
- **`Destruir`.** En la contigua no hace nada; en la enlazada libera todos los nodos. Está en la interfaz de las dos para que el intérprete no cambie.
- Compilado con `g++ -std=c++17 -Wall -Wextra -fsanitize=address,undefined`, sin errores ni fugas.

## 💡 Trampas típicas / posibles preguntas

- **Olvidar el contador `longitud`.** El código de partida sólo trae el `std::array`; sin saber cuántos links usa, `GetCapacidad` suma basura de posiciones sin inicializar.
- **Aceptar un 11.º link.** `.at(10)` lanza `out_of_range`; mejor controlarlo antes y informarlo.
- **Pasar una `Formación` enlazada por valor y usar la vieja** después del `AddVagón`: dos objetos comparten nodos y un `delete` doble rompe el programa.
- **Fugas en la enlazada:** reservar un nodo con `new` y no liberarlo al `Sacar` o al terminar.
- **Dejar la lista sin enganchar** al sacar el primer nodo (`p.primero = actual->siguiente`) o uno del medio.
- **Tipos mezclados:** `AddLocomotora` con un vagón, o `AddVagón` con una locomotora. Las operaciones validan el tipo.
- **`const` en `static const unsigned`:** si lo usás como argumento por referencia (por ejemplo `std::min`), puede pedir una definición fuera de la clase; compararlo o usarlo como tamaño de array no lo necesita.
- Pregunta de oral probable: *"¿qué cambió entre las dos versiones y qué no?"* → cambian los dos `struct` y sus operaciones; no cambian la interfaz, el intérprete ni el formato de los comandos.
- Pregunta de oral probable: *"¿qué costo tiene `Agregar` en cada una?"* → contigua O(1); en mi enlazada O(n) porque recorre hasta el final. Se podría guardar un puntero al último nodo para que sea O(1).

## 🔁 Cómo se relaciona con el resto de la guía

| Si venís de... | Qué reutilizás |
|---|---|
| [[05 - Geometría]] | Composición de tipos y especificar operaciones antes de implementar |
| [[06 - Estructuras Dinámicas - Geometría II]] (caps. 28–30) | Misma idea: versión con arreglo y versión con nodos con la misma interfaz |
| [[07 - Secuencias, Pilas y Colas]] | Contigua vs enlazada, `Destruir` y reenganchar punteros |
| [[04 - Abstracción con Tipos]] | `enum struct`, `std::array` |
| [[09 - Línea AyED - Viajes de Pasajeros y Cargas]] | El análisis de cuántas formaciones hacen falta; sirve para dimensionar `comandos.txt` |
