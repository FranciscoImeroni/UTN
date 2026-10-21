## [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Assets/AED 2018 K1051 Clase #26.pdf|PowerPoint]]

> Continúa el caso de estudio del CLI de [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Modulos.md|Módulos]]. Ahora se le agregan `redo`, `repeat` y persistencia entre sesiones.

# CLI con Undo + Redo + Persistencia

## El comportamiento nuevo

```
$ cli
> comando1
comando1
> comando2
comando2
> comando3
comando3
> repeat
comando3
> undo
comando3 undone
> undo
comando3 undone
> undo
comando2 undone
> undo
comando1 undone
> redo
comando1
> redo
comando2
> redo
comando3
> redo
comando3
> redo
No hay nada para rehacer
> quit
Bye!
$ cli
> undo
comando3 undone
> undo
comando3 undone
> quit
Bye!
$ cli
> undo
comando2 undone
```

Dos cosas nuevas respecto de la clase anterior:
1. Ahora hay **dos stacks**: uno de comandos hechos (Undo Stack) y uno de comandos deshechos (Redo Stack).
2. El historial de comandos **persiste entre sesiones** (fijate que en el ejemplo, después de cerrar con `quit` y volver a abrir `cli`, `undo` sigue funcionando con lo que había quedado).

## Dos Stacks: Undo y Redo

La lógica es simétrica:

- `undo`: sacás el tope del **Undo Stack**, lo marcás `undone`, y lo metés en el **Redo Stack**.
- `redo`: sacás el tope del **Redo Stack**, lo volvés a ejecutar/imprimir, y lo metés de nuevo en el **Undo Stack**.
- Cualquier comando **nuevo** que se ejecute debería vaciar el Redo Stack (si hacés `undo`, `undo` y después ejecutás un comando nuevo, ya no tiene sentido poder hacer `redo` de lo que habías deshecho — se pierde esa rama del historial). *(Chequealo en el enunciado real de tu TP, esta regla es la convención estándar de undo/redo tipo editor de texto, pero puede variar según cómo lo pida la cátedra.)*

**Ejemplo paso a paso, siguiendo la traza de arriba:**

Después de `comando1`, `comando2`, `comando3`:
```
Undo Stack (tope a la izq.): [comando3, comando2, comando1]
Redo Stack:                  []
```

Después de `repeat` (repite el último comando ejecutado sin sacarlo de nada, se vuelve a apilar):
```
Undo Stack: [comando3, comando3, comando2, comando1]
```

Después de 4 `undo` (deshace comando3, comando3, comando2, comando1 en orden):
```
Undo Stack: []
Redo Stack: [comando1, comando2, comando3, comando3]
```

Después de 1 `redo` (rehace comando1):
```
Undo Stack: [comando1]
Redo Stack: [comando2, comando3, comando3]
```

> 💡 Trampa de parcial: notá que `undo` saca del **tope** del Undo Stack (el ÚLTIMO hecho) y `redo` saca del **tope** del Redo Stack (el ÚLTIMO deshecho) — ambos son LIFO, pero como los vas llenando en el orden inverso al que los vaciás, el resultado es que `redo` te devuelve las cosas en el mismo orden en que las habías deshecho. Dibujá siempre los dos stacks por separado, no intentes hacerlo "de memoria".

## Centinela de pila vacía

Cuando el Undo Stack (o el Redo Stack) está vacío y pedís `undo` (o `redo`), el programa debe imprimir el mensaje correspondiente (`No hay nada para deshacer` / `No hay nada para rehacer`) en vez de fallar. Esto se resuelve chequeando si el stack está vacío antes de hacer `Pop` — es lo que se llama un **centinela**: una verificación que evita operar sobre una estructura vacía.

## Historial persistente entre sesiones

- Al hacer `quit`, se llama a **`SaveCommandHistory`**: guarda el estado de los stacks en un archivo.
- Al arrancar (`main`), se llama a **`OpenCommandHistory`**: lee ese archivo y reconstruye los stacks.
- El módulo responsable de todo esto es **`CommandHistory`** — es el único que conoce la estructura interna (`theUndoStack`, `theRedoStack`). El resto del sistema no necesita saber cómo se guarda ni cómo son los stacks por dentro (ocultamiento de información, como vimos en [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Modulos.md|Módulos]]).
- Hay que pensar en **precondiciones, poscondiciones y excepciones** sobre la disponibilidad de archivos: ¿qué pasa si el archivo de historial no existe la primera vez que corrés el programa? ¿Qué pasa si no se puede escribir (permisos, disco lleno)?

### Structure Chart actualizado

```
                         Main
              /       |        \        \
           Read   Interpret         OpenCommandHistory
                  /   |   |  \   \          |
                >>  Quit Echo Undo Repeat Redo   getline
                 |    |    |    |    |     |
             SaveCommandHistory  PushCommand  PopCommand  GetLastCommand  PopUndoneCommand
```

`Undo` usa `PopCommand` + guarda en el Redo Stack; `Redo` usa `PopUndoneCommand`; `Repeat` usa `GetLastCommand` (mira el tope sin sacarlo) y vuelve a apilarlo con `PushCommand`. Todos estos, en el fondo, llaman a las primitivas genéricas `Push`/`Pop` del módulo `Stack`.

---

# Flujo de Líneas — `getline`

Para leer/escribir línea por línea (por ejemplo, para el archivo de historial de comandos), se usa `std::getline` en vez de `cin >> variable` (que corta en el primer espacio).

## Copiar un archivo línea por línea, usando `string`

```cpp
#include <fstream>
#include <string>

int main(){
    std::ifstream in{"input.txt"};    // stream de ENTRADA (lectura) de archivo
    std::ofstream out{"output.txt"};  // stream de SALIDA (escritura) de archivo

    for(std::string s; std::getline(in, s); )
        out << s << '\n';
}
```

Cómo leerlo:
- `std::ifstream in{"input.txt"}` abre el archivo para leer.
- `std::ofstream out{"output.txt"}` abre (o crea) el archivo para escribir.
- `std::getline(in, s)` lee **una línea entera** de `in` (hasta el `\n`, sin incluirlo) y la guarda en `s`. Devuelve el stream, que en un `if`/`for`/`while` se evalúa como `true` mientras la lectura haya sido exitosa (o sea, mientras no se llegó al final del archivo).
- El `for(std::string s; std::getline(in, s); )` es el patrón estándar para "mientras haya líneas para leer": la inicialización declara `s`, la condición es la propia lectura, y no hay incremento (el "incremento" en sí es volver a leer en la próxima vuelta).
- `out << s << '\n';` escribe la línea leída, agregando el salto de línea que `getline` se había comido.

> 💡 Diferencia clave con `cin >> variable`: `>>` corta en el primer espacio/salto de línea (sirve para leer "palabras" o números). `getline` lee la línea **completa**, incluyendo espacios, hasta el `\n`. Para leer nombres compuestos, oraciones, o líneas de un archivo de configuración, siempre `getline`.

---

# 🧪 Práctica

1. Tenés el Undo Stack `[c3, c2, c1]` (tope a la izquierda) y el Redo Stack vacío. Hacés `undo`, `undo`, y después ejecutás un comando nuevo `c4`. Dibujá cómo quedan los dos stacks (pensá si `c2` y `c3` siguen disponibles para `redo` o no, y por qué).

2. Escribí (a mano, en papel) qué haría la función `Repeat`: ¿qué operación de stack usa para "mirar" el último comando sin sacarlo? ¿Por qué no alcanza con un `Pop` común?

3. ¿Por qué el archivo de historial se guarda recién en `Quit` y se abre recién en `Main`, en vez de guardarse/leerse en cada comando?

4. Escribí un programa que lea `nombres.txt` (un nombre por línea, puede tener espacios, ej: "Juan Pérez") y escriba en `mayusculas.txt` cada línea convertida a mayúsculas. (Pista: usá el patrón de `getline` de arriba; para convertir a mayúsculas podés recorrer el string carácter por carácter.)

5. ¿Qué pasaría si en el ejercicio anterior usaras `in >> s` en vez de `getline(in, s)`? Dá un ejemplo concreto de una línea que se procesaría mal.
