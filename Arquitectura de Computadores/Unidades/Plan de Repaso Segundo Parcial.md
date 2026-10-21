# Plan de repaso: Segundo parcial (unidades 8 a 13)

## Repaso base

[[Repaso Unidades anteriores a 8-12]] ya está actualizado. Cubre la unidad 7 y las ideas base, y su sección 10 junta lo que se usa de las unidades 1 a 6: complemento a 2, little endian, potencias de 2 y bits de dirección, frecuencia y período, decodificadores, paridad y buses. Tiene además 20 ejercicios de opción múltiple con clave.

---

## Paso 1: base mínima (unidades 1 a 7, solo lo que se usa después)

- [ ] Leer [[Repaso Unidades anteriores a 8-12]] completo (la sección 10 es la de las unidades 1 a 6) y hacer los 20 ejercicios de opción múltiple del final.
- [ ] Prestar atención a: hexa ↔ binario, complemento a 2, little endian, bits de dirección, y qué hacen MAR, MDR, IP y el reloj (unidad 7).
- [ ] No estudiar las unidades 1 a 6 enteras. Si algo de la sección 10 no queda claro, ahí están los enlaces a los resúmenes completos.

**Meta:** poder pasar `FF` a −1 y calcular la dirección física de `3A2B:1301` sin mirar.

## Paso 2: unidad 8 (registros)

Nota: [[Resumen Unidad 8 Microprocesadores - Registros]]
- [ ] Leer las secciones 1 a 7.
- [ ] Memorizar:
  - la tabla de registros generales;
  - los **flags** (CF, ZF, SF, OF, PF, AF);
  - los 6 registros de segmento;
  - modo real contra modo protegido.
- [ ] Hacer la sección 8 (el TP) sin mirar las respuestas y comparar después.

## Paso 3: unidad 10 antes que la 9 (instrucciones y direccionamiento)

Nota: [[Resumen Unidad 10 Microprocesadores - Instrucciones]]
- [ ] Leer las secciones 1 a 10: los modos de direccionamiento, con un ejemplo de cada uno.
- [ ] Hacer las preguntas 5 a 12 del TP (ADD, SUB, CMP, AND, ROL, SHLD), que son cuentas con flags.
- [ ] Hacer la sección 12 (DEBUG), tipeando los programas en un emulador si se puede. Si no, seguir los volcados a mano.

Se hace antes que la 9 porque la pila y las instrucciones `INT`, `CALL` y `RET` se entienden mejor así.

## Paso 4: unidad 9 (interrupciones)

Nota: [[Resumen Unidad 9 Microprocesadores - Interrupciones]]
- [ ] Leer todo.
- [ ] Aprender:
  - la IDT en modo real (dirección del vector = `n × 4`);
  - las fases de atención;
  - la diferencia entre interrupción y excepción.
- [ ] Hacer el ejemplo de la sección 10 (INT 21h → `0084`).
- [ ] Hacer las 14 preguntas del TP.

## Paso 5: unidad 11 (memoria y segmentación)

Nota: [[Resumen Unidad 11 Segmentación]]
- [ ] Leer las secciones 1 a 13. De la parte de memorias (RAM, DRAM, ROM, tipos) alcanza con saber diferenciarlas.
- [ ] Hacer los ejercicios de la sección 14: dirección física en modo real y decodificar selectores. Practicar con 5 selectores distintos.
- [ ] Hacer las 20 preguntas del TP (sección 15).

## Paso 6: unidad 12 (paginación)

Nota: [[Resumen Unidad 12 Direccionamiento en Modo Protegido - Paginación]]
- [ ] Leer las secciones 1 a 14. El corazón es: dirección lineal = 10 + 10 + 12 bits, CR3, fallo de página y TLB.
- [ ] Hacer los ejercicios de la sección 15 con 3 direcciones lineales distintas.
- [ ] Hacer las 8 preguntas del TP (sección 16).

## Paso 7: unidad 13 (caché y E/S)

Nota: [[Resumen Unidad 13 Memoria Cache y Entrada Salida]]
- [ ] Es lo más cuenta-intensivo. Primero las fórmulas (sección 3) y la tabla de campos por organización (sección 4).
- [ ] Rehacer a mano el ejercicio del PDF (sección 5): campos, direcciones en hexa, tamaños y tiempos.
- [ ] Leer E/S (sección 10), con foco en las 4 modalidades y los pasos del DMA.

## Paso 8: integración

- [ ] Armar un cuadro propio con las **traducciones de dirección**: modo real → selector/descriptor → lineal → directorio/tabla → física. Une las unidades 8, 11 y 12.
- [ ] Rehacer de memoria los ejercicios que más costaron.
- [ ] Armar una hoja de una carilla con fórmulas y flags.
- [ ] Si hay parciales viejos, resolverlos como práctica final, con tiempo.

---

## Distribución sugerida de tiempo

| Tema | Peso aproximado |
|---|---|
| Pasos 1 a 3 (base, registros, instrucciones) | 30% |
| Pasos 4 a 6 (interrupciones, segmentación, paginación) | 35% |
| Paso 7 (caché y E/S) | 20% |
| Paso 8 (integración y práctica) | 15% |

El tiempo extra va a donde más cueste en el paso 8. Las cuentas se aprenden haciéndolas, no leyéndolas.
