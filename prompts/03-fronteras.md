# Prompt 3 — Fronteras, acoplamiento y territorio

Lee `_harness-kit/salida/01-inventario.md` y `_harness-kit/salida/02-flujo.md`.

Ahora toca lo que separa este sistema de uno normal: **parte del código es de otro
equipo, y parte del nuestro vive en sitios que no controlamos**.

## 1. La superficie real de ext-lib

La librería del otro equipo se usa dentro de la nuestra, pero probablemente solo una
fracción de ella. Averigua **exactamente qué se usa**:

- Todas las clases, métodos y tipos de ext-lib referenciados desde nuestro código, con
  fichero:línea de cada uso.
- Si hay un JAR compilado disponible, corre `jdeps` para obtener la superficie real
  incluyendo dependencias transitivas y métodos heredados, que un grep de imports no
  ve. Si no lo hay, hazlo con búsqueda de imports y **di que es una aproximación**.
- Clasifica cada uso: ¿es un tipo de datos, una operación reutilizada, una utilidad?
- Marca los que aparecen en tipos que se persisten como estado de Flink. Esos son los
  peligrosos: un cambio ahí puede romper la recuperación desde savepoint sin romper la
  compilación.

## 2. La frontera de nube

- Qué código es específico de AWS, cuál de GCP, y cuál es común.
- ¿Están separados en paquetes o módulos, o están entremezclados?
- ¿Puede hoy un import de AWS colarse en el camino de GCP, o hay algo que lo impida?
- Versiones de Flink y de Java en cada runtime, si se pueden determinar desde el build.

## 3. Nuestro territorio en repos ajenos

Si hay código nuestro dentro del proyecto de otro equipo (por ejemplo submódulos en un
microfrontend), localízalo y documenta: qué ficheros son nuestros, si hay CODEOWNERS o
algún mecanismo de propiedad, y qué pasaría si un agente trabajando ahí tocara lo que
no debe.

## 4. El proyecto de locks

Determina qué es, quién lo usa, y por qué vía entra en el sistema. Si no encaja en el
flujo del prompt 2, dilo — puede que sea una pieza independiente.

## Reglas

- Distingue siempre lo verificado con herramienta (jdeps, el build) de lo deducido
  leyendo código. Etiqueta cada hallazgo.
- No propongas soluciones todavía.

## Salida

Escribe `_harness-kit/salida/03-fronteras.md` con:
1. Tabla **superficie de ext-lib**: elemento | tipo de uso | dónde se usa | ¿va a estado?
2. Tabla **frontera de nube**: componente | AWS | GCP | común | ¿aislado?
3. Sección **territorio en repos ajenos**.
4. Sección **locks**.
5. Una sección final: **los cinco puntos donde un cambio de otro equipo nos rompería**,
   ordenados por probabilidad de que pase sin que nos enteremos.
6. **Dudas**.

Al terminar, dime la ruta y cuántos elementos de ext-lib usamos de verdad.
