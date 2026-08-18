## 1. Veredicto y comparacion con la ronda 2

**Distribucion (25 estrategias, corte 9.0, panel de 4 lentes):**

| Banda | N | Estrategias |
|---|---|---|
| ≥9.0 | **0** | — |
| 8.5–8.9 | 7 | multicloud-context-1 (8.90), jvm-contracts-1 (8.73), desacoplamiento-2 (8.70), multicloud-context-3 (8.68), flink-agents-5 (8.60), desacoplamiento-1 (8.57), flink-agents-2 (8.55) |
| 7.5–8.4 | 5 | message-schemas-1 (8.23), multicloud-context-2 (8.17), flink-agents-3 (8.10), jvm-contracts-4 (7.95), message-schemas-4 (7.62) |
| Killshot | 9 | jvm-contracts-2, desacoplamiento-5/-3, jvm-contracts-5, multicloud-context-5, flink-agents-1, message-schemas-3, multicloud-context-4, message-schemas-5 |
| <7.5 | 4 | message-schemas-2 (7.45), jvm-contracts-3 (7.25), desacoplamiento-4 (7.20), flink-agents-4 (7.07) |

**Por frente** (banda alta / banda media / killshot / resto):
- multicloud-context: **2** / 1 / 2 / 0
- desacoplamiento: **2** / 0 / 2 / 1
- flink-agents: **2** / 1 / 1 / 1
- jvm-contracts: **1** / 1 / 2 / 1
- message-schemas: **0** / 2 / 2 / 1

message-schemas es el unico frente sin nada en banda alta, y no por mala ejecucion: por una pregunta sin responder (¿cual es el formato del wire hoy?) que trato en §4 y §10.

**¿Subieron las notas al conocer el stack real? Si, y de forma medible.** Ronda 2: 24 estrategias, mejor 7.85, cero por encima de 8.0. Ronda 3: mejor 8.90, **siete** por encima de 8.5, doce por encima de 7.5. El techo sube ~1.05 puntos y la masa se desplaza una banda entera. Ademas hay **nueve puntuaciones individuales de lente ≥9.0** repartidas en seis estrategias (jvm-contracts-1 saca 9.2 en evidencia y 9.1 en encaje; multicloud-context-1 saca 9.1 del refutador, el lente mas duro; desacoplamiento-2 saca 9.1 en coste; multicloud-context-3 y flink-agents-3 sacan 9.1 y 9.0 en encaje; message-schemas-1 saca 9.1 en evidencia y 9.0 en coste). En la ronda 2 no hubo ninguna.

**Que informacion concreta desbloqueo esto** — esto es lo importante para la charla, porque es una lista corta y cada item mapea a estrategias concretas:

1. **"Es JVM"** desbloqueo la clase de mecanismo entera. La ronda 2 murio buscando el equivalente OpenAPI de un contrato entre equipos y encontro que Optic estaba archivada (12-01-2026) y que la mejor opcion sacaba 7.65. En JVM el sensor no es una herramienta externa: es el compilador (`implementation` de Gradle), el JDK (`jdeps`), o un JAR unico sin instalacion (japicmp, 8 releases entre enero y mayo de 2026, 773 stars, archived=false, pushed 27-05-2026). Seis de las siete estrategias de banda alta tienen coste de infraestructura **cero**.
2. **"svc-lib es del usuario, y esta en las DOS direcciones del acoplamiento"** desbloqueo la unilateralidad. La causa de muerte dominante en la ronda 2 fue "esto empieza pidiendole permiso a otro owner". Saber que existe un artefacto que el usuario posee al 100% y que ademas es exactamente el punto donde ext-lib entra y desde donde svc sale, convierte la unilateralidad de obstaculo en propiedad por defecto: **las 7 de banda alta son unilaterales**, y 6 de ellas viven enteras dentro de svc-lib.
3. **"Multi-cloud en curso, Dataproc + Managed Flink"** produjo el unico hallazgo tecnico duro del catalogo, verificado abriendo codigo y no inferido: `public class PubSubSource<OUT> extends RichSourceFunction<OUT>` (apache/flink-connector-gcp-pubsub, rama main) y Flink 2.0 **elimino** `RichSourceFunction` (https://nightlies.apache.org/flink/flink-docs-release-2.0/release-notes/flink-2.0/). Dataproc 2.3 trae Flink 1.17.0 / Java 11 / Scala 2.12.18 (https://docs.cloud.google.com/dataproc/docs/concepts/versioning/dataproc-release-2.3); AWS MSF documenta el upgrade a 2.2 y ejemplifica con `-Dflink.version=2.3.0`. Sin el dato "multi-cloud" esa incompatibilidad es indescubrible.
4. **"Es stateful"** abrio el segundo contrato que nadie habia visto: el formato binario del estado persistido, que depende de tipos cuyo owner es el equipo TP. Añadir un campo a un POJO es binario-**compatible** (japicmp da verde) y aun asi cambia la evolucion de estado. Eso es flink-agents-3 (encaje 9.0).
5. **"NO es Kafka"** ahorro trabajo muerto y mato dos candidatos por evidencia: AsyncAPI no tiene binding de Kinesis (verificado listando el directorio del repo: 20 bindings, hay `sns` y `sqs`, no hay `kinesis` — https://api.github.com/repos/asyncapi/bindings/contents) y el binding `googlepubsub` esta en 0.2.0 con operation/server bindings literalmente vacios. Confluent Schema Registry es irrelevante; Glue valida compatibilidad al registrar, Pub/Sub **no** valida entre revisiones — o sea que el sensor no puede vivir en GCP.

**Matiz de honestidad, y hay que decirlo antes de usar esto en la charla:** siguen siendo **cero** estrategias por encima de 9.0, y esta ronda introdujo una clausula de bonificacion que los lentes citan explicitamente (unilateral + artefacto que falla + herramienta madura). Parte del salto es contexto y parte es rubrica. El salto de techo (7.85 → 8.90) y el cambio de causa de muerte son solidos; atribuir el numero entero al contexto no lo es.

---

## 2. Las que superan 9.0

**Ninguna.** Cero de 25, igual que en la ronda 2 (cero de 24). El corte de 9.0 sigue sin cruzarse en tres rondas y 49 estrategias evaluadas.

Lo que si cambio: en la ronda 2 la mejor sacaba 7.85 y su lente mas bajo la mataba por "no compras nada con esto". Aqui la mejor (multicloud-context-1, media 8.90) tiene su **minimo en 8.7** y el refutador — el lente que en la ronda 2 firmo 16 de 17 killshots — le da un **9.1**, la unica puntuacion de refutador por encima de 9 del catalogo. Las objeciones que la frenan son de coste (un refactor de uno o dos dias, duplicar la config de shadowJar), no de mecanismo. Eso es un tipo de fallo cualitativamente distinto: la ronda 2 producia ideas que no funcionaban; la ronda 3 produce ideas que funcionan y cuestan.

---

## 3. Banda 8.5–8.9 y banda 7.5–8.4

Formato: **notas [evidencia / encaje / coste / refutador]**.

### Banda 8.5–8.9

---

**multicloud-context-1 — Frontera de nube como frontera de modulo Gradle (media 8.90) [8.7 / 8.8 / 9.0 / 9.1]**

- **Mecanismo:** partir svc-lib en `:svc-core` (agregaciones, tipos, deserializacion, depende de ext-lib, cero nube), `:svc-aws` (`flink-connector-aws-kinesis-streams:6.0.1`) y `:svc-gcp` (`flink-connector-gcp-pubsub:3.1.0`), sin dependencia entre las dos de nube. `implementation` de Gradle hace que un `import software.amazon.awssdk` dentro de `:svc-gcp` **no resuelva** — la doc dice verbatim que las dependencias `implementation` "do not leak into the compile classpath of consumers anymore" (https://docs.gradle.org/current/userguide/java_library_plugin.html). Consulta para el agente por CLI, no MCP: `./gradlew projects`, `./gradlew -q :svc-gcp:dependencies --configuration compileClasspath`.
- **Artefacto:** `settings.gradle.kts` + `svc-core/build.gradle.kts`, `svc-aws/build.gradle.kts`, `svc-gcp/build.gradle.kts`.
- **Unilateral:** si, sin matices. svc-lib es repo propio en las dos lecturas de la ambiguedad; el `settings.gradle` de ext-control no se toca.
- **Que la subiria de banda:** presupuestar y resolver la duplicacion de la config de shadowJar por modulo (el unico coste recurrente que el lente de coste le señala), y verificar si el build es Maven — la ficha resuelve el caso Maven con `<modules>` en una linea sin comprobarlo.
- **Nota:** es la unica cuyo "artefacto que falla al desincronizarse" es el caso limite perfecto: no hay nada que sincronizar porque la frontera **es** el fichero de build, no una descripcion de el. Resuelve ademas un problema operativo real: hoy un unico svc-lib arrastra el SDK de AWS al Dataproc de GCP, con el limite de 512 MB del JAR de MSF documentado al lado.

---

**jvm-contracts-1 — Gate de bump de ext-lib con japicmp CLI, acotado con `-i` (media 8.73) [9.2 / 9.1 / 8.2 / 8.4]**

- **Mecanismo:** el usuario es CONSUMIDOR, no puede meter un plugin en el build de ext-lib — pero japicmp compara dos JARs cualesquiera. Fijar la version de ext-lib, y un job de CI que ejecute `java -jar japicmp-0.26.1-jar-with-dependencies.jar -o <ext-lib-1.4.0.jar> -n <ext-lib-1.5.0.jar> -b --only-incompatible --error-on-binary-incompatibility --ignore-missing-classes -i "com.tp.ops.Sum;..." --markdown`. Flags verificados uno a uno en https://siom79.github.io/japicmp/CliTool.html. El `-i` es lo que lo convierte de ruido en señal.
- **Artefacto:** `.github/workflows/ext-lib-drift.yml` + `docs/contracts/ext-lib-<old>..<new>.md` + el pin de version en el build de svc-lib.
- **Unilateral:** si — solo consume artefactos ya publicados. El equipo TP no se entera de que existe. Ese es el motivo exacto por el que sobrevive al filtro de la ronda 2.
- **Que la subiria de banda:** resolver el generador del filtro `-i` a granularidad de **metodo**. Su generador declarado (jdeps, en desacoplamiento-2) solo da nivel de clase; el refutador lo llama "la pieza concreta que falta". O el filtro es tosco y el gate ruidoso, o hay que escanear bytecode con ASM, que nadie presupuesto.
- **Discrepancia notable:** evidencia 9.2 (la mejor del catalogo; el lente comprobo el mecanismo flag por flag) frente a coste 8.2 y refutador 8.4. El refutador señala que la frecuencia de disparo es baja: se activa solo en el PR de bump, o sea dos veces al año si TP publica cada varios meses.
- **Riesgo estructural:** si svc-lib es Scala, el bytecode genera ruido sintetico masivo y el equivalente correcto es MiMa (lightbend-labs/mima, pushed 2026-08-10, 481 stars).

---

**desacoplamiento-2 — bannedDependencies en el modulo GCP + superficie de ext-lib con jdeps (media 8.70) [8.7 / 8.5 / 9.1 / 8.5]**

- **Mecanismo:** tres capas. (A) `maven-enforcer-plugin` 3.6.3 con `<bannedDependencies>` y `searchTransitive=true` en el POM del modulo GCP, o `configurations.all { exclude group:..., module:'ext-lib' }` en Gradle (https://maven.apache.org/enforcer/enforcer-rules/bannedDependencies.html). (B) mover ext-lib de `api` a `implementation` en svc-lib: si compila, ya estabas mas desacoplado de lo que creias; si no, **la lista de errores del compilador ES el inventario exacto de fuga**. (C) `jdeps -verbose:class -e '<org>\.tplib\..*' svc-lib.jar > contracts/ext-lib-surface.txt`, versionado, con diff en CI.
- **Artefacto:** `<modulo-gcp>/pom.xml`, `svc-lib/build.gradle`, `contracts/ext-lib-surface.txt`.
- **Unilateral:** si. jdeps viene en el JDK, enforcer es de Apache, y el fichero de superficie se genera leyendo un JAR que ya se consume.
- **Que la subiria de banda:** la capa (C) es la que la lastra — el refutador dice que duplica un grep de imports y que su diff se pone rojo ante refactors legitimos, y la propia ficha lo admite al proponer fallo duro solo en gcp y aviso en el resto. Subiria si (C) se justifica por lo que un grep no puede hacer: resolucion de tipos sobre bytecode incluyendo dependencias transitivas y llamadas a metodos heredados.
- **Nota:** **coste 9.1, la mejor economia del catalogo entero.** 30-60 minutos para (A), 1-2 horas para (C). Presupone que el modulo GCP existe como modulo, o sea que depende de multicloud-context-1.

---

**multicloud-context-3 — Matriz de runtimes: compilar y probar contra Flink 1.17/Java 11 y Flink 2.x en el mismo `check` (media 8.68) [8.9 / 9.1 / 7.9 / 8.8]**

- **Mecanismo:** dos sensores. (a) `tasks.named<JavaCompile>("compileJava") { options.release = 11 }` — **una linea** que convierte la restriccion Java 11 de Dataproc en error de compilacion local; la doc de Gradle dice que "validates that the Java sources are not using language features introduced in later Java versions, and also that the code does not access APIs from more recent JDKs". (b) dos `JvmTestSuite` (`flink117` con `flink-streaming-java:1.17.0`, `flink2x` con `2.3.0`) colgadas de `check`, corriendo los mismos tests de `:svc-core` contra las dos generaciones.
- **Artefacto:** `svc-lib/build.gradle.kts` (bloque `testing.suites` + `options.release`) y `gradle/libs.versions.toml` con `flink-aws` y `flink-dataproc` como dos lineas revisables en cada PR.
- **Unilateral:** si. Lo unico externo es saber la version de imagen Dataproc, dato que el usuario ya tiene porque es quien monta el camino GCP.
- **Que la subiria de banda:** el `JvmTestSuite` plugin esta declarado **incubating** por la propia doc de Gradle ("is an incubating API and is subject to change in a future release") — el mecanismo se apoya en una pieza que el proveedor avisa que puede cambiar. Sustituir la matriz por dos source sets convencionales, o por dos jobs de CI con distinto `-Pflink.version`, la subiria. Y confirmar la version real de MSF y de la imagen Dataproc antes de fijar numeros.
- **Discrepancia notable:** encaje 9.1 y refutador 8.8 frente a **coste 7.9**, la mayor brecha de la banda alta. El lente de coste separa las dos mitades explicitamente: `options.release=11` por si sola valdria sobresaliente; la matriz duplica el tiempo de CI, depende de API incubating, y las dos versiones son numeros mantenidos a mano en el version catalog sin nada que detecte que la imagen de Dataproc subio de Flink — "la matriz afirma reflejar produccion y esa afirmacion se pudre en silencio".

---

**flink-agents-5 — ArchUnit que congela el desacoplamiento GCP, con la matriz de semantica colgada (media 8.60) [8.6 / 8.7 / 8.7 / 8.4]**

- **Mecanismo:** `noClasses().that().resideInAPackage("..svc.gcp..").should().dependOnClassesThat().resideInAnyPackage("..extcontrol..")`, añadida **antes** de escribir el codigo GCP para que el desacoplamiento nazca verificado. Para lo existente, el patron de freeze que usa el propio Flink. Colgada del test, la matriz de semantica por runtime: Kinesis exactly-once / orden por partitionId / approximateArrivalTimestamp frente a Pub/Sub at-least-once, ack solo en checkpoints exitosos, y el aviso de que si el intervalo de checkpoint supera el ack deadline "messages will most likely be processed multiple times".
- **Artefacto:** `svc-lib/src/test/java/.../architecture/CloudBoundaryTest.java` + `archunit-violations/` + `docs/runtime-semantics.md`.
- **Unilateral:** si. Es un test dentro del codigo del usuario que restringe lo que el codigo del usuario puede importar.
- **Que la subiria de banda:** separar el paquete. La mitad no-test (la matriz de semantica) es documento puro sin sensor — la propia ficha dice que "se pudre en silencio" y que su unica proteccion es la proximidad fisica al test. El refutador: "eso es exactamente lo que la ronda 2 descarto, y la proximidad no es un mecanismo". La regla sola vale mas que el paquete.
- **Aviso de catalogo, lo dice el refutador literalmente:** flink-agents-5, desacoplamiento-1 y multicloud-context-2 son **la misma idea con tres envoltorios**. En una charla eso es una idea. Hay que elegir una.

---

**desacoplamiento-1 — ArchUnit con FreezingArchRule como libro mayor del acoplamiento (media 8.57) [8.5 / 8.4 / 8.9 / 8.5]**

- **Mecanismo:** el mismo ArchUnit, pero con un giro que lo distingue: el `ViolationStore` congelado y versionado deja de ser configuracion y pasa a ser **el inventario, linea a linea, de cada punto de acoplamiento que queda por cortar**. `freeze.store.default.allowStoreCreation=true`, `freeze.store.default.path=src/test/resources/archunit_store`, la regla de `..svc.gcp..` sin congelar (nace en verde), y `freeze.refreeze=true` solo deliberado con diff revisado en PR.
- **Artefacto:** `svc-lib/src/test/java/<pkg>/ArchitectureTest.java` + `src/test/resources/archunit.properties` + `src/test/resources/archunit_store/`.
- **Unilateral:** si en la lectura "svc-lib es del usuario"; **parcial** si svc es modulo dentro del repo de ext-control (el test corre en el build compartido). Mitigacion que lo devuelve a unilateral: reglas apuntando solo a `..svc..` y naciendo congeladas.
- **Que la subiria de banda:** dos agujeros de podredumbre silenciosa que ningun lente perdona. (1) `freeze.refreeze=true` en local + commit del store limpio desactiva el sensor sin ruido; la mitigacion propuesta (CODEOWNERS con proteccion de rama) **no es unilateral**, la activa un admin. (2) ArchUnit razona sobre bytecode y no ve reflexion, SPI ni carga por nombre — en un servicio que **procesa plantillas** eso significa falsos verdes permanentes.
- **Golpe del refutador que hay que oir:** "el store solo inventaria violaciones de las reglas que ya escribiste, asi que 'mapa de acoplamiento' es una promesa condicionada a que el usuario ya sepa donde estan los seams — precisamente lo que dice no saber".
- **Es la mejor de las tres versiones de ArchUnit.** Si hay que elegir una, esta.

---

**flink-agents-2 — Golden de identidad de operadores: uids explicitos + topologia congelada (media 8.55) [8.8 / 8.6 / 8.0 / 8.8]**

- **Mecanismo:** CAPA 1, un JUnit que construye el StreamGraph sin desplegar (`createLocalEnvironment()`, `env.getStreamGraph()`), recorre `getStreamNodes()`, llama a `getTransformationUID()` y falla si alguno es null o si el conjunto {uid → operador} no coincide con un golden. CAPA 2, `env.getExecutionPlan()` congelado como JSON: incluye por arista el `SHIP_STRATEGY`, o sea detecta que un `keyBy` se convirtio en `rebalance`. **Verificado abriendo JSONGenerator.java: ese JSON NO incluye el uid**, por eso hacen falta las dos capas. CAPA 3 opcional, `pipeline.auto-generate-uids = false`.
- **Artefacto:** `svc-lib/src/test/java/.../OperatorIdentityTest.java` + `src/test/resources/operator-uids.golden.txt` + `execution-plan.golden.json`.
- **Unilateral:** si para capas 1 y 2. **Parcial** para la 3: si el arranque del job lo hace ext-control, ese flag toca su configuracion.
- **Que la subiria de banda:** la capa que porta el valor central depende de `StreamNode#getTransformationUID()`, API **@Internal**; la capa publica (`getExecutionPlan`) explicitamente no trae el uid. Con dos generaciones de Flink en juego, el riesgo de que el test no compile contra ambos runtimes es la semana que viene, no teorico.
- **Ventana temporal real:** poner uid() explicito hay que hacerlo **antes** de que exista mas estado en produccion sin uid, o toca mapear hashes viejos con `setUidHash()`. Doc oficial: "The generated IDs depend on the structure of your program and are sensitive to program changes" (https://nightlies.apache.org/flink/flink-docs-release-2.0/docs/ops/state/savepoints/).

---

### Banda 7.5–8.4

---

**message-schemas-1 — Golden `.avsc` + `SchemaCompatibility.checkReaderWriterCompatibility` (media 8.23) [9.1 / 8.5 / 9.0 / 6.3]**

- **Mecanismo:** `ReflectData.get().getSchema(Evento.class)` en un test extrae el esquema que hoy esta implicito, se commitea con `toString(true)` como golden, y un JUnit recalcula y compara con `checkReaderWriterCompatibility` (https://avro.apache.org/docs/1.12.0/api/java/org/apache/avro/SchemaCompatibility.html), fallando si el tipo no es `COMPATIBLE`. Variante proto: `buf breaking --against '.git#branch=main'` (bufbuild/buf-action, pushed 2026-08-14).
- **Artefacto:** `svc-lib/src/test/resources/schemas/<mensaje>.frozen.avsc` + `svc-lib/src/test/java/.../MessageContractTest.java`.
- **Unilateral:** si, y es la mas limpia del catalogo en esto: "cero credenciales, cero red, cero cuentas cloud, cero conversacion con el equipo TP".
- **Que la subiria de banda:** **responder cual es el formato del wire hoy.** Es literalmente lo unico que la separa de la banda alta.
- **DISCREPANCIA MAS GRANDE DEL CATALOGO (2.8 puntos):** evidencia 9.1 y coste 9.0 frente a **refutador 6.3**. El argumento del refutador es serio y hay que resolverlo antes de implementar: si el wire es JSON deserializado con Jackson a POJOs — que la propia ficha lista como riesgo 1 — el `.avsc` congelado no describe el wire, describe la forma de una clase Java que el agente puede simplemente **abrir**. El sensor detecta entonces cambios en un POJO que ya aparecen en el diff del mismo PR, revisados por el mismo humano. Y no detecta deriva del productor, que es el unico riesgo que el usuario no controla. Con Avro o Protobuf en el wire, esta estrategia es banda alta. Con JSON+Jackson, vale 6.3.

---

**multicloud-context-2 — ArchUnit como sensor de la divergencia aws/gcp (media 8.17) [8.5 / 8.2 / 8.6 / 7.4]**

- **Mecanismo:** tres reglas: `gcp_no_toca_aws`, `aws_no_toca_gcp`, `nucleo_es_agnostico`. Adopcion sobre codigo que ya viola con `FreezingArchRule.freeze()`.
- **Artefacto:** `svc-lib/src/test/java/.../FronteraDeNubeTest.java` + `archunit.properties` + store versionado.
- **Unilateral:** si.
- **Que la subiria de banda: nada, deberia descartarse.** El refutador (7.4) señala que es **redundante con multicloud-context-1**: una vez que `:svc-aws` y `:svc-gcp` son modulos Gradle sin dependencia entre si, el classpath ya hace imposible el cruce y las dos primeras reglas **no pueden fallar nunca**. "Un test que estructuralmente no puede ponerse rojo no es un sensor, es decoracion" — con el coste añadido de mantener a mano listas de paquetes prohibidos. Lo unico irreducible es la tercera regla, que ya la dan desacoplamiento-1 y flink-agents-5.
- **Aporta una cita util para la charla aunque la estrategia se descarte:** arXiv 2601.20404 ("On the Impact of AGENTS.md Files on the Efficiency of AI Coding Agents", 10 repos, 124 PRs, Codex gpt-5.2-codex): −28.64% runtime mediano, −16.58% output tokens, "while maintaining a comparable task completion behavior". AGENTS.md compra velocidad, no correccion.

---

**flink-agents-3 — Gate de compatibilidad de estado sobre tipos de ext-lib (media 8.10) [8.6 / 9.0 / 7.0 / 7.8]**

- **Mecanismo:** PIEZA 1, heredar de `TypeSerializerUpgradeTestBase<PreviousElementT, UpgradedElementT>` (verificado abriendo el fichero en apache/flink master) y afirmar que la compatibilidad esperada es `COMPATIBLE_AS_IS` o `COMPATIBLE_AFTER_MIGRATION`, con los bytes del serializador viejo commiteados. PIEZA 2, un test que falla si el serializador resuelto para un tipo en estado es `KryoSerializer` — motivo textual de la doc: "When Kryo is used, there is no possibility for the framework to verify if any incompatible changes have been made". PIEZA 3, golden de la config de TTL, porque "restore state, which was previously configured without TTL, using TTL enabled descriptor or vice versa will lead to compatibility failure and StateMigrationException".
- **Artefacto:** `svc-lib/src/test/java/.../state/SvcStateSerializerUpgradeTest.java`, `NoKryoInStateTest.java`, `StateTtlGoldenTest.java` + `src/test/resources/serializer-snapshots/<version>/`.
- **Unilateral:** si, entera.
- **Que la subiria de banda: partirla y quedarse con la PIEZA 2.** El refutador lo dice: "esa pieza sola valdria un 8,5; el paquete completo arrastra el coste de las otras dos". El detector de Kryo son decenas de lineas y revela si la evolucion de estado **existe siquiera**.
- **DISCREPANCIA (2.0 puntos):** encaje 9.0 (el mejor diagnostico del catalogo: identifica que japicmp aprueba añadir un campo a un POJO de ext-lib y aun asi cambia el formato binario del estado) frente a **coste 7.0** y refutador 7.8. `TypeSerializerUpgradeTestBase` es API @Internal de un test-jar, con ergonomia aspera, 1-2 dias el primer tipo, y obliga a commitear binarios de referencia por version. Y el riesgo (3) es demoledor: si los tipos de ext-lib no son POJOs limpios ni Avro, la evolucion de estado **no existe** y el gate solo certifica un callejon sin salida.

---

**jvm-contracts-4 — Tests de caracterizacion sobre la deserializacion (media 7.95) [7.5 / 8.7 / 7.6 / 8.0]**

- **Mecanismo:** capturar payloads reales, commitearlos como fixtures, y tests de aprobacion (`Approvals.verify(...)`) que serializan el objeto **ya deserializado** — incluyendo los tipos de ext-lib — a un `.approved.txt`. Segundo nivel: aprobar tambien el resultado de las agregaciones. El mismo par de fixtures corre contra la ruta AWS y la GCP, o sea que es a la vez sensor de contrato y **test de paridad del desacoplamiento en curso**.
- **Artefacto:** `src/test/resources/contracts/<stream>.json` + `src/test/resources/contracts/<caso>.approved.txt`.
- **Unilateral:** si.
- **Que la subiria de banda:** presupuestar el determinismo. El refutador: "timestamps, UUIDs y mapas sin orden en un pipeline de streaming no son casos raros, son el caso normal". Y cerrar el riesgo de sello de goma: si el agente puede regenerar aprobados, el sensor se apaga sin ruido, y CODEOWNERS no es unilateral.
- **Evidencia debil, y el lente lo pilla:** el segundo de sus dos items es el PR PIsberg/async-test-lib#262, cuyo titulo real es "align the repository with the Vibe Architecture scorecard (42 to 66 of 66)", en un repo de 5 estrellas de un solo mantenedor vinculado a su libro autopublicado. Las filas citadas entre comillas no aparecen como tales en el cuerpo del PR. Generalizar eso a "practica de industria 2026" es n=1 autopublicitario.
- **Tiene el mejor mensaje de error del catalogo:** diff campo a campo.

---

**message-schemas-4 — Golden payloads: bytes reales capturados (media 7.62) [8.5 / 8.4 / 6.4 / 7.2]**

- **Mecanismo:** capturar mensajes reales de Kinesis, commitearlos como `.bin` con manifest, pasarlos por el deserializador real. El sensor de verdad es el segundo paso: un job programado que recaptura N mensajes frescos y falla si no deserializan. Lado AWS con SerDe de Glue, `GlueSchemaRegistryDeserializerImpl.getSchema(bytes)` extrae del header el esquema con el que se escribio realmente; lado GCP, `gcloud pubsub schemas validate-message --message-encoding=binary`.
- **Artefacto:** `svc-lib/src/test/resources/payloads/*.bin` + `PayloadContractTest.java` + `.github/workflows/payload-drift.yml`.
- **Unilateral:** si en lo esencial, **con matiz honesto**: la recaptura periodica necesita credenciales de lectura del stream (que el usuario ya tiene), y la variante con `getSchema` necesita red y permiso `glue:GetSchemaVersion`.
- **Que la subiria de banda:** resolver PII y credenciales. **coste 6.4** es la nota mas baja de la banda y el razonamiento es correcto: los nightly son lo primero que nadie mira cuando llevan dos semanas en rojo, las credenciales de produccion en CI son el clasico rojo permanente que acaba desactivado, y sin recaptura los payloads congelados son "un documento disfrazado de test". Anonimizarlos degrada su unica virtud (ser reales).
- **Golpe del refutador que reencuadra su valor:** si el productor cambia el wire format, el job de produccion se rompe **solo**, en minutos, con alarma. Un nightly no llega antes. Lo que si aporta de verdad es la version estatica: red de seguridad cuando **el agente** refactoriza el deserializador.
- **Propiedad unica que ninguna otra tiene:** es el unico sensor que detecta deriva del **otro** equipo sin pedirle nada.

---

## 4. Los dos contratos

### (a) ext-lib como contrato binario verificable — **VALIDADO**

La hipotesis de la ronda 3 es correcta y es el hallazgo que mas eleva el catalogo. ext-lib es un JAR versionado que cruza la frontera organizativa, y en JVM el tooling de compatibilidad es maduro y **utilizable desde el lado consumidor**, que es la propiedad que la ronda 2 nunca encontro en OpenAPI. Tres sensores lo cubren en capas:

| Capa | Estrategia | Que ve | Que NO ve |
|---|---|---|---|
| Firma binaria | jvm-contracts-1 (8.73) | METHOD_REMOVED, CONSTRUCTOR_REMOVED, cambios de firma | comportamiento, wire, formato de estado |
| Superficie usada | desacoplamiento-2 capa C (8.70) | que N clases de ext-lib usas de verdad | ext-lib shadeada/vendorizada dentro de otro JAR |
| Formato de estado | flink-agents-3 (8.10) | un campo añadido a un POJO que rompe el restore | nada si los tipos no son POJO limpios ni Avro |

**El matiz mas util de todo el research, y viene de las notas crudas:** la hipotesis del brief es correcta pero incompleta. *El contrato no es ext-lib entero: es el subconjunto de ext-lib que svc-lib usa de verdad, probablemente decenas de clases de un JAR con cientos.* japicmp te dice si TP rompio su API; **jdeps te dice si te rompio a TI**. La segunda pregunta es la que importa y es mas barata de contestar. Por eso el orden correcto es jdeps primero, japicmp despues usando el resultado como filtro `-i`.

**Verificado y decisivo:** existe **un** caso agentico publicado de esto, no cero. PIsberg/async-test-lib PR #138 (mergeado 04-07-2026): un gate de japicmp con `breakBuildOnBinaryIncompatibleModifications` atrapo un `CONSTRUCTOR_REMOVED` introducido en un PR anterior, y el arreglo lo co-firmo Claude Code. Escala: un solo mantenedor, libro autopublicado. Es una instancia real y fechada, **no una tendencia**. Y en org grande hay intencion sin implementacion: camunda/connectors#7621 (abierta 24-06-2026, etiqueta `agentic-ai`) lista "API/compatibility guards (e.g. japicmp / revapi) for the public connector plus SPI surface" como area a investigar. Sigue abierta.

### (b) El esquema de mensajes como contrato de datos — **VALIDADO PERO BLOQUEADO POR UNA PREGUNTA**

Es el unico contrato que cae **entero** dentro de svc-lib, sin ambiguedad de ownership. El tooling existe y esta vivo: `SchemaCompatibility` esta en el JAR core de Avro (no es una herramienta externa archivable, a diferencia de Optic); `buf breaking` con Action actualizada el 2026-08-14.

Pero el frente entero es el unico sin nada en banda alta, y la causa es una: **no sabemos que formato tiene el wire hoy.** Si es Avro binario o Protobuf, message-schemas-1 es banda alta trivialmente. Si es JSON deserializado con Jackson a POJOs, el refutador tiene razon con su 6.3 y el `.avsc` describe una clase Java que el agente puede abrir.

Restricciones verificadas que condicionan cualquier diseño aqui: Glue acepta proto2/proto3 y **valida compatibilidad al registrar**; Pub/Sub acepta `PROTOCOL_BUFFER` pero **prohibe imports, exige un solo tipo top-level y NO comprueba compatibilidad entre revisiones**. Cualquier diseño que asuma simetria entre los dos registries se rompe en silencio en GCP. Conclusion: **el sensor no puede vivir en la nube, tiene que vivir en el build del usuario.**

### ¿Cual ataca mejor el dolor de "re-descubrir el sistema"?

Ninguno de los dos, tal cual. La respuesta honesta es que el dolor tiene dos mitades y cada contrato ataca una:

- **"¿Que forma tienen los datos que fluyen?"** → el contrato de mensajes, y especificamente el `.approved.txt` de jvm-contracts-4: colapsa tres capas de deserializadores y tipos de otro JAR a **una lectura con valores reales**. Es lo que mas se parece al "indice precomputado consultable" que la ronda 1 encontro en Anyline y Meta.
- **"¿Que depende de que, y que me falta por cortar?"** → no es ninguno de los dos contratos: es el **`archunit_store` congelado** (desacoplamiento-1) mas el `ext-lib-surface.txt` de jdeps (desacoplamiento-2). Son los dos unicos artefactos del catalogo que son a la vez indice precomputado y sensor, y que el **build** mantiene en vez de una persona. Esa es la propiedad que la ronda 1 identifico como la que escala mas alla del techo de mabl ("works well for features spanning 2-3 repos").

### ¿Cual puede montar el usuario SOLO?

**Los dos, enteros, dentro de svc-lib.** Esa es la respuesta corta y es lo que cambia respecto a la ronda 2. Dos condiciones no confirmadas:
- El contrato binario asume que **ext-lib se publica a un repositorio de artefactos**. Si entra por otra via, jvm-contracts-1 no aplica tal cual.
- El contrato binario asume poder **fijar la version de ext-lib explicitamente**. En la lectura "svc vive dentro del build de ext-control", pelearse con su BOM es exactamente el tipo de friccion que la ronda 2 penalizo. El refutador lo marca como el punto (c) de su rebaja.

El contrato de mensajes no tiene ninguna condicion externa: cero credenciales, cero red, cero conversacion.

---

## 5. El desacoplamiento GCP como palanca

El principio: **hay cosas que hoy cuestan minutos y dentro de tres meses cuestan un refactor mas una conversacion.** Todo lo demas puede esperar. Ordenado en el tiempo.

### AHORA, antes de escribir la primera clase del camino GCP (coste: una tarde entera para las tres)

1. **La regla ArchUnit `..svc.gcp..` → NO depende de `..extcontrol..`, sin congelar, naciendo en verde.** Esta es la unica pieza con ventana temporal estricta y valor irrecuperable. El usuario tiene una intencion arquitectonica — "la parte GCP debe ser diferente y no depender de ext-control" — que **hoy solo existe en su cabeza** y que ningun agente puede deducir de ningun import. Un agente al que se le pide reutilizar reutilizara felizmente lo primero que encuentre de ext-control, porque reutilizar es lo que se le pide siempre. Cada dia que pasa añade violaciones que habra que congelar en vez de prevenir. Coste hoy: minutos. Coste en tres meses: refactor.
2. **`bannedDependencies` / `exclude` en el modulo GCP** (desacoplamiento-2 capa A). Es lo mismo pero a nivel de coordenadas y cubriendo lo transitivo, o sea cubre lo que ArchUnit no ve. La ficha lo dice bien: "el dia que creas el modulo GCP el gate cuesta 30 minutos; el dia que ya hay codigo GCP acoplado a ext-lib, quitarlo es refactor y conversacion".
3. **`options.release = 11`.** Una linea. Convierte la restriccion de Java 11 de la imagen Dataproc 2.3 en un error de compilacion local en vez de en un fallo en el cluster a las tres de la mañana. Ademas audita el bytecode de ext-lib contra Java 11 **dentro del build propio**, o sea verifica al equipo TP sin pedirle nada.

### ESTA SEMANA, mientras el codigo GCP es poco (coste: 1-2 dias)

4. **La separacion en modulos Gradle** (multicloud-context-1, 8.90). Mueve la frontera de nube al fichero de build, donde el compilador es el guardarrail y no hay nada que sincronizar. Cuanto mas codigo GCP exista, mas caro es el reparto de paquetes. Bonus operativo: hoy un unico svc-lib arrastra el SDK de AWS al Dataproc de GCP, con el limite de 512 MB del JAR de MSF documentado al lado.
5. **`api` → `implementation` para ext-lib** (desacoplamiento-2 capa B). Hacerlo ahora, cuando la respuesta es informativa pase lo que pase: si compila, ya estabas mas desacoplado de lo que creias; si no, la lista de errores del compilador ES el inventario de fuga de ext-lib en tu superficie publica — que es research que hoy se hace a grep.
6. **El detector de Kryo** (flink-agents-3 pieza 2). Decenas de lineas. Responde una pregunta binaria y urgente: **¿existe siquiera la evolucion de estado en svc?** Si un tipo cae a Kryo por un generico o un campo sin getter, "there is no possibility for the framework to verify if any incompatible changes have been made" y el codigo se ve identico. Si la respuesta es "no existe", eso cambia el plan entero y es mejor saberlo antes de invertir en flink-agents-3 completa.

### ANTES DE QUE HAYA ESTADO GCP EN PRODUCCION

7. **uid() explicito en todos los operadores stateful + golden de uids** (flink-agents-2). Ventana dura: despues hay que mapear los hashes viejos con `setUidHash()`. La ficha lo dice con precision: "si svc ya lleva tiempo en produccion sin uids, este es el momento mas barato que va a haber nunca".

### CUANDO EL CAMINO GCP YA CORRA

8. **Fixtures compartidos AWS/GCP como test de paridad** (jvm-catalog-4 / message-schemas-4 version estatica). El mismo par de fixtures contra las dos rutas verifica que el desacoplamiento produce el mismo resultado. Es un test que el desacoplamiento **va a necesitar si o si**, asi que su coste esta ya presupuestado en el trabajo aprobado.

### Lo que NO hay que hacer ahora

Nada que toque ext-control, nada que requiera fijar versiones contra su BOM, nada que exija que el equipo TP publique o apruebe algo. Y **no unificar Kinesis y Pub/Sub tras una interfaz comun**: desacoplamiento-3 murio por killshot exactamente por eso — unifica dos fuentes con garantias de entrega y de orden distintas (exactly-once vs at-least-once), en un sistema stateful, justo cuando el usuario ha dicho que quiere la rama GCP separada.

---

## 6. El codebase de streaming: que NO se infiere grepeando

Aplicando el criterio de Osmani (si se descubre grepeando, borralo). Esta es la respuesta directa al dolor: **la razon por la que el research se re-hace cada sesion no es que los ficheros sean dificiles de encontrar, es que buena parte de lo que hay que saber no esta en ningun fichero.**

| # | Conocimiento no inferible | Por que no se grepea | ¿Verificable automaticamente? |
|---|---|---|---|
| (a) | **Identidad de operador.** El estado en un savepoint se ata al operador por uid. Los IDs autogenerados "depend on the structure of your program and are sensitive to program changes". Insertar un `map()` en medio del DAG rompe el restore: compila, pasa tests, muere al desplegar. | La informacion no es "que hace este operador", es "estos uids son un contrato con datos que ya existen en produccion". No hay nada que grepear. | **SI.** Golden de `StreamNode#getTransformationUID()` + `pipeline.auto-generate-uids=false`. |
| (b) | **TTL es un cambio de esquema.** "Trying to restore state, which was previously configured without TTL, using TTL enabled descriptor or vice versa will lead to compatibility failure and StateMigrationException." Añadir `.enableTimeToLive()` parece higiene; es una migracion destructiva. | El diff es de una linea y parece inocuo. | **SI.** Golden de la config de TTL de cada StateDescriptor. Decenas de lineas. |
| (c) | **Que serializador esta realmente en uso.** "Schema evolution is supported only for POJO and Avro types". Si un tipo cae a Kryo por un generico o un campo sin getter, el codigo **se ve identico** y la evolucion de estado desaparece en silencio. Contagia a los tipos contenidos. | Es una propiedad de resolucion de TypeInformation en runtime, no un simbolo en el fuente. | **SI, y es lo mas barato del catalogo.** Test que resuelve la TypeInformation y falla ante `GenericTypeInfo`/`KryoSerializer`. |
| (d) | **Las garantias cambian al cruzar de nube.** Kinesis: exactly-once, checkpoint por shard, orden por partitionId a traves de resharding, `approximateArrivalTimestamp` como event time. Pub/Sub: "at-least-once guarantee and as such the connector delivers the same guarantees", ack solo en checkpoints exitosos, y "if the time between successful checkpoints is larger than the acknowledgment deadline of your subscription messages will most likely be processed multiple times". El connector aun lleva la nota "It has not received widespread testing yet". | Portar una agregacion asumiendo la misma semantica es un bug **de datos**, no de compilacion. | **PARCIALMENTE.** El invariante "intervalo de checkpoint < ack deadline de la suscripcion" **si** es asertable como test sobre la config. La diferencia exactly-once / at-least-once y sus consecuencias sobre las agregaciones **no**: es prosa irreducible. |
| (e) | **El runtime no es el mismo.** AWS MSF no expone `flink run -s`: expone `ApplicationRestoreType` con `SKIP_RESTORE_FROM_SNAPSHOT / RESTORE_FROM_LATEST_SNAPSHOT / RESTORE_FROM_CUSTOM_SNAPSHOT`. Dataproc corre Flink sobre YARN (application/per-job/session) y documenta que los clusters HA de 3 masters no soportan el modo HA de Flink. | Un agente que escriba el runbook de despliegue de las dos nubes a partir del codigo **inventara la mitad**. | **NO.** Prosa irreducible. Es el unico item que merece un AGENTS.md sin disculpas. |
| (f) | **Topologia de particionado.** `keyBy` define no solo correccion sino la unidad de estado y de paralelismo. Cambiar la KeySelector redistribuye todo el estado: el restore "funciona" y los resultados salen mal. | Corrupcion silenciosa. Ningun test de unidad la ve. | **SI.** Golden de `env.getExecutionPlan()`, que incluye `SHIP_STRATEGY = inEdge.getPartitioner().toString()` por arista (verificado abriendo JSONGenerator.java). |
| (g) | **El formato binario del estado depende de clases de OTRO equipo.** Si svc-lib guarda en estado tipos de ext-lib, un bump que añade un campo a un POJO es binario-**compatible** y aun asi cambia la evolucion de estado. | japicmp da verde. El compilador da verde. Solo se ve en el restore. | **SI, con coste.** `TypeSerializerUpgradeTestBase`, API @Internal, 1-2 dias el primer tipo. |

**Balance: 5 de 7 son verificables mecanicamente, 1 parcialmente, y solo 1 es prosa irreducible.** Ese es el numero que responde al dolor. La regla operativa que sale de aqui:

> En un CRUD el harness sirve para que el agente **encuentre** las cosas mas rapido. En streaming stateful el harness sirve para que el agente **no destruya datos que ya existen**. El fallo tipico no es "la IA no encontro el fichero", es "la IA hizo un cambio que compila, pasa los tests y rompe el restore". Y eso convierte el smell de harness insuficiente en algo medible: **si tu suite verde no distingue un cambio que preserva el estado de uno que lo pierde, tu harness no es insuficiente para la IA, es insuficiente para cualquiera.**

**Hallazgo que invalida una sospecha del brief:** "nadie escribe harness para streaming" es **falso**. apache/flink añadio AGENTS.md el 2026-04-17 (FLINK-39477), lo edito 5 veces hasta el 2026-08-06, y tiene AGENTS.md **anidados por modulo** (flink-table-planner, flink-table-runtime). Tambien flink-cdc (2026-07-07), flink-kubernetes-operator (2026-08-05), paimon, fluss, streampark, seatunnel. Y los tres de streaming **convergen sin coordinarse** en poner lo mismo en su seccion "Ask first": flink → "Changes to serialization formats (affects state compatibility)" y "Changes to checkpoint/savepoint behavior"; flink-cdc → "Changes to serialization formats or checkpoint behavior"; flink-kubernetes-operator → "Changes to savepoint/checkpoint or state-upgrade behavior (affects job recovery)". La comunidad que mas sabe de streaming dedica su seccion de mayor riesgo exactamente al eje estado/serializacion/savepoint. **El usuario no tiene que inventar la plantilla, tiene que podarla.**

---

## 7. Herramientas de indice: ¿le sirven en JVM?

**Respuesta corta: no como las vendio la ronda 1, y la verificacion es concreta.**

La ronda 1 concluyo que Anyline (6 repos) y Meta (4 repos) superan el techo de mabl con "indice precomputado consultable + memoria entre sesiones". La ronda 3 fue a comprobar el mecanismo y encontro esto:

- **Serena** tiene en `project.yml` el campo `ls_additional_workspace_folders`, con el ejemplo `../sibling-package` y `../shared-lib` en su propio template — que es literalmente el caso ext-control / ext-lib. **Pero la propia doc que la estrategia cita dice que esas carpetas "are not indexed by Serena"**: se registran como LSP workspace folders para que el servidor de lenguaje resuelva simbolos, no se indexan. Y el indexado de librerias externas es exclusivo del plugin de pago. Eso es exactamente el killshot que descalifico multicloud-context-4 (media 6.13): *"la capacidad distintiva —resolver simbolos de ext-lib sin su repo— esta explicitamente excluida en la doc que la propia estrategia cita"*. El otro killshot: clones hermanos de repos ajenos sincronizados a mano con fallo silencioso, mas varios GB de servidores de lenguaje por dev.
- **Scala es el eslabon debil en las tres herramientas revisadas.** El Hybrid LSP de codebase-memory-mcp no lo soporta. Metals tiene un caveat documentado con multiples build tools. Y si svc-lib es Scala, Flink 2.0 elimino ademas la Scala DataStream API entera.
- **El hueco duro, y es real, no una carencia de busqueda:** *no existe herramienta que genere contexto legible A PARTIR de la superficie de un JAR de terceros del que solo tienes el binario.* El patron "fichero `.api` commiteado que es sensor y contexto a la vez" existe y esta maduro, pero **solo en direccion productor** (dumpeas TU libreria desde TU codigo fuente). japicmp emite **diffs**, no dumps. metalava y el `abiValidation` del Kotlin Gradle Plugin necesitan fuentes. El apaño es `javap` sobre las clases, o japicmp contra un JAR vacio para que todo salga como añadido.

**El reencuadre que hay que hacer, y es la respuesta util:** el indice precomputado consultable que la ronda 1 buscaba **ya existe en JVM y no es un MCP**. Es el grafo de build (`./gradlew projects`, `./gradlew -q dependencies --configuration compileClasspath`, `dependencyInsight`), es `jdeps` para la superficie realmente usada, es `javap` para el binario ajeno, y es el compilador para la resolucion de tipos. Todo con bus factor institucional, cero instalacion, cero credenciales, y consumible por CLI — que es exactamente lo que **el panel de la ronda 2 ya prefirio frente a un servidor MCP**. La honestidad que hay que mantener: la doc de Gradle no documenta salida JSON para el grafo de dependencias, el formato es texto de arbol.

---

## 8. Plan concreto

Marcado con banda de confianza. **Nada de la columna unilateral requiere hablar con el equipo TP.**

### Unilateral — camino critico, por impacto/esfuerzo

| # | Accion | Coste | Banda | De |
|---|---|---|---|---|
| 1 | `options.release = 11` en el build de svc-lib | **1 linea** | 8.5–8.9 | multicloud-context-3 (mitad barata) |
| 2 | Regla ArchUnit `..svc.gcp..` → no `..extcontrol..`, sin congelar + freeze del resto | **1-2 h** | 8.5–8.9 | desacoplamiento-1 (usar esta, no las otras dos) |
| 3 | `bannedDependencies`/`exclude` de ext-lib en el modulo GCP | **30-60 min** | 8.5–8.9 | desacoplamiento-2 capa A |
| 4 | Detector de Kryo en tipos que van a estado | **~1 h** | 7.5–8.4 (la pieza sola, ~8.5) | flink-agents-3 pieza 2 |
| 5 | `api` → `implementation` para ext-lib | 30 min…**dias** si hay fuga real | 8.5–8.9 | desacoplamiento-2 capa B |
| 6 | Golden de TTL de StateDescriptors | ~2 h | 7.5–8.4 | flink-agents-3 pieza 3 |
| 7 | Separacion en modulos Gradle `:svc-core` / `:svc-aws` / `:svc-gcp` | **1-2 dias** | 8.5–8.9 | multicloud-context-1 (la mejor del catalogo) |
| 8 | `jdeps` surface de ext-lib commiteada + diff en CI | 1-2 h | 8.5–8.9 | desacoplamiento-2 capa C |
| 9 | uid() explicito + golden de uids + golden de execution plan | 100-200 lineas + primera pasada | 8.5–8.9 | flink-agents-2 |
| 10 | Gate japicmp del bump de ext-lib, con `-i` alimentado por (8) | 1 tarde | 8.5–8.9 | jvm-contracts-1 |
| 11 | Matriz `flink117`/`flink2x` como suites de `check` | medio dia + CI x2 | 8.5–8.9 (pieza incubating) | multicloud-context-3 |
| 12 | Contrato de mensajes — **solo despues de responder cual es el wire** | media tarde…dias | 8.23 o 6.3 segun la respuesta | message-schemas-1 / jvm-contracts-4 |
| 13 | `TypeSerializerUpgradeTestBase` para los tipos de ext-lib en estado | **1-2 dias el primero** | 7.5–8.4 | flink-agents-3 pieza 1 |

**Si solo se hace UNA cosa:** el (1) + (2) + (3) juntos, que caben en una tarde y cubren tres clases de fallo distintas. **Si solo se hace una estructural:** el (7), que es la unica con media 8.9 y refutador 9.1.

**Orden con dependencias reales:** 1→2→3 (hoy, ventana temporal) → 4 (responde una pregunta binaria que puede cambiar el plan) → 7 (habilita que 3 y 11 sean limpios) → 8 (genera el filtro de 10) → 10 → 9 → 11 → 12/13.

### Requiere hablar con el equipo TP — nada esta en el camino critico

| Situacion | Cuando aparece | Que hacer |
|---|---|---|
| ext-lib **no** se publica a un repositorio de artefactos | Bloquea el (10) | Preguntar por que via entra. Sin esto japicmp no aplica tal cual |
| La version de ext-lib viene heredada del BOM de ext-control | Bloquea el pin del (10) en la lectura "svc es modulo" | Es la friccion que el refutador marca. Fijar version localmente si se puede; si no, aplazar el (10) |
| El gate detecta una rotura real | Cuando (10) o (13) se pongan rojos | El gate detecta pero no arregla. Pero llegar a esa conversacion con **un test rojo y un nombre de campo** es cualitativamente distinto de llegar con una sospecha |
| `pipeline.auto-generate-uids=false` | Capa 3 del (9) | Si el arranque del job lo hace ext-control, toca su configuracion. Las capas 1 y 2 dan el 90% con el 0% de negociacion |
| Un test de svc rompe el build compartido | Solo en la lectura "svc es modulo dentro del repo de ext-control" | Reglas apuntando solo a `..svc..` y naciendo congeladas. Coste politico, no tecnico |

---

## 9. Municion nueva para la charla

Solo lo que no salio en las rondas 1 y 2.

**1. El caso publico donde el sensor funciona y el harness lo ignora — abrir con esto.**
PIsberg/async-test-lib: repo con `AGENTS.md`, `CLAUDE.md` de 13KB, `GEMINI.md` de 16KB y directorio `.claude`. En el PR #138 (mergeado 04-07-2026) un gate de japicmp atrapo un `CONSTRUCTOR_REMOVED` y el arreglo lo co-firmo Claude Code. **Y el `CLAUDE.md` no menciona japicmp ni compatibilidad binaria en ninguna parte** — verificado abriendo el raw. El sensor funciona; el harness no le habla de el; el agente solo se entera cuando el build se pone rojo. Es la tesis del usuario con un ejemplo publico y fechado. Honestidad obligatoria en la slide: un solo mantenedor, vinculado a un libro autopublicado suyo. Es una instancia, no una tendencia.

**2. El numero que separa velocidad de correccion.**
arXiv 2601.20404, v2 del 30-03-2026, 10 repos / 124 PRs con Codex gpt-5.2-codex: AGENTS.md baja el runtime mediano **−28.64%** y los output tokens **−16.58%**, "while maintaining a comparable task completion behavior". Es la unica medicion publicada del efecto de AGENTS.md. Traduccion: **AGENTS.md compra velocidad, no correccion.** Documentar la divergencia AWS/GCP hara al agente mas rapido y no le impedira aplicar el patron de AWS en el camino de GCP.

**3. El caso tecnico que demuestra que el agente no se equivoca por tonto.**
Un agente competente escribira `env.fromSource(pubSubSource, ...)` para el camino de GCP, porque es el patron correcto, moderno, y el que ve en el modulo de al lado. Y `PubSubSource` es un `RichSourceFunction` — clase que Flink 2.0 **elimino**. La imagen Dataproc 2.3 trae Flink 1.17.0 y Java 11; AWS MSF ya va por 2.2/2.3. El agente se equivoca porque **el harness no le dijo que las dos mitades de su propio repositorio viven en dos epocas distintas de la misma libreria**. Eso no se arregla con mas contexto. Se arregla con una segunda suite de tests. Es el slide mas potente del material tecnico y esta verificado abriendo el codigo fuente del conector, no inferido.

**4. La documentacion oficial que envejece y el agente que la copia.**
La doc de AWS muestra `software.amazon.glue:schema-registry-flink-serde:1.0.0` y `schema-registry-serde:1.1.5`. Maven Central tiene **1.1.27** en ambos, con `lastUpdated 20260122001125`. Un agente que copie el pom de la documentacion oficial se lleva una version de hace años. Ejemplo perfecto, de una sola diapositiva, de por que el harness necesita sensores y no documentos.

**5. La contra-evidencia, puesta delante y no escondida.**
Böckeler, 10-08-2026, "TDD inside the agent loop - theater or actual value?" (https://martinfowler.com/articles/exploring-gen-ai/tdd-in-the-agent-loop.html): cinco tandas, generacion con Sonnet 4.6, juicio con Opus 4.8, **sin diferencia discernible de calidad** entre workflow TDD y no-TDD, a veces el no-TDD puntuo mejor, y **3-8.5x mas tokens** con TDD. Su frase: *"watching a test go red is only proof of anything if someone is checking why it went red."* Es una objecion directa a toda esta tesis, es de hace una semana, y la audiencia la va a conocer. La respuesta defendible: el estudio mide **el ritual del agente escribiendo tests dentro del loop**, no el valor de un gate preexistente en CI que el agente no controla y no puede reescribir. No es lo mismo. Pero decirlo como interpretacion, no como refutacion.

**6. El apoyo con nombre propio, publicado seis meses antes.**
Böckeler, 17-02-2026, "Harness Engineering - first thoughts" (https://martinfowler.com/articles/exploring-gen-ai/harness-engineering-memo.html), citando al equipo de Codex de OpenAI: *"When the agent struggles, we treat it as a signal: identify what is missing — tools, guardrails, documentation — and feed it back into the repository"*, con restricciones arquitectonicas aplicadas mediante *"deterministic custom linters and structural tests"*. Es casi literalmente la tesis del usuario, de una fuente que la audiencia respeta, y nombra la categoria exacta de mecanismo del plan de §8. El memo **no reporta metricas** — decirlo.

**7. Una linea sobre por que "libreria" gana a "herramienta".**
Optic, la herramienta canonica de drift de API, archivada el 12-01-2026. `SchemaCompatibility.checkReaderWriterCompatibility` es un metodo dentro del JAR core de Avro. **Una llamada de libreria no puede archivarse.** Criterio de seleccion de tooling de harness en una frase.

### El angulo meta: ¿lo sostienen los datos?

**Si, y con matices que hay que decir.** El arco es:

- **Ronda 1** (11 agentes): descubrio que AGENTS.md anidado no cruza repos (es mecanismo de arbol de directorios) y que mabl publica su techo — "works well for features spanning 2-3 repos. Beyond that, agents lose track of dependencies". Conocimiento general, ninguna recomendacion accionable.
- **Ronda 2** (10 agentes, mismo panel, mismo corte): **24 estrategias, cero pasaron, la mejor 7.85.** Y — este es el dato — **casi ninguna murio por falta de evidencia**: el calificador de evidencia solo firmo 1 de 17 killshots. Murieron por "no compras nada con esto" y por "esto empieza pidiendole permiso a otro owner". Ademas gasto trabajo en Kafka, Confluent Schema Registry, OpenAPI y Optic — cuatro cosas que resultaron **irrelevantes** para el sistema real.
- **Ronda 3** (mismo panel, mismo corte, unica variable cambiada: la arquitectura): **7 estrategias en 8.5-8.9, techo 8.90, nueve puntuaciones de lente ≥9.0.**

La formulacion mas honesta y a la vez mas fuerte: **la causa de muerte de la ronda 2 era un diagnostico de contexto ausente, no de capacidad ausente.** "Esto empieza pidiendo permiso a otro owner" solo es un veredicto posible mientras no sabes que existe svc-lib, un artefacto que el usuario posee al 100% y que esta exactamente sobre las dos direcciones del acoplamiento. En el momento en que ese dato entra, la unilateralidad deja de ser un obstaculo y pasa a ser gratis: las siete de banda alta la tienen. Nadie penso mejor en la ronda 3. Se le dio al proceso el contexto que le faltaba.

**Dos matices que hay que poner en la misma diapositiva o el argumento se cae en el turno de preguntas:**
1. **Siguen siendo cero por encima de 9.0.** El contexto subio el techo 1.05 puntos y no cruzo la barra. La tesis correcta es "el harness insuficiente explica la mayor parte de la diferencia", no "el harness lo explica todo".
2. **La rubrica de la ronda 3 añadio una clausula de bonificacion** que los lentes citan explicitamente. Es un confound. Parte del salto es contexto y parte es rubrica; no se puede separar limpiamente con estos datos.

Con esos dos matices puestos, el arco es solido y es el mejor material de la charla: **tres rondas de research sobre el mismo problema, dos de ellas produjeron cero recomendaciones adoptables, y la variable que cambio no fue el modelo ni el numero de agentes ni el presupuesto de busqueda — fue decirle al sistema en que codebase estaba.**

---

## 10. Lo que sigue sin saberse

### La ambiguedad no resuelta: ¿svc es repo propio o modulo dentro de ext-control?

**No cambia nada de lo que esta en el camino critico**, porque svc-lib es del usuario en las dos lecturas y ahi vive todo el plan de §8. Lo que si cambia:

| | svc = repo propio | svc = modulo dentro del repo de ext-control |
|---|---|---|
| ArchUnit | Se puede **endurecer a nivel de coordenadas del build** (no declarar ext-control como dependencia), que es mas fuerte que una regla de paquete | La regla por paquete es el **unico** mecanismo disponible, y por eso pasa a ser imprescindible. Mas violaciones legitimas que congelar de entrada |
| Pin de version de ext-lib (japicmp) | Trivial | Puede venir del **BOM de ext-control**; pelearse con el es friccion con otro owner. Es el punto (c) de la rebaja del refutador a jvm-contracts-1 |
| AGENTS.md | La raiz es del usuario | La raiz es de TP; el usuario se limita a subdirectorios y hay que verificar que no colisione. **El anidamiento por directorio SI funciona** — era cruzar REPOS lo que fallaba en la ronda 1, no anidar dentro de uno |
| Un test que rompe el build | Sin coste | Puede romper el build del equipo TP. Coste politico, pagado en conversaciones y no en horas |
| Serena (si se usara) | `ls_additional_workspace_folders` | `ls_workspace_folders` |

**Recomendacion de dos investigadores independientes, y coincide:** no tocar svc hasta que lo de svc-lib este funcionando.

### Preguntas que cambian el ranking y que solo el usuario puede responder

1. **¿Que formato tiene el wire HOY?** Avro binario / Protobuf / JSON con Jackson. Cambia message-schemas-1 de 8.23 a banda alta o a 6.3. Si es JSON+Jackson, las estrategias de codegen pasan de unilaterales a negociadas con ext-control.
2. **¿El productor de Kinesis usa el SerDe de Glue?** Si lo usa, cada mensaje lleva el esquema en el header y message-schemas-4 se vuelve trivial y potente. Si no, hay que inferir del payload crudo.
3. **¿svc construye su DAG de Flink dinamicamente a partir de las plantillas que procesa ext-control?** Dos investigadores la marcaron independientemente como **la primera pregunta a hacer**. Si la respuesta es si, el golden de flink-agents-2 deja de ser un fichero y pasa a ser una funcion de un conjunto de plantillas fixture — mas caro y mas valioso. Y ArchUnit da falsos verdes sobre acoplamiento por nombre de clase en plantilla, que es un guardarrail dando falsa seguridad en su caso de uso central.
4. **¿Maven o Gradle?** La mitad de los mecanismos estan escritos en DSL de Gradle. El equivalente Maven existe en todos los casos pero la ficha de multicloud-context-1 lo resuelve en una linea **sin verificarlo**.
5. **¿svc-lib tiene Scala?** Si si: japicmp → MiMa; ArchUnit → falsos positivos con clases sinteticas (`$anonfun`, companions); y Flink 2.0 elimino la Scala DataStream API entera.
6. **¿Que version de Flink corre realmente en MSF, y que imagen de Dataproc?** La matriz 1.17 vs 2.3 esta inferida de documentacion, no de la config del usuario. Si resulta ser 1.20 en AWS, la matriz sigue teniendo valor pero pierde el filo del corte de API.
7. **¿ext-lib se publica a un repositorio de artefactos?** Si no, jvm-contracts-1 no aplica tal cual.

### Claims no verificados dentro del catalogo

- "44 ficheros referencian `TypeSerializerUpgradeTestBase` dentro de apache/flink" — el lente de evidencia declara no haberlo comprobado.
- Numeros de linea 388/396 en `StreamNode.java` — no verificados uno a uno.
- La equivalencia Maven `<modules>` de multicloud-context-1 — afirmada en una linea, sin comprobar.
- El coste del shadowJar duplicado en multicloud-context-1 — declarado, **no dimensionado**.

### Ausencias reales, distinguiendo los dos tipos

**Herramientas maduras SIN uso agentico documentado** (adopcion verificada, aplicacion nueva — esto es material de charla, no riesgo de que no funcione): ArchUnit para fronteras de nube, `TypeSerializerUpgradeTestBase` como guardarrail de agente, State Processor API como gate de CI, `pipeline.auto-generate-uids`, maven-enforcer bannedDependencies, jdeps, `buf breaking`, Avro `SchemaCompatibility`. Ninguna tiene un caso publicado abierto donde se use explicitamente como guardarrail agentico. **La brecha es de literatura, no de tecnologia.**

**Ausencias reales:**
- No existe **"japicmp para savepoints"**: ninguna herramienta verifica automaticamente la compatibilidad de un savepoint contra un JAR nuevo. En JVM el tooling de compatibilidad de API binaria es maduro, pero el contrato que de verdad rompe produccion en un pipeline stateful — el formato del estado persistido — no tiene equivalente empaquetado. Hay que montarselo. **Buen final de charla.**
- No existe herramienta que emita un **dump legible de la superficie de un JAR de terceros** del que solo tienes el binario (§7).
- Cero casos publicados con metricas de **extraccion de un componente fuera del servicio de otro equipo** asistida por agentes. Lo mas cercano con numeros es Thoughtworks CodeConcise (24-09-2024, ~6 semanas → ~2 en reverse engineering de 10.000 lineas de COBOL/IDMS), pero es comprension de un monolito propio, no extraccion cross-team, y es anterior a la generacion actual de agentes.
- Cero relatos de ingenieria de un equipo ejecutando el mismo JAR de Flink sobre Managed Flink de AWS y sobre Dataproc.
- Sigue sin haber casos de propiedad compartida de un repo entre equipos con el mecanismo de frontera para agentes documentado — coherente con la ronda 2.

### Limitacion metodologica de la ronda entera, declarada

**Tres de los cinco investigadores reportaron el presupuesto de WebSearch agotado (200/200) antes de su primera llamada.** Compensaron con WebFetch sobre URLs canonicas, la API de GitHub (busqueda de issues y de codigo autenticada) y la API de arXiv, y todos declaran haber abierto cada URL citada — lo cual da verificacion de primera mano excelente pero **cobertura de descubrimiento pobre**. Consecuencia concreta: el barrido de casos agenticos publicados en 2026 fue esencialmente inexistente. La ausencia de casos publicados de ArchUnit/japicmp/TypeSerializerUpgradeTestBase como guardarrail agentico es un **dato negativo debil**, no una ausencia comprobada. Hueco concreto que cubriria una ronda 4 con presupuesto: buscar "binary compatibility" / "japicmp" / "ArchUnit" dentro de ficheros `AGENTS.md` y `CLAUDE.md` via GitHub code search autenticada.