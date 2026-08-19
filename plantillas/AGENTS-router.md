<!-- PLANTILLA. Va en la raíz del workspace (svc/AGENTS.md).
     Máximo 40 líneas (~1.000 tokens, que es el presupuesto que fija Aider para
     su repo map por defecto). Si crece más, algo inferible se ha colado.

     CRITERIO DE ADMISIÓN, en una línea: entra si un agente con acceso a todo el
     workspace NO podría deducirlo leyendo código.

     APUNTA A IDENTIDAD, NO A RUTAS. Coordenadas Gradle/Maven y nombre del artefacto
     en el bucket, no `src/main/java/.../Handler.java`. Las rutas son lo primero que
     se pudre; las coordenadas aguantan. (Lección de diseño de SCIP.)

     VA EN LA RAÍZ, no anidado: tras compactar contexto, Claude Code relee y reinyecta
     el fichero raíz, pero NO los anidados ni las reglas con `paths:`. -->

# SVC — workspace

<!-- Una línea por repo. Qué es, no cómo está hecho. -->
- `svc-control` — <control plane. Qué hace exactamente>
- `svc-lib` — <el JAR de Flink. Dónde se ejecuta>
- `ext-lib` — <librería del equipo TP. Qué usamos de ella>
- `svc-docs` — <documentación, ejemplos, research>
- `locks` — <qué es>

## Acoplamientos que no están en el código

<!-- LO MÁS IMPORTANTE DEL FICHERO.
     Enlaces reales entre componentes que no son imports ni llamadas.
     Salen del prompt 2. Si esta sección está vacía, no la has buscado bien. -->

- <ej: svc-lib se sube a un bucket; svc-control le pasa la ruta a la app al crearla.
  No hay import entre ambos: el enlace solo existe en runtime.>

## Decisiones que no hay que volver a discutir

<!-- Un agente que no las sepa propondrá justo lo que ya se descartó. -->

- <ej: el control plane de ext-control se evaluó y no servía para nuestros workflows.>
- <ej: el camino de GCP se maneja solo desde svc-control.>

## Si el cambio es… toca

| Cambio | Repo | Y probablemente |
|---|---|---|
| <tipo de cambio> | <repo> | <otro repo> |

## Antes de planificar un flujo completo

Lee `svc-docs/architecture/flow.md`.
