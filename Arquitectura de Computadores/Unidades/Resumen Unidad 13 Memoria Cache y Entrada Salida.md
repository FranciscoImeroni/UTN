# Unidad 13 – Memoria (Organización y Caché) y Entrada/Salida

> Basado en: *Memoria Caché 2020 P1 y P2* (Panizzo), *MemoriaOrganizacion*, *Unidad_EntradaSalida*, el TP de Memoria Caché y *Ejercicios de Caché resueltos*.
> El TP dice "Unidad 9" por la numeración vieja de la cátedra. Es el mismo tema.

## 🎯 Qué te van a tomar (prioridad)

La lista de temas del parcial (`TEMAS.md`) nombra **Memoria caché**, **Entrada/Salida** y **RAM/DRAM/ROM**. El TP y el PDF de ejercicios resueltos muestran cómo lo toma la cátedra:

1. **Ejercicio numérico de caché** (el más probable). Son tres tipos de cuenta:
   - Armar la **dirección física** a partir de etiqueta, línea o conjunto, y posición. También se pide pasarla a hexa.
   - Sacar la **memoria principal** y el **tamaño de la caché** a partir de los bits.
   - Calcular **factor de velocidad, tiempo medio, eficacia y rendimiento**.
2. **Teoría de caché**: las 3 organizaciones con ventajas y desventajas, serie contra paralelo, escritura inmediata/diferida/obligada, y reemplazo (RANDOM y LRU).
3. **E/S**: las 4 modalidades (programada, interrupción, DMA, canal), qué hace cada una y cuál usa menos CPU. Piden también los registros de la interfaz (REP, RB, RDI).
4. **Organización de memoria**: armar una memoria grande con módulos chicos (cuántos módulos y qué bit los selecciona).

---

## 1. Organización de memoria (capacidad y módulos)

**Capacidad**
- Se escribe `N K × m`: N posiciones (direcciones) de m bits cada una. Ejemplo: 16K×8.
- El **bus de direcciones** fija el máximo direccionable. Con `n` bits de dirección hay `2ⁿ` posiciones.
- El **mapa de memoria** dice qué rangos están ocupados y cuáles no. La computadora casi nunca tiene instalado el máximo.
- Unidades: KB = 2¹⁰, MB = 2²⁰, GB = 2³⁰, TB = 2⁴⁰ bytes.

**Armar memoria con módulos**

```
Nº de módulos = (capacidad total / capacidad módulo) × (ancho total / ancho módulo)
```

| Caso | Qué pasa |
|---|---|
| **Más posiciones** (ej. 16K×8 con módulos 8K×8) | Hacen falta 2 módulos. Cada módulo usa 13 bits (2¹³ = 8K). Los bits de dirección que sobran (14 − 13 = 1) **seleccionan el módulo**: A13 = 0 → módulo 0 y A13 = 1 → módulo 1. |
| **Más ancho** (ej. 16K×16 con módulos 16K×8) | Hacen falta 2 módulos, y **se acceden los dos a la vez**. Uno guarda el byte alto y el otro el bajo. Comparten todas las líneas de dirección. |

> **Truco de examen:** primero verificá que los bits del bus alcanzan (16K = 2¹⁴ → 14 bits). Después calculá cuántos bits usa cada módulo. Los que sobran son el decodificador (chip select).

---

## 2. Memoria caché: para qué existe

**Problema.** La CPU es mucho más rápida que la DRAM. El ejemplo de la cátedra:
- CPU a 1 GHz → ciclo de 1 ns.
- DRAM con 50 ns de acceso.
- Un procesador segmentado de 5 etapas tiene dos etapas de CPU (decodificar y ejecutar, 1 ns cada una) y tres etapas que acceden a memoria (buscar instrucción, buscar operandos y escribir resultado, 50 ns cada una).
- Total: 2 ns de CPU contra 150 ns de memoria. Las etapas quedan desbalanceadas.

**Solución: jerarquía de memoria.** No se reemplaza la DRAM por memoria rápida, porque sería carísima. Se pone una **memoria pequeña, ultrarrápida y cara (caché, SRAM)** entre la CPU y la DRAM.

**Tres recursos que usan los micros modernos para rendir más:**
1. Arquitectura **superescalar** (RISC, instrucciones simples).
2. **Supersegmentación** (muchas etapas). Necesita técnicas para evitar riesgos en saltos condicionales.
3. **Caché** potenciada.

**Limitación:** la velocidad de la memoria, los buses y los periféricos. La CPU espera.

**Flujo:** CPU → controlador de caché → ¿está en caché?
- **Acierto (presencia):** entrega el dato.
- **Fallo (ausencia):** va a la memoria principal. Si tampoco está ahí, la MMU lo trae de memoria virtual (disco).

**Por qué funciona**
- **Localidad espacial:** se piden direcciones cercanas. Por eso en un fallo se trae el **bloque/línea completa**, no un solo dato.
- **Localidad temporal:** lo usado recién se vuelve a usar pronto.
- **Regla 80/20:** el 20% de los datos o programas se usa el 80% del tiempo.
- La caché es **transparente** para la CPU. La CPU siempre direcciona memoria principal y el controlador decide.
- La caché **no acelera la RAM ni el disco**. Acelera la transmisión de datos entre el procesador y ellos.
- Es **SRAM**, de 8 KB a 512 KB, con acceso de 3 a 10 ns.

### Componentes de la caché
| Componente | Qué es |
|---|---|
| **Controlador de caché** | Gobierna todo. Implementa los algoritmos y mueve la información. |
| **Directorio / bloque de etiquetas (RAM-CAM)** | Memoria de **acceso por contenido**. Guarda las etiquetas de las direcciones de MP que están en caché. |
| **Bloque de datos (SRAM)** | Guarda las réplicas, agrupadas en **líneas**. El "tamaño de la caché" es solo el tamaño de esta parte. |
| **Lógica de control** | Comparadores del ancho de la etiqueta. |

---

## 3. Fórmulas de rendimiento (las más tomadas)

```
Factor de velocidad = t_MP / t_caché
t medio             = α·tc + (1–α)·(tc + tp)      ← caché en SERIE
Eficacia            = t_caché / t medio
Rendimiento (%)     = eficacia × 100
Cantidad de bloques de MP = tamaño MP / tamaño de línea
```

- α = tasa de acierto.
- **Caché en paralelo** (se consulta MP al mismo tiempo): `t medio = α·tc + (1–α)·tp`.
- Con la fórmula en serie, un fallo cuesta `tc + tp`. Con la fórmula en paralelo, un fallo cuesta solo `tp`.

**Ejemplo de la cátedra (serie):** tc = 5 ns, tp = 50 ns, α = 0,9
- Factor de velocidad = 50/5 = **10**.
- t = 0,9·5 + 0,1·(5+50) = 4,5 + 5,5 = **10 ns**.
- Con t = 20 ns, la eficacia sería 5/20 = 25%.
- Si fuera en paralelo: 0,9·5 + 0,1·50 = **9,5 ns**.

**Ejemplo del PDF resuelto:** µP 2,5 GHz, tc = 3 ns, tp = 30 ns, α = 80%
- Período del µP = 1/2,5 GHz = 0,4 ns.
- Factor de velocidad = 30/3 = **10**.
- t = 0,8·3 + 0,2·(3+30) = 2,4 + 6,6 = **9 ns**.
- Eficacia = 3/9 = 0,33 → **rendimiento 33%**.
- **¿Cómo mejorar sin tocar hardware?** Cambiar el algoritmo de reemplazo para subir la tasa de aciertos.

> Si te dan la frecuencia del micro y no la usás en la cuenta, es un dato de distracción. En el PDF solo se usó para calcular el período (0,4 ns).

---

## 4. Organización de la caché (la parte más importante)

La dirección de memoria se parte en campos. **Lo que cambia entre organizaciones es qué campos hay.**

> Con estos campos, el parcial se resuelve **contando bits**.

| Organización | Campos de la dirección | Dónde puede ir un bloque |
|---|---|---|
| **Totalmente asociativa** | `ETIQUETA + POSICIÓN` | En **cualquier** línea |
| **Asociativa de 1 vía** (directa) | `ETIQUETA + LÍNEA + POSICIÓN` | En **una sola** línea fija |
| **Asociativa de N vías** | `ETIQUETA + CONJUNTO + POSICIÓN` | En cualquier vía del **conjunto** que le toca |

- **POSICIÓN** = log₂(bytes por línea). Elige el byte dentro de la línea.
- **LÍNEA** = log₂(cantidad de líneas de la caché).
- **CONJUNTO** = log₂(cantidad de conjuntos).
- **ETIQUETA** = lo que falta hasta completar los bits de dirección.

### Ejemplo base de la cátedra: MP 4 GB, caché 4 KB, línea 32 B
- Dirección = 32 bits (4 GB = 2³²).
- POSICIÓN = 5 bits (32 = 2⁵).
- Líneas de caché = 4 KB / 32 B = 128 (7 bits).
- Bloques de MP = 4 GB / 32 B = 128 M = 2²⁷.

| Organización | Etiqueta | Línea / Conjunto | Posición | Total |
|---|---|---|---|---|
| Totalmente asociativa | **27** | – | 5 | 32 |
| 1 vía | **20** | 7 (línea) | 5 | 32 |
| 2 vías (64 conjuntos de 2 líneas) | **21** | 6 (conjunto) | 5 | 32 |

### Totalmente asociativa
- Un **comparador por etiqueta**, todos en paralelo. La etiqueta que coincide indica la línea.
- ✅ Es la mejor tasa de aciertos. Aprovecha todas las líneas, y solo hay que reemplazar cuando la caché está llena. Sirve cualquier algoritmo de reemplazo.
- ❌ Etiquetas larguísimas, CAM grande y comparadores lentos y caros.

### Asociativa de 1 vía (correspondencia directa)
- La MP se divide en **grupos del tamaño de la caché**. El bloque *i* de cualquier grupo va a la línea *i*. Es como alumnos en bancos asignados.
- Bloques consecutivos de MP van a líneas consecutivas.
- ✅ Es la más barata: **un solo comparador**, sin CAM. **No necesita algoritmo de reemplazo**, porque lo decide la construcción. Es buena para código, que es lineal.
- ❌ Puede **expulsar un bloque aunque haya líneas libres**, si dos bloques caen en la misma línea. Por eso la tasa de fallos se dispara.

### Asociativa de N vías (intermedia)
- Las líneas se agrupan en **conjuntos**. N = líneas por conjunto = número de vías.
- El bloque va a un **conjunto fijo** (como en 1 vía), pero dentro del conjunto puede ir en **cualquier vía** (como en la totalmente asociativa).
- Hay **N comparadores**.
- ✅ Junta lo mejor de las otras dos. El reemplazo solo se aplica dentro del conjunto, y no se desaprovecha la caché.
- **Casos particulares:**
  - 1 conjunto → totalmente asociativa.
  - 1 línea por conjunto → 1 vía.

### Caché real: Pentium
- **2 cachés independientes** (datos e instrucciones), de 8 KB cada una, **asociativas de 2 vías**.
- Línea de **32 B**. Cada vía tiene **128 etiquetas** (128 × 32 = 4 KB por vía).
- Etiqueta de 20 bits + 2 bits de control (**WP** protege contra escritura, **V** indica línea válida).
- Dirección: **20 (etiqueta) + 7 (conjunto) + 5 (posición) = 32 bits**.
- Reemplazo: **LRU**.

### Tamaño de la caché
- Más grande **no siempre** es mejor: una caché más grande tarda más en buscar.
- El precio sube de forma exponencial. Con 8 veces más tamaño, la tasa de acierto sube solo ~4%.
- Lo que influye es el **algoritmo**. El tamaño óptimo está entre **32 KB y 256 KB**.

---

## 5. Cómo resolver el ejercicio tipo (el PDF resuelto)

**Datos del PDF:** 8 líneas por conjunto, 8 bytes por línea, etiquetas de 5 dígitos hexa.

**Primero sacá los anchos de campo:**
- Etiqueta = 5 hexa = **20 bits**.
- POSICIÓN = 8 posiciones → **3 bits**.
- LÍNEA = 8 líneas → **3 bits**.
- CONJUNTO = 7 bits (el enunciado indica conjunto 7F = último, o sea 128 conjuntos).

**Punto 1 – armar direcciones.** Primero filtrás los datos que son números ASCII (0x30 a 0x39). Salen:
- Línea 1 (etiqueta DEF10), posición 0 (dato 34).
- Línea 3 (AD057), posición 5 (dato 30).
- Línea 6 (DCBA0), todas las posiciones (30 a 37).

Después concatenás los campos según la organización:
- **Totalmente asociativa:** `etiqueta (20) + posición (3)` = 23 bits.
- **1 vía:** `etiqueta + línea (3) + posición (3)` = 26 bits.
- **N vías:** `etiqueta + conjunto 1111111 (7) + posición (3)` = 30 bits.

**Pasar a hexa:** juntás los bits y agrupás de a 4 **desde la derecha**. Ejemplo (totalmente asociativa, DEF10 pos 000): `110 1111 0111 1000 1000 0000` = **6F7880h**.

**Punto 2 – memoria física instalada = 2^(bits de dirección):**
- Totalmente asociativa: 23 bits → **8 MB**.
- 1 vía: 26 bits → **64 MB**.
- N vías: 30 bits → **1 GB**.

**Punto 3 – tamaño de caché N vías** = bytes por fila × cantidad de conjuntos = 64 B × 128 = **8 KB**.

**Punto 4 – bloque que se transfiere de MP en cada actualización** = tamaño de una línea = **8 B (64 bits)**.

**Punto 5 – caché del Pentium** = 128 conjuntos × 128 etiquetas × 32 B = **512 KB**.
Campos: etiqueta 20 + conjunto 7 + posición 5. Ojo: la cuenta es del enunciado hipotético. El Pentium real tiene 2 vías y 8 KB.

**Punto 8 – bloques de MP** = tamaño MP / tamaño de línea:
- 8 MB / 8 B = 1 M bloques.
- 64 MB / 8 B = 8 M bloques.
- 1 GB / 8 B = 128 M bloques.

> ⚠️ **Error en el PDF:** en la parte (b) 1 vía, las filas de DCBA0 muestran los bits de DEF10 (37BC430...37BC437). Lo correcto es `1101 1100 1011 1010 0000 110 xxx` = **372E830h a 372E837h**. Si lo copiás de ahí, hacelo con la etiqueta correcta.

---

## 6. Actualización de la caché (reemplazo)

Ante un fallo, se trae la **línea completa** de MP a una línea libre. Si no hay libre, hay que elegir una para reemplazar.
- Totalmente asociativa → se puede usar cualquier algoritmo.
- 1 vía → no hay elección, así que no hay algoritmo.
- N vías → se elige dentro del conjunto.

| Algoritmo | Idea | Pro / Contra |
|---|---|---|
| **RANDOM** | Elige al azar | Es el más simple y no usa bits de control. Es el de las cachés baratas. No aprovecha la localidad. |
| **LRU** (Least Recently Used) | Desaloja la que hace más tiempo que no se usa | Rinde ~10% mejor que RANDOM. Necesita muchos bits de estado, y por eso se usa pseudo-LRU. |
| FIFO | La que entró primero | – |
| LFU | La menos frecuentemente usada | – |
| NLU | La no usada últimamente | – |

**Carga de la línea**
- **Lectura directa (el dato pedido va primero):** la CPU sigue trabajando mientras se completa la línea. Rinde más, pero el diseño es más complejo.
- **El dato pedido va último:** es simple, pero la CPU espera toda la línea.

---

## 7. Actualización de la memoria principal (coherencia)

**Problema:** si la CPU escribe en la caché, la MP queda **desactualizada** (discordancia).

| Método | Cómo | Pro / Contra |
|---|---|---|
| **Escritura inmediata** (write-through) | Cada escritura en la caché se manda también a MP | Es seguro y no hay incoherencia. Pero satura el bus y baja el rendimiento. |
| **Escritura diferida** (write-back con buffers) | Acumula cambios y actualiza cuando el bus está libre | Es más rápido. Pero hay un riesgo temporal: un periférico puede leer un dato viejo. |
| **Escritura obligada** | Solo cuando no queda otra | Nunca falla. |

**La escritura obligada ocurre en dos casos:**
1. Se va a leer una posición de MP que está modificada en la caché.
2. Hay que **desalojar una línea modificada**.

---

## 8. Conexionado: serie contra paralelo

|  | **Serie** | **Paralelo** |
|---|---|---|
| Cómo | CPU ↔ caché ↔ bus del sistema. Todo pasa por la caché. | CPU y caché cuelgan del bus del sistema. La CPU consulta ambas a la vez. |
| t con acierto | tc | tc |
| t con fallo | **tc + tp** (penaliza) | **tp** (sin penalización) |
| ✅ | Si hay acierto no se usa el bus, y la E/S y el DMA trabajan en paralelo con la CPU. | La caché es **opcional**: se puede sacar o agregar. En el peor caso es más rápido que la serie. |
| ❌ | La caché es **obligatoria**. Con muchos fallos se sobrecarga el bus. | Todo pasa por el bus, que casi nunca se libera. Deja poco lugar a E/S y DMA. |

> Frase clave: **t_paralelo = tp < t_serie = tc + tp** en caso de fallo.

---

## 9. Temas del TP (sección 5.5 y 5.6), a nivel conceptual

El TP también pregunta cosas que **no están en estas diapositivas**. Salen del libro de Angulo, cap. 5. Resumen para que sepas qué responder:

- **Caché L1 (primaria):** dentro del procesador, la más rápida y la más chica.
- **Caché L2 (secundaria):** más grande y algo más lenta. Puede estar dentro del chip o afuera.
- **Caché L3:** más grande todavía. En los multinúcleo suele ser compartida.
- **Multiprocesador:** el problema es que cada CPU tiene su propia copia de un dato.
- **Protocolo de coherencia:** mantiene consistentes las copias de un mismo dato en distintas cachés.
- **MESI:** cada línea tiene 4 estados posibles:
  - **M**odified: modificada, es la única copia válida.
  - **E**xclusive: igual que MP, solo esta caché la tiene.
  - **S**hared: igual que MP, varias cachés la tienen.
  - **I**nvalid: inválida.

Si el profesor sigue el libro, conviene leer ese capítulo para L1/L2/L3 y MESI. Con estas definiciones llegás a lo básico.

---

## 10. Entrada/Salida

**Jerarquía:** periférico ↔ interfaz/controlador ↔ módulo de E/S ↔ bus ↔ CPU y memoria.

**Componentes**
- **Periféricos:** interactúan con el mundo externo.
- **Módulo de E/S:** gestiona la comunicación entre el procesador y el periférico. Puede controlar varios periféricos. Cada interfaz de un periférico se llama **PUERTO**.
- **Buses** (dirección, datos y control). Pueden ser sincrónicos o asincrónicos (con reloj o sin él).

**Funciones del módulo de E/S:**
1. Control y temporización.
2. Comunicación con el procesador.
3. Comunicación con los dispositivos.
4. Almacenamiento temporal de datos (buffer, para absorber la diferencia de velocidad).
5. Detección de errores.

**Interfaz.** Es hardware que une el periférico con el bus. Adecúa las señales, informa el estado del periférico y es la **puerta (PORT)** de los datos. No se encarga de la transferencia completa: la toma la CPU o el canal. Se compone de registros:
- **REP:** Registro de Estado del Periférico. Dice si el periférico está listo.
- **RB:** Registro Buffer. Es el puerto por donde pasan los datos.
- **RDI:** Registro de Direcciones de la Interfaz. Solo se usa con DMA, para direccionar la memoria.
- Otros: registro de errores, contador de palabras.

**Controlador.** Gobierna el periférico. Tiene un **buffer interno (RAM)** y una **lógica de control** que interpreta comandos y genera las señales. Ejemplo: el movimiento del cabezal de una impresora.

### Las 4 modalidades

| Modalidad | Quién mueve el dato | Costo para la CPU |
|---|---|---|
| **Programada** (sondeo, *polling*) | La CPU pregunta el REP una y otra vez, y después transfiere por RB | **Máximo**. La CPU espera sin hacer nada útil. |
| **Por interrupción** | El módulo avisa con **IRQ#** cuando está listo. La CPU, al final de cada instrucción, mira si hay una pendiente, ejecuta la **SAI** (subrutina de atención) y retoma. | Medio. La CPU puede hacer otras cosas mientras espera. Igual mueve ella cada dato. |
| **DMA** | El controlador DMA transfiere **directo entre E/S y memoria**, sin pasar por la CPU | Bajo. La CPU solo programa la transferencia y recibe una interrupción al final. |
| **Canal / IOP** | Un procesador dedicado de E/S maneja la transferencia completa | Mínimo. |

**Pasos del DMA**
1. Se programa el controlador: tipo de operación (L/E), cantidad de datos y direcciones.
2. Se programa la interfaz para que use DMA.
3. La interfaz queda lista y **solicita el DMA**.
4. El controlador transfiere dato por dato mientras controla el bus. Termina cuando el contador llega a **0**.
5. Avisa a la CPU **con una interrupción**.

- El DMA es **maestro** del bus.
- Debe usar el bus cuando la CPU no lo necesita, o forzar a la CPU a suspenderse. Esto último es lo más común y se llama **robo de ciclo (cycle stealing)**.

**Ejemplos con interrupciones del 8086:** `INT 21h` (servicios del sistema, como entrada y salida por teclado y pantalla) e `INT 10h` (video).

---

## ✅ Checklist de repaso rápido

- [ ] Sé calcular factor de velocidad, t medio (serie y paralelo), eficacia y rendimiento.
- [ ] Dada una caché y una MP, saco etiqueta, línea o conjunto y posición para cada organización.
- [ ] Sé pasar la dirección armada a hexa (agrupando de a 4 desde la derecha).
- [ ] Sé decir qué organización tiene cuántos comparadores y por qué.
- [ ] Sé qué organización no necesita algoritmo de reemplazo (1 vía) y por qué.
- [ ] Distingo escritura inmediata, diferida y obligada.
- [ ] Sé decir cuándo es obligatorio actualizar la MP (los 2 casos).
- [ ] Sé decir qué es REP, RB y RDI, y el orden de las 4 modalidades de E/S.
- [ ] Sé explicar robo de ciclo y los pasos del DMA.
- [ ] Sé armar una memoria de 16K×16 con módulos de 8K×8 y qué bit selecciona el módulo.
