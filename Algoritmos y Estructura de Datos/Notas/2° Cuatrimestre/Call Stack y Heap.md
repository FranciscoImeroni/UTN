## [[UTN/Algoritmos y Estructura de Datos/Notas/2° Cuatrimestre/Assets/AED 2018 K1051 Clase #23.pdf|PowerPoint]]

# Call Stack (Pila de Invocaciones)

Cuando corrés un programa, el sistema operativo invoca a `main`. Cada vez que una función invoca a otra, se apila algo arriba (**push**); cuando una función termina y retorna, se desapila (**pop**). Eso es el **call stack**.

- Cada **proceso** tiene su propio call stack. Si el sistema permite varios **threads**, cada thread tiene el suyo.
- Justo al arrancar y justo antes de terminar el proceso, el call stack está **vacío**.
- En la **base** del stack está `main` (invocado por el Sistema Operativo).
- En el **top** (cima) está la función **activa** — la que se está ejecutando en este instante.
- La función en el top **nunca invocó** a otra en ese instante (si lo hubiera hecho, esa otra pasaría a ser el top).
- El registro **SP** (Stack Pointer) de la CPU apunta al top. Se mueve con cada push/pop.
- La plataforma decide detalles de implementación: dónde está la base, y si el stack crece hacia arriba o hacia abajo en memoria.

**Ejemplo:** si `main` invocó a `foo`, y `foo` invocó a `bar`, el call stack (de la base al top) es:

```
3 bar      <- top (activa)
2 foo
1 main
0 Sistema Operativo   <- base
```

## Ejercicio resuelto — reconstruir el call stack

```cpp
void foo(int);
int bar();

int main(){
    foo(bar());
}

void foo(int i){}
int bar(){ return 42; }
```

**¿Cuál función llamó a cuál?** `main` llamó a `bar` primero (porque hay que evaluar el argumento antes de poder llamar a `foo`), `bar` retorna 42, y recién ahí `main` llama a `foo(42)`.

**¿Cuántas funciones hay en ejecución cuando `bar` está corriendo?** 2: `main` y `bar` (en ese instante `foo` todavía no fue invocada, porque su argumento no terminó de evaluarse).

> 💡 Trampa típica de parcial: mirar `foo(bar())` y pensar que `foo` está "afuera" esperando. En C++ los argumentos se evalúan **antes** de la llamada, entonces `bar` se ejecuta y termina (se hace pop) antes de que `foo` siquiera empiece.

## Stack Frame (Activation Record)

Cada elemento del call stack es un **stack frame** (o *activation record*). El contenido depende de la plataforma, pero en general guarda:

- **Variables automáticas** (en general, las locales de la función)
- La dirección de **retorno** (el IP — Instruction Pointer — de la función que llamó, para saber dónde seguir cuando esta función termine)
- Los **argumentos** que le pasó la función llamante

Cada invocación hace push de un nuevo stack frame. Cada retorno hace pop. En assembler, tanto la invocación+push como el retorno+pop se implementan con más de una instrucción (no es atómico).

## Variables automáticas

"Automática" quiere decir: el compilador reserva y libera el espacio **solo** (vos no hacés `new`/`delete`). Viven en el stack, dentro del stack frame de la función.

**¿Automática implica local? ¿Local implica automática?** No necesariamente — podés tener una variable local que sea `static`, y esa NO es automática (vive en la sección de datos estáticos, no en el stack, y conserva su valor entre llamadas).

```cpp
void foo(int a){   // a: Automatic (parámetro)
  int b = 2;       // Automatic
  static int c = 3;// Static (¡ojo, esto NO es automática aunque sea local!)
}
```

### El error clásico: devolver la dirección de una variable automática

```cpp
int* pa(){
  int a = 1;
  return &a;   // ❌ MAL. Cuando pa() termina, se hace pop del stack frame.
}              // a ya no existe. El puntero queda "colgando" (dangling pointer).
```

Justificación tipo parcial: *"Incorrecto. `a` es automática, vive en el stack frame de `pa`. Al retornar la función se hace pop y ese espacio se libera/reutiliza. El puntero devuelto apunta a memoria que ya no le pertenece a nadie."*

En cambio, esto **sí es correcto**:

```cpp
int* ps(){
  static int s = 3;
  return &s;   // ✅ OK. s es static, vive en la sección de datos estáticos,
}              //    no se destruye cuando ps() termina.
```

Y este caso, con un puntero como parámetro:

```cpp
int* p(int* p){
  return &p;   // ❌ MAL por el mismo motivo: p (el parámetro) es automático.
}              //    &p es la dirección de la variable local p, no la del int al que apunta.
```

> 💡 Regla rápida para el parcial: si te piden justificar "¿es correcto devolver `&variable`?", preguntate: *¿esa variable vive en el stack (automática) o en otro lado (static, heap)?* Si vive en el stack, al retornar se destruye → dangling pointer.

## Reserva Dinámica de Memoria — Heap

A diferencia del stack (automático, el compilador se encarga), el **heap** es memoria que el programador reserva y libera **a mano**, con `new` y `delete`.

Analogía: pedir una reserva en un hotel/restaurante.
- `new` = hacer la reserva (¿qué tipo? ¿cuántos?)
- `delete` = cancelar la reserva

```cpp
// Un int en el heap
int* p = new int;
...
delete p;          // libera un solo elemento -> delete (sin corchetes)

// Un arreglo de ints en el heap
unsigned n;
cin >> n;
int* pa = new int[n];
...
delete pa[];       // libera un arreglo -> delete[] (con corchetes)
```

> 💡 Trampa de parcial: `delete` sin `[]` para un solo objeto, `delete[]` para un arreglo. Usar el que no corresponde es **comportamiento indefinido** (aunque a veces "funcione" en la práctica, no hay garantía).

**¿Qué diferencia hay entre la reserva del stack y la del heap?**

| | Stack (automático) | Heap (`new`/`delete`) |
|---|---|---|
| ¿Quién reserva/libera? | El compilador, solo | El programador, a mano |
| ¿Cuándo se libera? | Al retornar la función (pop) | Cuando el programador hace `delete` |
| ¿Qué pasa si te olvidás de liberar? | No aplica | **Memory leak** (fuga de memoria) |
| Nombre de los objetos | No tienen nombre propio necesariamente | Se accede solo a través de punteros |

**¿Qué es un Garbage Collector?** Un mecanismo (que C++ no tiene por defecto) que libera automáticamente la memoria del heap que ya nadie está usando, para no depender de que el programador se acuerde de hacer `delete`.

## Layout (Disposición) de la Memoria

La memoria de un proceso se organiza, de dirección menor a mayor, así:

```
0: Dirección menor
┌─────────────────────────┐
│ Texto (Código)          │  <- Sección de reserva ESTÁTICA
│ Datos Estáticos         │
├─────────────────────────┤
│ Heap                    │  <- crece hacia arriba (↑)
│                         │
│      Memoria libre      │  <- Sección de reserva DINÁMICA
│                         │
│ Stack                   │  <- crece hacia abajo (↓)
└─────────────────────────┘
99999: Dirección mayor
```

- El **Heap** crece hacia arriba (de direcciones bajas a altas).
- El **Stack** crece hacia abajo (de direcciones altas a bajas).
- Entre los dos queda "memoria libre". Si el stack y el heap se llegan a *chocar*, no hay más memoria disponible.

### Ejemplo completo, variable por variable

```cpp
void f();
int* g(int);

int a = 0;              // Static (variable global)

int main(){
  int b = 1;             // Stack (automatic)
  f();

  int* p = &b;            // p es Stack, apunta a algo del Stack (b)
  *p = 3;

  p = new int;             // ahora p apunta al Heap
  *p = 4;
  delete p;                // libero el heap; p queda dangling (habría que ponerlo en nullptr)

  p = g(21);
  delete p;
}

void f(){
  int c = 2;              // Stack
  static int d = 3;       // Static (¡ojo! es local pero NO automática)
}

int* g(int i){
  int* p = new int;       // p (el puntero) es Stack; *p (lo apuntado) es Heap
  *p = i;
  return p;                // ✅ OK devolver esto: apunta al Heap, no se destruye al retornar g
}
```

> 💡 Este es el ejercicio "trampa" favorito: distinguir cuándo un puntero *en sí mismo* es automático (siempre, si es una variable local no static) de cuándo *lo que apunta* vive en el heap (solo si se reservó con `new`). Son dos preguntas distintas.

**¿Qué es el stack overflow?** Cuando el stack crece tanto (por ejemplo, con recursión sin caso base, o reservando arreglos automáticos gigantes) que se queda sin espacio disponible y choca contra el límite de memoria. El programa termina abruptamente.

```cpp
// Ejemplo de cómo "empujar" el límite del Stack: recursión infinita
void Chrome(){
    int a[42*42*42*42];   // arreglo automático grande, se reserva en cada llamada
    Chrome();              // nunca corta -> stack overflow
}
```

```cpp
// Ejemplo de cómo "empujar" el límite del Heap: reservar sin liberar, en loop infinito
void Chrome(){
    for(;;) new int[42*42*42*42];   // memory leak infinito -> se agota el heap
}
```

---

# 🧪 Práctica

1. Dado este código, dibujá el call stack en el instante en que `bar` está ejecutándose, y decí cuántas funciones están en ejecución:
   ```cpp
   void bar(){ }
   void foo(){ bar(); }
   int main(){ foo(); }
   ```
2. ¿Es correcto este código? Justificá:
   ```cpp
   int* Crear(){
       int x = 10;
       return &x;
   }
   ```
3. ¿Y este?
   ```cpp
   int* Crear(){
       int* x = new int(10);
       return x;
   }
   ```
4. ¿Qué diferencia hay entre `delete p;` y `delete[] p;`? ¿Qué pasa si usás el que no corresponde?
5. Explicá con tus palabras por qué el Stack crece "hacia abajo" y el Heap "hacia arriba", y qué pasa si se encuentran.
