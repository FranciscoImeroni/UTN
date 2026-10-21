⬅️ [[00 - Índice y Requisitos Generales]]

# 08 — Otros Trabajos e Integrador (Capítulos 39–41)

Cierre del libro. El capítulo 39 es cortito (retoma templates, ya lo tenés bien cubierto en [[Templates, Arreglos Asociativos y Arboles]]), el 40 es un TP completo de simulación de un browser (historial con `back`/`forward`, muy parecido a lo que ya viste en [[Historial de Comandos (Undo, Redo) y Flujo de Lineas]]), y el 41 es el **trabajo integrador final** del libro: combina tipos, composición, estructuras y ordenamiento, todo junto.

---

## 39. Templates

**Objetivos** (textual, es todo lo que trae el capítulo — no tiene secciones de Temas, Problema, Restricciones ni Productos explícitas en el PDF):
- Matriz con cantidad y tipo de elemento parametrizado.
- Secuencia Dinámica Contigua con cantidad y tipo de elemento parametrizado.

**Problema / Necesidad:** el libro da por sentado que ya resolviste `Matrix` (tipos de dato concretos, en capítulos anteriores de Abstracción con Tipos) y `SecuenciaDinámicaContigua` (capítulo 32/33 de Secuencias — ver [[07 - Secuencias, Pilas y Colas]]) con un tipo de elemento **fijo** (por ejemplo, siempre `int` o siempre `double`). La consigna de este capítulo es **generalizar esas dos implementaciones** para que funcionen con **cualquier tipo `T`** (y, en el caso de `Matrix`, con cualquier cantidad de filas/columnas), usando `template`, en vez de tener una copia del código por cada tipo concreto que necesites.

**Restricciones:** el PDF no da restricciones adicionales a las de siempre (mismos flujos, mismo estilo de pruebas con `assert`, mismos requisitos generales del índice). La restricción implícita es de diseño: la interfaz pública (operaciones) de `Matrix<T,...>` y de `SecuenciaDinámicaContigua<T>` debe ser **la misma** que ya usabas en la versión no genérica — solo cambia que ahora `T` (y en `Matrix`, `rows`/`columns`) son parámetros de template en vez de estar hardcodeados.

**Productos:** el libro no lista archivos puntuales para este capítulo; se espera una carpeta con la versión templatizada de ambos tipos (consistente con el formato `DD-NombreTrabajo` del resto del curso).

### 📘 Teoría necesaria

Esto ya está desarrollado en detalle en [[Templates, Arreglos Asociativos y Arboles]] — repaso corto acá, enfocado a **cómo se aplica puntualmente** a este capítulo (Matrix + Secuencia genéricas):

- **Programación genérica** = escribir un tipo o función **una sola vez** que sirve para una familia entera de tipos concretos, en vez de copiar y pegar cambiando el tipo. `template<typename T>` es el mecanismo de C++ para esto: el compilador **instancia** (genera) el código real la primera vez que lo usás con un `T` concreto (`Stack<int>`, `Stack<string>`, etc.) — eso pasa en **tiempo de compilación**, no en ejecución.
- Diferencia con **sobrecarga** (`overload`): sobrecargar es escribir *N* funciones distintas, una por cada tipo que querés soportar (mismo nombre, cuerpo repetido o casi). Con template escribís **una sola** implementación y el compilador genera las *N* versiones por vos. Sobrecarga = repetís código vos; template = repetís código el compilador.
- `template<typename T, size_t rows, size_t columns>` acepta tanto parámetros de **tipo** (`typename T`) como de **valor** (`size_t rows`, conocido en compilación) — ver el ejemplo de `Matrix` en [[Templates, Arreglos Asociativos y Arboles]], que es exactamente el patrón que pide este capítulo.
- Mínimo ejemplo de función genérica: `template<typename T> T Max(const T& a, const T& b){ return a>b?a:b; }`. Sirve para `int`, `double`, `string`, cualquier tipo que tenga `operator>`.
- **Conexión con [[07 - Secuencias, Pilas y Colas]]**: la `SecuenciaDinámicaContigua<T>` de este capítulo es la misma estructura que ya viste ahí (arreglo dinámico con `capacidad`/`cantidad`, redimensionado cuando se llena) pero con el tipo de elemento parametrizado por `T` en vez de fijo. Todo lo que ya sabés de esa estructura (invariantes, cuándo redimensiona, cómo crece) sigue igual; lo único nuevo es el `template<typename T>` alrededor.

### 🧭 Cómo encararlo

1. Tomá tu implementación existente de `Matrix` (de Abstracción con Tipos) y de `SecuenciaDinámicaContigua` (de [[07 - Secuencias, Pilas y Colas]]) con tipo fijo (ej. `int`).
2. Para `Matrix`: envolvela en `template<typename T = int, std::size_t rows = 1, std::size_t columns = rows>` y reemplazá todo `int` interno por `T`. Verificá que siga siendo, por dentro, un `std::array<std::array<T, columns>, rows>` (o el `struct` equivalente que ya tenías).
3. Para `SecuenciaDinámicaContigua`: envolvela en `template<typename T>` (acá no hace falta parametrizar un tamaño porque es *dinámica*, no fija). Cambiá el puntero interno de `int*` a `T*`, y todas las firmas de `Agregar`, `Remover`, `Get`, etc. para que trabajen con `T` en vez de `int`.
4. Recompilá las pruebas con `assert` que ya tenías, pero ahora instanciando el template con **al menos dos tipos distintos** (por ejemplo `SecuenciaDinámicaContigua<int>` y `SecuenciaDinámicaContigua<string>`) para demostrar que de verdad es genérico y no quedó nada hardcodeado.
5. Ojo con **operaciones que asumen algo del tipo** (ej. si en algún lado sumabas o comparabas con `<`, necesitás que `T` soporte esa operación — si vas a instanciar con un tipo que no la tiene, el compilador te va a tirar un error de instanciación, no en la definición del template sino en el punto de uso).

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* templates.h
>  * Matrix y SecuenciaDinamicaContigua genericos (parametrizados por tipo y, en Matrix, por tamano)
>  * Autor
>  * Fecha
>  */
> #pragma once
> #include <array>
> #include <cassert>
> #include <cstddef>
> #include <stdexcept>
>
> // ---------- Matrix<T, rows, columns> ----------
> template <typename T = int, std::size_t rows = 1, std::size_t columns = rows>
> using Matrix = std::array<std::array<T, columns>, rows>;
>
> // ---------- SecuenciaDinamicaContigua<T> ----------
> template <typename T>
> class SecuenciaDinamicaContigua {
> public:
>     SecuenciaDinamicaContigua() : datos(nullptr), cantidad(0), capacidad(0) {}
>
>     ~SecuenciaDinamicaContigua() { delete[] datos; }
>
>     // No copiable "a lo simple" -> deshabilitamos copia para no duplicar el puntero por accidente.
>     SecuenciaDinamicaContigua(const SecuenciaDinamicaContigua&) = delete;
>     SecuenciaDinamicaContigua& operator=(const SecuenciaDinamicaContigua&) = delete;
>
>     std::size_t GetCantidad() const { return cantidad; }
>     std::size_t GetCapacidad() const { return capacidad; }
>     bool EstaVacia() const { return cantidad == 0; }
>
>     void Agregar(const T& elemento) {
>         if (cantidad == capacidad)
>             Redimensionar(capacidad == 0 ? 1 : capacidad * 2);
>         datos[cantidad] = elemento;
>         ++cantidad;
>     }
>
>     void Remover() {
>         assert(!EstaVacia());
>         --cantidad;
>     }
>
>     T& Get(std::size_t indice) {
>         if (indice >= cantidad) throw std::out_of_range("indice fuera de rango");
>         return datos[indice];
>     }
>
>     const T& Get(std::size_t indice) const {
>         if (indice >= cantidad) throw std::out_of_range("indice fuera de rango");
>         return datos[indice];
>     }
>
> private:
>     T* datos;
>     std::size_t cantidad;
>     std::size_t capacidad;
>
>     void Redimensionar(std::size_t nuevaCapacidad) {
>         T* nuevo = new T[nuevaCapacidad];
>         for (std::size_t i = 0; i < cantidad; ++i)
>             nuevo[i] = datos[i];
>         delete[] datos;
>         datos = nuevo;
>         capacidad = nuevaCapacidad;
>     }
> };
>
> int main() {
>     // Pruebas de Matrix
>     Matrix<int, 2, 3> m{};
>     m.at(0).at(0) = 5;
>     assert(m.at(0).at(0) == 5);
>     assert(m.size() == 2 && m.at(0).size() == 3);
>
>     // Pruebas de SecuenciaDinamicaContigua<int>
>     SecuenciaDinamicaContigua<int> si;
>     assert(si.EstaVacia());
>     si.Agregar(10);
>     si.Agregar(20);
>     assert(si.GetCantidad() == 2);
>     assert(si.Get(0) == 10 && si.Get(1) == 20);
>
>     // Pruebas de SecuenciaDinamicaContigua<std::string> -> demuestra que es genérica de verdad
>     SecuenciaDinamicaContigua<std::string> ss;
>     ss.Agregar("hola");
>     ss.Agregar("mundo");
>     assert(ss.GetCantidad() == 2);
>     assert(ss.Get(0) == "hola");
>
>     return 0;
> }
> ```
> **Notas:**
> - `Matrix` sigue siendo un simple `using` (alias de template), no una clase — no necesita comportamiento propio más allá de lo que ya da `std::array` anidado.
> - `SecuenciaDinamicaContigua<T>` sí es una clase, porque necesita manejar memoria propia (`new[]`/`delete[]`) y eso requiere un destructor — algo que un `using` no puede tener.
> - Se deshabilitó la copia (`= delete`) para evitar el bug clásico de "doble `delete`" si copiás por valor sin querer; si tu enunciado puntual pide que sea copiable, hay que implementar constructor de copia y `operator=` haciendo *deep copy* del arreglo interno (regla de los tres/cinco).

### 💡 Trampas típicas / posibles preguntas

> 💡 El capítulo no da un enunciado extenso — en el parcial/oral lo típico es pedirte que "generalices" una función o tipo concreto ya armado. La pregunta clave siempre es: **¿qué se está generalizando?** (¿el tipo del dato? ¿un tamaño que antes era fijo?).

- ¿Por qué `SecuenciaDinamicaContigua` es una clase con destructor y `Matrix` es solo un `using`? → Porque la primera maneja memoria dinámica propia (`new`/`delete`); la segunda es puramente un alias sobre `std::array`, que ya administra su propia memoria en la pila.
- ¿Qué pasa si instanciás `SecuenciaDinamicaContigua<T>` con un `T` que no tiene constructor por copia accesible? → Falla en el punto de instanciación (al compilar el `Agregar` o el `Redimensionar` para ese `T` en particular), no en la definición del template.
- ¿Por qué en `Matrix<T, rows, columns>` el default de `columns` es `rows` y no un número fijo? → Para que, si no especificás `columns`, la matriz salga **cuadrada** por default (ver trampa igual en [[Templates, Arreglos Asociativos y Arboles]]).
- Diferencia entre esto y sobrecargar `SecuenciaDinamicaContiguaInt`, `SecuenciaDinamicaContiguaString`, etc. a mano → con sobrecarga manual tenés que mantener *N* copias sincronizadas del mismo código; con template hay **una sola fuente de verdad**.

---

## 40. Historial del Browser

**Objetivos:** implementar el comportamiento de navegación `back`/`forward` que tienen todos los navegadores web, a partir de un flujo de comandos por entrada estándar.

**Necesidad:** implementar la funcionalidad *back* y *forward* común a todos los browsers.

**Restricciones sobre la Interacción:**
- Procesamiento línea a línea.
- Una línea puede contener `B` para *back*, `F` para *forward*; el resto de las líneas se consideran *URL* destino correctas (no hay que validar formato de URL en la versión base).
- Por cada línea leída, se debe enviar una línea a la salida estándar: si es una URL, se envía esa URL; si es `B`, se envía la URL anterior; si es `F`, se envía la URL siguiente.
- El procesamiento finaliza cuando no hay más líneas.

**Tabla 40.1. Ejemplo de interacción** (tal cual el libro):

| Secuencia | Entrada | Salida |
|---|---|---|
| 1 | alfa | alfa |
| 2 | beta | beta |
| 3 | gamma | gamma |
| 4 | delta | delta |
| 5 | B | gamma |
| 6 | F | delta |
| 7 | B | gamma |
| 8 | epsilon | epsilon |
| 9 | B | gamma |
| 10 | F | epsilon |

**Cómo se lee esta traza (la Figura 40.1 "Líneas de tiempo BTTF2" del libro dibuja exactamente esto, con flechas de ida y vuelta sobre la secuencia `alfa → beta → gamma → delta`):**
1. Se visitan `alfa`, `beta`, `gamma`, `delta` en orden (pasos 1–4): cada URL nueva se muestra a sí misma y queda como "actual".
2. Paso 5, `B` (back): vuelve de `delta` a `gamma` → imprime `gamma`.
3. Paso 6, `F` (forward): avanza de `gamma` a `delta` de nuevo (todavía existía adelante porque no se visitó nada nuevo desde el back) → imprime `delta`.
4. Paso 7, `B`: vuelve otra vez de `delta` a `gamma` → imprime `gamma`.
5. Paso 8, `epsilon`: es una URL **nueva**, visitada estando parado en `gamma`. Esto es el punto clave de la figura: al visitar una URL nueva desde un punto intermedio del historial, la rama `delta` que quedaba "adelante" se **descarta** — típico patrón *Back to the Future 2* de líneas de tiempo que se bifurcan y una desaparece. El nuevo historial queda `alfa, beta, gamma, epsilon`.
6. Paso 9, `B`: vuelve de `epsilon` a `gamma` → imprime `gamma`.
7. Paso 10, `F`: avanza de `gamma` a `epsilon` → imprime `epsilon`. Notar que ya **no existe** `delta` como destino de forward — quedó eliminado en el paso 8.

**Restricciones de solución:**
- Obtención de líneas:
  - En C++: `string linea; while(getline(cin, linea)) ...` — obtiene una línea de `cin` y la guarda en `linea`.
  - En C: `#define MAX_LINE_LENGTH 1000` y `char line[MAX_LINE_LENGTH+1+1]; while(fgets(line, sizeof line, stdin)) ...`.
- Diseñar las siguientes funciones:
  - `GetLínea()` — retorna una línea de la entrada estándar.
  - `GetTipo(línea)` — retorna un código para los diferentes tipos de líneas.
  - `AccionarSegún(GetTipo(línea))` — realiza la acción correspondiente.
  - `Mostrar(unaUrl)` — envía `unaUrl` a la salida estándar.
  - `Back()` — vuelve una URL atrás y la muestra.
  - `Forward()` — avanza a la URL siguiente y la muestra.
  - `GuardarUrl()` — realiza lo necesario para guardar una URL.
  - `GetPrevUrl()` — obtiene la anterior URL.
  - `GetNextUrl()` — obtiene la siguiente URL.

**Mejoras** (ejercicios opcionales y avanzados que completan la funcionalidad):

*Nuevos comandos para el manejo del historial:*
- `Refresh`: envía por la salida estándar la URL actual.
- `PrintHistory`: envía por la salida estándar todas las URL visitadas en orden, primero la primera visitada y último la última.
- `ClearHistory`: borra el historial.
- `PrintThisTimeLine`: envía por la salida estándar una representación textual en *dot* [DOT] de la línea temporal actual.
- `PrintAllTimeLines`: lo mismo que `PrintThisTimeLine` pero para todas las líneas de tiempo en forma de árbol, en vez de secuencia, cuya raíz es la primera URL visitada.
- Agregar al historial la fecha y hora de cada visita (`<chrono>` en C++, `<time.h>` en C).
- Al finalizar el procesamiento, generar los archivos `History.txt`, `ThisTimeLine.gv`, `AllTimeLines.gv`.

*Mejoras al intérprete de comandos:*
- Requerir que los comandos comiencen con `.` (punto).
- Agregar a los comandos `PrintX` una opción `-f` para indicar que la salida se envía a un archivo, y no a la salida estándar (default `History.txt`, `ThisTimeLine.gv`, `AllTimeLines.gv` respectivamente).
- Agregar a la opción `-f` de los comandos `PrintX` un argumento para indicar el nombre del archivo destino, para poder personalizar los archivos destino.
- Agregar validación de las líneas, para que el programa pueda emitir mensajes del tipo `Comando inválido.`, `Opción inválida.`, `Argumento inválido.`, y `URL inválida.`. La función que implementa la validación es `GetComandoOUrl(línea)`, que retorna un valor de la enumeración `{NoHayMásLíneas, Back, Forward, Url, Refresh, ClearHistory, PrintThisTimeLine, PrintAllTimeLines, UrlInválida, ComandoInválido}`. Se puede implementar de tres formas: con las tres estructuras de control de flujo de ejecución, con un autómata finito con tantos estados finales como situaciones posibles, o con expresiones regulares (`regex` en C++, `lex` en C).
- Agregar *alias* a los comandos y hacer el intérprete *case-insensitive*:

| Comando | Alias |
|---|---|
| `Back` | `B` |
| `Forward` | `F` |
| `Refresh` | `R` |
| `PrintHistory` | `PH` |
| `ClearHistory` | `CH` |
| `PrintThisTimeLine` | `PTL` |
| `PrintAllTimeLines` | `PATL` |

**Productos:**
- `BrowserSimple/browse.cpp`
- `BrowserMásComandos/browse.cpp`
- `BrowserMejorIntérprete/browse.cpp`
- `BrowserValidadorEstructurado/browse.cpp`
- `BrowserValidadorAutómata/browse.cpp`
- `BrowserValidadorRegex/browse.cpp`

### 📘 Teoría necesaria

Este capítulo es, en esencia, el mismo **patrón de dos pilas** que ya viste en detalle en [[Historial de Comandos (Undo, Redo) y Flujo de Lineas]] (undo/redo del CLI) — te conviene releer esa nota antes de seguir, acá solo va el mapeo puntual:

| CLI (undo/redo) | Browser (back/forward) |
|---|---|
| `Undo Stack` (comandos hechos) | pila de URLs **anteriores** |
| `Redo Stack` (comandos deshechos) | pila de URLs **siguientes** |
| `undo` → pasa el tope del Undo Stack al Redo Stack | `Back()` → pasa la URL actual a la pila de "siguientes" y trae el tope de "anteriores" |
| `redo` → pasa el tope del Redo Stack al Undo Stack | `Forward()` → pasa la URL actual a la pila de "anteriores" y trae el tope de "siguientes" |
| comando nuevo vacía el Redo Stack | visitar una URL nueva vacía la pila de "siguientes" |

Es la **misma regla que causa la bifurcación en la Tabla 40.1**: cuando en el paso 8 se visita `epsilon` estando en `gamma`, la URL `delta` (que estaba "adelante", disponible para forward) se pierde porque la pila de "siguientes" se vacía al llegar una URL nueva. Con la implementación de dos pilas, esto sale solo: simplemente no copiás nada a la pila de "siguientes" al visitar una URL nueva, y si tenía algo adentro de una navegación previa, se descarta (`Clear()` o simplemente perder la referencia, según cómo la implementes).

- **`std::getline` línea por línea**: igual patrón que en
  [[Historial de Comandos (Undo, Redo) y Flujo de Lineas]] (`for(std::string s; std::getline(cin, s);)`), acá aplicado a `cin`/`cout` en vez de archivo, pero es exactamente el mismo mecanismo.
- **Centinela de pila vacía**: si estás en la primera URL visitada y llega un `B`, o no hay ninguna URL "adelante" y llega un `F`, hay que chequear antes de hacer `Pop` (ver la sección de "Centinela de pila vacía" en la nota de Undo/Redo). El enunciado no dice explícitamente qué hacer en ese caso (no aparece en la tabla de ejemplo) — una decisión razonable es no hacer nada y volver a mostrar la URL actual, documentándolo en tu `readme.md`.
- **`GetTipo(línea)` / `AccionarSegún`**: es el patrón *dispatch* — separar "identificar qué es la línea" de "actuar en consecuencia" en dos funciones distintas, en vez de un único bloque de ifs gigante. Esto es diseño de módulos (cohesión): cada función hace una sola cosa.

### 🧭 Cómo encararlo

1. Empezá por `BrowserSimple`: dos pilas de `string` (`atras`, `adelante`) más una variable `actual` (la URL mostrada en este momento). No hace falta ninguna otra estructura para cumplir la Tabla 40.1.
2. `GetTipo(línea)`: función que mira si la línea es exactamente `"B"`, exactamente `"F"`, o cualquier otra cosa (URL). Devolvé un enum simple: `{Back, Forward, Url}`.
3. Al visitar una URL nueva (`GuardarUrl`): si ya había una `actual`, metela en `atras`; **vaciá** `adelante`; seteá `actual` a la nueva URL; mostrala.
4. `Back()`: si `atras` está vacía, no hagas nada (o mostrá `actual` de nuevo, según tu criterio); si no, metés `actual` en `adelante`, sacás el tope de `atras` y lo ponés como `actual`, lo mostrás.
5. `Forward()`: simétrico, con `adelante` y `atras` invertidos.
6. Armá el bucle principal: `while(getline(cin, línea)) AccionarSegún(GetTipo(línea));` — cada rama del `AccionarSegún` llama a `Back()`, `Forward()`, o al flujo de "visitar URL nueva".
7. Probá primero a mano con la Tabla 40.1 completa (los 10 pasos) antes de tocar nada de las Mejoras.
8. Recién después, si tu cátedra las pide, sumá los comandos nuevos (`Refresh`, `PrintHistory`, `ClearHistory`) como funciones extra que leen/muestran el estado de las pilas sin modificarlo (salvo `ClearHistory`).
9. Para `PrintHistory` en particular: como las URLs "anteriores" están en una pila (tope = más reciente), para imprimirlas "primero la primera visitada" tenés que recorrerla **de abajo hacia arriba** — si usás `std::stack` no podés iterarla directo; conviene usar `std::vector<string>` (ver [[Vectores - std vector]]) como pila (con `push_back`/`pop_back`) para poder recorrerla en cualquier orden cuando haga falta imprimir.

### ✅ Solución completa

> [!example]- Solución completa — BrowserSimple (click para expandir)
> ```cpp
> /* browse.cpp
>  * Simula back/forward de un browser a partir de un flujo de comandos por linea
>  * Autor
>  * Fecha
>  */
> #include <iostream>
> #include <string>
> #include <vector>
>
> enum class TipoLinea { Back, Forward, Url };
>
> TipoLinea GetTipo(const std::string& linea) {
>     if (linea == "B") return TipoLinea::Back;
>     if (linea == "F") return TipoLinea::Forward;
>     return TipoLinea::Url;
> }
>
> class Browser {
> public:
>     void GuardarUrl(const std::string& url) {
>         if (hayActual)
>             atras.push_back(actual);
>         adelante.clear();   // una URL nueva invalida el historial "adelante"
>         actual = url;
>         hayActual = true;
>         Mostrar(actual);
>     }
>
>     void Back() {
>         if (atras.empty()) { if (hayActual) Mostrar(actual); return; }
>         adelante.push_back(actual);
>         actual = atras.back();
>         atras.pop_back();
>         Mostrar(actual);
>     }
>
>     void Forward() {
>         if (adelante.empty()) { if (hayActual) Mostrar(actual); return; }
>         atras.push_back(actual);
>         actual = adelante.back();
>         adelante.pop_back();
>         Mostrar(actual);
>     }
>
>     void AccionarSegun(const std::string& linea) {
>         switch (GetTipo(linea)) {
>             case TipoLinea::Back:    Back();    break;
>             case TipoLinea::Forward: Forward(); break;
>             case TipoLinea::Url:     GuardarUrl(linea); break;
>         }
>     }
>
> private:
>     void Mostrar(const std::string& url) { std::cout << url << '\n'; }
>
>     std::vector<std::string> atras;
>     std::vector<std::string> adelante;
>     std::string actual;
>     bool hayActual = false;
> };
>
> int main() {
>     Browser browser;
>     for (std::string linea; std::getline(std::cin, linea); )
>         browser.AccionarSegun(linea);
>     return 0;
> }
> ```
> **Verificación contra la Tabla 40.1:** con entrada `alfa, beta, gamma, delta, B, F, B, epsilon, B, F` da exactamente `alfa, beta, gamma, delta, gamma, delta, gamma, epsilon, gamma, epsilon` — coincide línea por línea con la columna Salida de la tabla del libro.
>
> **Explicación de partes no obvias:**
> - `adelante.clear()` en `GuardarUrl` es la línea que implementa la "bifurcación BTTF2": es la que hace que `delta` desaparezca para siempre en el paso 8 del ejemplo.
> - Se usa `std::vector<string>` en vez de `std::stack<string>` a propósito, para poder iterarlo después si se implementan las Mejoras (`PrintHistory`, etc.) sin cambiar la estructura de datos.
> - El centinela `if (atras.empty())` evita hacer `pop_back()` sobre un vector vacío (comportamiento indefinido) — el criterio elegido acá es simplemente re-mostrar la URL actual, documentalo en tu `readme.md` porque el libro no lo especifica para este caso.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué al visitar una URL nueva hay que vaciar la pila "adelante" y no solo dejarla como está? → Porque si no, `Forward` te llevaría a una URL de una rama de navegación que ya no es válida (la Figura 40.1 es exactamente la ilustración de esta regla).
- ¿Qué pasa si hacés `Back` con la pila "atrás" vacía? El libro no lo dice en la tabla de ejemplo — es una decisión de diseño tuya, pero **tenés que manejarla explícitamente** (centinela), nunca dejar que el programa explote con un `pop` sobre vacío.
- Diferencia entre `GetTipo(línea)` (versión simple: 3 casos) y `GetComandoOUrl(línea)` (versión con Mejoras: enumeración completa con validación) → la segunda agrega detección de errores (`UrlInválida`, `ComandoInválido`) y de comandos nuevos; son la misma idea de "clasificar antes de actuar" llevada un paso más allá.
- ¿Por qué conviene `std::vector` y no `std::stack` acá? → `std::stack` no permite iterar (solo ve el tope); en cuanto necesitás `PrintHistory` (recorrer todo el historial en orden) necesitás poder iterar, cosa que `std::vector` sí permite manteniendo la misma disciplina LIFO con `push_back`/`pop_back`.
- Relación 1 a 1 con undo/redo: si ya resolviste el TP de [[Historial de Comandos (Undo, Redo) y Flujo de Lineas]], este es literalmente el mismo problema con otro nombre — `Back`≈`undo`, `Forward`≈`redo`, "URL nueva"≈"comando nuevo que vacía el redo stack".

---

## 41. Ordenamiento de Polígonos

**Objetivos:**
- Diseñar e implementar tipos.
- Aplicar composición de tipos.
- Demostrar capacidad de simular estructuras dinámicas.

**Temas:**
- Tipos.
- Estructuras.
- Flujos.
- Ordenamientos.

**Problema:** dado un flujo de polígonos, emitirlo primero ordenado por cantidad de lados y después por perímetro (**dos salidas separadas**, cada una con un criterio de orden distinto — así lo deja claro el `main` del Código de Partida: `OrdenarPorCantidadDeLados` → `EscribirPolígonos` → `OrdenarPorPerimetro` → `EscribirPolígonos`).

**Requisitos:**
- Textualmente, los polígonos se representan como una secuencia de puntos; la secuencia de puntos termina repitiendo el primer punto de la secuencia, cerrando el polígono. Ejemplo (dos polígonos, un triángulo y un cuadrado):
  ```
  1 1  2 2  3 1  1 1
  4 4  4 5  5 5  5 4  4 4
  ```
  (cada par de números es un punto `x y`; el último punto de cada polígono repite al primero para cerrarlo — el triángulo tiene 3 lados con 4 puntos listados, el cuadrado 4 lados con 5 puntos listados).
- La secuencia de polígonos tiene longitud máxima mil (hasta 1000 polígonos), dado por la variable `MaxPolígonos`.
- La secuencia de puntos de los polígonos tiene longitud máxima cien, dado por la variable `MaxPuntos`.
- Los polígonos tienen un color por defecto, a elegir por los desarrolladores.

**Restricciones:**
- Los flujos a usar son los estándar: `cin` y `cout`.
- El programa ejecutable debe llamarse `pol`.
- Se debe poder ejecutar desde la línea de comando así: `pol < desordenados.txt > ordenados.txt`.
- Tipos con sus operaciones a implementar:
  - **Punto**: `GetDistancia : Punto x Punto → R`
  - **Polígono**: `GetPerímetro : Polígono → R`, `GetCantidadDeLados : Polígono → N`, `AddPunto : Polígono x Punto → Polígono`, `CrearPolígono : Flujo → Flujo x Polígono`
  - **SecuenciaPuntos**: `AddPunto : SecuenciaPuntos x Punto → Polígono`, `GetCantidad : SecuenciaPuntos → N`
  - **SecuenciaPolígonos**: `OrdenarPorCantidadDeLados : SecuenciaPolígonos → SecuenciaPolígonos`, `OrdenarPorPerímetro : SecuenciaPolígonos → SecuenciaPolígonos`

**Código de Partida** (transcripto tal cual del PDF — los bloques marcados `█` son placeholders que el libro deja para que el estudiante complete el cuerpo del `struct`/función):

```cpp
#include<array>
#include<iostream>
#include<cassert>
#include<cmath> //std::sqrt

struct Punto{ double x, y; };
double GetDistancia(Punto, Punto);

struct Color{ /* completar */ };

constexpr unsigned CANTIDAD_MAXIMA_DE_PUNTOS{100};

struct SecuenciaPuntos{ /* completar */ };

struct Polígono{ /* completar */ };

Polígono GetNewPolígono(std::ifstream&);

constexpr unsigned CANTIDAD_MAXIMA_DE_POLÍGONOS{1'000};

struct SecuenciaPolígonos{ /* completar */ };

int main()
{
    { // Pruebas
        {// Pruebas de Punto /* completar */}
        {// Pruebas de Polígono /* completar */}
    }

    { // Procesar
        // LeerPolígonos
        // OrdenarPorCantidadDeLados
        // EscribirPolígonos
        // OrdenarPorPerimetro
        // EscribirPolígonos
    }
}
```

**Créditos Extra:**
- `GetPunto`
- `RemovePunto`
- `SecuenciaPuntosEnlazada`

**Tareas:**
- Escribir en `main` las pruebas con `assert` de las operaciones de *Punto* y *Polígono*.
- Declarar los tipos y prototipos de las funciones que implementan las operaciones.
- Compilar, pero no linkear.
- Desarrollar las funciones que implementan las operaciones.
- Compilar y ejecutar hasta que no haya errores.
- Escribir `desordenados.txt`.
- Escribir `ordenados_esperado.txt` que tiene el listado original pero ordenado de dos maneras diferente, tal cual lo esperado.
- Agregar a `main` el procesamiento.
- Implementar las dos funciones de ordenamiento.
- Compilar y ejecutar hasta que no haya errores.

**Productos:**
```
DD-Polígonos
  |-- readme.md
  `-- pol.cpp
```

### 📘 Teoría necesaria

Este es el trabajo **integrador**: no trae teoría nueva propia, sino que combina lo que ya viste en varios capítulos anteriores del mismo libro. Repaso de qué aporta cada bloque:

- **Punto y Polígono** (geometría — capítulos 22 a 31, ver [[05 - Geometría]] y
  [[06 - Estructuras Dinámicas - Geometría II]]): un `Punto` es un par `(x, y)`; `GetDistancia(p1, p2)` es la distancia euclídea, `sqrt((x2-x1)² + (y2-y1)²)` — por eso el Código de Partida incluye `<cmath>` con el comentario `//std::sqrt`. Un `Polígono` es una secuencia cerrada de puntos; su perímetro es la suma de las distancias entre puntos consecutivos (incluyendo el segmento que cierra del último punto al primero — aunque acá, como la entrada ya repite el primer punto al final, el perímetro sale de sumar las distancias consecutivas de la secuencia tal cual viene).
- **SecuenciaPuntos / SecuenciaPolígonos** (secuencias con capacidad máxima fija — capítulos 32-38, ver [[07 - Secuencias, Pilas y Colas]]): son *secuencias dinámicas contiguas* con un tope máximo (`CANTIDAD_MAXIMA_DE_PUNTOS = 100`, `CANTIDAD_MAXIMA_DE_POLÍGONOS = 1'000`) — internamente un `std::array<T, MAX>` más un contador de cuántos elementos están realmente usados (no todo el array está lleno necesariamente). Esto es literalmente lo mismo que ya armaste para `Stack`/`Queue` en ese capítulo, con otro tipo de elemento.
- **Composición de tipos**: `Polígono` se compone de `SecuenciaPuntos`, que a su vez se compone de `Punto`. `SecuenciaPolígonos` se compone de `Polígono`. Es la misma idea de "producto cartesiano anidado" que ya viste con `struct` en Abstracción con Tipos — acá aplicada a tipos propios en vez de primitivos.
- **Ordenamiento con criterio custom**: "ordenar" siempre significa reacomodar una secuencia según algún criterio de comparación. Acá el criterio no es un número simple sino una propiedad **derivada** de un tipo propio (`GetCantidadDeLados(polígono)` o `GetPerímetro(polígono)`). Dos formas de implementarlo:
  - Con `std::sort(inicio, fin, comparador)` — el comparador es una lambda `[](const Polígono& a, const Polígono& b){ return GetCantidadDeLados(a) < GetCantidadDeLados(b); }`. Es lo recomendado si tu cátedra no exige un algoritmo hecho a mano: `std::sort` es `O(n log n)`, estable en la práctica (aunque no garantizado — para estabilidad garantizada existe `std::stable_sort`) y ya está probado.
  - A mano (burbuja/inserción/selección) si el enunciado puntual de tu curso lo pide explícitamente — el libro **no** lo exige en este capítulo (no aparece ninguna restricción sobre el algoritmo a usar), así que `std::sort` con comparador es una implementación válida y más prolija.
- **Flujos**: `CrearPolígono : Flujo → Flujo x Polígono` es la firma funcional de "leer un polígono de un stream y devolver tanto el stream avanzado como el polígono leído" — en C++ esto se traduce naturalmente a una función que recibe `std::istream&` y devuelve `Polígono` (el stream "avanza" por referencia, no hace falta devolverlo explícitamente como en la notación matemática).

### 🧭 Cómo encararlo

1. Arrancá exactamente como dice el Código de Partida: completá primero `struct Punto` (ya viene dado) y el prototipo `GetDistancia`. Escribí en `main`, dentro de `{// Pruebas de Punto}`, un par de `assert` con casos conocidos (ej. `GetDistancia({0,0},{3,4})` debe dar `5`).
2. Definí `SecuenciaPuntos` como un `struct` con `std::array<Punto, CANTIDAD_MAXIMA_DE_PUNTOS> puntos` + `unsigned cantidad`. Implementá `AddPunto` y `GetCantidad`.
3. Definí `Polígono` como un `struct` con una `SecuenciaPuntos` adentro + un `Color` (con el default que elijas). Implementá `GetCantidadDeLados` (cuidado: cantidad de lados = cantidad de puntos **sin contar la repetición del primero al final** — un triángulo tiene 4 puntos en la entrada pero 3 lados), `GetPerímetro` (sumar `GetDistancia` entre puntos consecutivos de la secuencia), y `AddPunto`.
4. Escribí las pruebas de `Polígono` con `assert` antes de seguir (paso obligatorio según las Tareas del libro: "Compilar, pero no linkear" primero con solo los prototipos, después completar cuerpos).
5. Implementá `GetNewPolígono` / `CrearPolígono`: lee pares `x y` de un `std::istream&` hasta que el punto leído sea igual al primero de ese polígono (ahí cierra); devolvé el `Polígono` armado.
6. `SecuenciaPolígonos`: mismo patrón que `SecuenciaPuntos` pero de `Polígono`, con `CANTIDAD_MAXIMA_DE_POLÍGONOS`. Implementá las dos funciones de ordenamiento usando `std::sort` con comparador (o el algoritmo que pida tu cátedra).
7. Armá `desordenados.txt` a mano (varios polígonos de distinta cantidad de lados y perímetro, deliberadamente fuera de orden) y `ordenados_esperado.txt` con el resultado esperado de las dos pasadas de ordenamiento, para poder comparar la salida real contra lo esperado (`diff`).
8. Recién al final completá el `main` de "Procesar": leer todos los polígonos del flujo de entrada (`LeerPolígonos`, un loop de `CrearPolígono` hasta EOF), ordenar por cantidad de lados, escribir, ordenar por perímetro, escribir de nuevo.
9. Probá con `./pol < desordenados.txt > ordenados.txt` y compará contra `ordenados_esperado.txt`.

### ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* pol.cpp
>  * Lee un flujo de poligonos, los emite ordenados por cantidad de lados y luego por perimetro
>  * Autor
>  * Fecha
>  */
> #include <array>
> #include <iostream>
> #include <fstream>
> #include <cassert>
> #include <cmath>      // std::sqrt
> #include <algorithm>  // std::sort
>
> struct Punto { double x, y; };
>
> double GetDistancia(Punto a, Punto b) {
>     double dx = b.x - a.x;
>     double dy = b.y - a.y;
>     return std::sqrt(dx * dx + dy * dy);
> }
>
> bool operator==(const Punto& a, const Punto& b) { return a.x == b.x && a.y == b.y; }
>
> struct Color { unsigned char r = 0, g = 0, b = 0; };   // default: negro
>
> constexpr unsigned CANTIDAD_MAXIMA_DE_PUNTOS{100};
>
> struct SecuenciaPuntos {
>     std::array<Punto, CANTIDAD_MAXIMA_DE_PUNTOS> puntos{};
>     unsigned cantidad{0};
> };
>
> void AddPunto(SecuenciaPuntos& s, Punto p) {
>     assert(s.cantidad < CANTIDAD_MAXIMA_DE_PUNTOS);
>     s.puntos.at(s.cantidad) = p;
>     ++s.cantidad;
> }
>
> unsigned GetCantidad(const SecuenciaPuntos& s) { return s.cantidad; }
>
> struct Polígono {
>     SecuenciaPuntos puntos;
>     Color color{};   // color por defecto
> };
>
> void AddPunto(Polígono& poligono, Punto p) { AddPunto(poligono.puntos, p); }
>
> // La entrada repite el primer punto al final para cerrar el poligono:
> // cantidad de lados = cantidad de puntos listados - 1.
> unsigned GetCantidadDeLados(const Polígono& poligono) {
>     assert(GetCantidad(poligono.puntos) >= 2);
>     return GetCantidad(poligono.puntos) - 1;
> }
>
> double GetPerímetro(const Polígono& poligono) {
>     double perimetro = 0.0;
>     unsigned n = GetCantidad(poligono.puntos);
>     for (unsigned i = 0; i + 1 < n; ++i)
>         perimetro += GetDistancia(poligono.puntos.puntos.at(i), poligono.puntos.puntos.at(i + 1));
>     return perimetro;
> }
>
> // Lee un poligono de "in": pares "x y" hasta repetir el primer punto (que cierra el poligono).
> Polígono GetNewPolígono(std::istream& in) {
>     Polígono poligono;
>     Punto primero{}, actual{};
>     bool esElPrimero = true;
>
>     while (in >> actual.x >> actual.y) {
>         AddPunto(poligono, actual);
>         if (esElPrimero) { primero = actual; esElPrimero = false; }
>         else if (actual == primero) break;   // se cerro el poligono
>     }
>     return poligono;
> }
>
> constexpr unsigned CANTIDAD_MAXIMA_DE_POLÍGONOS{1'000};
>
> struct SecuenciaPolígonos {
>     std::array<Polígono, CANTIDAD_MAXIMA_DE_POLÍGONOS> poligonos{};
>     unsigned cantidad{0};
> };
>
> void AddPolígono(SecuenciaPolígonos& s, const Polígono& p) {
>     assert(s.cantidad < CANTIDAD_MAXIMA_DE_POLÍGONOS);
>     s.poligonos.at(s.cantidad) = p;
>     ++s.cantidad;
> }
>
> void OrdenarPorCantidadDeLados(SecuenciaPolígonos& s) {
>     std::sort(s.poligonos.begin(), s.poligonos.begin() + s.cantidad,
>               [](const Polígono& a, const Polígono& b) {
>                   return GetCantidadDeLados(a) < GetCantidadDeLados(b);
>               });
> }
>
> void OrdenarPorPerímetro(SecuenciaPolígonos& s) {
>     std::sort(s.poligonos.begin(), s.poligonos.begin() + s.cantidad,
>               [](const Polígono& a, const Polígono& b) {
>                   return GetPerímetro(a) < GetPerímetro(b);
>               });
> }
>
> void EscribirPolígono(std::ostream& out, const Polígono& poligono) {
>     for (unsigned i = 0; i < GetCantidad(poligono.puntos); ++i) {
>         const Punto& p = poligono.puntos.puntos.at(i);
>         out << p.x << ' ' << p.y << "  ";
>     }
>     out << '\n';
> }
>
> void EscribirPolígonos(std::ostream& out, const SecuenciaPolígonos& s) {
>     for (unsigned i = 0; i < s.cantidad; ++i)
>         EscribirPolígono(out, s.poligonos.at(i));
> }
>
> SecuenciaPolígonos LeerPolígonos(std::istream& in) {
>     SecuenciaPolígonos s;
>     while (in.peek() != EOF && in.good()) {
>         Polígono p = GetNewPolígono(in);
>         if (GetCantidad(p.puntos) < 2) break;   // no habia mas poligonos validos
>         AddPolígono(s, p);
>     }
>     return s;
> }
>
> int main() {
>     { // Pruebas
>         { // Pruebas de Punto
>             assert(GetDistancia({0, 0}, {3, 4}) == 5.0);
>             assert(GetDistancia({1, 1}, {1, 1}) == 0.0);
>         }
>         { // Pruebas de Polígono
>             Polígono triangulo;
>             AddPunto(triangulo, {1, 1});
>             AddPunto(triangulo, {2, 2});
>             AddPunto(triangulo, {3, 1});
>             AddPunto(triangulo, {1, 1});   // cierra
>             assert(GetCantidadDeLados(triangulo) == 3);
>             assert(GetPerímetro(triangulo) > 0);
>
>             Polígono cuadrado;
>             AddPunto(cuadrado, {4, 4});
>             AddPunto(cuadrado, {4, 5});
>             AddPunto(cuadrado, {5, 5});
>             AddPunto(cuadrado, {5, 4});
>             AddPunto(cuadrado, {4, 4});   // cierra
>             assert(GetCantidadDeLados(cuadrado) == 4);
>             assert(GetPerímetro(cuadrado) == 4.0);
>         }
>     }
>
>     { // Procesar
>         SecuenciaPolígonos poligonos = LeerPolígonos(std::cin);
>
>         OrdenarPorCantidadDeLados(poligonos);
>         EscribirPolígonos(std::cout, poligonos);
>
>         OrdenarPorPerímetro(poligonos);
>         EscribirPolígonos(std::cout, poligonos);
>     }
>
>     return 0;
> }
> ```
> **Explicación de partes no obvias:**
> - `GetCantidadDeLados` resta 1 a la cantidad de puntos porque el primer punto está repetido al final para cerrar el polígono (así lo define el Requisito del capítulo con el ejemplo `1 1 2 2 3 1 1 1`: 4 puntos listados, 3 lados).
> - `GetNewPolígono` corta de leer en cuanto el punto leído es igual al primero (`actual == primero`) — ahí se cerró el polígono y hay que dejar el resto del flujo para el próximo polígono.
> - `LeerPolígonos` usa `in.peek() != EOF` como condición de corte del flujo completo (no de un polígono individual) — así se leen todos los polígonos del archivo de entrada hasta que no quede nada más.
> - Las dos funciones de ordenamiento usan `std::sort` con un comparador lambda en vez de un algoritmo escrito a mano, porque el enunciado de este capítulo no exige ningún algoritmo en particular (solo pide *qué* ordenar, no *cómo*); si tu cátedra puntual sí lo exige, reemplazá el cuerpo de `OrdenarPorX` por burbuja/inserción/selección comparando con el mismo criterio (`GetCantidadDeLados` o `GetPerímetro`).
> - Se compila con dos structs distintos (`SecuenciaPuntos` para puntos de un polígono, `SecuenciaPolígonos` para el flujo entero de polígonos) siguiendo exactamente la composición de tipos que pide la consigna: `Polígono` contiene una `SecuenciaPuntos`, `SecuenciaPolígonos` contiene varios `Polígono`.

### 💡 Trampas típicas / posibles preguntas

- ¿Por qué "cantidad de lados" no es lo mismo que "cantidad de puntos leídos"? → Porque el último punto leído es una repetición del primero (para cerrar el polígono); hay que restar 1.
- ¿Qué significa que el problema pida "primero ordenado por cantidad de lados **y después** por perímetro"? → Son **dos salidas separadas** con **dos criterios independientes**, no un ordenamiento compuesto (primero por lados, empate por perímetro) — así lo confirma el `main` del Código de Partida con dos llamadas a `EscribirPolígonos` intercaladas con cada `Ordenar...`.
- ¿Por qué el Código de Partida separa `SecuenciaPuntos` (con `MaxPuntos = 100`) de `SecuenciaPolígonos` (con `MaxPolígonos = 1'000`)? → Son dos niveles de composición distintos con límites distintos: un polígono individual tiene como máximo 100 puntos; el flujo completo tiene como máximo 1000 polígonos.
- ¿`std::sort` es estable? → No está garantizado (usa introsort); si tu cátedra pide estabilidad explícita (mantener el orden relativo entre elementos "iguales" según el criterio), usá `std::stable_sort` en vez de `std::sort`.
- Créditos extra (`GetPunto`, `RemovePunto`, `SecuenciaPuntosEnlazada`) → son variantes: `GetPunto` para acceder a un punto por índice sin modificar, `RemovePunto` para sacar un punto de en medio de la secuencia (hay que correr los siguientes un lugar, igual que en las secuencias contiguas de [[07 - Secuencias, Pilas y Colas]]), y `SecuenciaPuntosEnlazada` es reimplementar `SecuenciaPuntos` con lista enlazada en vez de `std::array` — mismo patrón de "TDA implementado dos veces" que ya viste en capítulos anteriores del libro.

---
