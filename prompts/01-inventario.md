# Prompt 1 — Inventario y mapa de repos

Estás en la raíz de un workspace con varios repositorios de un sistema de
procesamiento en tiempo real (SVC) sobre Flink, desplegado en AWS (Managed Flink,
Kinesis) y GCP (Dataproc, Pub/Sub).

Tu tarea es producir un inventario que sirva de base para los análisis siguientes.

## Qué investigar, por cada repo

- **Propósito real**, deducido del código, no del README. Si el README y el código se
  contradicen, dilo.
- **Puntos de entrada**: mains, controllers, handlers, jobs, funciones de Flink.
- **Paquetes o módulos principales** y qué responsabilidad tiene cada uno.
- **Dependencias externas relevantes** (no listes todas: las que expliquen qué hace).
- **Dependencias hacia los otros repos de este workspace**, y por qué vía: import de
  código, coordenada de artefacto, llamada HTTP, cadena de configuración, bucket.
  Anota la vía exacta con el fichero y la línea.
- **Configuración específica de nube** y dónde diverge AWS de GCP.
- **Tests**: qué hay y qué cubren de verdad.

## Reglas

- Cita rutas de fichero reales. Sin rutas, no lo escribas.
- Cuando no estés seguro, escríbelo en una sección "Dudas", no lo maquilles.
- No propongas mejoras. Este prompt es solo describir lo que hay.

## Salida

Escribe `_harness-kit/salida/01-inventario.md` con:
1. Una tabla resumen: repo | lenguaje | build | propósito en una línea.
2. Una sección por repo con lo de arriba.
3. Una tabla final de **dependencias entre repos**: origen | destino | vía | evidencia
   (fichero:línea).
4. Una sección **Dudas** con lo que no pudiste determinar.

Cuando termines, dime solo la ruta del fichero y las tres cosas que más te
sorprendieron.
