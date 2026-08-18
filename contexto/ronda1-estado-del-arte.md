# Estado del arte: SDD, loops agénticos y contexto compartido entre repos
**Síntesis de 5 frentes de investigación verificados · corte 17-08-2026**

Nota de método: sólo entra material que un calificador adversarial confirmó abriendo la fuente. Lo rechazado se ha eliminado; lo no verificado va marcado como tal y concentrado en la sección 7. Cuando dos frentes se contradicen, se dice cuál tiene mejor evidencia.

---

## 1. A quién seguir

### Imprescindibles

| Persona | Dónde publica | Qué aporta que no aporten los demás | Idioma |
|---|---|---|---|
| **Birgitta Böckeler** (Thoughtworks) | [martinfowler.com/articles/harness-engineering.html](https://martinfowler.com/articles/harness-engineering.html) | La única taxonomía operativa del harness: *guides* (feedforward) vs *sensors* (feedback), cada uno computacional o inferencial. Y es la única que publica resultados que contradicen su propio bando: [TDD dentro del loop no mejoró código ni tests](https://martinfowler.com/articles/exploring-gen-ai/tdd-in-the-agent-loop.html) (10-08-2026). Implementación real en [sensors-cli](https://martinfowler.com/articles/sensors-for-coding-agents.html) (27-05-2026, 19 estrellas: es prueba de concepto, no adopción). | en |
| **Dex Horthy** (HumanLayer) | [github.com/humanlayer/advanced-context-engineering-for-coding-agents](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents) | Origen del artefacto que resuelve el dolor (`thoughts/shared/research/`) **y** la autocrítica más honesta que existe: en [wsff.md](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/wsff.md) (jul-2026) documenta que su fábrica lights-off degradó el codebase hasta requerir reescritura. Para una charla sobre harness es la fuente que impide que suene a folleto. | en |
| **Geoff Cooney** (mabl) | [mabl.com/blog/...75-repos](https://www.mabl.com/blog/how-we-built-a-system-for-ai-agents-to-ship-real-code-across-75-repos) y [parte 2](https://www.mabl.com/blog/how-we-built-a-system-for-ai-agents-to-ship-real-code-across-75-repos-part-2) | El **único** caso real multi-repo con números publicados *y con el límite publicado*. Apareció de forma independiente en tres de los cinco frentes. 25 ingenieros, 100+ repos, grafo de 850+ líneas cubriendo 79. Es el caso más cercano a tu topología. | en |
| **Lilian Weng** | [lilianweng.github.io/posts/2026-07-04-harness/](https://lilianweng.github.io/posts/2026-07-04-harness/) | Definición canónica de harness verificada palabra por palabra, y los **7 componentes editables** (system prompt, descripción de tool, implementación de tool, middleware, skill, config de subagente, memoria a largo plazo). Es tu esqueleto de slides. | en |
| **Kief Morris** (Thoughtworks) | [martinfowler.com/.../humans-and-agents.html](https://martinfowler.com/articles/exploring-gen-ai/humans-and-agents.html) | La frase exacta de tu tesis: *"The 'in the loop' way is to fix the artefact... The 'on the loop' way is to change the harness that produced the artefact so it produces the results we want."* (04-03-2026) | en |
| **Marcia Villalba** | [desplegando.substack.com](https://desplegando.substack.com/) | La mejor fuente en español y la cita-gancho verificada palabra por palabra: *"Un modelo de lenguaje solo no hace nada — es el harness el que lo convierte en un agente de verdad: orquesta el loop, ejecuta las herramientas y arma el contexto en cada vuelta."* ([10-08-2026](https://desplegando.substack.com/p/kiro-crew-mi-nuevo-companero-de-desarrollo)). Mantiene "harness" sin traducir. 2-4 posts/semana. Sesgo AWS fuerte. | es |
| **Daniel Primo** (Web Reactiva) | [webreactiva.com/blog/ai-harness](https://www.webreactiva.com/blog/ai-harness) · hub [ingeniería agéntica](https://www.webreactiva.com/ia/ingenieria-agentica) | Tiene el corpus en español más grande sobre esto (hub con 83 recursos), incluido un explicador dedicado de harness (24-03-2026, definición por componentes idéntica a la de Kiro) y un artículo específico de *loop engineering vs harness*, que es exactamente tu intersección. Un frente lo infravaloró como "mero curador" tras abrir un solo artículo suyo; el calificador lo desmintió. | es |
| **Clare Liguori, Romain Dura, Al Harris, Richard Threlkeld** (AWS Kiro) | [kiro.dev/blog/one-agent/](https://kiro.dev/blog/one-agent/) | El único post de ingeniería de 2026 que describe un harness de producción **desde dentro** y dibuja la frontera harness/cliente: el harness posee *"the agent loop, tool execution, sub-agent delegation, session management, configuration loading"*; ACP llegó a 1.0 en junio de 2026. | en |

### Opcionales

| Persona | Dónde | Qué aporta | Idioma |
|---|---|---|---|
| **Leonardo Gonzalez** | [trilogyai.substack.com](https://trilogyai.substack.com/) | Ver nota abajo. | en |
| **Armin Ronacher** | [lucumr.pocoo.org/2026/6/23/the-coming-loop/](https://lucumr.pocoo.org/2026/6/23/the-coming-loop/) | La voz escéptica que tu charla necesita: código defensivo, dependencia cognitiva, equipos que mergean código que no saben explicar. | en |
| **Addy Osmani** | [addyosmani.com/blog/agents-md/](https://addyosmani.com/blog/agents-md/) (23-02-2026) | El criterio operativo: *"can the agent discover this on its own by reading your code? If yes, delete it"*, más el patrón REFLECTION.md y la regla de que ningún agente escribe en AGENTS.md sin aprobación humana. | en |
| **Hari Krishnan** | [infoq.com/articles/enterprise-spec-driven-development](https://www.infoq.com/articles/enterprise-spec-driven-development) (19-02-2026) | El único que describe SDD multi-repo como proceso: un intent → sub-issues *específicos por repositorio* → tarea. | en |
| **Jesse Vincent (obra)** | [blog.fsck.com/2026/07/05/new-patterns/](https://blog.fsck.com/2026/07/05/new-patterns/) | Metodología como plugin versionado distribuible a 14+ harnesses, y "Scribble": un agente que abre tickets y actualiza el wiki interno solo. | en |
| **Simon Willison** | [simonw.substack.com/p/agentic-engineering-patterns](https://simonw.substack.com/p/agentic-engineering-patterns) | Termómetro: sus posts de jul-ago 2026 van de modelos, no de contexto de equipo. El tema aún no ha llegado al discurso general. Aprovechable: "linear walkthroughs". | en |
| **Jorge Hidalgo (deors)** | [speakerdeck.com/deors/...ecomputing-2026](https://speakerdeck.com/deors/desarrollo-guiado-por-especificaciones-at-jornadas-ecomputing-2026) (03-07-2026) | Calibración de registro para una charla de SDD en castellano. Marco conceptual, cero mecanismo. | es |
| **Steve Yegge** | [github.com/gastownhall/beads](https://github.com/gastownhall/beads) | El research como grafo de nodos con dependencias en vez de prosa. Anti-hype: bus factor 1, arquitectura reescrita en 2026 (SQLite+JSONL → Dolt), 702 issues abiertos. | en |

**Sobre Leonardo Gonzalez / Tribu AI, con honestidad.** Tres correcciones al punto de partida, todas verificadas: (1) la publicación se llama *Trilogy AI Center of Excellence*, es de Trilogy (portfolio de ESW Capital) donde él es VP, **no** de Tribu iA, y está **en inglés**; (2) el vínculo con Tribu iA es real pero es otro sombrero: es program head de Tribu IA Papers ([verificado en tribuiapapers.org](http://tribuiapapers.org/en/cohorts/cohort-3/)); (3) los posts son de autoría individual suya o de David Proctor por separado, no co-firmados.

Su contenido **hoy es derivativo**: de ~13 posts entre 17-jul y 16-ago-2026, la mayoría son roundups de modelos (Grok/GLM/Qwen/MiniMax). Lo único que sirve a tu charla es de **abril**: [*From Spec-Driven Work to Work Orchestration*](https://trilogyai.substack.com/p/from-spec-driven-work-to-work-orchestration) (10-04-2026, citas verificadas: orquestador Rust, OpenHands como execution harness, Linear como work source, *"repository policy comes from WORKFLOW.md"*) y [*GSD-2*](https://trilogyai.substack.com/p/gsd-2-and-the-next-step-in-agentic) (27-04-2026, *"GSD-2 moves the center of control outside the harness"*). Ambos son análisis o descripción de software de terceros/interno, no práctica con adopción demostrada, y **ninguno resuelve multi-repo**. Su aportación real es un eje narrativo: *orquestación dentro del harness vs fuera del harness*. No esperes material fresco suyo sobre harness: no lo hay desde hace cuatro meses.

**Dato de oro asociado y verificado**: el temario de Tribu IA Papers Cohorte 3 (1-jul a 30-sep-2026, LATAM, español) tiene como entregable de la semana 10 *"Minimal agent harness with tool calls, traces, failure handling, and an evaluation task"* y de la semana 11 *"Mini orchestration specification for the capstone: issues, states, workspaces, policies, review, retries, and recovery"*. Hay un programa formativo hispanohablante en marcha ahora mismo cuyo capstone es construir un harness. Eso es gancho de charla y canal de audiencia.

---

## 2. Lo que de verdad se está usando en agosto de 2026

Ordenado por madurez real. **[SOLO]** = práctica de un dev individual. **[EQUIPO]** = tiene mecanismo de propiedad y revisión.

### Mainstream (adopción amplia, estándar de facto)

**AGENTS.md anidado, resolución "el más cercano gana"** — **[EQUIPO]**
Artefacto: markdown plano, sin frontmatter obligatorio, en la raíz y anidado por subdirectorio. Lo escribe el equipo dueño a mano, lo lee cualquier agente al arrancar, se revisa en PR. [60.000+ proyectos, 20+ herramientas, stewardship de la Agentic AI Foundation bajo Linux Foundation](https://agents.md/); el repo principal de OpenAI tiene 88 ficheros.
*Límite duro para tu caso*: la resolución "más cercano gana" es un mecanismo de **árbol de directorios**. Cuatro AGENTS.md en cuatro repos separados no se ven entre sí. Es tu suelo, no tu solución.

**RPI (Research → Plan → Implement) con artefactos markdown fechados** — **[EQUIPO]**
`thoughts/shared/research/YYYY-MM-DD-*.md` con frontmatter obligatorio (`date`, `researcher`, `git_commit`, `branch`, **`repository`**, `topic`, `tags`, `status`, `last_updated`); `thoughts/shared/plans/` para los planes. Comandos en `.claude/commands/research_codebase.md`. Lo escribe el agente, lo **revisa el humano antes de que se escriba código**. [Fichero vigente en main](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md).
*Aviso de fecha que sólo detectó un calificador*: los commits de `ace-fca.md`, el documento que define la práctica, van de **2025-08-29 a 2025-12-03**. El repo tiene push de agosto de 2026, pero el contenido es de 2025. La práctica sigue viva; la evidencia primaria es pre-2026.

**Spec Kit como vehículo de SDD** — **[SOLO / EQUIPO pequeño]**
[129.853 estrellas, releases casi diarias (v0.16.4 el 14-08-2026)](https://github.com/github/spec-kit). Produce `.specify/` y `specs/NNN-feature/`. **Sin historia multi-repo en el core**: el mantenedor lo dice en la [discussion #1743](https://github.com/github/spec-kit/discussions/1743) (*"Spec Kit will stay out of your lane in this regard"*, recomienda `--no-git`). Hay [issue #1950](https://github.com/github/spec-kit) abierto pidiendo partir la constitution por coste de contexto.
*Nota de encaje*: Thoughtworks señala que Spec Kit está sesgado a **greenfield** y prefiere OpenSpec por su foco en spec-deltas sobre código existente. Tú eres brownfield puro.

**Compactación intencional** — **[SOLO]**
Comprimir el contexto verboso en un markdown y arrancar sesión nueva, manteniendo la utilización en el 40-60%. Institucionalizado ya dentro de harnesses comerciales (Kiro, Flue). El artefacto sobrevive sólo si se commitea.

### Adoptado por early adopters (mecanismo verificado, un puñado de equipos)

**Cross Repo Base + CLAUDE.md como operating manual + Repo Coordination Graph** — **[EQUIPO]** — *el mejor encaje de todo el informe*
Tres capas de mabl: (1) un repo dedicado que se monta en la raíz de todos los downstream con guías, patrones y skills; (2) un `CLAUDE.md` por repo que declara propósito, arquitectura, **mapa de dependencias (a quién consumo / quién me consume)**, convenciones y workflows numerados; (3) un registro plano: *"At 850+ lines, it covers 79 repositories with detailed dependency graphs, Pub/Sub topic maps, database table ownership, and prescribed release ordering"*. El agente lo consulta **en fase de planning** para decidir qué repos tocar, en qué orden mergear y a quién taggear vía CODEOWNERS.
Resultado publicado: *"context drift dropped from ~40% of our failures to <5%"*; commits AI-assisted 17% → 70% (oct-25 → mar-26); PRs 291 → 732/mes.
*Hueco crítico que ningún frente pudo cerrar*: **mabl no publica cómo se distribuye el repo base** (¿submódulo? ¿symlink? ¿script de sync?) ni quién mantiene el grafo. Es la pregunta más interesante que puedes plantear en tu charla.

**Regla en prosa → check determinista** — **[EQUIPO]** — *la palanca más barata*
Cada invariante que se pueda expresar mecánicamente sale del fichero de instrucciones y se convierte en un check que **falla**. Caso literal verificado ([HN, 17-02-2026](https://news.ycombinator.com/item?id=47044921)): *"I have a line there that says Codex should never use Node APIs where Bun APIs exist... Routinely, Claude Code and now Codex would ignore this. I just replaced that rule with a TypeScript-compiler-powered AST based deterministic rule. Now the agent can attempt to commit code with banned Node API usage and the pre-commit script will fail, so it is forced to get it right."*
Encaja con Böckeler: un contrato verificado en CI es un **sensor computacional**, no un documento que envejece.

**AGENTS.md dirigido por fallos, con verificación A/B** — **[EQUIPO]**
Pamela Fox ([HN, 17-02-2026](https://news.ycombinator.com/item?id=47044313)): *"I only add information to AGENTS.md when the agent has failed at a task. Then, once I've added the information, I revert the desired changes, re-run the task, and see if the output has improved."* Y sólo lo hace en repos *"where I know that other developers will attempt very similar tasks"*. Es la disciplina que evita que cuatro ficheros escritos por dos equipos se pudran.

**Índice de documentación comprimido embebido en AGENTS.md, generado por codemod** — **[EQUIPO]**
Vercel, [27-01-2026](https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals): *"A compressed 8KB docs index embedded directly in AGENTS.md achieved a 100% pass rate, while skills maxed out at 79% even with explicit instructions telling the agent to use them. Without those instructions, skills performed no better than having no documentation at all."* 40KB → 8KB por codemod determinista, no por LLM.

**Generación de AGENTS.md a escala entregada como merge request al equipo dueño** — **[EQUIPO]**
Cloudflare sobre [~3.900 repos](https://blog.cloudflare.com/internal-ai-engineering-stack/) (20-04-2026): analizador → mapeo al Engineering Codex → generación → **"the system opens a merge request so the owning team can review and refine it"** → el AI Code Reviewer (100% de cobertura) marca cuándo un cambio debería actualizar el AGENTS.md. Las dos piezas transferibles a 4 repos: el contexto entra por PR, y el revisor automático detecta la desincronización.

**Propagación cross-repo con workflows agénticos y safe-outputs** — **[EQUIPO]** — *el único caso con métricas de propagación entre repos*
Microsoft Aspire, [GitHub Blog 08-07-2026](https://github.blog/ai-and-ml/github-copilot/automating-cross-repo-documentation-with-github-agentic-workflows/): workflow en markdown (`.github/workflows/pr-docs-check.md`) compilado a `.lock.yml`, disparado en PR mergeado. El agente **no escribe directo**: emite safe-outputs JSON que un handler con GitHub App de permisos acotados materializa como **PR draft en el otro repo, asignado al mismo SME que revisó la feature original**. Ventana 3-may a 2-jun-2026: 396 PRs de producto → 82 PRs de docs, **100% mergeados, mediana 44,8 h**, 96% en menos de 7 días. Herramienta: [gh-aw](https://github.com/githubnext/gh-aw), 4.9k estrellas, MIT (ojo: releases 0.68.4–0.71.3 retiradas por un bug de facturación).

**Skills empaquetadas dentro del paquete publicado (context-as-a-dependency)** — **[EQUIPO]**
[TanStack Intent](https://tanstack.com/blog/from-docs-to-agents) (04-03-2026): `npx @tanstack/intent install` inspecciona `node_modules`, descubre paquetes con skills y las cablea en CLAUDE.md/.cursorrules. `npm update` actualiza el contexto con el código. [Electric ya lo hace en producción](https://electric.ax/blog/2026/03/06/agent-skills-now-shipping) en `@electric-sql/client`, `@tanstack/db`, `@durable-streams/client`. Funciona igual con un registry privado.

**Índice de descubrimiento en `.well-known` con digests verificables** — **[EQUIPO]** — *lo más reciente del informe*
Evil Martians, [12-08-2026](https://evilmartians.com/chronicles/publishing-agent-skills-discovery-index): `/.well-known/agent-skills/index.json` (schema `schemas.agentskills.io/discovery/0.2.0`) con `name`/`description`/`type`/`url`/`digest: sha256:...` verificado antes de instalar. Cuatro rutas de instalación (`npx skills add`, `claude plugin marketplace add`, `gh skill install`, curl|tar). Formato introducido por Anthropic oct-2025, abierto como estándar en diciembre, con RFC de Cloudflare sobre RFC 8615. Coste: un JSON estático en un host interno.

**Progressive context disclosure / marketplace de plugins privado** — **[EQUIPO]**
Thoughtworks Radar Vol.34 (abr-2026): [progressive context disclosure en TRIAL](https://www.thoughtworks.com/radar/techniques/progressive-context-disclosure), [agent instruction bloat en CAUTION](https://www.thoughtworks.com/en-us/radar/techniques/agent-instruction-bloat), [marketplace privado de plugins de Claude Code en TRIAL](https://www.thoughtworks.com/en-us/radar/tools/claude-code-plugin-marketplace) (con el caveat literal: *"Enterprises should try this technology on a project that can handle the risk"*). Límites del marketplace: 500 plugins con sync de GitHub, sync de hasta 30 minutos, repo privado o interno obligatorio.

**Compound engineering: la lección aprendida como fichero versionado** — **[EQUIPO]**
[Plugin de Every](https://github.com/EveryInc/compound-engineering-plugin/blob/main/README.md), 33 skills, 24.335 estrellas. Loop de **seis** pasos que termina en `/ce-compound`, que escribe el aprendizaje en `docs/solutions/`. Coste de adopción casi cero. Todas las rutas son relativas a un repo: con 4 repos tendrías 4 `docs/solutions/` desconectados. Señal de fragilidad: la metodología está acoplada al comportamiento del modelo del momento.

### Experimental (mecanismo interesante, adopción escasa o beta)

**OpenSpec Stores** — el repo de planificación separado. Un store es un repo git con `.openspec-store/store.yaml` + `openspec/specs/` + `openspec/changes/`; cada repo de código apunta con `store: <nombre>` y puede declarar `references: [otro-store]` como **contexto de solo lectura**. El patrón está literalmente documentado: *"A platform team owns the requirements. Product teams build against them"*, y *"References are read-only context"*. [Guía verificada línea a línea](https://github.com/Fission-AI/OpenSpec/blob/main/docs/stores-beta/user-guide.md). 65.227 estrellas, releases quincenales. **Beta declarada**; y *"No sync, ever — by design"*: tú haces pull/push, y el registro de stores es estado local por máquina (fricción de onboarding real).

**Grafo de código commiteado como artefacto binario** — [codebase-memory-mcp](https://github.com/DeusData/codebase-memory-mcp) (39.283 estrellas, push 17-08-2026). Indexa con tree-sitter (158 lenguajes, resolución de tipos en 12), construye aristas **CROSS_\*** entre repos indexados en el mismo store, hace *"HTTP route <-> call-site matching with confidence scoring"*, detecta gRPC/GraphQL/tRPC y pub-sub (EMITS/LISTENS_ON en 8 lenguajes), y exporta `.codebase-memory/graph.db.zst` commiteable con `.gitattributes merge=ours` autocreado.

**Beads / issue tracker git-native como memoria del agente**, **wiki viva mantenida por agentes** ([CodeAlmanac](https://news.ycombinator.com/item?id=48995181), YC S26, 798 estrellas), **indexar los transcripts que ya tienes en disco** ([ctx](https://news.ycombinator.com/item?id=48763462), SQLite local, 1.032 estrellas) — **[SOLO]** los tres últimos salvo que añadas un paso explícito de compartición.

**"Context monorepo"** ([backnotprop, 15-06-2026](https://backnotprop.com/blog/context-monorepos/)): un directorio raíz que contiene N repos git y **no está bajo control de versiones**. Conceptualmente es la forma de tu problema; en la práctica es un post de un individuo con 3 puntos en HN y sin mecanismo de sincronización. Menciónalo como "lo que la gente está tanteando", nunca como práctica.

---

## 3. Tu problema: contexto compartido entre 4 repos y 2 equipos

Antes de las opciones, **el dato que más te afecta de todo el informe**, y es un límite publicado por el único equipo que ha medido esto (mabl, parte 2, verbatim):

> *"Our Repo Coordination Graph works well for features spanning 2-3 repos. Beyond that, agents lose track of dependencies."*

Con svc-lib + svc-control + ext-lib + ext-control estás **justo fuera** de la zona segura conocida. Esto no invalida nada de lo de abajo: significa que la respuesta correcta probablemente sea reducir cuántos repos entran en una unidad de trabajo, no aumentar el contexto que le das al agente.

### (a) Que el agente entienda el sistema sin re-grepear

**Opción A0 — Mapa de dependencias declarado (coste: 1 día · impacto alto)**
Escala hacia abajo lo de mabl: si 850 líneas cubren 79 repos, tus 4 caben en 40-60. Dos artefactos: (1) en el `AGENTS.md` de cada repo, una sección de tres líneas *"consumo X; me consume Y; orden de release: Z"*; (2) un único fichero de coordinación con el grafo, el orden de merge (`ext-lib` → `svc-lib` → `svc-control`) y quién revisa. Es lo único de esta sección con evidencia de impacto medido.
*Objeción que vas a recibir y para la que hay respuesta*: "¿quién lo actualiza?" → un check de CI que falle cuando el mapa declarado diverja del grafo real de imports. Nadie ha publicado esto; es el hueco que los calificadores señalan en los cinco frentes.

**Opción A1 — Workspace multi-root + repo de instrucciones compartido (coste: horas · impacto medio)**
Un quinto repo (`Copilot.Shared` en el original) con **sólo** ficheros de personalización, más un `.code-workspace` que lista los cuatro repos. Al abrir el workspace, las instrucciones del repo compartido aplican a todos, y cada repo puede sobreescribir. Jerarquía verificada: *"workspace-level defaults from the shared repo, overridden or extended by per-repo instructions"*. [Arinco, 04-06-2026](https://arinco.com.au/blog/github-copilot-multi-repo-instructions/). Cero herramientas nuevas. Atado a VS Code/Copilot en su forma original, pero el patrón (repo de config + script de clonado + orden de dependencia) es agnóstico.

**Opción A2 — Grafo de código con aristas cross-service (coste: días · impacto alto pero medible antes)**
`codebase-memory-mcp` es la única herramienta con adopción real que enlaza **servicios**, no sólo símbolos. Requiere que alguien tenga los cuatro repos clonados e indexados bajo el mismo store: **no hay modo servidor multi-equipo con permisos**.
*Mide antes de adoptar.* La única replicación independiente que existe de un MCP de grafo ([harrisonsec sobre Hono, 01-06-2026](https://harrisonsec.com/blog/i-tested-codegraph-on-hono-benchmark/)) encontró que los ahorros de tool calls sí se reproducen (−55% frente a −71% publicado) y la latencia también (−20% vs −46%), pero **el coste no**: +6,8% en vez de −35%. El desglose es lo que te sirve: el grafo ahorra dinero sólo en navegación multi-fichero amplia (−29% en la pregunta cross-adapter) y **cuesta un 22-43% más en lookups estrechos**. Tu pregunta típica ("¿qué de svc-lib rompe si toco ext-lib?") cae en el caso bueno; no lo pongas en el camino de toda pregunta.

**Opción A3 — Índice semántico de vendor (coste: contrato · impacto alto, único que cubre el repo del otro equipo)**
Es la única familia que ataca el repo que **no clonas**. [JetBrains Context](https://blog.jetbrains.com/ai/2026/07/introducing-jetbrains-context-repository-intelligence-for-coding-agents/): *"agents can discover relevant code across your organization's codebase, including repositories that aren't checked out locally"*, con hasta −68% de turnos, −59% latencia y −48% coste. [Atlassian Code Context](https://www.atlassian.com/blog/development/code-context) (12-08-2026, open beta): índice léxico + semántico multi-repo metido en el Teamwork Graph junto a Jira y Confluence, consumible desde Cursor, Claude Code y Codex. [Sourcegraph](https://sourcegraph.com/docs/api/mcp): `go_to_definition` con soporte cross-repo y precisión de compilador, **sólo Enterprise**. Sourcegraph publica además los únicos números cross-repo con metodología: [CodeScaleBench](https://sourcegraph.com/blog/codescalebench-testing-coding-agents-on-large-codebases-and-multi-repo-software-engineering-tasks) (370 tareas, 40+ repos, 220 de ellas exigen navegación cross-repo): +11,5% en comprensión, coste de $0,73 a $0,51, fase de resolución 38% más corta.
Todos en beta/early access y todos te atan a un vendor. Sólo tiene sentido si ya sois clientes.

**Opción A4 — La respuesta aburrida que ningún frente investigó, y que tres calificadores señalaron como hueco**
"Qué servicio llama a qué endpoint" es un problema resuelto desde hace una década: OpenAPI/AsyncAPI versionado como artefacto publicado, cliente generado, contract testing dirigido por el consumidor. Encaja perfectamente con el marco que sostiene tu charla: **un contrato verificado en CI es un sensor computacional, no un documento que envejece**. Si `ext-control` llama a `svc-control`, el artefacto de contexto compartido correcto puede ser un OpenAPI en un paquete versionado, no un grafo que exige clonar cuatro repos. Los cinco frentes buscaron herramientas AI-native y ninguno se preguntó si el problema es un contrato ausente. Márcalo como hipótesis fuerte, no como práctica documentada.

**Opción A5 — Dejar de tener 4 repos.** Monorepo, meta-repo, submódulos, o un repo agregador de solo lectura. Ningún frente lo evaluó ni para descartarlo, y es la primera pregunta que te van a hacer. Dado el límite de 2-3 repos de mabl, es obligatorio responderla en la charla aunque sea para decir por qué no.

### (b) Que la investigación hecha una vez se reutilice

| Mecanismo | Artefacto | Coste | Aviso |
|---|---|---|---|
| **Research commiteado** (RPI) | `thoughts/shared/research/YYYY-MM-DD-*.md` con frontmatter incluyendo `repository:` y `git_commit` (permite saber si caducó) | 1 tarde | El layout asume **un** repo. Con 4, o eliges el que más cambia y enlazas, o duplicas y divergen. El campo `repository:` existe realmente en main, así que el diseño lo contemplaba. |
| **Lecciones aprendidas** | `docs/solutions/` vía `/ce-compound` | Instalar un plugin | Capa de aprendizaje intra-repo, no mapa del sistema. |
| **Memoria de proyecto commiteada** | `.gsd/` con requisitos, decisiones, notas de runtime, planes y evidencia de validación ([verificado en el README de gsd-pi](https://github.com/open-gsd/gsd-pi)) | Adoptable **a mano hoy, sin instalar nada** | El producto no tiene multi-repo. La convención sí es copiable. |
| **Grafo commiteado** | `.codebase-memory/graph.db.zst` + `merge=ours` | Días | **Lee la letra pequeña**: `merge=ours` no "evita conflictos", **descarta siempre y en silencio la versión de tu compañero**. Y es un binario que cambia en cada indexado. |
| **Transcripts ya en disco** | SQLite local vía `ctx`, con un subagente que prepara un brief antes de empezar | Cero escritura nueva | Local a cada máquina. Arregla tu memoria, no la del equipo, salvo que adjuntes transcripts limpios al PR (que es lo que ellos proponen). |
| **Thread compartible** | Amp: referencias entre threads por URL, un modelo secundario extrae lo relevante | Producto | Resuelve la mitad **social** ("mi compañero ya investigó esto"), no la multi-repo. |

**Matiz importante, y es una contradicción entre frentes.** El autor del patrón `thoughts/` se ha movido. Dex Horthy explica que RPI no escalaba al equipo (research contaminado con opiniones, agentes que se saltaban pasos ~50% de las veces, prompts de 85+ instrucciones desbordando el presupuesto de atención) y lo rediseñó como CRISPY, donde el artefacto que importa es **un documento de design discussion de ~200 líneas compartido con los CODE OWNERS antes de implementar**, con cada etapa bajo 40 instrucciones. Y en el [podcast de Pragmatic Engineer (15-07-2026)](https://newsletter.pragmaticengineer.com/p/context-engineering-with-dex-horthy) enfatiza compactación intencional por sesiones, sin rutas persistentes obligatorias.
*Cuál tiene mejor evidencia*: el patrón `thoughts/` está verificado en el repo vigente; CRISPY sólo consta en un resumen secundario de ZenML **sin fecha de publicación** que ni siquiera acredita a Horthy por nombre. Conclusión operativa: adopta el research commiteado, pero preséntalo con la evolución — lo que importa no es la carpeta, es que **tenga dueño y revisor**.

### (c) Que el equipo A publique contexto legible por el agente del equipo B

Ordenado de menor a mayor coste:

1. **Diagrama de secuencia Mermaid + especificación de endpoints, commiteado** (fase "System Architecture" de wsff.md). Es lo único de esta lista que el otro equipo lee **sin instalar nada**. Coste: horas. Retorno alto.
2. **`.well-known/agent-skills/index.json` en un host interno.** `svc-control` publica skills tipo "cómo llamar a esta API", "qué significan estos códigos de error", "qué garantías de idempotencia hay", con digest sha256. El agente del otro equipo instala con un comando y las tiene versionadas y verificadas. Coste: un JSON estático. Es el mecanismo más limpio cuando el consumo es por HTTP, no por paquete.
3. **Skills dentro del paquete publicado.** Encaje casi perfecto para `ext-lib` → `svc-lib`: el equipo dueño mete las skills en el tarball, y `npm update` actualiza el contexto con el código. Resuelve el desfase de versiones sin que nadie sincronice nada.
4. **`references:` de OpenSpec**: el equipo externo publica su store, tú lo declaras read-only. Beta.
5. **PR draft automático en el repo del otro equipo** vía gh-aw, asignado al SME correcto. Coste: un workflow y una GitHub App con permisos acotados. Es el patrón con métricas.
6. **Catálogo de servicios por MCP** (Backstage). Sólo si ya lo tenéis. Thoughtworks pone [MCP by default en CAUTION](https://www.thoughtworks.com/en-us/radar/techniques/mcp-by-default): *"a well-designed CLI with good --help output, structured JSON responses and predictable error handling often gives agents everything they need without the protocol overhead"*.

**La conclusión honesta, y es material de charla.** Ningún frente encontró una práctica establecida para que dos equipos con dueños distintos compartan *research de agentes*. La formulación más honesta la da un practicante en HN ([jillesvangurp, 04-08-2026](https://news.ycombinator.com/item?id=49170811)):

> *"We're in this weird transition phase where none of the major AI tool providers are really focusing much on team use of their stuff. Current tools mostly focus on individuals doing things in isolation. A central company skills repository is simply our way of hacking around a solution."*

Con una advertencia metodológica: esa conclusión es una **afirmación de ausencia**, y ningún frente buscó en la literatura que sí lleva una década en ese límite organizativo (InnerSource, Team Topologies, Backstage/IDPs). Plantéala como pregunta abierta, no como hallazgo.

---

## 4. Lo que se está abandonando o no funciona

**AGENTS.md como configuración de comportamiento.** Tres piezas de evidencia independientes:
- [arXiv 2602.11988](https://arxiv.org/abs/2602.11988) (ETH/SRI, feb-2026, rev. jun-2026): los context files *"typically did not improve success rates while increasing computational costs by over 20%"*. Los **generados por LLM** bajan el resolution rate 0,5-2% y suben el coste 20-23%; los escritos por humanos rinden mejor que los generados, pero mejoran frente a "sin contexto" para todos los agentes **salvo Claude Code**.
- [arXiv 2607.27250](https://arxiv.org/abs/2607.27250) (28-07-2026, 288 runs, 17 tareas reales, 2 agentes): *"Context strategy does not measurably move correctness on either agent (bounded to <=10-15pp via equivalence testing)"* y *"agents fail on implementation skill—feature design, pattern selection, exact wiring—not missing repository knowledge"*.
- [arXiv 2602.14690](https://arxiv.org/abs/2602.14690) (N = 2.853 repos): *"Context Files dominate the configuration landscape and are often the sole mechanism in a repository"*, y *"few repositories adopt advanced mechanisms such as Skills and Subagents. Skills predominantly rely on static instructions rather than executable scripts."*

**La contradicción que resuelve esto, y es probablemente tu mejor slide.** El paper de ETH mide **resúmenes autogenerados del repo** (información que el agente puede descubrir con ripgrep). Vercel mide un **índice de documentación externa** (información que el agente **no** puede descubrir grepeando) y obtiene 53% → 100%. No se contradicen: definen la frontera de qué merece estar en contexto.

**Menos harness, no más.** Anthropic eliminó **>80% del system prompt de Claude Code** para Opus 5 sin degradación ([post 24-07-2026, hilo HN 49043889](https://news.ycombinator.com/item?id=49043889); de ~800 a 164 tokens). Vercel quitó el 80% de las tools de su agente y el success rate subió de 80% a 100%, con la mitad de tokens y la latencia de 724s a 141s ([AINews, 05-03-2026](https://www.latent.space/p/ainews-is-harness-engineering-real)).

**Handoff, eliminado por su propio creador.** Amp, [*"Amp, Rebuilt"* (06-05-2026)](https://ampcode.com/news/neo): *"Handoff is gone. As described above, compaction made it obsolete."* Junto con rollback de ediciones, sintaxis `$` para bash, temas custom y comandos de gestión de skills. Es el mejor ejemplo verificado de **práctica que la mejora de modelos borra**, y el argumento para apostar por el artefacto en disco en vez de por la feature del harness.

**Frameworks SDD pesados.** Críticas consistentes de quema de tokens en HN durante 2026 ("volví a plan mode", "GSD está sobreingenierizado", "specs gigantescas"). *Marca de honestidad*: esas citas concretas **no tienen permalink** en el research y no pudieron verificarse. Lo que sí está verificado: el [Ask HN "What Happened to Spec-Driven Development?" (05-08-2026)](https://news.ycombinator.com/item?id=49182353) recibió **4 comentarios**. Ese silencio es el dato. Y en paralelo, spec-kit sigue con push diario: SDD no murió, se desinfló hasta "plan mode + un md".

**RPI en su forma original**, repudiado públicamente por su autor. Su sustituto QRSPI **no está establecido**: no hay post primario de HumanLayer sobre él, la fuente es una keynote en YouTube y análisis de terceros, y las [release notes oficiales](https://docs.humanlayer.com/release-notes) siguen diciendo *"RPI is the new default workflow"*.

**Búsqueda vectorial pura de código**, perdiendo frente a grafos deterministas: `zilliztech/claude-context` (12.408 estrellas) lleva sin push desde el **14-07-2026**; `grepai` desde el 22-06-2026; `codegraph-rust` (866 estrellas) desde **diciembre de 2025**. Matiz del paper ["Is Grep All You Need?"](https://arxiv.org/abs/2605.15184): grep gana en general, pero el rendimiento es *"strongly dependent"* del harness — y el benchmark es LongMemEval, memoria conversacional, no código.

**Fuentes en declive.** Peter Steinberger: [último post 14-02-2026](https://steipete.me/posts), se unió a OpenAI, cero publicaciones desde entonces. Huntley movió su frente a verificación formal ([Antithesis, 24-07-2026](https://ghuntley.com/slop/): *"Creation is now near-free. Verification/understanding is not, yet."*). `humanlayer/humanlayer` lleva sin push público desde el **19-06-2026** mientras el producto saca releases: el open source y el producto se han separado.

**Cambios de nombre y de dueño que te dejarían desactualizado en escena**: `gsd-build/gsd-2` **no está archivado** — tiene un aviso de deprecación que apunta a [open-gsd/gsd-pi](https://github.com/open-gsd/gsd-pi), que arranca de cero (1,1k estrellas frente a las 7,8k del original: la comunidad no ha migrado). `steveyegge/beads` redirige hoy a `gastownhall/beads`. Amp se escindió de Sourcegraph. BMAD deprecó `bmad-automator` por `bmad-loop`. Augment retiró las completions inline de los planes Indie/Standard/Legacy el 31-03-2026.

**Lo que se creía obligatorio y no lo es.** [Böckeler, 10-08-2026](https://martinfowler.com/articles/exploring-gen-ai/tdd-in-the-agent-loop.html): *"there was no clearly discernable difference based on TDD workflow versus no TDD workflow. On the contrary, more than once Opus ranked the non-TDD workflow solutions slightly higher in design and test quality."* Es un golpe directo a Superpowers, compound engineering y todas las metodologías empaquetadas que asumen TDD como paso obligatorio.

---

## 5. Plan de adopción priorizado

### Esta semana (impacto alto, esfuerzo bajo, cero herramientas nuevas)

1. **Escribe el mapa.** Un fichero de coordinación de 40-60 líneas: grafo de dependencias de los 4 repos, orden de merge, ownership, y quién revisa qué. Más tres líneas en cada `AGENTS.md` declarando "consumo X / me consume Y". *(Base: mabl, el único con impacto medido.)*
2. **Poda los AGENTS.md.** Regla de Osmani: si el agente lo descubre leyendo el código, bórralo. Deja hechos no inferibles, gotchas de tooling y decisiones de producto. Objetivo de tamaño observado en practicantes: raíz <15 líneas, uno por módulo, ~80 tokens a nivel de feature respondiendo sólo *"¿esto es intencional?"*. Y **no** uses referencias estáticas a otros ficheros: está reportado como anti-patrón que el modelo no sigue.
3. **Convierte 3-5 reglas en checks.** Empieza por los invariantes del contrato `svc-lib` ↔ `ext-lib`: un test que falle si cambia una firma pública sin bump, una regla de dependency-cruiser o ArchUnit para los límites de módulo. Cada check es contexto que ya no tienes que compartir en prosa.
4. **Primer research commiteado.** `thoughts/shared/research/2026-08-XX-flujo-ext-control-a-svc-control.md`, con frontmatter (`date`, `researcher`, `git_commit`, `branch`, `repository`). Es el research que hoy tiras. Escríbelo una vez.
5. **`.code-workspace` con los 4 repos** + script de clonado + la regla de orden de dependencia (librerías primero, servicios después, consumidores al final).

### Este mes (proyecto pequeño, requiere acuerdo con el otro equipo)

6. **El contrato como artefacto.** Diagrama de secuencia Mermaid + especificación de endpoints de `svc-control` ↔ `ext-control`, commiteado y acordado. Si podéis, elevadlo a OpenAPI versionado publicado como paquete + un contract test que rompa el build de quien lo viole. Este es el punto donde tu problema deja de ser de IA y pasa a ser de ingeniería normal, que es más barato.
7. **Un hogar canónico para las specs.** O un repo `svc-plans` sencillo con un ID de feature compartido entre repos (`042-...`) y un PR por repositorio, o un piloto de OpenSpec Store si aceptas la beta. El patrón "un intent → N sub-issues por repositorio" es de InfoQ y es proceso, no herramienta.
8. **Un workflow gh-aw** que, al mergear un cambio de contrato en `svc-control`, abra un PR draft en el repo del otro equipo asignado a su SME. Es el único mecanismo cross-repo con métricas publicadas.
9. **Disciplina failure-driven en el repo frontera.** Sólo en `svc-control`, aplica el bucle de Pamela Fox: añade línea al AGENTS.md sólo tras un fallo observado, revierte, re-ejecuta, comprueba. Es caro; por eso sólo en el repo que consume otro equipo.

### Evaluar, no adoptar a ciegas

- **Grafo de código.** Mide primero con tus preguntas reales: los ahorros de tool calls se reproducen, los de coste no, y el grafo **cuesta más** en lookups estrechos.
- **Índice de vendor.** Sólo si ya sois Atlassian, JetBrains o Sourcegraph Enterprise. Es lo único que cubre el repo que no clonas.
- **Multica / HumanLayer workspaces / Beads.** Los tres son 0.x o de bus factor bajo. Roba las ideas (`workspace.json` commiteado, pinning por lockfile, research como grafo de dependencias), no las dependencias.
- **La pregunta incómoda**: dado el límite de 2-3 repos, ¿el harness correcto no será un checkout unificado?

---

## 6. Munición para la charla

Tu tesis es *"si la IA no hace lo que quiero, es un smell de harness insuficiente"*. Las cinco primeras la sostienen; la sexta es el contra-argumento que **tienes que meter tú** antes de que te lo metan en el turno de preguntas.

**1. La frase que es literalmente tu tesis, y viene de Thoughtworks, no de un vendor.**
> *"The 'in the loop' way is to fix the artefact... The 'on the loop' way is to change the harness that produced the artefact so it produces the results we want."*
Kief Morris, [Humans and Agents in Software Engineering Loops](https://martinfowler.com/articles/exploring-gen-ai/humans-and-agents.html), 04-03-2026.

**2. La prueba numérica de que el harness mueve más que el modelo.** Vercel quitó el **80% de las tools** de su agente y el success rate subió de 80% a 100%, con la mitad de tokens y la latencia de 724s a 141s. LangChain pasó de la cola al top-5 de Terminal Bench 2.0 (52,8% → 66,5%) **cambiando sólo el harness**. [Latent Space, *Is Harness Engineering real?*, 05-03-2026](https://www.latent.space/p/ainews-is-harness-engineering-real). El giro que hace que esto sea buena charla: en ambos casos el mejor harness era **menos** harness.

**3. El slide que resuelve la contradicción aparente sobre AGENTS.md.** Dos resultados que parecen incompatibles: el paper de ETH mide 288 configuraciones y concluye que los context files no mejoran el éxito y suben el coste >20% ([arXiv 2602.11988](https://arxiv.org/abs/2602.11988)); Vercel mide y obtiene 100% de pass rate con un índice de 8KB en AGENTS.md frente a 79% con skills ([27-01-2026](https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals)). No se contradicen: **el paper mide resúmenes autogenerados del repo — información que el agente puede grepear. Vercel mide un índice de documentación externa — información que el agente no puede descubrir.** Esa línea es la definición operativa de qué merece estar en el harness.

**4. El número que traduce harness a dinero, con su límite incluido.** mabl, 25 ingenieros, 100+ repos: *"context drift dropped from ~40% of our failures to <5%"*, con **850+ líneas** de registro cubriendo 79 repos. Commits AI-assisted 17% → 70% en cinco meses. Y el límite que ellos mismos publican en la parte 2: *"works well for features spanning 2-3 repos. Beyond that, agents lose track of dependencies."* [Parte 1](https://www.mabl.com/blog/how-we-built-a-system-for-ai-agents-to-ship-real-code-across-75-repos) · [parte 2](https://www.mabl.com/blog/how-we-built-a-system-for-ai-agents-to-ship-real-code-across-75-repos-part-2). Un equipo que publica su propio techo es más creíble que cinco que publican su ROI.

**5. Apertura en español, si la charla es en español.**
> *"Un modelo de lenguaje solo no hace nada — es el harness el que lo convierte en un agente de verdad: orquesta el loop, ejecuta las herramientas y arma el contexto en cada vuelta."*
Marcia Villalba, [10-08-2026](https://desplegando.substack.com/p/kiro-crew-mi-nuevo-companero-de-desarrollo). Bonus de decisión terminológica, ya resuelta: las tres fuentes hispanas con más audiencia (Villalba, Web Reactiva, Código en Casa) **no traducen** "harness"; sólo un blog personal usa "ingeniería de arneses" ([Frederick Salazar, 13-05-2026](https://fredericksalazar.github.io/blog/comparativa-agentes-ia-desarrollo-software-2026/)). No le dediques dos minutos: no traduzcas y sigue.

**6. El contra-argumento, y viene de quien más harness ha construido.**
> *"no amount of harness engineering or loopsmaxxing can solve what is fundamentally a model-training issue"*
Dex Horthy, [wsff.md](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/wsff.md), julio de 2026 — en el mismo documento donde cuenta que su propia fábrica lights-off degradó el codebase hasta que su cofundador pasó *"two whole weeks"* arreglándolo a mano. Refuérzalo con [arXiv 2607.27250](https://arxiv.org/abs/2607.27250): 288 runs, la estrategia de inyección de contexto **no mueve la correctness** (acotado a ≤10-15pp), y los fallos son de *"feature design, pattern selection, exact wiring"*, no de falta de conocimiento del repo.
La reconciliación honesta que puedes ofrecer como cierre: **el harness sube el suelo, no el techo.** Y el harness que funciona no es el que añade instrucciones, es el que añade *sensores* — cosas que fallan.

---

## 7. Lo que no pudimos verificar

**No usar bajo ninguna circunstancia** (comprobado que están rotos o mal atribuidos):
- El **"Anthropic 2026 Agentic Coding Trends Report"** y sus cifras (+55% más rápido, −40% errores). No existe fuente primaria; huele a alucinación de SEO. Un frente descargó la landing y no expone ni fecha ni metodología ni muestra.
- **"El 58% de PRs en el mayor monorepo de Vercel se mergean sin revisión humana"**: la página devuelve **404**.
- Los números de OpenAI sobre harness engineering (~1M LOC en 5 meses, ~1.500 PRs, 3,5 PRs/ingeniero/día): [openai.com/index/harness-engineering/](https://openai.com/index/harness-engineering/) devuelve **403**. Sólo existen como citas dentro de un hilo de HN donde además fueron duramente cuestionados.
- **"El repo gsd-2 está archivado"**: falso, está deprecado con puntero al sucesor.
- El **+98% de PRs / 91% del cycle time** atribuido a Multica: es el informe *AI Productivity Paradox* de **Faros AI** sobre asistencia de IA en general, no telemetría de Multica. Si lo usas, atribúyelo bien.
- Cifras de Cloudflare: los MRs/semana pasaron de ~5.600 a **~8.700 de media móvil**; 10.952 fue el pico de una semana concreta.
- Cifras de Uber (84% de devs, 11% de PRs de agentes, marzo 2026): probablemente ya obsoletas, hay declaraciones públicas posteriores con números muy distintos.

**Claims plausibles pero sin fuente primaria abierta**:
- El post de **Peter Steinberger** que habría acuñado "loop engineering" (*"you shouldn't be prompting coding agents anymore. You should be designing loops that prompt your agents"*, ~jun-2026, supuestos 6,5M de visualizaciones): x.com devuelve 402 y las fuentes que lo corroboran son contenido SEO de baja calidad. No cites el número de visualizaciones.
- El caso de **Meta** (índice cross-repo, ~6.000 → ~200 tokens por consulta): existe sólo en el blog de Riftmap, un vendor que vende exactamente eso.
- **QRSPI** entero: fases, keynote, fecha (03-03-2026) y el principio "usa control flow para el control flow". Sólo análisis de terceros; el análisis principal ni siquiera menciona la keynote y no contiene esa frase.
- **CRISPY**: la única fuente es un resumen de ZenML de un vídeo de YouTube, sin fecha de publicación y sin acreditar a Horthy por nombre.
- **OpenAI Symphony**: no se pudo confirmar ni que la spec sea explícitamente mono-repo ni la declaración de "sin roadmap, sin soporte". El README sólo dice *"a low-key engineering preview for testing in trusted environments"*.
- **Kiro Crew** (04-08-2026) — el post existe y su fecha está confirmada, pero su capacidad multi-repo, que es lo único que tocaría tu dolor, no se abrió. Igual con **AWS Agent Plugins** (anuncio del 17-08-2026, sin formato de fichero especificado).
- **tribuia.org/programas/agentic-engineering** (14 sesiones, gratis, temario con steering files de Kiro y manifiesto "Vibe Coding vs Vibe Engineering"): **HTTP 403 para dos investigadores distintos**. Probablemente el recurso hispano más relevante de toda la investigación, y sigue sin abrirse. Ábrelo tú a mano.
- El **paper de codebase-memory** (arXiv 2603.27277, 83% de calidad de respuesta, 10x menos tokens): es de los propios autores de la herramienta evaluando su herramienta, anunciado en su propio README. Sin abrir.
- Toda la mecánica de **Beads** (backend Dolt, IDs hash, modo contributor cross-repo), la de **thts** (symlinks, perfiles, git hooks — 1 estrella, 0 forks: es un script personal), y las extensiones multi-repo de Spec Kit (`multi-repo-sync`, preset `multi-repo-branching`), que son el único camino multi-repo del framework más adoptado y **nadie las abrió**.

**Gaps estructurales que señalaron los calificadores** (no son fallos de fuentes, son cosas que nadie investigó):
1. **Capa de contratos**: cero menciones a OpenAPI/AsyncAPI, protobuf, schema registries o Pact en cinco frentes, siendo la respuesta madura al problema exacto del usuario.
2. **Backstage / catalog-info.yaml / IDPs**: el Repo Coordination Graph de mabl es Backstage reinventado a mano, y ningún frente lo nombró. Igual con InnerSource y Team Topologies, que son literatura establecida sobre el límite organizativo que los frentes declaran "terreno virgen".
3. **La alternativa estructural** (monorepo, meta-repo, submódulos, repo agregador): no evaluada ni para descartarla.
4. **Coste de mantenimiento y caducidad**: nadie buscó datos de drift de documentación ni el patrón de mitigación obvio (un check de CI que falle cuando el mapa diverja del grafo real). El único dato de coste en todo el material es "1-2 h/semana" de un proyecto individual.
5. **MCP como primitiva DIY**: las tres soluciones cross-repo ofrecidas son de vendor, en beta y de pago. La opción de que el equipo dueño de `svc-control` exponga su propio servidor MCP con recursos que describan su contrato — implementable en un sprint — no aparece en ningún frente.
6. **Prior art de charlas**: cero investigación sobre qué charlas de harness ya se han dado en 2026 (Codemotion, Commit Conf, T3chFest, Software Crafters). Si vas a hablar en español, saber qué ya se contó es preparación de primer orden.
7. **Segundo caso multi-repo**: toda la sección cross-repo descansa en mabl. Nadie buscó un segundo equipo.
8. **Encaje técnico sin comprobar**: en ningún momento se estableció en qué lenguajes están tus repos. La resolución de tipos de `codebase-memory-mcp` cubre 12 lenguajes y la detección de pub-sub 8. Esa lista decide si la recomendación estrella te sirve.
9. **Estrellas de GitHub como señal de adopción**: todas verificadas contra la API (Superpowers 273k, spec-kit 130k, Graphify 107k en 4,5 meses, codebase-memory-mcp 39k en 6 meses), pero ninguna contrastada con descargas, contributors o uso real. Varias son anómalas. Si van a una slide, compruébalas el día antes y no las presentes como adopción.
10. **Origen del término "harness"**: Böckeler (02-04-2026) da la mejor taxonomía, pero no es la primera. Un calificador encontró rastro de un lanzamiento anterior de "Harness Engineering" (Mitchell Hashimoto, ~05-02-2026) y de un memo previo en martinfowler.com (*"Harness Engineering - first thoughts"*), ninguno verificado a fondo. Para una charla titulada con esa palabra, **verifica la genealogía antes de subir al escenario**: presentar a Böckeler como origen es atacable.