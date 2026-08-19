# Prompt 08 — Reproceso con la ronda 4

Se ejecuta **en sesión limpia**, con el agente abierto en `svc/`, **después** de haber
copiado al portátil los ficheros de la actualización de agosto (ver `CAMBIOS.md`).

Sirve para reconciliar el trabajo ya hecho con el vocabulario y los criterios nuevos,
**sin rehacerlo**. Si aún no has ejecutado ningún prompt numerado, sáltate este: empieza
por el 01 y la ronda 4 ya estará incorporada.

Produce `salida/08-reproceso.md`.

Copia desde aquí:

---

Estás en la raíz de un workspace con varios repositorios clonados como hermanos y
`_harness-kit/` al lado.

Se ha incorporado una cuarta ronda de investigación al kit. Tu trabajo **no** es rehacer
el análisis: es revisar lo ya producido a la luz de los criterios nuevos y decir qué
sigue en pie, qué cambia y qué falta.

## 1. Ponte al día

Lee, en este orden:

1. `_harness-kit/CLAUDE.md` entero, con atención a la sección **"La rejilla con la que
   clasificar cualquier propuesta"** y a las entradas nuevas de "Lo que ya se descartó".
2. `_harness-kit/contexto/ronda4-harness-y-flujo.md`.
3. Todos los ficheros que existan en `_harness-kit/salida/`.

Si `salida/` está vacío, dilo y para aquí: este prompt no aplica todavía.

## 2. Reclasifica lo ya producido

Coge cada hallazgo y cada propuesta que aparezca en `salida/` y colócalo en la rejilla
guide/sensor × computacional/inferencial. No cambies el contenido: clasifícalo.

Marca explícitamente:

- Las propuestas que resultan ser **guides inferenciales** (prosa en un fichero de
  contexto). Sobre 4.550 PRs agénticos, el cumplimiento de ese tipo de instrucción fue
  del 8,3 %. Para cada una: ¿puede ascender a sensor computacional? ¿Cómo, concretamente,
  en este stack?
- Las propuestas que ya eran **controles computacionales**. Estas se quedan como están.

## 3. Aplica los criterios nuevos

Revisa lo producido contra estos cuatro cambios de criterio y di, para cada uno, si algo
de lo que hay queda afectado:

**a) Criterio de admisión al router.** Entra si un agente con acceso a todo el workspace
no podría deducirlo leyendo código. Un resumen de repositorio no entra; un acoplamiento
que solo existe en runtime, sí. Revisa cualquier borrador de AGENTS.md que hayas
generado y **borra de él todo lo que sea inferible**.

**b) Apunta a identidad, no a rutas.** En los ficheros de contexto, sustituye referencias
del tipo `src/main/java/.../Handler.java` por coordenadas Gradle/Maven, nombre de módulo
o nombre del artefacto en el bucket. Las rutas se pudren; las coordenadas aguantan.

**c) El router va en la raíz del workspace.** Tras compactar contexto, Claude Code relee
y reinyecta el fichero raíz, pero no los anidados ni las reglas con `paths:`. Si algún
borrador coloca información crítica en un fichero anidado, señálalo.

**d) Lo que se descartó en la ronda 4.** Si alguna salida propone frameworks de proceso
(Superpowers, GSD), orquestadores de workspace, bucles tipo Ralph, TDD obligatorio
dentro del bucle del agente, o `@import` como técnica para ahorrar contexto — márcalo
como descartado y di por qué, citando la ronda 4.

## 4. Lo que la ronda 4 permite y antes no se contemplaba

Revisa si alguno de estos mecanismos resuelve algo que quedó abierto en las salidas
anteriores:

- **Subagentes para rastrear flujos entre repos.** Un subagente explora en su propia
  ventana y devuelve solo el resumen: en el ejemplo de la documentación oficial, gastó
  6.100 tokens leyendo y devolvió 420. Si en `salida/02` o `salida/03` quedó trabajo sin
  terminar por saturación de contexto, este es el mecanismo.
- **Un sensor inferencial en CI que verifique el fichero de contexto** contra el cambio
  del PR. Es lo que hace Cloudflare con uno de sus siete revisores.
- **Anclar a una aplicación de referencia** para detectar deriva de patrones, si varios
  repos comparten topología.

## 5. Salida

Escribe `_harness-kit/salida/08-reproceso.md` con:

- **Qué sigue en pie sin cambios.** Sé explícito: es la sección más importante y
  probablemente la más larga. No rehagas trabajo válido.
- **Qué cambia**, con el porqué y la referencia a la ronda 4.
- **Tabla de ascensos**: guides inferenciales que pueden convertirse en sensores
  computacionales, ordenada por relación valor/esfuerzo, con el cómo concreto.
- **Qué hay que borrar** de los borradores de AGENTS.md por ser inferible.
- **Qué queda abierto** y si alguno de los mecanismos del punto 4 lo destraba.

Cita siempre fichero y línea. Un hallazgo sin ruta no está verificado. No propongas nada
que requiera el permiso de otro equipo. Cuando no sepas algo, dilo.

---

Hasta aquí.

## Después

Si el reproceso deja la rejilla a medias, ejecuta `07-rejilla-harness.md` en sesión
nueva: es el que la completa contra el código y produce los ejemplos para la charla.
