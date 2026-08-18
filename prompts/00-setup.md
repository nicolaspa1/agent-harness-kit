# Prompt 0 — Verificación de setup

Estás en la raíz de un workspace que contiene varios repositorios hermanos de un
sistema de procesamiento en tiempo real. Antes de analizar nada, verifica el terreno.

Haz esto y responde en el chat (no escribas ficheros todavía):

1. Lista los directorios de primer nivel. Para cada uno que sea un repo git, dime:
   nombre, rama actual, último commit (fecha y hash corto), y si tiene cambios sin
   commitear.
2. Para cada repo, identifica: lenguaje principal, sistema de build (Gradle, Maven,
   npm, otro), y versión de Java o del runtime si es declarable.
3. Cuenta ficheros de código por repo, para hacerme una idea del tamaño.
4. Dime si alguno de estos ficheros ya existe en algún repo: `AGENTS.md`,
   `CLAUDE.md`, `.cursorrules`, `.claude/`, `.cursor/`, `docs/`, `README.md`.
5. Comprueba si hay algún fichero de configuración que apunte a otro repo o a un
   bucket: busca referencias a S3, GCS, `s3://`, `gs://`, nombres de bucket, o
   coordenadas de artefactos (groupId/artifactId) que correspondan a otro de estos
   repos.

Termina con un resumen de una línea por repo y **una lista explícita de lo que no
pudiste determinar**. No adivines nada.
