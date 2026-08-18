<!-- Guardar como .claude/commands/research.md en el repo (o en el workspace).
     Se invoca con /research <pregunta>.
     Adaptado del research_codebase.md de HumanLayer. -->

---
description: Investiga el sistema y deja el resultado escrito en svc-docs/research/
---

Vas a investigar el sistema para responder a esta pregunta:

$ARGUMENTS

## Antes de empezar

1. Mira si ya existe research sobre esto en `svc-docs/research/`. Si lo hay,
   **léelo primero** y comprueba los hashes de commit de su cabecera contra el estado
   actual de los repos. Si sigue vigente, tu trabajo es ampliarlo, no repetirlo.
2. Lee `AGENTS.md` de la raíz del workspace y el del repo o repos implicados.
3. Lee `svc-docs/architecture/flow.md` si la pregunta cruza repos.

## Cómo investigar

- Trabaja sobre los repos del workspace. Si la pregunta cruza varios, síguelo hasta el
  final: el valor de este documento está justamente en los saltos entre repos.
- Anota fichero y línea de cada hallazgo sobre la marcha. Un hallazgo sin ruta no vale.
- Presta atención especial a los enlaces que no son imports: punteros en configuración,
  buckets, variables de entorno, convenciones de nombrado. Es lo que nadie encuentra
  dos veces.

## Qué NO hacer

- No propongas cambios. Esto es investigación, no diseño.
- No resumas lo que se ve leyendo el primer fichero. El valor está en lo que cuesta.
- No escribas lo que ya está en un AGENTS.md.

## Al terminar

Escribe `svc-docs/research/<AAAA-MM-DD>-<tema-en-kebab>.md` siguiendo la plantilla de
`_harness-kit/plantillas/research.md`, con la cabecera completa — incluidos los hashes
de commit actuales de cada repo que hayas tocado, que son lo que permite saber después
si esto caducó.

Luego dime en el chat: la ruta del fichero, la respuesta en tres líneas, y qué te
sorprendió.
