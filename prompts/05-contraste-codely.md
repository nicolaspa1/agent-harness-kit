# Prompt 5 — Contraste con el harness de CodelyTV

Lee `_harness-kit/salida/04-diagnostico.md`.

Hay dos repos públicos de CodelyTV que empaquetan un harness y la metodología RPI
(Research → Plan → Implement). El objetivo es contrastar lo que hemos diseñado contra
lo que hace gente que se dedica a esto, y **robar lo que sirva**.

Clónalos en un directorio temporal fuera del workspace:

```
git clone --depth 1 https://github.com/CodelyTV/agent-harness
git clone --depth 1 https://github.com/CodelyTV/rpi-course
```

## Qué mirar

1. **El árbol completo** de `agent-harness`. Qué componentes tiene un harness según
   ellos: skills, comandos, hooks, ficheros de contexto, configuración por herramienta.
2. **Los skills y comandos**: léelos. En particular los que tengan que ver con crear
   planes, investigar el código y documentar. Fíjate en cómo están escritos, no solo en
   qué hacen.
3. **Los hooks**, si los hay. Son el mecanismo de restricción determinista, que es la
   categoría que más pesa en nuestro diagnóstico.
4. **`rpi-course`**: cómo materializan Research → Plan → Implement. Qué artefacto
   produce cada fase, dónde lo dejan, y — la pregunta clave — **¿ese artefacto
   sobrevive al cierre de la sesión, o se tira?**

## Las tres preguntas que hay que responder

- **¿Qué de esto es copiable tal cual a nuestro caso?** Con la ruta del fichero de
  origen y qué habría que cambiar.
- **¿Qué no aplica?** Su material asume un único repositorio. Nosotros tenemos varios y
  el flujo cruza repos. Sé explícito sobre qué se rompe con esa diferencia; no fuerces
  encajes.
- **¿Qué tenemos nosotros que ellos no cubren?** Esa lista es la parte que hay que
  inventar y la más interesante.

## Reglas

- Cita rutas reales de sus repos. Si un fichero no existe, dilo en vez de suponerlo.
- No asumas que por ser de un curso está bien pensado: si ves una incoherencia entre lo
  que predican y lo que hace su propio repo, anótala.

## Salida

`_harness-kit/salida/05-codely.md` con:
1. Inventario de componentes de su harness.
2. Tabla: componente | ¿copiable? | qué cambiaría | prioridad.
3. Lo que no aplica y por qué.
4. Los huecos que tenemos que cubrir nosotros.
5. Cualquier incoherencia que hayas detectado en sus repos.

Al terminar, dime qué tres cosas suyas copiarías y qué es lo que ellos no resuelven.
