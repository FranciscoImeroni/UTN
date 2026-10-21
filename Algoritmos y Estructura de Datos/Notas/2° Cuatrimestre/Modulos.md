## [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Assets/AED 2018 K1051 Clase #25.pdf|PowerPoint]]

# Módulos en C++

Un **módulo** es una unidad de código con una parte pública (lo que otros pueden usar) y una parte privada (los detalles de cómo lo hace, que a nadie de afuera le tienen que importar).

En C++, un módulo típicamente son **dos archivos**:

- **`.h`** (header) → la parte pública, el "contrato". Acá van:
  - Tipos públicos
  - Prototipos públicos (firmas de función, sin cuerpo)
  - Objetos públicos
- **`.cpp`** → el núcleo, la parte privada. Acá van:
  - La estructura de datos privada
  - El método (la implementación real, el cuerpo de las funciones)
  - Objetos, tipos y funciones auxiliares que nadie de afuera necesita ver

## Analogía con la célula

| Sistemas | Célula | Módulo | C++ |
|---|---|---|---|
| Interfaces | Membrana celular | Interfaz, contrato | `.h` (Membrana) |
| Parte pública | Pasaje de info por proteínas de transporte | Invocaciones, envío de mensajes | Tipos/prototipos/objetos públicos en el `.h` |
| Parte privada | Núcleo con estructura interna privada | Estado, comportamiento, elementos privados | Estructura de datos y métodos privados en el `.cpp` |

La idea: el `.h` es la membrana (lo único que se toca desde afuera), el `.cpp` es el núcleo (nadie de afuera necesita saber qué hay ahí adentro).

## Dependencias entre módulos

- Un **sistema** es un conjunto de elementos relacionados con un objetivo en común, organizado en jerarquía de sistemas y subsistemas.
- Un **programa** es un conjunto de módulos relacionados entre sí con un objetivo en común, también con jerarquía.
- En C++, la dependencia entre módulos se expresa con `#include`.
  - Un include que **importa** = "depende de" (necesito lo que ese módulo expone).
  - Un include que **exporta** = "implementa" (el `.cpp` incluye a su propio `.h` para implementar lo que promete).

## Atributos de calidad de un buen módulo

- **Alta Cohesión (funcional):** todo lo que está adentro del módulo tiene que ver entre sí, hace UNA cosa bien delimitada.
- **Bajo Acoplamiento:**
  - Pocas dependencias con otros módulos.
  - Las dependencias que tenga deben ser sobre **abstracciones**, no sobre implementaciones (no me tiene que importar CÓMO hace las cosas otro módulo, solo QUÉ hace).
  - Referencia recomendada por la cátedra: *high cohesion, loose coupling* (thebojan.ninja).
- **Ocultamiento de información:** el cliente (quien usa el módulo) no necesita conocer los detalles de la implementación para poder usar la abstracción.
- **Encapsulamiento:** bloquear activamente el acceso del cliente a los detalles de implementación.
  - `static` (a nivel archivo) "cierra los poros de la membrana" — hace que algo del `.cpp` sea invisible fuera de ese archivo.
  - *Forward declaration* (`struct Nodo;` sin definir el cuerpo) se usa para punteros a objetos cuya estructura interna no hace falta conocer desde el `.h`.

> 💡 Para el parcial, si te preguntan "¿por qué este módulo está mal diseñado?", pensá en estos 4 ejes: ¿está mezclando responsabilidades (baja cohesión)? ¿depende de más módulos de los necesarios (alto acoplamiento)? ¿el cliente necesita saber cómo funciona por dentro (falta ocultamiento)? ¿hay algo que debería ser privado y es público (falta encapsulamiento)?

---

# Caso de estudio: diseñar un CLI (Command Line Interpreter) con Undo

Este es el ejemplo que se usa en clase para bajar la teoría de módulos a un diseño real.

## El comportamiento esperado

```
$ cli
> comando1
comando1
> comando2
comando2
> comando3
comando3
> undo
comando3 undone
> comando4
comando4
> undo
comando4 undone
> undo
comando2 undone
> undo
comando1 undone
> undo
No hay nada para deshacer
> quit
Bye!
$
```

Cada comando ingresado se **hace eco** (se imprime), y `undo` deshace el último comando que no haya sido deshecho ya, en orden LIFO — es decir, con un **Stack**.

## Los 3 comandos del sistema

- `Echo`: repite lo que el usuario escribió.
- `Undo`: saca el último comando del stack y lo marca como `undone`.
- `Quit`: termina el programa (imprime `Bye!`).

## Diseño de módulos

Pensando en cohesión/acoplamiento, el diseño natural separa:

- **Reader**: lee una línea de entrada (`Read`).
- **Interpreter**: decide qué comando es e invoca la acción correspondiente (`Interpret`).
- **Executer**: implementa las acciones concretas: `Echo`, `Undo`, `Quit`. Internamente usa `theStack` (el stack de comandos hechos).
- **Stack**: la estructura de datos genérica (`Push`, `Pop`).
- **Main**: orquesta todo (`Read` → `Interpret` → repetir).

Dependencias: `Main` depende de `Read` e `Interpret`. `Interpret` depende de `Echo`, `Quit`, `Undo` (dentro de Executer) y de `Stack` (a través de Executer). Ni `cin` ni `cout` aparecen directamente en el *structure chart* de alto nivel: están escondidos adentro de `Read` (usa `cin`) y de `Echo`/`Quit` (usan `cout`) — el resto de los módulos no necesita saber que existen.

## Structure Chart (Carta Estructurada)

Es un diagrama que muestra "quién invoca a quién", de arriba hacia abajo:

```
                Main
              /       \
           Read      Interpret
            |          /  |  \
            <<      Echo Quit Undo
                      |    |    |
                    Push  >>   Pop
                      |
                     >>
```

> 💡 Pregunta típica de parcial sobre este diagrama: *"¿por qué `cout`/`cin` no aparecen en el structure chart?"* Porque son detalles de implementación de bajo nivel (E/S), ocultos dentro de los módulos hoja (`Read` hace `cin>>`, `Echo`/`Quit` hacen `cout<<`). El structure chart muestra la jerarquía de **invocaciones entre módulos del sistema**, no cada llamada a la biblioteca estándar.

**¿Agregarías o quitarías algún módulo?** Es una pregunta abierta típica de la cátedra — pensá en si `theStack` debería ser su propio módulo separado de `Executer` (mejor cohesión: `Executer` decide QUÉ hacer, `Stack` sabe CÓMO guardar comandos), y notá que eso es justo lo que se termina haciendo en la clase siguiente (ver [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Historial de Comandos (Undo, Redo) y Flujo de Lineas.md|Historial de Comandos]]).

---

# 🧪 Práctica

1. Explicá con tus palabras la analogía célula ↔ módulo: ¿qué sería la membrana, qué sería el núcleo, y por qué el `.h` cumple el rol de membrana?
2. Dado un módulo `Fecha` que valida fechas y además, adentro, tiene funciones para conectarse a una base de datos y guardar logs: ¿qué atributo de calidad está violando? Justificá.
3. ¿Por qué un include que "exporta" (el `.cpp` incluyendo a su propio `.h`) no es lo mismo que un include que "importa" (un módulo dependiendo de otro)?
4. En el diseño del CLI: ¿qué pasaría con la cohesión del módulo `Executer` si además de `Echo`, `Quit` y `Undo` le agregás una función que lee archivos de configuración del sistema operativo? Justificá por qué eso rompe el diseño.
5. ¿Para qué sirve `static` a nivel de archivo `.cpp` en términos de encapsulamiento? Dá un ejemplo de una función auxiliar que tendría sentido marcar `static`.
