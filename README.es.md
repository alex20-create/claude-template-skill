# Claude Template Skill

*[English version](README.md)*

Dos Skills de Claude que ayudan a decidir qué plantilla(s) `CLAUDE.md` aplicar a un proyecto, a partir de una colección de archivos `.md` que el usuario mantiene (plantillas base, una guía de migración, una plantilla de historial de desarrollo, docs específicos de plataforma como Cloudflare o Convex).

Ambas skills hacen las mismas preguntas base antes de recomendar nada:

- **¿Qué tipo de proyecto es?** Técnico (código puro), no técnico (guión, producción audiovisual, escritura, investigación), o mixto (una herramienta cuyo propio contenido/decisiones creativas son inseparables del código, como una app de escritura de guiones hecha a medida).
- **¿Ya existe un `CLAUDE.md`?** Y si existe, ¿fue verificado contra el repositorio real (tiene detalle específico que no se podría inventar) o es solo escrito a mano/heredado (seguro de migrar)?
- **¿Es un proyecto puntual/experimental o algo que se mantiene semanas/meses?** Esto decide si usar la plantilla compacta o la más completa, y si añadir plantilla de historial de desarrollo.
- **¿Qué docs específicos de plataforma aplican de verdad?** Solo se copian para los servicios que el proyecto usa realmente — un doc de Cloudflare que cubre Workers, D1, KV, R2, etc. se recorta a solo las secciones que el proyecto necesita, no se copia entero.

Ninguna de las dos skills inventa información que falta: los campos desconocidos se marcan como `[PENDIENTE: ...]` en vez de suponerlos.

## Las dos skills

### [`skills/claude-ai/`](skills/claude-ai/SKILL.md) — para Proyectos de claude.ai

Corre dentro de un Proyecto de claude.ai. No tiene acceso directo al sistema de archivos, así que:
- Lee la colección de plantillas desde los archivos de conocimiento del Proyecto (con `project_knowledge_search`).
- Pregunta al usuario lo que no puede inferir (tipo de proyecto, horizonte de mantenimiento, estado del `CLAUDE.md` existente).
- Genera el `CLAUDE.md` final (y archivos complementarios como `CLAUDE-history.md`) con `create_file`.
- Se los entrega al usuario con `present_files` — el usuario luego los copia a mano a la carpeta de su proyecto.

### [`skills/claude-code/`](skills/claude-code/SKILL.md) — para Claude Code

Corre dentro de Claude Code, así que tiene acceso real al sistema de archivos y se salta el paso de copiar a mano. Esta versión:
- Lista y lee directamente la carpeta local de plantillas (por defecto `C:\Claude Code\Plantillas`).
- Inspecciona los archivos reales del proyecto destino — lee `package.json`/`requirements.txt`/etc. para inferir el stack técnico, comprueba si ya existe un `CLAUDE.md`, y compara uno existente contra el repo real para distinguir "verificado" de "escrito a mano."
- Escribe el `CLAUDE.md` compuesto (y archivos complementarios) directamente en el proyecto, tras confirmar con el usuario.

## Por qué dos skills separadas en vez de una sola

La lógica de decisión (tipo de proyecto, reglas de plantillas, cómo tratar proyectos mixtos, cómo preservar contenido específico del usuario) es idéntica. Lo que cambia es *cómo cada entorno recibe sus datos y entrega el resultado* — una tiene que preguntar-y-entregar, la otra puede inspeccionar-y-escribir-directo. Mantenerlas como `SKILL.md` separados evita que una sola skill necesite ramas condicionales tipo "si tengo acceso al sistema de archivos, haz X, si no, haz Y" repartidas por todo el archivo.

## Licencia

MIT. Ver `LICENSE`.
