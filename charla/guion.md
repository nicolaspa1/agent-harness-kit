# Guion — *Si la IA no hace lo que quiero, es un smell de harness insuficiente*

**Duración base: 30 min.** Los bloques marcados `[+]` se estiran para llegar a 45.
Los bloques marcados `[recortable]` son los primeros que caen si vas mal de tiempo.

Regla de construcción: **ningún bloque de teoría sin un número o un fichero:línea
detrás.** Una charla solo de taxonomía se olvida el lunes.

Material de apoyo: `contexto/ronda4-harness-y-flujo.md` y la salida del
`prompts/07-rejilla-harness.md`, que es la que te da los ejemplos propios.

---

## 0 · El síntoma (3 min)

Abre con **un caso real tuyo**, con fichero y línea. Sin teoría, sin definiciones.
El agente hizo algo que no querías. Cuéntalo como se lo contarías a un compañero.

Candidato fuerte de este sistema: **el acoplamiento svc-control → svc-lib no es un
import.** El JAR se sube a un bucket y svc-control le pasa la ruta a la aplicación al
crearla. Solo existe en runtime. Ninguna herramienta de análisis estático lo ve —
y el agente tampoco, por bueno que sea.

Cierra el bloque con la pregunta que estructura todo lo demás:

> No es que el modelo sea tonto. Es que le pedí que adivinara algo que no está escrito
> en ninguna parte. **¿Dónde debería haber estado?**

---

## 1 · Las tres capas, como herramienta de diagnóstico (6 min)

Aquí van tus láminas. El giro importante: no las presentes como taxonomía, preséntalas
como **triaje**. La pregunta útil no es "¿qué le pongo al agente?" sino **"¿en qué capa
está el hueco?"**

| Capa | Pregunta | Se rompe cuando… |
|---|---|---|
| **Harness** | ¿Cómo ejecuta el agente? | No puede usar herramientas, observar resultados ni iterar |
| **Context engineering** | ¿Con qué información razona? | Tiene buenas herramientas pero no sabe qué fichero importa ni qué restricción manda |
| **Orquestación** | ¿Cómo se coordina el trabajo? | Funciona en tareas aisladas pero es frágil en trabajo largo |

El recurso de "sin X qué pasa" de tus láminas es lo mejor que tienen. Úsalo tal cual.

Vuelve al caso del bloque 0 y clasifícalo en voz alta: **el bucket no es un fallo de
harness ni de orquestación. Es un agujero de contexto.** Que la audiencia vea el triaje
funcionando sobre un caso concreto antes de darles más teoría.

### `[recortable]` La precisión sobre el humo

Tu propia lámina de orquestación ya lo dice: *"no toda coordinación es multi-agent
fuerte; muchos son flujos de trabajo orquestados"*. Apóyate en eso treinta segundos.
Dato para respaldarlo: durante la investigación de agosto, Terragon cerró y Crystal
quedó deprecado. **Es un ecosistema de menos de un año de vida media.**

---

## 2 · El término está en disputa (2 min)

No lo escondas: te posiciona.

- **La de las láminas** (Schmid, Farrelly): el harness es la capa de *ejecución* — bucle,
  herramientas, permisos, estado. El contexto es una capa aparte.
- **La de Böckeler** (martinfowler.com, abril 2026): **agente = modelo + harness**, y el
  contexto está *dentro* del harness. Los ficheros de contexto son *guides*; los tests
  son *sensors*.

> Las dos sirven, para cosas distintas. La primera te dice **en qué capa** está el
> hueco. La segunda te dice **qué tipo de control** tienes que poner. El resto de la
> charla usa la segunda.

Transición limpia al bloque 3.

---

## 3 · La rejilla: qué tipo de control poner (6 min) `[+]`

Dibuja la rejilla y déjala en pantalla el resto de la charla.

|  | **Computacional** (determinista) | **Inferencial** (LLM) |
|---|---|---|
| **Guide** — dirige *antes* de actuar | `options.release = 11`, módulos Gradle, tipos | AGENTS.md, el router, el flow.md |
| **Sensor** — observa *después*, permite autocorregir | ArchUnit, japicmp, tests, compilador | revisión por agente, LLM de juez |

Cita de Böckeler para lámina:

> "Por separado, obtienes o un agente que repite los mismos errores (solo feedback) o un
> agente que codifica reglas pero nunca se entera de si funcionaron (solo feedforward)."

### El número que cambia la conversación

> **8,3 %.** Sobre 4.550 PRs agénticos en 81 repositorios, ese fue el cumplimiento de
> las instrucciones que vivían en un AGENTS.md. *(arXiv 2604.09409)*

Déjalo respirar. Y remata:

> **Escribir una regla en prosa es casi no escribirla.** Una regla de ArchUnit se cumple
> el 100 % de las veces, porque rompe la build.

De ahí sale la regla operativa que se llevan a casa:

> **La regla de las dos veces.** Cuando el agente comete el mismo error por segunda vez,
> no arregles el artefacto: arregla el guide o el sensor. Si se puede comprobar, es un
> sensor. Si hay que saberlo de antemano, es un guide.

Kief Morris lo formula igual desde el otro lado: *"la forma 'dentro del loop' es
arreglar el artefacto; la forma 'sobre el loop' es cambiar el harness."*

### `[+]` Harnessability

*"No todos los codebases son igualmente susceptibles de ser harnesseados."* Java con
Gradle es un caso alto: compilador, ArchUnit, japicmp, frontera de módulo verificable.
Y el corolario incómodo, que conecta con cualquier zona legacy de tu sistema:

> "El harness es más necesario justo donde es más difícil de construir."

---

## 4 · ¿Esto mueve la aguja de verdad? (5 min)

El bloque que te separa de una charla de opinión. Tres mediciones **que se contradicen**,
y la reconciliación es la moraleja.

| Estudio | Qué aisló | Resultado |
|---|---|---|
| METR, feb 2026 | Scaffolds especializados vs. genéricos | **Sin diferencia significativa** |
| Bölük, feb 2026 | Solo el formato de la herramienta de edición | **+15 puntos** de media en 15 modelos. Grok Code Fast: 6,7 % → 68,3 %. Coste: 300 USD |
| Vaughan, abr 2026 | Mismo modelo, seis harnesses | CORE-Bench: **42 % → 78 %** |

> Cambiar de un harness maduro a otro mueve poco. Cambiar **un componente concreto**
> mueve muchísimo.

Y el dato que cierra el círculo, del propio estudio de Vaughan: una configuración de
CLAUDE.md afinada por la comunidad cerró casi toda la brecha de 16 puntos entre
herramientas.

> **Importa menos qué herramienta uses que lo bien que la configures.**

Para un equipo pequeño esto es una buena noticia y conviene decirlo así: el trabajo de
mayor retorno es escribir ficheros, no comprar plataformas.

---

## 5 · `[recortable]` `[+]` Las tres corrientes spec-* (5 min)

Preséntalo como **caso de estudio de cuándo la ceremonia deja de pagar**, no como
recomendación. La taxonomía es de Böckeler y se enseña en un minuto:

| Corriente | Qué pasa con el spec después | Quién lo persigue |
|---|---|---|
| **spec-first** | Se descarta. Es líquido de arranque | Kiro, Spec Kit en la práctica |
| **spec-anchored** | Vive como capa permanente de gobierno | Formalizado académicamente en 2026 |
| **spec-as-source** | El spec *es* la fuente; el código se genera y es desechable | Solo Tessl |

### La conexión que hace que este bloque valga

**Un spec es un guide inferencial.** O sea: hereda el 8,3 %.

> Escribir más markdown no arregla que el markdown se cumpla poco.

Por eso Böckeler, que acuñó la taxonomía, es escéptica de sus propias herramientas:

> "Preferiría revisar código antes que todos estos ficheros markdown."

Y le pone nombre alemán: **Verschlimmbesserung**, una mejora que empeora. Thoughtworks
lo tiene en *Assess*, no en *Adopt*. El modo de falla más reportado es aplicar el
proceso completo a un trabajo que necesitaba un párrafo y un test.

Remate del bloque, que es la regla 2 del kit demostrada desde otro ángulo:

> **El spec no compite con el test. El test gana.**

---

## 6 · El límite honesto (3 min)

El bloque que te salva de sonar a vendedor, y el que la gente agradece.

> "Ninguno de los dos [ni guides ni sensors] atrapa de forma fiable algunos de los
> problemas de mayor impacto: diagnóstico equivocado, sobre-ingeniería y funcionalidades
> innecesarias, instrucciones mal entendidas."

Y el porqué, que es la mejor cita de toda la investigación:

> "Un agente de código no tiene nada de esto: ni responsabilidad social, ni asco
> estético ante una función de 300 líneas, ni la intuición de que 'aquí no lo hacemos
> así', ni memoria organizacional."

De ahí la tesis en su forma definitiva:

> "Un buen harness no busca eliminar el input humano, sino **dirigirlo a donde más
> importa**."

Si tienes tiempo, añade el contrapunto — que Boris Cherny, ingeniero líder de Claude
Code, dice de su propia herramienta que es *"el wrapper más delgado posible"* y que
*"toda la salsa secreta está en el modelo"*. Que la audiencia vea que el campo no está
de acuerdo consigo mismo.

---

## 7 · Cierre: tus tres ejemplos (3 min)

Tres casos propios, **con fichero y línea**, en el formato:

> *La IA no hizo lo que yo quería. Aquí está el hueco de harness que lo explica.
> Y aquí está la casilla de la rejilla donde debería haber estado el control.*

Los produce el `prompts/07-rejilla-harness.md`. **No inventes estos ejemplos: sácalos
del código real.** Es lo que separa esta charla de las cincuenta que hay en YouTube.

Última lámina, una sola frase:

> **Si la IA no hace lo que quiero, es un smell de harness insuficiente.**
> La pregunta no es qué modelo usar. Es en qué casilla falta el control.

---

## Trampas a evitar

- **No prometas certeza.** Böckeler: *"mientras haya LLMs de por medio, nunca podemos
  estar seguros de nada."* Habla en probabilidades.
- **No vendas orquestadores.** A nivel multi-repo todos son "un repo, N worktrees":
  paralelizan dentro de un repositorio, que es otro problema.
- **No cites cifras de blogs de marketing.** Buena parte del material de 2026 tiene
  números atractivos sin metodología. Las tres del bloque 4 y el 8,3 % sí tienen paper
  o post con método detrás; el resto, fuera.
- **No presentes el kit como una solución.** Es un procedimiento para averiguar qué
  solución sirve. Tres rondas confirmaron que no existe framework publicado que resuelva
  compartir contexto entre repos con mecanismo y métricas.

---

## Fuentes para la lámina final

- Böckeler, *Harness engineering for coding agent users* — martinfowler.com, abr 2026
- Morris, *Humans and Agents in Software Engineering Loops* — martinfowler.com, mar 2026
- METR, *Measuring Time Horizon using Claude Code and Codex* — feb 2026
- Bölük, *The Harness Problem* — feb 2026
- Vaughan, *The Harness Effect* — abr 2026
- arXiv 2604.09409 (cumplimiento 8,3 %) · arXiv 2602.11988 (ficheros que resumen repos)
- Informe completo: https://claude.ai/code/artifact/ea350ca8-7674-44d5-871a-7aa07b03a88d
