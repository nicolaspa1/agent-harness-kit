# Cambios de la actualización de agosto 2026

Documento temporal para copiar a mano en el portátil de trabajo. **Bórralo una vez
copiado.**

Contexto: se incorporó una cuarta ronda de investigación (diez agentes en paralelo,
~110 fuentes) centrada en el vocabulario del harness, la evidencia medida sobre cuánto
compra, y la capa de flujo de trabajo que las rondas anteriores no cubrían.

Informe completo navegable:
https://claude.ai/code/artifact/ea350ca8-7674-44d5-871a-7aa07b03a88d

---

## Nada de lo que había quedó obsoleto

Es el titular. Las tres rondas previas siguen en pie, y **las dos reglas del kit salieron
reforzadas**: la ronda 4 encontró el marco publicado en el que encajan (Böckeler,
martinfowler.com) y confirmó que las cuatro estrategias que sobrevivieron a la ronda 3
son las cuatro que la evidencia predice que funcionan. No hay que deshacer nada.

Lo único que cambia de criterio es un matiz sobre el router, explicado abajo.

---

## Qué copiar, en orden

### 1. Fichero nuevo: `contexto/ronda4-harness-y-flujo.md`

**323 líneas.** Es el grueso de la actualización y el único fichero largo. Contiene el
vocabulario para la charla, la evidencia medida, la capa de flujo y la lista de lo que
se evaluó y no conviene adoptar.

Si tienes poco tiempo de copia manual, **este es el que importa**; el resto son
retoques que puedes reconstruir a partir de él.

### 2. `CLAUDE.md` — tres bloques añadidos

- **Cabecera**: nota de que ahora son cuatro rondas + enlace al informe.
- **Nueva sección "La rejilla con la que clasificar cualquier propuesta"**, justo
  después del corolario de la ronda 2. Trae la tabla guide/sensor × computacional/
  inferencial, la observación de que las dos reglas existentes son coordenadas de esa
  rejilla, la nota de harnessability alta de Java+Gradle, y la regla de las dos veces.
- **"Lo que ya se descartó"**: cinco entradas nuevas (frameworks de proceso,
  orquestadores de workspace, Ralph, TDD obligatorio en el bucle, `@import` como
  técnica de ahorro).

### 3. `README.md` — tres retoques

- La frase de `contexto/` ahora dice cuatro informes y recomienda leer el de la ronda 4.
- La tabla de prompts gana la fila del 07.
- Tras "Las dos reglas", dos párrafos nuevos: **el matiz que salva al router** y **el
  subagente como mecanismo**.

### 4. Fichero nuevo: `prompts/07-rejilla-harness.md`

**~90 líneas.** Se ejecuta después del 04. Clasifica el harness existente en la rejilla,
encuentra los huecos, y produce los tres ejemplos concretos para la charla.

### 5. `plantillas/AGENTS-router.md` — cabecera ampliada

Solo el comentario de arriba. Añade: presupuesto en tokens (~1.000, el de Aider), el
criterio de admisión en una línea, la regla de apuntar a coordenadas y no a rutas, y el
aviso de que va en la raíz por el comportamiento de la compactación.

### 6. `plantillas/AGENTS-repo.md` — bloque final añadido

Un comentario al final: antes de añadir una línea, pregúntate si puede ser un sensor,
con el dato del 8,3 % y el test por línea de la documentación oficial.

---

## Lo único que cambia de criterio: el router

La regla 1 ("si lo descubre grepeando, no va en el harness") parecía condenar cualquier
fichero de mapa, porque arXiv 2602.11988 midió que los ficheros que **resumen el
repositorio** no mejoran nada y encarecen la inferencia un 20 %.

No lo condena. La distinción es el propio criterio de la regla:

> Un resumen de repo duplica lo que el grep encuentra. Un router de workspace contiene
> justo lo que el grep **no puede** encontrar, porque vive en un repo que el agente no
> ha abierto o no vive en ningún fichero.

El acoplamiento svc-control → svc-lib por bucket es ingrepeable por construcción:
ninguna herramienta de análisis estático lo ve. Por eso la sección "acoplamientos que no
están en el código" es la importante del router, y por eso todo lo demás del fichero
debe seguir famélico.

**Criterio de admisión, para usar al revisar la salida del prompt 04:** entra si un
agente con acceso a todo el workspace no podría deducirlo leyendo código.

---

## Tres cosas operativas que no sabíamos

1. **El subagente resuelve el problema que originó el proyecto.** Un subagente gastó
   6.100 tokens leyendo ficheros y devolvió 420 a la conversación principal; sus
   lecturas cuestan cero contra tu ventana. Cuando haya que rastrear un flujo entre
   repos, que lo haga un subagente.

2. **Tras compactar, solo se reinyecta el CLAUDE.md raíz.** Los anidados y las reglas
   con `paths:` no vuelven hasta que el agente toca un fichero que las active. Lo que no
   puede perderse va arriba.

3. **`@import` no ahorra contexto.** Los ficheros importados se cargan enteros al
   arrancar. Sirven para organizar, no para adelgazar.

---

## Para la charla

La tesis —*si la IA no hace lo que quiero, es un smell de harness insuficiente*— ahora
tiene vocabulario publicado y tres cifras que la sostienen:

- **8,3 %** de cumplimiento de instrucciones escritas en un AGENTS.md, sobre 4.550 PRs
  agénticos (arXiv 2604.09409). Escribir una regla en prosa es casi no escribirla.
- **+15 puntos** de media en 15 modelos cambiando **solo** el formato de la herramienta
  de edición, sin reentrenar nada, por 300 USD (Bölük, feb 2026). El harness mueve la
  aguja de verdad.
- **42 % → 78 %** en CORE-Bench con el mismo modelo, según el harness (Vaughan, abr
  2026). Y una configuración de CLAUDE.md afinada cerró casi toda la brecha entre
  herramientas: **importa menos qué herramienta uses que lo bien que la configures.**

Y el cierre honesto, de Böckeler: ningún harness atrapa de forma fiable el diagnóstico
equivocado, la sobre-ingeniería ni las instrucciones mal entendidas — *"un buen harness
no busca eliminar el input humano, sino dirigirlo a donde más importa"*.
