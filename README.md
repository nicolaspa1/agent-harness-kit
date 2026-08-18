# Kit de análisis de harness — SVC

Esto no es una solución. Es un **procedimiento para averiguar qué solución te sirve**,
ejecutado contra tu código real en tu portátil de trabajo.

Tres rondas de investigación (49 estrategias evaluadas) confirmaron que no existe
ningún framework publicado que resuelva compartir contexto entre repos con mecanismo
y métricas. Lo que hay es una convención, y qué convención te sirve depende de cómo
esté tu código de verdad. Por eso esto se ejecuta ahí, no aquí.

---

## 0. Antes de nada

Lee `CLAUDE.md`. Es el contexto permanente: la arquitectura tal y como la conocemos,
las dos reglas que gobiernan cualquier propuesta, y **la lista de lo que ya se descartó**
para que no se vuelva a proponer. Claude lo carga solo al abrir el proyecto.

En `contexto/` están los tres informes completos de la investigación previa (49
estrategias evaluadas por un panel de cuatro calificadores). No hace falta leerlos para
usar el kit — están ahí para consultarlos cuando una decisión necesite respaldo.

## 1. Monta la carpeta

En el portátil de trabajo, crea una carpeta y clona dentro **todos** los repos.
Usa los nombres reales de tus repos, no los de este ejemplo.

```
svc/
├── _harness-kit/        ← esta carpeta
├── svc-control/
├── svc-lib/
├── ext-lib/
├── svc-docs/
└── locks/               ← el proyecto de locks
```

Importante: los repos van **hermanos** de `_harness-kit`, no dentro.
Y el agente se abre en `svc/`, no dentro de un repo. Si lo abres dentro de uno,
no ve los demás y todo esto deja de funcionar.

> Si algún repo no lo puedes clonar (permisos, tamaño), sáltalo y anótalo.
> Los prompts están escritos para tolerar que falte alguno.

## 2. El prompt inicial

Abre Claude en `svc/` y pega el contenido de `PROMPT-INICIAL.md`. Solo reconoce el
terreno y contrasta lo que hay contra lo documentado. **Si encuentra discrepancias con
`CLAUDE.md`, corrige ese fichero antes de seguir**: todo lo demás se apoya en él.

## 3. Ejecuta los prompts, en orden, uno por sesión

Cada prompt es un fichero en `prompts/`. Copias el contenido, lo pegas en una
sesión **limpia** del agente abierta en `svc/`, y esperas a que escriba su fichero
de salida.

**La regla que más importa: una sesión nueva por prompt.** No encadenes los seis en
la misma conversación. Cada prompt está diseñado para leer los ficheros que
escribieron los anteriores, así que no necesita que el contexto sobreviva — y si lo
encadenas, la calidad cae por saturación de contexto. Esto es el hallazgo central de
las tres rondas y aquí se aplica a nosotros mismos.

| Orden | Prompt | Qué produce | Cuánto tarda |
|---|---|---|---|
| 1 | `01-inventario.md` | `salida/01-inventario.md` | 10-20 min |
| 2 | `02-flujo.md` | `salida/02-flujo.md` | 15-30 min |
| 3 | `03-fronteras.md` | `salida/03-fronteras.md` | 15-30 min |
| 4 | `04-diagnostico.md` | `salida/04-diagnostico.md` + borradores de AGENTS.md | 20-40 min |
| 5 | `05-contraste-codely.md` | `salida/05-codely.md` | 15 min |

El `00-setup.md` es redundante si ya corriste el prompt inicial; está por si quieres
la verificación por separado.

El 6 (`06-medicion.md`) es distinto: se ejecuta **antes** de aplicar nada y otra vez
**después**. Es lo que te da los números para la charla.

## 4. Qué hacer con la salida

Los ficheros de `salida/` son el material. El prompt 4 además te deja borradores de
los `AGENTS.md` listos para revisar. **Revísalos tú antes de commitear nada** — están
escritos por un agente que acaba de conocer tu sistema y se va a equivocar en cosas.

Los que sobrevivan a tu revisión van a sus repos. El resto de la salida se queda en
`svc-docs/`.

---

## Las dos reglas que hacen que esto funcione

**Si el agente puede descubrirlo grepeando, no va en el harness.** Es el criterio de
Addy Osmani y está respaldado por medición: un estudio de ETH/SRI sobre 288
configuraciones encontró que los ficheros de contexto que resumen el repo *no mejoran*
la tasa de éxito y encarecen la inferencia más de un 20%. Lo que sí funciona es lo
contrario: información que el agente **no puede** descubrir leyendo el código.

**Lo que se pueda comprobar, se comprueba; no se escribe.** Un estudio sobre 4.550 PRs
agénticos midió que cuando la instrucción vivía en un AGENTS.md, el cumplimiento fue
del **8,3%**. Escribir una regla en prosa es casi no escribirla. Si se puede convertir
en un test o un check de CI que falle, ahí es donde va.

Los prompts aplican estas dos reglas por ti, pero conviene que las tengas presentes
al revisar la salida.

---

## Qué modelo usar

| Prompt | Modelo | Por qué |
|---|---|---|
| Inicial, 00 | Sonnet 5 | Es listar y comparar. No necesita más. |
| 01, 02, 03 | **Opus 5** | Rastrear un flujo entre repos y encontrar acoplamientos que no son imports es donde se nota la diferencia. |
| 04 | **Opus 5** | Es el que juzga y decide. Si un prompt merece el modelo bueno, es este. |
| 05 | Sonnet 5 | Leer dos repos ajenos y comparar. |
| 06 | **El que uses a diario** | Estás midiendo tu flujo real. Si mides con un modelo que no usas, mides otra cosa. |

Si tienes que elegir uno solo para todo: **Opus 5**. El coste de que el 02 o el 04
salgan flojos es tener que repetirlos.
