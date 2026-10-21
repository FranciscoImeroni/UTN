---
sticker: lucide//list-checks
---

# Guía completa — "Trabajos de Algoritmos y Estructura de Datos" (José María Sola)

Esta es una guía **capítulo por capítulo** de todo el libro de assignments (`[[Aed Assignments.pdf]]`, 41 trabajos numerados del 3 al 41, Rev. 7.2.0-rc.1). Está pensada para hacerse **en orden**, ejercicio por ejercicio, junto con el código en `26-053-01/`.

**Formato de cada ejercicio en las notas de esta guía:**
1. **Enunciado resumido** — Objetivos, Temas, Problema, Restricciones, Tareas (parafraseado del PDF).
2. **📘 Teoría necesaria** — cada tema del enunciado explicado desde cero: teoría → sintaxis → ejemplo mínimo → cómo aparece en este TP. Esto es lo que hay que entender *antes* de escribir una línea.
3. **🧭 Cómo encararlo** — una guía paso a paso de diseño (qué variables, qué estructura de datos, qué loop, qué habría que probar con `assert`) **sin dar el código final armado**. La idea es que a partir de acá vos escribas tu propia solución.
4. **✅ Solución completa** — dentro de un callout plegado (hacé click para abrir). Está para **revisar después de intentarlo vos**, no para copiar antes. Respeta las restricciones del enunciado y el estilo que ya venís usando en `26-053-01/`.
5. **💡 Trampas típicas / preguntas de parcial u oral** — errores comunes y preguntas que suelen hacer.

**Notas del mismo estilo ya hechas** (para copiar el tono): [[Call Stack y Heap]], `26-053-01/18-Arreglos & Dimensiones - Total de Ventas/Guia-Examen-Oral.md`.

---

## Índice de la guía

| Archivo                                       | Trabajos que cubre                                                                                                      | Parte del libro |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | --------------- |
| [[01 - Trabajos Introductorios]]              | 3–6: Problemas y Soluciones, Hello World, Adición, Valores y Tipos de Datos                                             | I               |
| [[02 - Funciones]]                            | 7–12: Celsius/Fahrenheit, Func. y Operador Condicional, Bisiesto, Func. Recursivas, Días del Mes, Conceptos Matemáticos | II              |
| [[03 - Control de Flujo]]                     | 13–14: Mayor de dos Números, Repetición de Frase                                                                        | III             |
| [[04 - Abstracción con Tipos]]                | 15–21: Enumeraciones, Uniones, CUIL, Arreglos & Dimensiones, Diagonal de Matriz, Tipo Color, Fecha                      | IV              |
| [[05 - Geometría]]                            | 22–25: Plano y Punto, Círculo, Triángulo, Cuadriláteros y Rectángulos                                                   | V               |
| [[06 - Estructuras Dinámicas - Geometría II]] | 26–31: Polígonos, Interfaces & Implementaciones, Desarrollo de Tipos, I/O, Estructuras Enlazadas, Renderizar SVG        | VI              |
| [[07 - Secuencias, Pilas y Colas]]            | 32–38: Secuencia/Stack/Queue (contigua y enlazada), Árbol de Búsqueda Binaria                                           | VII             |
| [[08 - Otros Trabajos e Integrador]]          | 39–41: Templates, Historial del Browser, Ordenamiento de Polígonos                                                      | VIII y IX       |
| [[09 - Línea AyED - Viajes de Pasajeros y Cargas]] | Enunciado aparte (fuera del PDF): dimensionar locomotoras y vagones de carga y pasajeros                           | —               |
| [[10 - Trenes]] | Enunciado de revisión más nueva (ex 42): Formación y PoolDeRecursos, versión contigua y enlazada, comandos por `cin` | — |

Cada archivo empieza con un link de vuelta acá (`⬅️`).

---

## Requisitos generales de entrega (Capítulo 2 del libro)

Estos aplican a **todos** los trabajos, además de lo particular que pida cada enunciado.

### Repositorios (GitHub)

- **Repositorio público personal** (uno por estudiante) para trabajos individuales. Nombre = el de la materia. `readme.md` en la raíz (front page) con: materia, curso, año/cuatrimestre, legajo, apellido, nombre.
- **Repositorio privado de equipo** (uno por equipo, este es `26-053-01`) para trabajos grupales. `readme.md` en la raíz con: materia, curso, año/cuatrimestre, número de equipo, nombre (opcional), integrantes actualizados (usuario GitHub, legajo, apellido, nombre). — Esto ya está hecho en `26-053-01/readme.md`.

### Carpeta por cada resolución

Formato de nombre obligatorio: **`DD-NombreTrabajo`** (dos dígitos + guion + nombre sin espacios), regex `[0-9]{2}-[a-zA-Z]+`. El "DD" es el número de trabajo que fije el curso (no necesariamente el número de capítulo del libro — en `26-053-01` ya se ve que "01" se usó para el trabajo de Valores y Tipos de Datos, que en el libro es el capítulo 6; lo importante es ser consistente con la numeración que use tu curso/cátedra).

Cada carpeta debe tener:
- Su propio `readme.md` (front page de la resolución) con: equipo, autores (usuario GitHub, legajo, apellido, nombre), número y título del trabajo, **transcripción del enunciado**, e **hipótesis de trabajo** (qué se espera lograr, a partir de leer el enunciado). Este es exactamente el formato que ya usás en los `readme.md` de `26-053-01` (Objetivo/Temas/Problema/Restricciones/Tareas/Hipótesis).
- Opcional pero recomendado: `.gitignore`, `Makefile`, tests (para algunos trabajos son obligatorios, se indica en el enunciado puntual).
- **No** debe incluir ejecutables ni archivos intermedios de compilación (`.o`, binarios, etc. — para eso está el `.gitignore`).

### Header Comments — obligatorio en TODO archivo fuente

```cpp
/* Qué: Nombre
 * Breve descripción
 * Quiénes: Autores
 * Cuando: Fecha de última modificación
 */
```
Ejemplo del libro:
```cpp
/* Stack.h
 * Interface for a stack of ints
 * JMS
 * 20150920
 */
```
> 💡 Esto todavía no se está aplicando en los `.cpp` de `26-053-01` — conviene agregarlo antes de entregar, es un requisito de forma explícito y fácil de perder puntos por no tenerlo.

### Lenguaje y estándar

El libro dice que el curso fija la versión del estándar de C++ a usar (no viene un número fijo en el texto general, lo define la cátedra/campus virtual). A juzgar por lo que ya está en `26-053-01` (`auto`, range-for, `std::array`, sufijo de literal `"..."s`, `assert`), están compilando como mínimo con **C++14** (por el sufijo `s` de `std::string`) — el `Makefile`/compilación manual debería fijar explícitamentre `-std=c++17` (o el que indique la cátedra) para que sea reproducible.

### Requisitos de tiempo

Los commits después de la fecha/hora límite del calendario del curso no se toman en cuenta. No hay prórroga implícita por seguir commiteando.

---

## Filosofía general del libro (para tener en la cabeza en TODOS los ejercicios)

- **Todo se prueba con `assert`**, no con `cout` + inspección visual. Un ejercicio "anda" cuando todos los `assert` pasan en silencio (si un `assert` falla, el programa aborta y te dice la línea). Esto ya lo aplicaste bien en el TP01 (Valores y Tipos de Datos).
- **Léxico antes que código**: para funciones, primero se escribe la definición matemática (dominio, fórmula/casos), después el prototipo, después la implementación. Ver `[[02 - Funciones]]`.
- **Trabajos incrementales**: varios TPs son el mismo problema resuelto varias veces agregando una restricción/dimensión (el caso más claro: TP18 Arreglos, con `Dim0`→`Dim3`). Conviene *no* borrar las versiones anteriores: son la prueba de la progresión de la solución.
- **De abstracción con tipos a estructuras dinámicas**: el libro repite el mismo dominio (Geometría) tres veces con creciente complejidad — primero con tipos simples (Partes IV-V), después con memoria dinámica y punteros (Parte VI) — no es casualidad, es la misma lección aplicada dos veces con más herramientas. Por eso conviene tener fresco `[[Call Stack y Heap]]` y `[[Punteros y Listas Enlazadas]]` antes de llegar a la Parte VI.
