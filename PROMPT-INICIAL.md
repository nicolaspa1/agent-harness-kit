# Prompt inicial

Este es el primer mensaje que le pasas a Claude, con el agente abierto en la carpeta
`svc/` (la que contiene `_harness-kit/` y los repos clonados como hermanos).

Su único trabajo es orientarse y decirte si el terreno está bien montado. No analiza
nada todavía: eso lo hacen los prompts numerados, uno por sesión.

Copia desde aquí:

---

Estás en la raíz de un workspace de trabajo. Dentro tienes `_harness-kit/`, que es un
proyecto de análisis, y varios repositorios de código clonados como carpetas hermanas.

Antes de nada:

1. Lee `_harness-kit/CLAUDE.md` entero. Ahí está el contexto del sistema, las reglas
   que gobiernan cualquier propuesta, y la lista de lo que ya se descartó en
   investigaciones previas.
2. Lee `_harness-kit/README.md` para entender el procedimiento.
3. Lista los prompts disponibles en `_harness-kit/prompts/`.

Después, sin analizar el código todavía, haz esto:

4. Comprueba qué repositorios hay realmente clonados como hermanos de `_harness-kit/`.
   Para cada uno: nombre, si es un repo git, rama, último commit, lenguaje principal y
   sistema de build.
5. Contrasta lo que encuentres con la tabla de arquitectura de `CLAUDE.md`. Dime
   explícitamente: qué coincide, qué no coincide, y qué falta. **Si algo del CLAUDE.md
   resulta ser inexacto, dilo — está escrito a partir de lo que el usuario describió de
   memoria, no de una verificación.**
6. Dime si hay algo que impida arrancar: repos que faltan, permisos, tamaños raros.

Termina con:
- Un resumen de una línea por repositorio encontrado.
- Las discrepancias entre lo documentado y lo real.
- Tu recomendación de por cuál de los prompts numerados empezar y por qué.

No leas el código en profundidad, no propongas soluciones y no escribas ningún fichero.
Esto es solo reconocimiento del terreno.

---

Hasta aquí.

## Después

Cuando este responda, abres una **sesión nueva** y pegas el contenido de
`prompts/01-inventario.md`. Y así con cada uno, siempre en sesión limpia.

Si el reconocimiento revela que la arquitectura documentada en `CLAUDE.md` está
equivocada en algo, **corrige ese fichero antes de seguir**. Todo lo que venga después
se apoya en él.
