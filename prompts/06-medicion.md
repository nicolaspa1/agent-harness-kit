# Prompt 6 — Medición (antes y después)

Este prompt es distinto: **se ejecuta dos veces**. Una antes de aplicar ningún cambio
de harness, y otra después. Sin esto no puedes saber si algo funcionó, y sin esto no
tienes números para la charla.

## Cómo se hace

Elige **tres tareas reales** que sean representativas de tu día a día. Sirven mejor
tareas que ya hiciste, porque sabes cuál era la respuesta correcta. Por ejemplo:

- una que toque un solo repo,
- una que toque dos,
- una que requiera entender el flujo completo.

Escríbelas como las escribirías normalmente. **No las adaptes al agente.**

Para cada tarea, en una **sesión completamente limpia**:

1. Lanza la tarea pidiendo únicamente un **plan**, no la implementación.
2. Mientras corre, no ayudes. No des pistas. Deja que se equivoque.
3. Al terminar, registra:

| Métrica | Cómo se obtiene |
|---|---|
| Llamadas a herramientas | Cuenta los greps, reads y búsquedas de la transcripción |
| Ficheros leídos | Cuántos abrió, y cuántos de ellos eran relevantes |
| Tiempo hasta el plan | Reloj |
| ¿El plan era correcto? | Tu juicio: correcto / parcialmente / no |
| ¿Qué se le escapó? | Lo que un miembro del equipo sí habría sabido |
| Coste | `/cost` si tu herramienta lo reporta |

La columna **"qué se le escapó"** es la más valiosa de todas. Cada cosa de esa lista es
un candidato directo a entrar en el harness — y es la única forma honesta de decidir
qué escribir, en vez de escribir lo que nos parece.

## Salida

`_harness-kit/salida/06-medicion-ANTES.md` la primera vez.
`_harness-kit/salida/06-medicion-DESPUES.md` la segunda.

Estructura: las tres tareas, la tabla de métricas de cada una, y la lista acumulada de
"qué se le escapó".

## Para la charla

Cuando tengas las dos mediciones, la comparación es tu dato propio. Vale más que
cualquier estadística de un blog, porque es de tu sistema y nadie te la puede discutir.

Y si el después **no** mejora, ese también es un resultado que merece contarse. Es más
creíble que una charla donde todo funciona.
