## 1. Veredicto del panel

**Cero estrategias de 24 pasaron el corte de 9.0. Cero llegaron siquiera a 8.0.** La mejor nota del catálogo es 7.85. No hay sección 2 y no hay sección 3 con contenido, y eso no es un fallo del panel: es el resultado.

Distribución de las 24 medias (media global 5.34, mediana 5.37, máximo 7.85, mínimo 2.08):

| Banda | Nº | Estrategias |
|---|---|---|
| ≥ 9.0 | **0** | — |
| 8.0–8.9 | **0** | — |
| 7.0–7.9 | 4 | estructural-1 (7.85), contratos-2 (7.65), estructural-4 (7.25), estructural-3 (7.23) |
| 6.0–6.9 | 4 | mcp-diy-2 y estructural-2 (6.95), prior-art-1 (6.45), contratos-4 (6.05) |
| 5.0–5.9 | 6 | contratos-1 (5.98), mcp-diy-1 (5.95), mcp-diy-4 (5.8), contratos-3 (5.6), prior-art-2 y prior-art-5 (5.13) |
| 4.0–4.9 | 5 | mcp-diy-3 (4.72), codely-5 (4.35), codely-2 (4.25), prior-art-3 (4.18), codely-3 (4.08) |
| 3.0–3.9 | 4 | codely-1 (3.8), contratos-5 (3.65), prior-art-4 (3.6), codely-4 (3.4) |
| 2.0–2.9 | 1 | estructural-5 (2.08) |

Medias por frente: **estructural 6.27 > mcp-diy 5.86 > contratos 5.79 > prior-art 4.90 > codely 3.98**.

**El dato más informativo del panel está en quién disparó los killShots.** De 17 killShots repartidos en 10 estrategias: el **refutador firmó 10 de 10** (todas las descalificadas lo fueron, al menos, por él), encaje 3, coste 3, y **evidencia solo 1** (estructural-5 / Nx Polygraph, el único caso donde las cuatro lentes coincidieron). Traducción: casi nada murió por falta de evidencia. Murió por **"no compras nada con esto"** o **"esto empieza pidiéndole permiso a otro"**. Ese es el patrón de la ronda:

- `contratos-3` (Pact): *"Una estrategia cuyo primer paso es que otro owner escriba código no es una estrategia, es una petición en un backlog ajeno."*
- `prior-art-2` (Backstage catalog-info): *"Infraestructura de plataforma para un grafo de 4 nodos que el usuario ya conoce de memoria."*
- `codely-3` (doc-create en volumen): *"Produce en volumen la única clase de contexto con efecto medido NEGATIVO."*

Y la segunda lectura, más incómoda: **las dos hipótesis que el usuario trajo de la ronda 1 (contratos verificados, MCP DIY) son los frentes 2º y 3º por la cola.** Sus mejores exponentes se quedan en 7.65 y 6.95. El frente que mejor puntúa es el que el usuario no propuso: cambiar la estructura de repos.

Por qué nadie llegó a 9.0, en una frase: **para 4 repos y 2 owners, agosto de 2026 no tiene ni una sola práctica publicada con mecanismo, dueño y métricas.** Todo lo que puntúa alto es de coste bajo pero sin respaldo externo (es del usuario), y todo lo que tiene respaldo externo o asume un repo (Codely), o asume una plataforma que no tienes (Backstage), o exige el sí del otro equipo (Pact), o no publica su mecanismo (Polygraph).

## 2. Las que superan 9.0

**Ninguna.** No hay nada que detallar aquí. Inflar una nota para llenar esta sección sería exactamente el fallo que la charla del usuario quiere denunciar: presentar como mecanismo algo que no ha pasado la verificación.

## 3. Las casi-aprobadas y qué les falta

**La banda 8.0–8.9 también está vacía.** Para que la sección sea accionable, detallo las cuatro de la banda 7.x — **dejando claro que el panel las dejó por debajo del corte y que no las rehabilito** — con la pieza exacta que las movería.

**estructural-1 — Colapsar svc-lib + svc-control + ext-lib en un repo (7.85).** Le falta **una cita de autoridad que no existe**. El investigador lo dice sin adornos: no hay ninguna fuente seria de 2026 que defienda con datos que la frontera de repo deba seguir a la de equipo; la única página directa (`aipatternbook.com/conways-law`) no tiene autor ni fecha y huele a generada. Y el único estudio con muestra grande va en contra para humanos (Faros, 320 equipos: mediana de PR cycle time 19h en monorepo vs 2h en multirepo). **Lo que la subiría: una medición propia.** Coge las tres últimas specs implementadas, cuenta los greps y los tool calls, y repite tras el colapso. Un dato tuyo de N=3 vale más en tu charla que la ausencia de dato de la industria, y encaja con la tesis.

**contratos-2 — `openapi.yaml` versionado + `oasdiff breaking` como gate (7.65).** Le falta **cerrar la capa B**. El propio investigador trae la crítica: *"Pure spec diffing assumes the spec is the source of truth"* — los cambios peligrosos ocurren sin que nadie toque el fichero. Un diff spec-vs-spec no detecta que el handler dejó de respetar la spec. **Lo que la subiría: añadir verificación provider-side contra el servicio vivo** (Schemathesis contra svc-control en CI), que convierte el YAML de documento en sensor. Es tu lado fuerte: en HTTP eres el **proveedor** (ext-control llama a svc-control), así que esto es 100% unilateral, cero negociación. Nota de contexto: `oasdiff` tiene 1.3k estrellas y 1.907 commits (github.com/oasdiff/oasdiff) — es contratos **sin** agentes y con adopción real; el paquete equivalente para agentes (pactflow-agent-skills, de SmartBear, 81 commits) tiene **6 estrellas**.

**estructural-4 — Read-many / write-one (7.25).** Es un principio, no un artefacto, y ahí pierde. Su fuente (Neilos, 27-03-2026, 15+ repos) es **un fundador solo**, no un equipo. **Lo que la subiría: convertirlo en mecanismo comprobable** — un hook `PreToolUse` que bloquee Write/Edit fuera del repo designado como unidad de escritura de la sesión. Eso lo mueve de "buena práctica que el agente puede ignorar" a guardarraíl determinista, que es justo lo que el paper de los 4.550 PRs dice que hace falta (ver §8).

**estructural-3 — Grafo precomputado y CONSULTABLE, no leído (7.23).** Es la estrategia con mejor respaldo externo de las cuatro: es el mecanismo común de los **tres** casos que superan el techo de mabl (Anyline: 6 repos, 8 MRs, 5 sesiones, 3 semanas, 05-03-2026; Meta: 4.100+ ficheros en 4 repos, research de ~2 días a ~30 min, −40% tool calls, engineering.fb.com, 06-04-2026; Neilos). Le falta **el mecanismo anti-podredumbre**: nadie publica coste de mantenimiento ni tasa de deriva de un índice así. **Lo que la subiría: un job de CI que reconstruya el índice en cada push y falle si el índice commiteado difiere.** Sin eso es otro artefacto que se pudre en silencio, y el panel lo penaliza correctamente.

## 4. Los repos de CodelyTV

**Acceso conseguido: ambos repos son públicos y se clonaron completos.** `CodelyTV/agent-harness` (HEAD `e600f55`, 2026-08-06, 47 ficheros) y `CodelyTV/rpi-course` (HEAD `ddde978`, 2026-07-30). Se leyó el árbol entero del primero y todo `.agents/` de las seis etapas del segundo.

**Copiable tal cual a tu setup:**
- **`codely-doc-create`** — el bucle de tu tesis hecho mecanismo: el skill *"está pensado para ejecutarse después de una interacción donde el usuario ha corregido tu implementación basándose en convenciones del equipo que aún no están documentadas"*. Corrección → doc → el agente deja de fallar. **Ojo: el panel descalificó `codely-3` (4.08) por escalarlo en volumen**, no por el bucle en sí. El bucle como anécdota de charla es oro; el bucle como fábrica de docs produce justo el artefacto con efecto medido negativo.
- **El patrón task-blind** (subagentes exploradores que no conocen la tarea) hace los informes reutilizables entre sesiones. **Pero `codely-1` cayó a 3.8** con killShot doble: su mecanismo declarado tira los informes al cerrar sesión, que es literalmente tu problema.
- **`skills-lock.json`** — el único canal cross-repo que existe en todo el material. Transporta **procedimiento, no hechos**. `codely-2` sacó 4.25.
- Los hooks de bloqueo (`.cursor/hooks/block-export.sh`, `.opencode/plugins/block-export.ts`): guardarraíl determinista, y el patrón que las lentes premian.

**Lo que NO aplica: prácticamente todo lo demás.** Grep de `multi.?repo|cross.repo|other team|separate repos|submodule|mcp|worktree` sobre todos los .md/.json/.sh/.ts: cuatro hits de "monorepo" que hablan de velocidad del LSP de TypeScript, dos de "repositories" que son el patrón Repository de DDD, y **un solo hit relevante**: seis palabras en una lista de ejemplos de `codely-plan-create/SKILL.md` ("…explore architecture, frontend, backend, different features, separate repos…"), sin desarrollo. **Cero menciones a MCP en todo el harness**, pese a que su curso de fundamentos tiene tres lecciones de MCP — señal de que para ellos el harness son ficheros en el repo, no un servidor. **Cero OpenAPI, cero AsyncAPI, cero protobuf, cero Pact, cero schema registry.** Los seis snapshots del curso son seis copias de la misma app Next.js de un solo repo. Si esperabas la respuesta al gap de contratos aquí, no está.

**Anécdota verificada para la charla:** el skill `codely-plan-create` tiene frontmatter `name: codely-plan-create-subagents` pero vive en la carpeta `codely-plan-create/`, violando su propio `docs/skills/skills-frontmatter.md` (que exige que coincidan) y rompiendo `docs/skills/plugin-registration.md`. **La doc de convenciones existe y aun así la convención se rompió.** Doc sin verificación automática no basta — es tu argumento, en su repo, a fecha de su último commit.

No verificado: el contenido en vídeo (pro.codely.com es de pago). La lección *"Product discovery y Research desde Slack"* es la única del temario que apunta a persistir research fuera del repo, y solo se tiene el título.

## 5. Las dos hipótesis de la ronda 1, resueltas

**(a) Contratos verificados en CI como artefacto de contexto compartido → CONFIRMADA COMO IDEA, SIN EVIDENCIA DE PRÁCTICA, Y MAL FORMULADA.**

La corrección importa más que el veredicto: **la propiedad "no se pudre: falla" no pertenece al contrato, pertenece al verificador.** Un `openapi.yaml` sin CI que lo ejecute es un markdown con sintaxis YAML. Hay tres podredumbres distintas y cada una necesita mecanismo propio: **A)** el contexto se desincroniza de la spec → se genera en CI desde la spec; **B)** la spec se desincroniza de la implementación → verificación provider-side contra el servicio vivo; **C)** el consumidor asume cosas que la spec no dice → solo lo pilla contract testing dirigido por consumidor. Casi todo el mundo se salta la B, y por eso "la spec está desactualizada" es el cliché.

Evidencia de práctica: **esencialmente un practicante documentado, y es el vendor haciendo dogfooding** (SmartBear contract-testeando su propio servidor MCP, 11-08-2026). Su paquete de skills tiene 6 estrellas y 3 forks. No se encontró **ni un caso de estudio de un equipo independiente** usando contratos como contexto/guardarraíl entre dos equipos, tras ~10 búsquedas dirigidas (trátalo como "no encontrado", no como "no existe"). Señal adicional: **Optic murió** — la herramienta canónica de detección de drift de API, adquirida por Atlassian en abril de 2024, archivada el 12-01-2026 sin release desde agosto de 2025 y sin continuación de la comunidad pese a 91 forks (github.com/opticdev/optic). Que la pieza central de "el contrato como sensor" muriera justo al arrancar la era agéntica es un dato incómodo que conviene incluir **precisamente porque es incómodo**.

El panel lo refleja: `contratos-2` (7.65) sobrevive porque es unilateral; `contratos-3` (Pact) muere por depender del otro owner; `contratos-5` (AsyncAPI/Kafka) muere con tres killShots por apoyarse en una premisa que **nadie ha verificado** — que tu tiempo real sea Kafka.

El mejor argumento que sale de aquí y que no aparece en ninguna fuente: **un contrato verificado convierte una dependencia entre repos en un objeto local y compacto.** El agente ya no necesita seguir el hilo por cuatro repos; lee un fichero en el suyo. No escala el grafo de mabl: **lo corta.**

Y el límite, que debes decir en la charla: tu dolor es *"re-descubrir el sistema a base de greps"*. El contrato te da la superficie de la frontera. No te da dónde vive la lógica de reintentos, por qué svc-lib llama a ext-lib ahí y no allá, ni qué invariante temporal asume el procesamiento. **Es el 20% de mayor valor y menor coste, y el único 20% autoverificable. El otro 80% sigue abierto.**

**(b) MCP DIY del equipo dueño → SIN EVIDENCIA. Hipótesis adelantada respecto a lo publicado.**

Hallazgo duro: **no se encontró ni un solo caso publicado de un equipo exponiendo un MCP interno específicamente para que los agentes de OTRO equipo consumieran el contrato de su servicio.** Se preguntó explícitamente al caso mejor documentado (Cloudflare, 20-abr-2026: 13 servidores MCP internos, 182+ herramientas, equipo tigre "iMARS", 11 meses) y la respuesta fue literal: *"The article does not contain any sentence about one team's MCP server being used by another team."* Lo que sí existe son MCPs internos que **envuelven SaaS de terceros** (Jira, GitLab, Sentry) para todos los ingenieros. Es otro patrón.

Tres correcciones adicionales: **(1) Resources es la primitiva equivocada** — la spec 2026-07-28 los define como *"application-driven, with host applications determining how to incorporate context"*; Claude Code parchea el hueco auto-generando tools sobre ellos, pero eso es un cliente, no el protocolo, y SEP-2640 (Skills Extension) sigue **In Review**. **(2) No existe registro interno de MCP y no está previsto**: el charter del Registry WG dice que una implementación *"enterprise-ready or reusable"* está **fuera de alcance** y que *"the codebase supports this instance only"*; Server Cards sigue en Draft (SEP-2127, target 03-04-2026 ya pasado). Cloudflare, con 13 servidores, lo resolvió con Backstage, no con MCP. **(3) El protocolo rompió dos veces en 2026**: la revisión 2026-07-28 elimina sesiones, `Mcp-Session-Id`, el handshake `initialize`, `ping` y `logging/setLevel`, y deprecia Roots, Sampling y Logging enteros. Hay ahora una política de ciclo de vida con ventana de 12 meses — tranquilizador y a la vez la confesión de que hacía falta. **Ese es el coste de mantenimiento que nadie cuantifica y que tú sí puedes documentar con fuente primaria.**

**El contra-argumento CLI, y por qué se ha movido.** Thoughtworks puso MCP en **CAUTION en el Volumen 34 (abril 2026)** citando a Simon Willison (octubre 2025): *"almost everything I might achieve with an MCP can be handled by a CLI tool instead"*. Poehnelt (06-03-2026) lo fundamenta como *"abstraction tax"*. **Pero el propio Willison publicó el 31-07-2026 "Stateless MCP has recaptured my interest"** y argumenta lo contrario: *"Giving an agent a shell environment with the ability to access the internet is fraught with risk… MCP tools are easier to audit and control"*, tras la eliminación de sesiones y handshake. **El blip de Thoughtworks cita una posición que su propio autor matizó tres meses después, y casi nadie lo sabe.** Es material de charla de primera.

Contrapeso empírico desde el otro frente: **Anyline documentó que para producción los scripts de shell le resultaron más fiables que MCP**, tras descubrir un servidor MCP que devolvía solo metadatos de paginación en vez de datos de pipeline, y lo escribieron en su propia doc como *"usa bash, no MCP, para esto"*. Es tu tesis en miniatura: el agente no fallaba, fallaba la herramienta que le habías dado, y el arreglo fue del harness.

El panel: el mejor de este frente es **`mcp-diy-2` (CLI con `--help`/`--json` + SKILL.md, 6.95)** — es decir, **el panel prefirió el CLI al servidor**. `mcp-diy-3` (marketplace + EMA) murió por *"infraestructura antes que contenido"*.

## 6. La pregunta estructural

**¿Debería tener 4 repos? La respuesta defendible es: el número de repos no es la variable, y quien te pregunte eso en el turno de preguntas está usando el modelo mental equivocado.**

Lo que puedes afirmar con fuente:

1. **No existe ningún experimento controlado agente-en-monorepo vs agente-en-multirepo.** Ninguno, a agosto de 2026. Lo que circula es: la demo "4x más rápido" de Nx (caso único, del vendor de monorepos), la estimación de "~1/3 menos de esfuerzo" (estimación declarada, no medición), y "Airbnb comprimió 18 meses a 6 semanas" citado por Nx **sin enlace primario** — no lo repitas. **Esta ausencia es de los mejores momentos posibles de tu charla: la industria está migrando topologías de repo apelando a los agentes sin haber medido a los agentes ni una sola vez.**
2. **El techo de 2-3 repos de mabl no es una ley: es el techo de SU harness.** Tres casos publicados lo superan — Anyline (6 repos, equipo real, mecanismo publicado completo, 05-03-2026), Meta (4 repos, 4.100+ ficheros, tres lenguajes, research de ~2 días a ~30 min, −40% tool calls, engineering.fb.com 06-04-2026) y Neilos (15+ repos, pero es un fundador solo: vale el principio "read many, write one", no la prueba social). **Los tres lo superan con precómputo consultable y memoria persistente entre sesiones, no dándole más contexto al agente.** Eso es lo que te separa de mabl, no el conteo de repos.
3. **La única muestra grande real va contra el monorepo, y mide humanos**: Faros, 320 equipos, un año, mediana de PR cycle time 19h vs 2h.
4. **Conway aplicado a agentes: ausencia casi total.** No hay fuente seria de 2026 que argumente con datos que la topología de repos deba seguir a la de equipos en la era agéntica. Nada de Team Topologies sobre esto en 2026. **El argumento de colapsar tus tres repos propios es sólido pero es TUYO**: se sostiene con el dato de mabl y con la lógica de que los tres son del mismo equipo, no con una cita.
5. **Los `.code-workspace` no sirven**: nadie los propone en 2026 y hay razón técnica — los agentes de CLI leen el árbol de directorios, no esos ficheros. Lo que sí ha ganado esa función es el directorio raíz con N clones más un AGENTS.md de router (Anyline) o el repo-of-repos. **El "context monorepo" existe con al menos tres implementaciones publicadas independientes.**
6. **No hay ninguna migración a monorepo justificada explícitamente por los agentes en fuente primaria.** Block sí es primaria (18-01-2026: 40.000+ ficheros, 650+ servicios, ~95% de ingenieros usando IA, +69% de código escrito por IA en tres meses) **pero no atribuye su topología a los agentes**: ya tenían el monorepo. Trata cualquier titular tipo "X migró por los agentes" como no verificado.

**La respuesta corta para el turno de preguntas:** *"Tengo cuatro repos y solo dos de las fronteras son reales — las de owner. Las otras dos son fronteras de módulo que heredé. No voy a colapsar nada apelando a un estudio, porque el estudio no existe: nadie ha medido agentes en monorepo contra multirepo, ni una vez. Lo que sí está medido es que los tres equipos que superan el techo de 2-3 repos lo hacen con un índice precomputado y memoria entre sesiones. Así que mi variable no es el número de repos, es si el agente descubre o consulta."*

Nota: **estructural-5 (Nx Polygraph) fue descalificada por las cuatro lentes** (2.08). Documentación stub, `nx.dev/docs/enterprise/polygraph` devuelve 404, sin mecanismo de indexado, sin precio, cero adoptantes públicos, y requiere el sí del otro owner para indexar su repo privado. No es una salida.

## 7. Qué hacer el lunes

**Por el criterio que fijaste, esta lista está vacía.** Cero estrategias ≥9.0 y cero en 8.0–8.9 con el hueco cerrado. Ejecutar cualquier cosa de la banda 7.x sería saltarse tu propio corte, y ese salto es exactamente el smell que tu charla denuncia.

Lo que queda el lunes no son estrategias, son **decisiones y verificaciones de coste casi nulo** que no requieren aprobación del panel:

1. **Decide el corte, explícitamente.** O bajas a 7.5 —y entonces entran las cuatro de la banda 7.x, con los huecos de §3 documentados como deuda— o mantienes 9.0 y aceptas que el resultado de la ronda 2 es *"no hay práctica adoptable publicada para mi topología"*. **Las dos son defendibles en la charla; lo indefendible es ejecutar sin haberlo decidido.**
2. **Responde la pregunta que evapora una estrategia entera: ¿tu tiempo real es Kafka?** `contratos-5` murió en parte por depender de una premisa que nadie te preguntó. Sea cual sea la respuesta, cierra el frente.
3. **Abre tú el PDF del informe DORA 2025** antes de proyectar la diapositiva. Lo que hay verificado es la página de capacidades (dora.dev/capabilities/platform-engineering): *"When platform quality is high, the effect of AI adoption on organizational performance becomes strong and positive. When platform quality is low, the effect… is negligible."* Es tu tesis dicha por Google con ~5.000 encuestados, pero **la página no publica tamaños de efecto ni intervalos de confianza**. No proyectes números que no has visto.
4. **Abre `api-extractor.com/pages/overview/intro/`** si vas a citar el `.api.md` (`contratos-4`, 6.05). La página confirma que el report se genera trazando exports y sirve *"as the basis for an API review workflow"*, pero **no confirma** que se commitee a git ni que el CI falle al divergir — que es justo la parte que hace de la estrategia un mecanismo.
5. **Mide tu propio baseline** (esto es lo único que puede sacar a estructural-1 y estructural-3 de la banda 7.x): en las próximas tres specs, cuenta greps, tool calls y minutos hasta el primer edit correcto. Sin baseline no puedes demostrar que ningún harness funcionó, y `mcp-diy-4` ("mide que el harness sirve, o se borra") sacó 5.8 precisamente porque nadie tiene ese dato.

## 8. Lo que sigue sin saberse

**Ausencias que nadie encontró** (todas tras búsqueda dirigida, no exhaustiva — los cuatro investigadores agotaron el presupuesto de WebSearch, 200/200, cada uno):

- **Ni un caso público, con empresa nombrada y métricas, de dos equipos con dueños distintos usando un catálogo de servicios como contexto compartido de agentes.** Lo que hay es contenido de vendor con código de ejemplo, o auto-reporte del vendor sobre su propia plataforma (los 100:1 y el 80% de BackstageCon son Roadie midiéndose a sí misma).
- **Ni un caso de un equipo exponiendo MCP interno para los agentes de otro equipo.** Verificado por negación contra el artículo de Cloudflare.
- **Ni un caso de un equipo independiente (no vendor) usando contratos como guardarraíl de agentes entre dos equipos.**
- **Ningún experimento controlado agente-en-monorepo vs multirepo.**
- **Ningún dato de coste de mantenimiento de un MCP interno**: ni horas/mes, ni incidentes, ni tasa de abandono. Solo tres señales indirectas: Nango (<10 de varios cientos de equipos mantuvieron MCPs genéricos en producción, jul-2026), Lowin (*"massive long tail of public servers with close to zero users"*) y Cloudflare (11 meses y equipo dedicado para 13 servidores).
- **Ningún dato cuantitativo de deriva de documentación de contexto.** El estudio de AIware 2026 sobre 2.853 repos concluye explícitamente que hace falta investigación longitudinal: **la academia dice que este dato no existe todavía.**
- **Ninguna herramienta implementa el hook que tu tesis pediría**: un `PreToolUse` que BLOQUEE al agente al violar el contrato. Las skills de PactFlow son prosa que el agente puede ignorar; el gate vive solo en CI, o sea, después. **Hueco real y barato: `oasdiff breaking` antes de dejarle editar el handler.** Si lo montas en un sprint tienes demo original, que vale más que otra slide de teoría.

**Contradicciones que debes conocer antes de subirte al escenario:**

- **El paper "Guardrails Beat Guidance" (arXiv 2604.11088, 28-05-2026, AWS GenAI Innovation Center + HSBC, 5.000+ ejecuciones sobre SWE-bench Verified) NO compara guardarraíles contra prosa.** Lo dice él mismo. Estudia reglas en prosa y no compara ese canal contra hooks, CI, linters ni tests. **El título promete un head-to-head que el estudio no hace.** Lo que sí demuestra es demoledor por sí solo: reglas curadas 63,8% vs reglas **aleatorias** 63,8% (Cochran's Q=4,70, p=0,697); reglas de dominio equivocado (58,6%) rinden algo **mejor** que las del correcto (56,9%). Y hay polaridad: prohibiciones ayudan (*"do not refactor unrelated code"* +20,0pp, p=0,016), directivas positivas **dañan** (*"follow code style"* −14,3pp; *"read test files"* −14,3pp). **Si lo presentas como prueba de que los guardarraíles ganan, alguien del público te corrige. Preséntalo como "la prosa es débil" y sale más fuerte.** La segunda mitad de tu tesis sigue siendo inferencia razonable, no medición.
- **El mismo paper refuta el cliché de "tu CLAUDE.md es demasiado largo"**: de 0 a 50 reglas el pass rate se mantiene en 59-67% (Q=22,12, p=0,227), sin transición de fase. El problema no es el tamaño.
- **arXiv 2604.09409 (13-04-2026, 4.550 PRs agénticos en 81 repos)**: cuando la instrucción vivía en AGENTS.md/CLAUDE.md, el cumplimiento fue del **8,3% — 3 de 36 PRs**. Conclusión literal: *"tool builders cannot rely solely on prompt engineering or context files (e.g., AGENTS.md) alone to enforce non-functional requirements."*
- **arXiv 2602.11988 (feb-2026, rev. jun-2026)** mide que los ficheros de contexto a nivel de repo **no mejoran** la tasa de éxito y encarecen la inferencia **>20%**, y que los "repository overviews" recomendados por los proveedores no ayudan. **Matiz que lo salva a medias: corre sobre tareas SWE-bench de UN SOLO repositorio**, justo donde el contexto cross-repo no puede aportar nada. Nadie lo ha replicado en multi-repo. Decirlo te da más credibilidad que ocultarlo.
- **Backstage se contradice consigo mismo**: la doc oficial te dice que declares `dependsOn`/`providesApis`/`consumesApis` en YAML; un evangelista del ecosistema, desde el escenario de BackstageCon Europe (13-04-2026), dice que el YAML a mano *"is a recipe for failure"*. Tensión no resuelta dentro del propio ecosistema.
- **La brecha que refuerza tu tesis**: el estudio de AIware sobre 2.853 repos encuentra que los context files son el mecanismo dominante y a menudo el **único**, y que Skills y Subagents apenas se adoptan. **Toda la infraestructura sofisticada existe y funciona, y el estado del arte real es un fichero markdown.**

**Claims que NO debes citar sin verificar tú primero:**
- **Shopify AI Toolkit / Dev MCP** (9-abr-2026): sería el mejor ejemplo público del patrón exacto, pero **shopify.dev/docs/apps/build/devtools/mcp da 404, github.com/Shopify/ai-toolkit da 404, github.com/Shopify/dev-mcp da 404** y la API de GitHub devuelve Not Found. No citado como evidencia.
- **"60% más rápido" de PactFlow**: cifra de vendor sin metodología. No la uses.
- **GetYourGuide service catalog MCP**: el caso más parecido al tuyo, pero la única fuente es Pragmatic Engineer del 09-12-2025 — **por debajo de tu suelo de recencia** — y no da mecanismo ni números.
- **Informe "Engineering in the Age of AI 2026" de Cortex** (+23,5% incidentes por PR, ~+30% change failure rate): detrás de formulario, no abierto.
- **Fecha del post de Nx sobre Polygraph**: el HTML mezcla mayo y agosto de 2026; no fijada.
- **Recencia de commits de `openapi-to-skills` (337 estrellas, 26 commits) y `pactflow-agent-skills` (6 estrellas, 81 commits)**: no verificable vía WebFetch.
- **Formato de `skills-lock.json`**: el de rpi-course es `version: 1` con `computedHash`; fuentes secundarias hablan de v3 con `skillFolderHash`. `skills.sh/docs/cli` **no documenta el lockfile en absoluto**.

**Una señal meta que vale como hallazgo:** cuatro búsquedas con fraseos distintos sobre casos reales de MCP devolvieron casi exclusivamente contenido SEO de vendedores (MintMCP, Truto, Apigene, Prefect, 200ok). **La densidad de marketing sobre densidad de evidencia en este tema es la más alta que vio ninguno de los cuatro investigadores. Trátalo como dato sobre la madurez del patrón.**