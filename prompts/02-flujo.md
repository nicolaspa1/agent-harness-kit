# Prompt 2 — El flujo end-to-end

Lee primero `_harness-kit/salida/01-inventario.md`. Es el inventario del sistema.

Tu tarea ahora es distinta y más difícil: **trazar el recorrido completo de un dato**,
desde que entra al sistema hasta que sale, cruzando los repos que haga falta.

## Lo que hay que reconstruir

Sigue el hilo y documenta cada salto:

1. **Ingesta**: cómo entra un mensaje. Kinesis en AWS, Pub/Sub en GCP. Qué formato
   tiene en el cable (JSON, Avro, Protobuf, otro), dónde se declara ese formato, y
   dónde exactamente se deserializa.
2. **Creación de la aplicación**: cómo se crea y arranca la app de Flink, quién lo
   dispara, y con qué configuración.
3. **El puntero al JAR**: la librería se sube a un bucket y el control plane le indica
   a la aplicación dónde está. **Encuentra ese punto exacto en el código y cita el
   fichero y la línea.** Es un acoplamiento que no es un import y que ninguna
   herramienta de análisis estático detecta.
4. **El pipeline**: qué operadores hay, en qué orden, dónde hay `keyBy`, dónde hay
   estado, y dónde se aplican las agregaciones.
5. **Las operaciones**: dónde está el catálogo (probablemente un enum), qué
   validaciones lleva cada una, y cuáles son propias y cuáles vienen de la librería
   del otro equipo.
6. **Salida**: a dónde va el resultado.
7. **Las diferencias AWS / GCP** en cada uno de los pasos anteriores.

## Reglas

- Un salto sin fichero:línea no está documentado. Márcalo como no verificado.
- Si un salto pasa por configuración, variable de entorno o infraestructura y no por
  código, **eso es lo más valioso de este documento**: destácalo.
- No inventes el camino de GCP si aún está a medias. Documenta lo que existe y marca
  lo que está en construcción.

## Salida

Escribe `_harness-kit/salida/02-flujo.md` con:
1. Un diagrama en Mermaid del recorrido completo, con los repos como subgraphs.
2. El recorrido paso a paso, cada paso con su fichero:línea.
3. Una sección **Acoplamientos invisibles**: todo enlace entre componentes que no sea
   un import ni una llamada — punteros en configuración, nombres de bucket, variables
   de entorno, convenciones de nombrado. Esta sección es la más importante del
   documento.
4. Una tabla **AWS vs GCP** con las divergencias.
5. **Dudas** y pasos no verificados.

Al terminar, dime la ruta y cuántos acoplamientos invisibles encontraste.
