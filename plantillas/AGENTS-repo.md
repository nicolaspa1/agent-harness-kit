<!-- PLANTILLA. Uno por repo, en su raíz. Máximo 25 líneas.
     Regla: si el agente lo descubre leyendo el código, NO va aquí. -->

# <nombre-del-repo>

<Una o dos frases: qué es y dónde se ejecuta.>

## Dónde está qué

<!-- Punteros, no volcados. Ahorras el primer grep de cada sesión. -->
- <ej: las operaciones están en el enum X, cada una con sus validaciones.>
- <ej: la deserialización entra por Y.>

## Lo que no se deduce leyendo el código

<!-- Restricciones de runtime, historia, cosas que viven fuera del repo. -->
- <ej: este JAR corre sobre Flink 1.17 y Java 11 en Dataproc; ojo con las APIs.>
- <ej: los tipos de X se persisten como estado; cambiarlos rompe savepoints.>

## Gotchas

<!-- Cosas que han mordido a alguien. Se añaden CUANDO muerden, no por si acaso. -->
- <ej: ...>

<!-- NO pongas aquí: descripción de la arquitectura de carpetas, listado de clases,
     resumen de qué hace cada paquete, convenciones que ya impone el linter.
     Todo eso lo descubre el agente solo y encarece cada sesión. -->
