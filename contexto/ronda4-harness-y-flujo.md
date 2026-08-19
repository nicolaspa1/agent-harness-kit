# Ronda 4 — Vocabulario del harness, evidencia medida y capa de flujo

> Investigación de agosto de 2026. Diez agentes de research en paralelo, ~110 fuentes.
> Informe completo navegable: https://claude.ai/code/artifact/ea350ca8-7674-44d5-871a-7aa07b03a88d
>
> Las tres rondas anteriores buscaban **qué estrategia** adoptar. Esta busca dos cosas
> distintas: **el vocabulario** para nombrar lo que ya estamos haciendo (necesario para
> la charla) y **la capa de flujo de trabajo**, que las rondas anteriores no cubrían.

---

## 1. El marco: agente = modelo + harness

Birgitta Böckeler (Thoughtworks) publicó en martinfowler.com el 2 de abril de 2026 el
artículo que da nombre a todo esto. La fórmula es literal: **Agente = Modelo + Harness**,
donde el harness es todo lo que el equipo construye alrededor del modelo para poder
confiar en el resultado con menos supervisión.

Clasifica los controles en dos ejes que se cruzan. **Es la rejilla con la que conviene
auditar lo que ya tenemos.**

|  | **Computacional** (determinista, barato, fiable) | **Inferencial** (LLM de juez, lento, probabilístico) |
|---|---|---|
| **Guides** — dirigen *antes* de actuar | `options.release = 11`, módulos Gradle, tipos | AGENTS.md, el router, el flow.md |
| **Sensors** — observan *después*, permiten autocorregir | ArchUnit, japicmp, tests, compilador | revisión por agente, LLM-as-judge |

Dos citas suyas que sostienen el resto:

> "Por separado obtienes o un agente que repite los mismos errores (solo feedback) o un
> agente que codifica reglas pero nunca se entera de si funcionaron (solo feedforward)."

> "Ninguno de los dos atrapa de forma fiable algunos de los problemas de mayor impacto:
> diagnóstico equivocado, sobre-ingeniería y funcionalidades innecesarias, instrucciones
> mal entendidas."

Y la tesis que ordena la charla:

> "Un buen harness no debería buscar necesariamente eliminar del todo el input humano,
> sino dirigirlo a donde nuestro input es más importante."

### Esto valida las dos reglas del kit, y las coloca

La rejilla explica *por qué* funcionan las dos reglas que ya gobiernan este proyecto:

- **Regla 2 ("lo comprobable se comprueba")** es exactamente "prefiere la columna
  computacional sobre la inferencial". El 8,3 % de cumplimiento de arXiv 2604.09409 es
  la medición de lo mala que es la columna inferencial cuando se usa sola.
- **Regla 1 ("si lo descubre grepeando, no va en el harness")** es un criterio de
  contenido para la fila de guides.

**Hallazgo favorable para este sistema:** las cuatro estrategias que sobrevivieron a la
ronda 3 —módulos Gradle, ArchUnit, `options.release = 11`, japicmp— son **todas
controles computacionales**. Sin conocer el marco, el panel convergió en la columna que
la evidencia dice que funciona. Eso es material de charla.

### Harnessability

Otro concepto suyo: *"no todos los codebases son igualmente susceptibles de ser
harnesseados"* — depende de la fuerza del tipado, de los límites entre módulos, de las
propiedades del framework. **Java con Gradle es un caso de harnessability alta**: hay
compilador, hay ArchUnit, hay japicmp, hay frontera de módulo verificable. Un stack
dinámico no tendría casi nada de eso.

Su corolario incómodo, aplicable al proyecto de locks y a cualquier zona legacy:
*"el harness es más necesario justo donde es más difícil de construir."*

---

## 2. Cuánto compra el harness: la evidencia se contradice

A diferencia de casi todo lo demás, aquí hay mediciones públicas. Y apuntan a lados
distintos:

| Estudio | Qué aisló | Resultado |
|---|---|---|
| METR, feb 2026 | Scaffolds especializados (Claude Code, Codex) contra genéricos (ReAct) | Sin diferencia significativa. Opus + Claude Code ganó en el 50,7 % de las muestras bootstrap |
| Can Bölük, feb 2026 | Solo el formato de la herramienta de edición, sobre 15-16 modelos | **+15 puntos de media** sin reentrenar nada. Grok Code Fast pasó de 6,7 % a 68,3 %. Coste del experimento: 300 USD |
| Daniel Vaughan, abr 2026 | Mismo modelo, seis harnesses distintos | CORE-Bench: de 42 % con scaffold mínimo a 78 % con harness completo |

**La reconciliación, que es la conclusión práctica:** cambiar de un harness maduro a
otro mueve poco; cambiar un *componente concreto* mueve mucho. Y el dato que cierra el
círculo está en el estudio de Vaughan: una configuración de CLAUDE.md afinada por la
comunidad cerró casi toda la brecha de 16 puntos entre herramientas.

> **Importa menos qué herramienta uses que lo bien que la configures.**

Para un equipo pequeño esto es buena noticia: el trabajo de mayor retorno es escribir y
afinar ficheros, no construir ni comprar infraestructura.

### El contraargumento, que conviene tener presente

Boris Cherny, ingeniero líder de Claude Code, sobre su propia herramienta: *"toda la
salsa secreta está en el modelo. Y esto es el wrapper más delgado posible."* Y Gregor
Zunic (browser-use), aplicando la lección amarga: *"no envuelvas al LLM. Tampoco
envuelvas sus herramientas… tus helpers también son abstracciones. Bórralos."*

Reconciliación de Lilian Weng: las implementaciones concretas se pudren rápido, pero la
capa no desaparece — se reconstruye a un nivel más alto. **Corolario para el kit:**
prefiere convenciones ligeras y desechables (ficheros markdown, reglas de ArchUnit)
sobre herramientas propias pesadas. Lo único que trataría como infraestructura durable
son los loops de verificación.

---

## 3. La capa que faltaba: el flujo de trabajo

Kief Morris, también en martinfowler.com (4 de marzo de 2026), distingue tres posturas
posibles del humano:

- **Fuera del loop** — los agentes corren solos, tú solo fijas requisitos.
- **Dentro del loop** — inspeccionas manualmente cada artefacto.
- **Sobre el loop** — construyes y mantienes el harness que dirige a los agentes.

> "El lugar correcto para nosotros los humanos es construir y gestionar el loop de
> trabajo, en vez de dejárselo a los agentes o micro-gestionar lo que producen."

Y de ahí sale la regla operativa más útil de toda la investigación:

> "La forma 'dentro del loop' es arreglar el artefacto. La forma 'sobre el loop' es
> cambiar el harness."

### La regla de las dos veces

Cuando el agente comete el mismo error por segunda vez, **no arregles el código:
arregla el guide o el sensor.** Si se puede comprobar mecánicamente, es un sensor
(ArchUnit, un test, japicmp). Si hay que saberlo de antemano, es un guide (una línea en
el AGENTS.md del repo, o en el router si afecta a varios). La primera vez arreglas el
artefacto; la segunda, el sistema.

La documentación oficial de Claude Code coincide casi literalmente: su tabla de
disparadores dice *"Claude se equivoca dos veces con una convención → entrada en
CLAUDE.md"*.

### Cuánto revisar

Böckeler rechaza el binarismo (*"¿el vibe coding es bueno o malo? Ninguna de las dos,
porque depende"*) y propone decidirlo por ejes: **impacto × probabilidad ×
detectabilidad × longitud del ciclo de feedback**. Su prueba mental, que es la más
honesta que encontramos: **¿desplegarías este cambio si estuvieras tú de guardia esta
noche?**

Implementación mecánica con números de producción: Cloudflare escala la revisión por
riesgo —un MR trivial pasa por 2 agentes revisores, uno completo por 7 o más—, con
131 246 ejecuciones sobre 48 095 MRs, mediana de 3 min 39 s y 0,98 USD por revisión, y
el mecanismo de emergencia para saltarse el gate usado en el 0,6 % de los casos.

**Detalle robable:** entre esos siete revisores hay uno cuyo trabajo es **verificar el
AGENTS.md** contra el cambio. Es un mecanismo real contra la obsolescencia del contexto,
y encaja como sensor inferencial barato.

---

## 4. Mecánica de Claude Code que no teníamos

Todo verificado contra `code.claude.com/docs`.

### Dónde va cada cosa (tabla oficial)

| Tipo de conocimiento | Dónde va | Cuándo carga | Coste |
|---|---|---|---|
| Reglas siempre válidas, comandos que no puede adivinar | `CLAUDE.md` | Cada sesión, completo | En cada request |
| Convenciones de un tipo de fichero o carpeta | `.claude/rules/*.md` con `paths:` | Solo al abrir fichero que casa | Cero hasta dispararse |
| Referencia que se usa a veces | Skill | Solo la descripción al inicio | ~450 tokens todas juntas |
| Flujo repetible que tú disparas | Skill con `disable-model-invocation` | Al teclear el comando | Cero hasta invocarlo |
| **Tarea que lee muchos ficheros** | **Subagente** | A demanda, ventana propia | **Cero contra tu ventana** |
| Guardarraíl sin excepciones | Hook | Determinista, en su evento | Cero |

### El subagente resuelve el problema que originó este proyecto

El planteamiento del proyecto es: *"el agente tiene que redescubrir el sistema a base de
greps, quemando contexto"*. La documentación oficial trae el número exacto del
mecanismo que lo arregla: **un subagente gastó 6 100 tokens leyendo ficheros y devolvió
420 a la conversación principal.** Anthropic lo generaliza: exploran usando *"decenas de
miles de tokens y devuelven un resumen destilado, a menudo de 1 000 a 2 000"*.

Su system prompt, su CLAUDE.md y sus lecturas cuestan **cero** contra la ventana
principal. Para rastrear un flujo entre repos —justo lo que hacen los prompts 02 y
03— esto es el mecanismo correcto, no un índice.

### Trampa de compactación

Tras compactar, el `CLAUDE.md` **raíz se relee del disco y se reinyecta**. Los CLAUDE.md
anidados y las reglas con `paths:` **no** — vuelven solo cuando el agente toca un
fichero que las active. Los skills se readjuntan con tope de 5 000 tokens cada uno y
25 000 en total.

**Consecuencia para el kit:** el router va en la raíz del workspace. Lo que no puede
perderse, arriba.

### Presupuestos oficiales

- `CLAUDE.md`: **menos de 200 líneas**. La comunidad (HumanLayer) baja a menos de 60,
  partiendo de que los modelos siguen ~150-200 instrucciones con consistencia y el
  prompt de sistema ya consume unas 50.
- `SKILL.md`: menos de 500 líneas. Descripción truncada a 1 536 caracteres.
- Test por línea, textual: *"¿quitar esto haría que Claude cometa errores? Si no,
  córtala."*

### `@import` no ahorra contexto

Confirmado en la documentación oficial: los ficheros importados **se cargan enteros al
arrancar**. Sirven para organizar, no para adelgazar. Muchas guías de blog recomiendan
lo contrario y se equivocan.

---

## 5. El router: reconciliación con la regla 1

La regla 1 del kit dice que si el agente lo descubre grepeando, no va en el harness, y
cita arXiv 2602.11988: los ficheros que **resumen el repositorio** no mejoran la tasa de
éxito y encarecen la inferencia más de un 20 %.

Eso parecería condenar el router. No lo condena, y la distinción es exactamente el
criterio de la propia regla:

> Un resumen de repo duplica lo que el grep encuentra. **Un router de workspace contiene
> justo lo que el grep NO puede encontrar**, porque vive en un repo que el agente no ha
> abierto, o no vive en ningún fichero.

El ejemplo canónico está en nuestro propio `CLAUDE.md`: *"el acoplamiento
svc-control → svc-lib no es un import. Solo existe en runtime: ninguna herramienta de
análisis estático lo ve."* Ese hecho es ingrepeable por construcción. Por eso la sección
"acoplamientos que no están en el código" es la más importante de
`plantillas/AGENTS-router.md` — y por eso el resto del fichero debe mantenerse famélico.

**Criterio de admisión al router, en una línea:** entra si un agente con acceso a todo
el workspace no podría deducirlo leyendo código.

### El precedente de diseño: el repo map de Aider

Aider construye con tree-sitter un mapa de símbolos y lo manda con cada request. Tres
decisiones robables:

1. **Presupuesto fijo y pequeño: 1 000 tokens por defecto.** No "todo lo importante".
2. **Rankea, no vuelca.** PageRank personalizado que castiga lo genérico (0,1× si el
   símbolo se define en más de cinco sitios) y premia lo específico (10× si el nombre es
   largo, 10× si el usuario lo mencionó), más búsqueda binaria para clavar el
   presupuesto.
3. **Se invalida por `mtime`**, no por calendario.

### La contra: Anthropic diseñó Claude Code en contra de los índices

Textual: *"primitivas como glob y grep le permiten navegar su entorno y recuperar
ficheros justo a tiempo, sorteando efectivamente los problemas de índices obsoletos y
árboles sintácticos complejos"*. Y: *"sugerimos empezar con búsqueda agéntica, y añadir
búsqueda semántica solo si necesitas resultados más rápidos"*.

**Reconciliación:** el router no compite con la búsqueda, la enfoca. No dice "aquí está
el código" —eso se pudre— dice "para este tipo de cambio, mira estos dos repos y este
contrato". Punteros, no contenido. Lo que ahorra no son greps: es **deambular**.

### Lección de SCIP, aplicable a este stack

SCIP (Sourcegraph) es el único sistema cuyo diseño apunta explícitamente a navegación
entre repos, y su lección es directa: **ancla los punteros a identidad de paquete, no a
rutas de fichero.** Las rutas son lo primero que se pudre; las coordenadas de paquete y
versión son estables.

Traducido a este sistema: apunta a **coordenadas Maven/Gradle y al nombre del artefacto
en el bucket**, no a `src/main/java/…/Handler.java`.

### Un dato que respalda hacerlo a mano

Devin tiene índice remoto computado por repo **y además** un sistema de conocimiento que
es texto curado a mano, fijable a un repo o a todos, que *"recupera cuando es relevante,
no todo junto ni al principio"*. La empresa que más invirtió en indexado automático
resolvió el conocimiento organizacional transversal con prosa escrita por humanos.

---

## 6. Lo que se evaluó en esta ronda y NO conviene adoptar

Se suma a la lista de descartados del `CLAUDE.md`.

- **Superpowers, GSD y frameworks de proceso similares.** Ninguno tiene historia
  multi-repo: Superpowers no tiene estado entre repos y GSD declara el multi-repo
  explícitamente fuera de alcance. Roba de Superpowers solo la idea de escalar ceremonia
  (*"la ceremonia escala con la tarea; el gate de aprobación nunca"*), que son diez
  líneas, no un framework. GSD además cambió de repositorio tres veces en ocho meses.
- **Orquestadores de workspace** (Conductor, Vibe Kanban, Sculptor, agent teams de
  Claude Code). Todos están construidos sobre "un repo, N worktrees": paralelizan
  trabajo *dentro* de un repo. No resuelven saber que cambió un contrato en otro. Los
  agent teams además consumen ~7× tokens. Durante la investigación Terragon cerró y
  Crystal quedó deprecado: es un ecosistema de menos de un año de vida media.
- **Ralph Wiggum** (agente en bucle infinito). Su propio autor: *"ni loco usaría Ralph
  en un codebase existente"* — es para greenfield. Lo robable es que el estado viva en
  ficheros y git, no en la ventana de contexto, y que el plan sea desechable: cuando se
  rompe, se regenera.
- **TDD obligatorio dentro del bucle del agente.** Böckeler lo midió el 10 de agosto de
  2026: *"no hubo diferencia claramente discernible entre el flujo con TDD y sin TDD"*,
  con 3 a 8,5× más tokens y agentes que *"a veces se saltaban o falseaban el paso rojo"*.
  No dice que los tests no sirvan —son el sensor computacional por excelencia— dice que
  obligar a la ceremonia del orden no compra nada.
- **Specs completos de ingeniería inversa.** Dos fuentes independientes convergen en
  acotar los specs al cambio, no al sistema. Markus Eisele (O'Reilly, agosto 2026)
  añade el criterio útil: los specs deben tener **estrategia de salida** — existir hasta
  que el equipo decida, y luego migrar su contenido durable a esquemas, tests y ADRs.
- **Symphony (OpenAI).** Es un spec abierto, Apache 2.0, pero asume mil millones de
  tokens diarios y cero revisión humana antes del merge. Robable: el *tech tracker*, una
  tabla markdown que el agente consulta al tocar lógica de negocio.

---

## 7. Fuentes principales

Informe completo con las ~110 fuentes:
https://claude.ai/code/artifact/ea350ca8-7674-44d5-871a-7aa07b03a88d

- Böckeler, *Harness engineering for coding agent users* — martinfowler.com, 2 abr 2026
- Böckeler, *TDD inside the agent loop* — martinfowler.com, 10 ago 2026
- Böckeler, *Context Engineering for Coding Agents* — martinfowler.com, 5 feb 2026
- Böckeler, *Anchoring AI to a reference application* — martinfowler.com, 25 sep 2025
- Morris, *Humans and Agents in Software Engineering Loops* — martinfowler.com, 4 mar 2026
- Joshi, *What Is Code?* — martinfowler.com, 12 may 2026
- METR, *Measuring Time Horizon using Claude Code and Codex* — 13 feb 2026
- Bölük, *The Harness Problem* — 12 feb 2026
- Vaughan, *The Harness Effect* — 19 abr 2026
- Anthropic, *Effective context engineering for AI agents* — 29 sep 2025
- Claude Code Docs — *features-overview*, *context-window*, *memory*, *large-codebases*
- Cloudflare, *Orchestrating AI Code Review at scale* — abr 2026
- Aider, *Repository map* + `repomap.py`
- Sourcegraph, *SCIP DESIGN.md*
- getsentry/sentry — `AGENTS.md` y `.claude/` como referencia real
