# Proyecto: harness y contexto compartido para SVC

Este repositorio no contiene el código del sistema. Contiene el **procedimiento** para
analizarlo y todo el contexto de investigación previa. El código real se clona como
carpetas hermanas cuando se usa (ver README.md).

> Cuatro rondas de investigación en `contexto/`. La ronda 4 aporta el vocabulario del
> harness, la evidencia medida sobre cuánto compra, y la capa de flujo de trabajo.
> Informe completo: https://claude.ai/code/artifact/ea350ca8-7674-44d5-871a-7aa07b03a88d

## Qué se está intentando resolver

Un equipo trabaja sobre un sistema de procesamiento en tiempo real repartido en varios
repositorios. Cada vez que empiezan una especificación nueva, el agente tiene que
redescubrir el sistema a base de greps, quemando contexto, y ese trabajo se pierde al
cerrar la sesión. El objetivo es averiguar **qué mecanismo concreto** arregla eso en
este sistema en particular.

El usuario prepara además una charla técnica cuya tesis es: *si la IA no hace lo que
quiero, es un smell de harness insuficiente*. El material que salga de aquí alimenta
esa charla.

## Arquitectura, según lo que sabemos hoy

> **Este fichero está genérico a propósito.** Los nombres de componentes son
> marcadores (`svc-control`, `svc-lib`, `ext-lib`…). Antes de usarlo, sustitúyelos por
> los reales de tu sistema, o reemplaza el fichero entero por tu versión. Todo lo que
> viene después se apoya en que esta sección sea exacta.


Verificar contra el código antes de dar nada por bueno. Esto es lo que el usuario ha
descrito, no lo que se ha comprobado.

| Componente | Dueño | Qué es |
|---|---|---|
| `svc-control` | Nosotros | Control plane. Crea las aplicaciones de Flink y les indica en qué bucket está el JAR. |
| `svc-lib` | Nosotros | El JAR que se adjunta a Managed Flink (AWS) y a Dataproc (GCP). Deserialización, funciones de Flink, agregaciones propias. |
| `svc-docs` | Nosotros | Ejemplos de templates y documentación de operaciones: Flink SQL, inputs, outputs. |
| Submódulos SVC en el microfrontend del equipo externo | Nosotros | Microfrontend del equipo TP con submódulos nuestros. Casi no se toca. **Fuera de alcance.** |
| `ext-lib` | Equipo TP | Librería que usamos dentro de svc-lib: tipos de datos y operaciones ya existentes (sumas, mapas). |
| `ext-control` | Equipo TP | Servicio maduro de procesamiento de templates. Su control plane se evaluó y no servía para nuestros workflows: por eso existe svc-control. |
| Proyecto de locks | Nosotros | Por determinar. |

Hechos relevantes:

- **Todo es Java**, salvo el microfrontend.
- **El acoplamiento svc-control → svc-lib no es un import.** La librería se sube a un
  bucket (S3 en AWS, GCS en GCP) y svc-control le pasa la ruta a la aplicación al
  crearla. Solo existe en runtime: ninguna herramienta de análisis estático lo ve.
- **Mensajería: Kinesis en AWS, Pub/Sub en GCP. No hay Kafka.**
- **El camino de GCP se maneja solo desde svc-control**, no desde ext-control. Hay un
  desacoplamiento en curso.
- Las operaciones están en un **enum**, cada una con sus validaciones. Se añaden pocas:
  la última fue la de SQL.

## Las dos reglas que gobiernan cualquier propuesta

No las ablandes. Tienen medición detrás.

**1. Si el agente lo descubre grepeando, no va en el harness.** Un estudio de ETH/SRI
sobre 288 configuraciones encontró que los ficheros de contexto que resumen el
repositorio no mejoran la tasa de éxito y encarecen la inferencia más de un 20%
(arXiv 2602.11988). Volcar un resumen del código en un AGENTS.md es contraproducente.

**2. Lo comprobable se comprueba, no se escribe.** Sobre 4.550 PRs agénticos en 81
repos, el cumplimiento de instrucciones que vivían en un AGENTS.md fue del **8,3%**
(arXiv 2604.09409). Si una regla se puede convertir en un test o un check que falle,
ese es su sitio.

Corolario práctico, verificado en la ronda 2: **toda estrategia que empieza pidiéndole
permiso a otro equipo no se ejecuta nunca.** Prioriza lo unilateral.

## La rejilla con la que clasificar cualquier propuesta

De la ronda 4 (Böckeler, martinfowler.com, abril 2026). **Agente = modelo + harness.**
Todo control cae en una de estas cuatro casillas, y saber en cuál cae decide si merece
la pena:

|  | **Computacional** (determinista, fiable) | **Inferencial** (LLM, probabilístico) |
|---|---|---|
| **Guide** — dirige *antes* de actuar | `options.release = 11`, módulos Gradle, tipos | AGENTS.md, router, flow.md |
| **Sensor** — observa *después*, permite autocorregir | ArchUnit, japicmp, tests, compilador | revisión por agente |

Las dos reglas de arriba son, en este marco: la regla 2 dice *prefiere la columna
izquierda*; la regla 1 dice *qué puede entrar en la fila de guides*. Las cuatro
estrategias que sobrevivieron a la ronda 3 son las cuatro computacionales — el panel
convergió en la columna correcta sin conocer el marco.

**Java con Gradle es un caso de harnessability alta**: compilador, ArchUnit, japicmp y
frontera de módulo verificable. Aprovéchalo antes de escribir prosa.

**La regla de las dos veces.** Cuando el agente comete el mismo error por segunda vez,
no arregles el artefacto: arregla el guide o el sensor. Si se puede comprobar, es un
sensor. Si hay que saberlo de antemano, es un guide.

## Lo que ya se descartó — no lo vuelvas a proponer

Tres rondas de investigación, 49 estrategias evaluadas por un panel de cuatro
calificadores. Detalle completo en `contexto/`.

- **AGENTS.md anidado no resuelve multi-repo.** Es un mecanismo de árbol de
  directorios: cuatro ficheros en cuatro repos separados no se ven entre sí.
- **Los `.code-workspace` no sirven.** Los agentes de CLI leen el árbol de
  directorios, no ese fichero.
- **Servidor MCP propio para exponer contexto a otro equipo**: cero casos publicados.
  El panel prefirió un CLI con `--help` y salida JSON.
- **Serena para indexar ext-lib**: su propia documentación dice que las carpetas
  adicionales de workspace *no se indexan*, y el indexado de librerías externas es de
  pago.
- **Backstage, catálogo de servicios, Pact**: todos requieren el sí del equipo TP.
- **Generar un catálogo de operaciones desde el enum**: se evaluó y no se paga, porque
  las operaciones casi no cambian.

Añadido en la ronda 4 (detalle en `contexto/ronda4-harness-y-flujo.md`):

- **Frameworks de proceso (Superpowers, GSD)**: ninguno tiene historia multi-repo. GSD
  lo declara explícitamente fuera de alcance y cambió de repositorio tres veces en ocho
  meses.
- **Orquestadores de workspace** (Conductor, Vibe Kanban, agent teams): todos son "un
  repo, N worktrees". Paralelizan *dentro* de un repo, que es otro problema.
- **Ralph Wiggum**: su autor dice explícitamente que no lo usaría en un codebase
  existente. Es para greenfield.
- **TDD obligatorio dentro del bucle del agente**: medido en agosto de 2026, sin
  diferencia discernible y con 3 a 8,5× más tokens. Los tests sí; la ceremonia del
  orden no.
- **`@import` como técnica para adelgazar contexto**: los ficheros importados se cargan
  enteros al arrancar. Sirven para organizar, no para ahorrar.

## Lo que sí sobrevivió

De la ronda 3, banda 8,5–8,9 (nada superó el 9,0 en tres rondas):

- Partir svc-lib en módulos Gradle `:svc-core` / `:svc-aws` / `:svc-gcp`. La frontera
  *es* el fichero de build, así que no hay nada que sincronizar.
- Una regla de ArchUnit que congela el desacoplamiento de GCP antes de escribir el
  código.
- `options.release = 11` para que la restricción de Dataproc sea un error de
  compilación local.
- Gate con japicmp en el bump de ext-lib. Unilateral: solo consume artefactos ya
  publicados.

**Hallazgo técnico urgente y no relacionado con el harness:** el conector de Pub/Sub de
Flink extiende `RichSourceFunction`, que **Flink 2.0 eliminó**. Dataproc 2.3 trae Flink
1.17. Si el camino de AWS sube a 2.x, esto bloquea. Verificar con las versiones reales.

## Cómo trabajar en este repositorio

- Los prompts de `prompts/` se ejecutan **uno por sesión limpia**, en orden. Cada uno
  lee lo que escribieron los anteriores en `salida/`, así que no necesita que el
  contexto sobreviva.
- Nada de lo que se genere se commitea a los repos de código sin que el usuario lo
  revise antes.
- Cuando no sepas algo, dilo. Un documento con dudas marcadas es fiable; uno que finge
  estar completo, no.
- Cita siempre fichero y línea. Un hallazgo sin ruta no está verificado.
