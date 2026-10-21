⬅️ [[00 - Índice y Requisitos Generales]]

# Línea AyED — Plataforma de viajes de pasajeros y cargas

> Este trabajo **no está en el PDF de assignments**: es un enunciado aparte. Lo traté igual que los capítulos de la guía: enunciado → teoría → cómo encararlo → solución plegada → trampas.

## 1. Qué se pide (enunciado ordenado)

**En una frase:** calcular cuántos recursos (locomotoras y vagones) hacen falta para cubrir la demanda máxima de dos servicios (carga y pasajeros), y **declarar las variables** que los representan y que registran los servicios.

### Datos del enunciado

| | Carga | Pasajeros |
|---|---|---|
| Recorrido cabecera a cabecera | 4 h (sin descansos) | 1 h 30 |
| Tiempo extra | 3 h de carga/descarga para despachar un servicio | 30 min **sólo si** hay que agregar o quitar vagones |
| Demanda máxima | 6 servicios por día | 1 servicio cada 15 min |
| Vías | un par de vías para este tipo de servicio | un par de vías para este tipo de servicio |

Condiciones generales:
- **No hay formaciones armadas** todavía.
- Las formaciones se **ensamblan en la cabecera de partida**.
- Recursos = **locomotoras** y **vagones**.
- Hay que **optimizar el uso** (reutilizar formaciones, no una nueva por servicio).

### Entregables

1. Declarar las variables necesarias (máximo de cada recurso).
2. **Relevamiento:** cantidad y detalle de servicios de carga y de pasajeros, y recursos para cubrir cada demanda.
3. **Bonus 1:** si algún requisito de demanda **no** se cumple en carga, indicar cuál.
4. **Bonus 2:** lo mismo para pasajeros.

## 2. Qué NO dice el enunciado (hipótesis que hay que declarar)

Igual que en los `readme.md` de `26-053-01` (sección *Hipótesis*), acá hay que escribirlas.

| # | Falta definir | Hipótesis que tomé | Si cambia... |
|---|---|---|---|
| H1 | Locomotoras por formación | 1 | Se multiplica el total de locomotoras |
| H2 | Vagones por formación (carga / pasajeros) | 10 / 5 (**valores inventados**) | Se multiplica el total de vagones |
| H3 | "Servicios" = ¿ida y vuelta? | Cada viaje (ida **o** vuelta) es un servicio; la formación vuelve y se reutiliza | Cambia el ciclo |
| H4 | "6 por día" y "cada 15 min" ¿en total o por sentido? | **Por sentido**, parejos en el día | Con otra lectura baja la cantidad |
| H5 | Tiempo de giro de pasajeros en cabecera | 0 (si no cambia vagones) | Sube el ciclo |
| H6 | Los 3 h de carga | Son **una** operación de despacho por servicio (descarga + carga juntas) | Si fueran 3 h + 3 h cambia el Bonus 1 |

> 💡 H2 es lo más importante de aclarar: el enunciado no da la cantidad de vagones, así que el número final de vagones es **sólo** `formaciones × vagonesPorFormación`. Lo que sí es resultado del razonamiento es la cantidad de **formaciones**.

## 📘 Teoría necesaria

### a) Ciclo de una formación y cuántas hacen falta

Una formación tarda un **ciclo** en volver a estar lista para otro servicio. Si sale un servicio cada `intervalo` minutos, la cantidad mínima de formaciones es:

```
formaciones = ceil( ciclo / intervalo )
```

Es la misma idea que "cuántas cajas abrir en un supermercado": si cada cliente tarda 14 min y llega uno cada 4, necesitás `14/4 = 3,5 → 4` cajeros. **Siempre se redondea hacia arriba**: no existe media formación.

- **Carga:** `ciclo = 2 × (3 h despacho + 4 h recorrido) = 14 h`. `intervalo = 24 h / 6 = 4 h`. → `ceil(14/4) = 4`.
- **Pasajeros:** `ciclo = 2 × 90 min = 180 min`. `intervalo = 15`. → `180/15 = 12` (justo, sin redondeo).

### b) División entera hacia arriba (ceil) en C++

`a / b` entre `int` trunca hacia abajo. Para redondear arriba:

```cpp
constexpr int CeilDiv(int a, int b) { return (a + b - 1) / b; }
```
Probalo: `CeilDiv(14, 4)` → `(14+3)/4 = 17/4 = 4`. `CeilDiv(180, 15)` → `194/15 = 12`. Ver [[02 - Funciones]] (léxico + prototipo + `assert` antes de implementar).

### c) `constexpr` y `static_assert`

`constexpr` calcula el valor **en compilación**. Sirve para fijar el tamaño de un `std::array` (`std::array<T, N>` exige `N` constante). `static_assert(cond)` verifica una condición también en compilación; si falla, **no compila**. Es el `assert` de las constantes.

### d) Dimensionar con `std::array` y structs

Es lo mismo que en el TP 18 (Total de Ventas, ver [[04 - Abstracción con Tipos]]): un arreglo es un producto cartesiano; acá `formaciones × vagones`. Y como en [[05 - Geometría]]: un `struct` agrupa datos relacionados (una formación = locomotora(s) + vagones).

```cpp
struct Vagon { int id; };
struct Formacion { Locomotora loc; std::array<Vagon, 10> vagones; };
std::array<Formacion, 4> formaciones{};   // 4 formaciones de 10 vagones
```

### e) Ocupación de vía (para los bonus)

Una vía de cabecera sólo atiende **una formación a la vez**. Si cada operación la ocupa `T` minutos y los servicios salen cada `I`, hace falta `T ≤ I`. Si no, se forma cola y la demanda no se cumple.

## 🧭 Cómo encararlo

1. **Pasar todo a una misma unidad** (minutos) y ponerlo en constantes con nombre. Nada de números mágicos.
2. **Escribir las hipótesis** H1–H6 en el `readme.md` (sección *Hipótesis*).
3. Por cada servicio: calcular **intervalo** (cada cuánto sale uno) y **ciclo** (cuánto tarda una formación en estar libre otra vez). Pensar un ciclo a mano con una línea de tiempo antes de usar la fórmula.
4. Calcular formaciones con `CeilDiv`, y recursos totales (`formaciones × recursos por formación`).
5. Diseñar los tipos: `Locomotora`, `Vagon`, `FormacionCarga`, `FormacionPasajeros`, `Servicio`.
6. Declarar las variables: arreglos de formaciones y de servicios.
7. Asignar servicio `k` a la formación `k % N` (rotación) y **probar con `assert`** que una formación vuelve a usarse recién después de `N × intervalo ≥ ciclo`.
8. Bonus: comparar tiempo de ocupación de vía contra intervalo.
9. Simular a mano 2–3 servicios con una línea de tiempo para comprobar que no hay choque.

**Pistas para tus asserts:** `formaciones × intervalo ≥ ciclo` (si falla, falta una formación); el servicio `k` y el `k+N` usan la misma formación; ocupación de vía ≤ intervalo.

## ✅ Solución completa

> [!example]- Solución completa (click para expandir)
> ```cpp
> /* Linea.cpp
>  * Dimensionamiento de recursos (locomotoras y vagones) de la línea AyED:
>  * servicios de carga y de pasajeros, con verificación de la demanda.
>  * Equipo 1
>  * 20261008
>  */
> #include <array>
> #include <cassert>
> #include <iostream>
>
> // ---------- Utilidades ----------
> constexpr int CeilDiv(int a, int b) { return (a + b - 1) / b; }  // división entera hacia arriba
>
> // ---------- Datos del enunciado (todo en MINUTOS) ----------
> constexpr int MinutosPorDia = 24 * 60;
>
> // Carga
> constexpr int CargaRecorrido      = 4 * 60;  // cabecera a cabecera, sin descansos
> constexpr int CargaDespacho       = 3 * 60;  // carga/descarga para despachar un servicio
> constexpr int CargaServiciosDia   = 6;       // demanda máxima diaria
>
> // Pasajeros
> constexpr int PasRecorrido        = 90;      // cabecera a cabecera
> constexpr int PasCambioVagones    = 30;      // sólo si hay que agregar/quitar vagones
> constexpr int PasIntervalo        = 15;      // un servicio cada 15 minutos
>
> // ---------- Hipótesis (NO vienen en el enunciado: confirmar con la cátedra) ----------
> constexpr int LocomotorasPorFormacion        = 1;
> constexpr int VagonesCargaPorFormacion       = 10;
> constexpr int VagonesPasajerosPorFormacion   = 5;
>
> // ---------- Cálculo ----------
> // Ciclo de una formación = ida + manejo en cabecera + vuelta + manejo en cabecera de partida.
> constexpr int CargaIntervalo   = MinutosPorDia / CargaServiciosDia;                   // 240
> constexpr int CargaCiclo       = 2 * (CargaDespacho + CargaRecorrido);                // 840
> constexpr int CargaFormaciones = CeilDiv(CargaCiclo, CargaIntervalo);                 // 4
>
> constexpr int PasCiclo         = 2 * PasRecorrido;                                    // 180
> constexpr int PasFormaciones   = CeilDiv(PasCiclo, PasIntervalo);                     // 12
> constexpr int PasServiciosDia  = MinutosPorDia / PasIntervalo;                        // 96
>
> // ---------- Tipos ----------
> struct Locomotora { int id; };
> struct Vagon      { int id; };
>
> struct FormacionCarga {
>     std::array<Locomotora, LocomotorasPorFormacion> locomotoras;
>     std::array<Vagon, VagonesCargaPorFormacion> vagones;
> };
> struct FormacionPasajeros {
>     std::array<Locomotora, LocomotorasPorFormacion> locomotoras;
>     std::array<Vagon, VagonesPasajerosPorFormacion> vagones;
> };
> struct Servicio {
>     int salida;      // minuto del día en que sale
>     int formacion;   // índice de la formación asignada
> };
>
> // ---------- Variables que piden: recursos y servicios ----------
> std::array<FormacionCarga, CargaFormaciones>        formacionesCarga{};
> std::array<FormacionPasajeros, PasFormaciones>      formacionesPasajeros{};
> std::array<Servicio, CargaServiciosDia>             serviciosCarga{};
> std::array<Servicio, PasServiciosDia>               serviciosPasajeros{};
>
> int main() {
>     // Totales de recursos
>     constexpr int locomotorasCarga = CargaFormaciones * LocomotorasPorFormacion;
>     constexpr int vagonesCarga     = CargaFormaciones * VagonesCargaPorFormacion;
>     constexpr int locomotorasPas   = PasFormaciones * LocomotorasPorFormacion;
>     constexpr int vagonesPas       = PasFormaciones * VagonesPasajerosPorFormacion;
>     static_assert(CargaFormaciones == 4 && PasFormaciones == 12);
>     static_assert(locomotorasCarga == 4 && vagonesCarga == 40);
>     static_assert(locomotorasPas == 12 && vagonesPas == 60);
>
>     // Servicios: la salida k va a la formación k % N (rotación)
>     for (int k = 0; k < CargaServiciosDia; ++k)
>         serviciosCarga.at(k) = {k * CargaIntervalo, k % CargaFormaciones};
>     for (int k = 0; k < PasServiciosDia; ++k)
>         serviciosPasajeros.at(k) = {k * PasIntervalo, k % PasFormaciones};
>
>     // Una formación vuelve a usarse cada N * intervalo minutos: tiene que alcanzar para el ciclo
>     assert(CargaFormaciones * CargaIntervalo >= CargaCiclo);   // 960 >= 840
>     assert(PasFormaciones   * PasIntervalo   >= PasCiclo);     // 180 >= 180
>     assert(serviciosCarga.at(4).formacion == serviciosCarga.at(0).formacion);
>
>     // Bonus 1: vía de la cabecera en carga
>     assert(CargaDespacho <= CargaIntervalo);                       // 180 <= 240 -> cumple
>     assert(CargaServiciosDia * CargaDespacho <= MinutosPorDia);    // 1080 <= 1440 -> cumple
>
>     // Bonus 2: vía en pasajeros si hay cambio de vagones
>     assert(!(PasCambioVagones <= PasIntervalo));                   // 30 > 15 -> NO cumple
>     constexpr int maxServiciosConCambio = MinutosPorDia / PasCambioVagones;  // 48 de 96
>
>     std::cout << "Carga: " << CargaFormaciones << " formaciones, " << locomotorasCarga
>               << " locomotoras, " << vagonesCarga << " vagones\n"
>               << "Pasajeros: " << PasFormaciones << " formaciones, " << locomotorasPas
>               << " locomotoras, " << vagonesPas << " vagones\n"
>               << "Pasajeros con cambio de vagones: max " << maxServiciosConCambio
>               << " de " << PasServiciosDia << " servicios/dia\n";
> }
> ```
> Compilé y ejecuté este código (`g++ -std=c++17 -Wall -Wextra`) y los asserts pasan. Salida:
> ```
> Carga: 4 formaciones, 4 locomotoras, 40 vagones
> Pasajeros: 12 formaciones, 12 locomotoras, 60 vagones
> Pasajeros con cambio de vagones: max 48 de 96 servicios/dia
> ```

### Relevamiento (la respuesta "en prosa", bajo las hipótesis H1–H6)

**Carga**
- 6 servicios/día, uno cada 4 h: salen a las 00:00, 04:00, 08:00, 12:00, 16:00, 20:00.
- Ciclo de una formación: 3 h + 4 h + 3 h + 4 h = **14 h**. Como salen cada 4 h → `ceil(14/4)` = **4 formaciones**.
- Servicios 0–5 → formaciones F0, F1, F2, F3, F0, F1 (la F0 vuelve a salir 16 h después, y necesita 14 h).
- Recursos: 4 locomotoras + 40 vagones (con H1 y H2).

**Pasajeros**
- 96 servicios/día, uno cada 15 min.
- Ciclo: 90 + 90 = **180 min** → `180 / 15` = **12 formaciones**, sin margen (la formación llega y sale justo).
- Recursos: 12 locomotoras + 60 vagones (con H1 y H2).

**Bonus 1 — carga:** **sí se cumple todo.** El despacho (3 h) cabe entre salidas (4 h) y la vía se ocupa 6 × 3 = 18 h de 24 h. *Ojo:* si los 3 h fueran sólo de **carga** y otras 3 h de **descarga** aparte, cada servicio ocuparía 6 h de vía > 4 h de intervalo y **no se cumpliría** (H6).

**Bonus 2 — pasajeros:** **no se cumple** el requisito de un servicio cada 15 min **en los servicios que requieran agregar o quitar vagones**: el cambio ocupa la vía 30 min > 15 min. Con cambio de vagones el máximo es uno cada 30 min (48 servicios de los 96). Además, esos servicios alargan el ciclo (180 + 30 = 210 min → 14 formaciones, o 16 si el cambio es en ambas cabeceras).

## 💡 Trampas típicas / posibles preguntas

- **Dividir y olvidarse del redondeo:** `14/4` da 3 en `int`, y con 3 formaciones el servicio de las 12:00 se queda sin formación. Siempre `ceil`.
- **Mezclar horas y minutos** (4 h con 15 min). Pasar todo a minutos primero.
- **Contar una formación por servicio** (6 formaciones de carga, 96 de pasajeros): ignora "optimizando su uso".
- **Olvidar la vuelta** en el ciclo: cuenta 7 h en vez de 14 h y se subestiman las formaciones.
- **Dar la cantidad de vagones como si fuera dato:** el enunciado no lo da. Hay que declarar la hipótesis o preguntarla.
- **No justificar los bonus:** pide *indicar qué requisito no se cumple*; la respuesta es una comparación (ocupación de vía vs intervalo).
- Pregunta de oral probable: *"¿por qué `std::array` y no `std::vector`?"* → todos los tamaños se conocen en compilación.

## 🔁 Cómo resolví los otros ejercicios de esta guía (para que veas el patrón)

Seguí el mismo procedimiento en todos, y es el que te conviene aplicar acá:

1. **Leer el enunciado completo y separar** objetivos, temas, restricciones y entregables (por eso la tabla de datos de arriba).
2. **Detectar qué falta o es ambiguo y anotarlo como hipótesis**, en vez de inventar en silencio. Ejemplos: en el cap. 12 el PDF sólo lista las funciones, así que completé la especificación matemática y lo dejé aclarado; en los caps. 32–38 la interfaz de Stack/Queue no estaba y la diseñé marcándola como criterio propio.
3. **Aprovechar lo que ya tenías hecho.** Para los caps. 6, 8, 10 y 18 partí de tu código real de `26-053-01/` en vez de reemplazarlo. Eso permitió encontrar problemas reales: el `assert` con paréntesis mal cerrado en `valoresYTiposDeDatos.cpp`, y el `Min.cpp` que prueba un `int` con literales `double`.
4. **Léxico y pruebas antes del código** (cap. 8): definición matemática → `assert` → prototipo → implementación. Acá es la fórmula `ceil(ciclo/intervalo)` → asserts → `CeilDiv`.
5. **Un problema repetido con más dimensiones** (cap. 18, `Dim0`→`Dim3`): lo mismo sirve acá, primero carga sola y después pasajeros.
6. **Tipos propios antes de las funciones** (caps. 15–25): `enum`/`struct` para modelar el dominio, como `Servicio` o `Formacion`.
7. **Verificar compilando.** Toda solución nueva la compilé y ejecuté con `-Wall -Wextra` antes de ponerla acá.

| Si venís de... | Qué reutilizás acá |
|---|---|
| Cap. 8 y 10 ([[02 - Funciones]]) | Función con léxico + `assert` (`CeilDiv`) |
| Cap. 18 ([[04 - Abstracción con Tipos]]) | `std::array` anidados, `.at()`, dimensionar con constantes |
| Caps. 22–25 ([[05 - Geometría]]) | `struct` compuestos (Formación = Locomotora + Vagones) |
| Cap. 27 ([[06 - Estructuras Dinámicas - Geometría II]]) | Si tu cátedra pide separar en `.h`/`.cpp` |
