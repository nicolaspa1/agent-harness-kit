# Prompt 4 — Diagnóstico de harness y borradores

Lee los tres ficheros de `_harness-kit/salida/`: inventario, flujo y fronteras.

Este es el prompt que decide qué se hace. Los tres anteriores describían; este juzga.

## El criterio, y aplícalo con dureza

Hay medición detrás de estas dos reglas, así que no las ablandes:

**Regla 1 — Si el agente lo descubre grepeando, NO va en el harness.** Un estudio de
ETH/SRI sobre 288 configuraciones encontró que los ficheros de contexto que resumen el
repositorio no mejoran la tasa de éxito y encarecen la inferencia más de un 20%. Meter
un resumen del código en un AGENTS.md es activamente contraproducente.

**Regla 2 — Lo comprobable se comprueba, no se escribe.** Sobre 4.550 PRs agénticos, el
cumplimiento de instrucciones que vivían en un AGENTS.md fue del **8,3%**. Si una regla
se puede convertir en un test, un check de build o un lint que falle, ese es su sitio.
Escribirla en prosa es casi no escribirla.

## Lo que tienes que producir

### A. La clasificación

Coge todo lo aprendido en los tres documentos anteriores y clasifica cada pieza de
conocimiento en una de estas cuatro:

| Categoría | Destino |
|---|---|
| **Inferible** — el agente lo saca leyendo el código | A la basura. No se escribe. |
| **No inferible** — decisiones, historia, acoplamientos por configuración, cosas que viven fuera del repo | AGENTS.md |
| **Verificable** — invariantes que se pueden expresar como check | Un test o un check de CI |
| **Caro de reconstruir** — el flujo end-to-end, el research | Documento duradero en el repo de docs |

Sé estricto con la primera categoría. La mayoría de lo que la gente escribe en un
AGENTS.md cae ahí.

### B. Los borradores

Escribe, en `_harness-kit/salida/borradores/`:

- `AGENTS-router.md` — para la raíz del workspace. Máximo 40 líneas. Qué es cada repo
  en una línea, la tabla de "si el cambio es X, toca el repo Y", los acoplamientos
  invisibles del prompt 2, y las decisiones de diseño que un agente no puede deducir y
  que le harían proponer lo que ya se descartó.
- `AGENTS-<repo>.md` por cada repo. Máximo 25 líneas cada uno. Solo lo no inferible de
  ese repo. Punteros a dónde vive qué, no volcados.
- `flow.md` — el flujo del prompt 2, limpio, listo para el repo de documentación.

Usa las plantillas de `_harness-kit/plantillas/` como estructura, pero **rellénalas con
lo que has encontrado de verdad**, no con lo que la plantilla sugiere.

### C. Los checks candidatos

Lista los invariantes que se pueden convertir en algo que falla, ordenados por
(impacto ÷ esfuerzo). Para cada uno: qué protege, cómo se implementa, cuánto cuesta, y
si se puede hacer **sin pedirle permiso a nadie de otro equipo**. Marca claramente
cuáles son unilaterales: la investigación previa demostró que las estrategias que
empiezan pidiendo permiso a otro owner no se ejecutan nunca.

### D. El veredicto honesto

Una sección final que responda: **de todo esto, ¿qué merece la pena de verdad para
este sistema concreto?** Si la respuesta es "poco, porque el sistema es más simple de
lo que parecía", dilo. Si es "el problema no es el contexto sino otra cosa", dilo
también. Un diagnóstico que recomienda todo no es un diagnóstico.

## Salida

`_harness-kit/salida/04-diagnostico.md` con las secciones A, C y D, más los ficheros
de `borradores/`. Al terminar, dime en el chat las tres cosas que harías primero y
cuánto crees que costarían.
