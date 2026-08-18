<!-- PLANTILLA. Va en la raíz del workspace (svc/AGENTS.md).
     Máximo 40 líneas. Si crece más, algo inferible se ha colado. -->

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
