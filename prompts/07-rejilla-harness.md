# Prompt 07 — La rejilla del harness

Se ejecuta **después del 04**, en sesión limpia, con el agente abierto en `svc/`.
Produce `salida/07-rejilla.md`: el inventario de tu harness clasificado, los huecos, y
el material para la charla.

Este prompt no propone estrategias nuevas — eso ya lo hizo el 04. Clasifica lo que hay
y lo que se propuso, y busca lo que falta.

Copia desde aquí:

---

Estás en la raíz de un workspace con varios repositorios clonados como hermanos y
`_harness-kit/` al lado.

Lee primero `_harness-kit/CLAUDE.md` (sección "La rejilla con la que clasificar
cualquier propuesta") y los ficheros que existan en `_harness-kit/salida/`.

Tu trabajo es clasificar el harness de este sistema en esta rejilla:

|  | Computacional (determinista) | Inferencial (LLM) |
|---|---|---|
| **Guide** (dirige antes de actuar) | | |
| **Sensor** (observa después, permite autocorregir) | | |

## 1. Inventario de lo que YA existe

Recorre los repos y encuentra los controles que ya están puestos, aunque nadie los
llamara así. Busca al menos: configuración del compilador y `release`/`source`/`target`,
plugins de Gradle, reglas de ArchUnit o equivalentes, checkstyle/spotless/errorprone,
tests y su cobertura real de las fronteras, gates de CI, validaciones en enums,
tipos que hacen imposible un estado inválido, ficheros AGENTS.md o CLAUDE.md existentes.

Para cada uno: **fichero y línea**, en qué casilla cae, y qué error concreto previene.
Si no sabes qué error previene, dilo — probablemente sea un control heredado que nadie
ha revisado.

## 2. Clasifica lo propuesto

Coge las propuestas que sobrevivieron al prompt 04 y colócalas en la rejilla. Señala
cuáles son computacionales y cuáles inferenciales.

## 3. Los huecos

Con la rejilla llena, responde:

- **¿Qué casilla está vacía o casi vacía?** Un sistema sin sensores computacionales
  repite errores; uno sin guides los repite desde cero cada sesión.
- **¿Qué guides inferenciales podrían ser sensores computacionales?** Esta es la
  pregunta de mayor valor del prompt. Por cada regla escrita en prosa en algún AGENTS.md
  o que alguien repite de palabra, di si se puede convertir en algo que rompa la build,
  y cómo. Contexto: sobre 4.550 PRs agénticos, el cumplimiento de una instrucción que
  vivía en un AGENTS.md fue del 8,3 %.
- **¿Qué fronteras del sistema no tienen ningún control de ningún tipo?** Cruza esto con
  `salida/03-fronteras.md`. El acoplamiento por bucket entre svc-control y svc-lib es el
  sospechoso principal: no lo ve el compilador ni ninguna herramienta estática.

## 4. Harnessability por repo

Puntúa cada repo de 1 a 5 según lo fácil que sea ponerle controles computacionales
—tipado, límites de módulo, build propio, tests existentes— y justifica. Un repo con
harnessability baja necesita más supervisión humana, no más prosa.

## 5. Salida

Escribe `_harness-kit/salida/07-rejilla.md` con:

- La rejilla rellena, con fichero y línea en cada entrada.
- Tabla de "guides inferenciales que pueden ascender a sensores computacionales",
  ordenada por relación valor/esfuerzo, con el cómo.
- Las fronteras sin control.
- La puntuación de harnessability por repo.
- **Tres ejemplos concretos, con fichero y línea, de "la IA no hizo lo que quería y
  aquí está el hueco de harness que lo explica"** — para la charla.
- Lo que no hayas podido verificar, marcado como tal.

Cita siempre fichero y línea. Un hallazgo sin ruta no está verificado. No propongas
nada que requiera el permiso de otro equipo.

---

Hasta aquí.
