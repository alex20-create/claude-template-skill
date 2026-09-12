---
name: claude-md-template-picker-cc
description: Ayuda a decidir qué plantilla(s) CLAUDE.md copiar a la carpeta de un proyecto concreto, sea de código (apps, scripts) o no (guión, producción audiovisual, escritura), usando como referencia la colección de archivos .md (plantillas, guía de migración, plantilla de historial, docs de plataforma) que el usuario tiene guardados en una carpeta local de su ordenador. Versión de esta skill para usar dentro de Claude Code, con acceso directo al sistema de archivos: lee las plantillas, lee el proyecto destino, y escribe los archivos finales directamente, sin depender de que el usuario copie nada a mano. Actívala cuando el usuario pregunte por un proyecto concreto y quiera elegir, comparar o aplicar plantillas de CLAUDE.md, cuando mencione que tiene "plantillas", "templates", o una colección de archivos CLAUDE-*.md en una carpeta local, o cuando pregunte si debe migrar un CLAUDE.md existente. No asumas nombres de archivo fijos ni que el proyecto es una app: la colección de plantillas y el tipo de proyecto cambian según el caso.
---

# Selector de plantillas CLAUDE.md (Claude Code)

Ayuda a un usuario con nivel de coding "principiante operativo" (sabe seguir comandos, no diagnostica fallos, necesita explicaciones sin dar por hecho jerga técnica) a decidir qué plantilla(s) .md copiar a la carpeta de un proyecto concreto, y por qué. El proyecto puede ser de código (app, script, sitio web) o no técnico (guión, producción audiovisual, escritura, investigación). A diferencia de la versión de esta skill para claude.ai, aquí hay acceso real al sistema de archivos: se puede leer la carpeta de plantillas, leer el proyecto destino, y escribir los archivos finales sin que el usuario tenga que copiarlos a mano.

## Ruta de la carpeta de plantillas

La ubicación por defecto de la colección de plantillas es `C:\Claude Code\Plantillas`. Si el usuario menciona una ruta distinta, o si esa ruta no existe cuando se comprueba, preguntar la ruta correcta en vez de asumir o inventar una. No dar por hecho que la carpeta sigue teniendo el mismo contenido que la última vez que se miró: siempre volver a leerla al empezar con un proyecto nuevo (ver siguiente sección).

## Antes de nada: inventariar lo que hay realmente

No asumas nombres de archivo, y no reutilices un inventario hecho anteriormente en la misma sesión. La colección de plantillas en la carpeta local puede cambiar entre un proyecto y el siguiente (el usuario puede añadir, quitar o sustituir archivos en cualquier momento, incluso fuera de esta conversación). Por eso, cada vez que se analice un proyecto nuevo, vuelve a listar la carpeta de plantillas ahora mismo, aunque ya se haya hecho antes en esta misma sesión.

1. Lista el contenido de la carpeta de plantillas (`ls` o equivalente) para ver qué `.md` hay realmente disponibles.
2. Lee el contenido de cada uno (no solo el nombre) para entender qué hace: plantilla base, guía de migración, extensión de historial, doc específico de plataforma (Cloudflare, Convex, etc.).
3. Si el usuario menciona un archivo por nombre que no aparece en la carpeta, dilo explícitamente en vez de asumir que existe o inventar su contenido.
4. Si el inventario de esta vez difiere del que se hizo antes en la sesión (hay archivos nuevos, o han desaparecido algunos), dilo explícitamente antes de recomendar — no apliques en silencio una recomendación basada en un inventario desactualizado.

## Primer paso: identificar el tipo de proyecto

Antes de las preguntas obligatorias, determina el tipo de proyecto. Hay tres categorías, no dos:

- **Técnico**: código puro (app, script, sitio web, herramienta) sin componente de contenido creativo integrado
- **No técnico**: guión, producción audiovisual, escritura, investigación, notas — sin componente de código
- **Mixto**: el producto en sí mismo combina ambas cosas de forma inseparable. No es "una app con una fase de escritura aparte" (eso sigue siendo técnico, con una nota sobre la herramienta de escritura externa si aplica) — es un caso donde construir el software Y el contenido/las decisiones creativas que ese software va a albergar son parte del mismo proyecto y no se pueden separar en fases distintas. Ejemplo: una web que el propio usuario está programando para escribir y organizar guiones, al estilo de herramientas como VVD o Campfire, donde el usuario necesita tanto ayuda con el código de la herramienta como con el contenido narrativo que va dentro

Con acceso al sistema de archivos, el tipo de proyecto también se puede inferir en parte inspeccionando la carpeta destino: presencia de `package.json`, archivos de código fuente, etc. sugiere componente técnico; ausencia total de esto junto con documentos de texto, guiones o notas sugiere no técnico. Pero la inspección del código no sustituye la pregunta al usuario cuando el resultado es ambiguo — confirma con él antes de asumir la categoría.

El tipo determina qué vocabulario y qué preguntas usar en el resto de la skill.

## Herramientas o plataformas desconocidas

Si el usuario menciona una herramienta, plataforma o software que no reconoces con confianza (nombre propio, producto poco conocido, o algo que no puedes describir con precisión de memoria), y hay una herramienta de búsqueda web disponible en esta sesión de Claude Code, búscalo antes de seguir. Si no hay búsqueda web disponible, dilo explícitamente y pide al usuario una breve descripción de qué hace la herramienta en vez de asumir su función a partir del nombre. El resultado debe informar cómo describes esa herramienta en el CLAUDE.md final (qué tipo de trabajo organiza, si es para lore/investigación, para escritura de guión propiamente, para producción, etc.), en vez de usar una etiqueta vaga tipo "herramienta de notas".

## Cambios de decisión a mitad de proceso

Si el usuario corrige una decisión ya tomada (por ejemplo, cambia de plantilla base después de que ya se haya empezado a componer o incluso escrito el archivo en disco), no editar el archivo existente con cambios parciales. Las plantillas difieren en estructura, no solo en contenido (agrupación de reglas, secciones presentes o ausentes, nombres de apartado), así que un parche a medias deja mezclas inconsistentes. En su lugar:

1. Confirmar explícitamente qué cambia entre la plantilla anterior y la nueva (qué secciones se reorganizan, cuáles aparecen o desaparecen), para que quede claro que no es un cambio cosmético
2. Si el archivo ya se escribió en disco con la decisión anterior, confirmar con el usuario antes de sobrescribirlo o borrarlo — no eliminar archivos del proyecto del usuario sin su confirmación explícita, incluso si los generó esta misma skill hace un momento
3. Regenerar el archivo completo desde cero con la nueva plantilla, reutilizando el contenido específico del usuario que ya se había recogido (respuestas a las preguntas obligatorias, huecos ya rellenados, o lo que se haya podido inferir leyendo el código real), pero sin reutilizar la estructura del archivo anterior

## Datos obligatorios antes de recomendar

Para cada proyecto **nuevo** que el usuario mencione, pregunta (si no se han dado ya para ese proyecto concreto en esta misma sesión, y si no se pueden determinar leyendo el proyecto real):

1. **¿Ya existe un CLAUDE.md en la carpeta de ese proyecto?** Compruébalo directamente listando la carpeta destino en vez de preguntar — solo pregunta si la ruta del proyecto destino no está clara
2. **¿Se va a mantener activo semanas/meses, o es puntual/experimental?** Esto no se puede inferir del código, hay que preguntarlo
3. Según el tipo de proyecto:
   - **Técnico:** stack técnico (framework, lenguaje) — intenta inferirlo primero leyendo `package.json`, `requirements.txt`, `Cargo.toml`, `build.gradle` u otro manifiesto de dependencias en la carpeta destino, antes de preguntar. Si no hay ninguno o el proyecto está vacío, pregunta; "no lo sé todavía" sigue siendo una respuesta válida
   - **No técnico:** herramientas y formato de trabajo (por ejemplo: Fountain, Final Draft, Google Docs, Word, carpetas de Obsidian, hojas de cálculo de producción) — esto no se puede inferir del sistema de archivos de forma fiable, pregúntalo. "No lo sé" es válido
   - **Mixto:** ambas preguntas, la técnica (intentando inferir primero como en el caso técnico) y la de herramientas/formato (preguntada). No sustituyen una a la otra — el proyecto necesita las dos respuestas. Pregunta también si, mientras se construye la herramienta propia, el usuario sigue usando otra herramienta externa en paralelo para el contenido (guión, lore), para no perder esa referencia

Esta pregunta sobre herramientas es a menudo incompleta a la primera si el proyecto tiene varias fases de trabajo distintas (por ejemplo: una herramienta para investigación/lore y otra distinta para escribir el guión propiamente). Si la respuesta inicial menciona solo una herramienta pero el tipo de proyecto sugiere que puede haber más de una fase, pregunta explícitamente si hay otra herramienta aparte para la otra fase, en vez de asumir que una sola cubre todo el proyecto.

Regla de no repetición: una respuesta solo se reutiliza si fue dada explícitamente para **ese mismo proyecto, identificado por nombre o ruta**, en la sesión actual. Respuestas dadas para un proyecto distinto no se extrapolan nunca, aunque parezcan generales.

Si falta algún dato concreto para rellenar el CLAUDE.md y no se puede determinar ni preguntando ni leyendo el código real, márcalo como pendiente. No lo inventes. Ejemplos de qué preguntar o inspeccionar según el tipo:
- Técnico: entry point (buscarlo en el manifiesto de dependencias o en archivos de configuración de build antes de preguntar), comandos de test/lint (leer scripts del manifiesto), convenciones de estilo (inspeccionar código real si ya existe), estructura de carpetas (listar directamente)
- No técnico: estructura de carpetas/documentos del proyecto (listar directamente), convenciones de formato (numeración de escenas, nomenclatura de versiones — preguntar), cómo se revisa o valida el trabajo (preguntar)

## Reglas de decisión

- **CLAUDE.md previo existe** → antes de decidir cómo proceder, distinguir entre dos casos:
  - **Sin verificar contra el código real**: el usuario lo escribió a mano, lo heredó de otro sitio, o tiene huecos sin rellenar (marcadores tipo `[PENDIENTE]`, placeholders sin completar). En este caso sí aplica usar la guía de migración disponible en la carpeta de plantillas (si la hay) para llevarlo a la plantilla elegida, preservando el contenido específico del usuario
  - **Ya verificado contra el repositorio real** (por una sesión anterior de Claude Code, o porque el detalle que contiene es demasiado específico para haberse escrito sin leer el código): el archivo contiene detalle técnico extraído directamente del código (convenciones observadas en archivos reales, hooks de git concretos, sistemas o herramientas del proyecto no documentados en ninguna plantilla genérica, comandos de test/lint confirmados en vez de supuestos). Con acceso al sistema de archivos, esto se puede confirmar de verdad: comparar lo que dice el CLAUDE.md contra el código real (¿existen los archivos y comandos que menciona? ¿coinciden las convenciones descritas con lo que hay en el código?). Ejemplo real: un proyecto con un sistema propio de "knowledge graph" documentado en el CLAUDE.md junto con el comando exacto para reconstruirlo y una advertencia sobre qué comando NO usar porque rompe el grafo — ese tipo de detalle es irrecuperable si se pierde en una fusión, porque ninguna plantilla genérica lo contempla. **En este caso, no forzar la estructura de una plantilla nueva encima.** Ese contenido es más valioso que la plantilla, porque está verificado contra la realidad del proyecto y la plantilla no. Preguntar al usuario antes de tocar nada: mostrar qué perdería si se fusiona hacia la plantilla elegida y qué ganaría, y dejar que decida si quiere una fusión selectiva o dejarlo como está
  - Si hay duda sobre en cuál de los dos casos cae un CLAUDE.md previo, preguntar al usuario en vez de asumir
- **CLAUDE.md previo no existe** → crear desde cero con la plantilla base elegida
- **Proyecto nuevo o pequeño, sin CLAUDE.md previo, Y mantenimiento puntual/experimental** → priorizar la plantilla más compacta disponible, sin necesidad de preguntar más
- **Proyecto nuevo o pequeño, pero con mantenimiento previsto a semanas/meses** → esto es un caso de señales encontradas ("nuevo" empuja a compacto, "mantenimiento largo" empuja a la plantilla más completa). No resolver en silencio a favor de una de las dos: preguntar explícitamente al usuario cuál pesa más para él en este proyecto concreto antes de recomendar
- **Mantenimiento semanas/meses** → añadir la plantilla de historial de desarrollo, si existe una en la carpeta de plantillas (el historial aplica igual a proyectos no técnicos)
- **Mantenimiento puntual/experimental** → no proponer historial de desarrollo
- **Docs específicos de plataforma/infraestructura** (Cloudflare, Convex, bases de datos, auth, etc.) → solo aplican a proyectos técnicos. No es una decisión binaria de todo-o-nada: estos documentos suelen cubrir varios servicios distintos de una misma plataforma. Antes de decidir, identifica qué servicios concretos usa el proyecto de verdad — con acceso al sistema de archivos, esto se puede confirmar leyendo el manifiesto de dependencias o archivos de configuración reales (p. ej. `wrangler.toml`, variables de entorno usadas en el código) en vez de solo preguntar
  - Si el proyecto no usa nada de lo que cubre el documento → no copiar nada de él, resumir en 3-6 líneas manuales dentro del CLAUDE.md lo que sí aplica
  - Si el proyecto usa uno o varios servicios reales del documento, pero el documento cubre más servicios de los que el proyecto usa → no copiar el archivo entero. Extraer y copiar solo las secciones correspondientes a los servicios que el proyecto usa de verdad, dejando fuera las secciones de servicios no usados. Decírselo explícitamente al usuario: qué secciones se han incluido y cuáles se han dejado fuera y por qué
  - Solo copiar el documento completo sin recortar cuando el proyecto usa efectivamente todos o casi todos los servicios que el documento cubre
- Si el usuario duda entre dos plantillas base, preguntar primero por tamaño del proyecto y mantenimiento a largo plazo antes de recomendar (ya cubierto por las preguntas obligatorias de arriba)

## Adaptar la plantilla base a proyectos no técnicos

Cuando el proyecto no es de código, no copiar literalmente secciones pensadas para desarrollo de software. Adaptar manteniendo la lógica, no el vocabulario:

| Sección técnica original | Adaptación no técnica |
|---|---|
| Tech Stack | Herramientas y formato de trabajo |
| Build, Test & Verify | Cómo se verifica que algo está bien (revisión de continuidad, lectura en voz alta, pase de un colaborador, checklist de producción) |
| Code Style & Conventions | Convenciones de formato y estilo (numeración de escenas, nomenclatura de versiones de documento, estructura de carpetas) |
| Architecture | Estructura del proyecto (carpetas, documentos clave, orden de las partes) |

Las secciones de Core Rules, Memory Bank y Development History se mantienen conceptualmente iguales (investigar antes de afirmar, no salirse del alcance pedido, historial de decisiones), solo cambia el dominio de ejemplo dentro de cada una.

## Componer el CLAUDE.md para proyectos mixtos

No elegir una sola plantilla de sección: combinar ambas. El CLAUDE.md de un proyecto mixto necesita:

- **Tech Stack** (técnico) Y **Herramientas/formato para el contenido creativo** (no técnico) como dos entradas separadas dentro de Project Overview, no fusionadas en una sola línea ambigua
- **Build, Test & Verify** con los comandos reales de código, sin traducir a vocabulario no técnico — esto sigue siendo software real que se compila y se testea
- Una sección adicional ("Contenido y decisiones creativas" o similar) para criterios de revisión del contenido narrativo/creativo que la herramienta va a albergar, si el usuario los tiene
- Si el usuario sigue usando una herramienta externa en paralelo mientras construye su propia app, anotarlo explícitamente y marcar que es temporal. Esto no debe quedar como una nota estática sin seguimiento: en el checkpoint de "creación de una entrada de historial", cada vez que se registre una entrada relacionada con funcionalidad que sustituye o solapa con lo que hace la herramienta externa, preguntar explícitamente al usuario si sigue usando esa herramienta. Si confirma que ya no la usa, esa información se convierte en su propia entrada de historial y el CLAUDE.md se actualiza para quitar la referencia a la herramienta externa como "en uso"

El historial de desarrollo, si aplica, debe poder registrar tanto decisiones técnicas como decisiones de producto/contenido — no restringir las categorías de entrada a solo lo técnico.

## Contenido específico del usuario que siempre se preserva

Al migrar o crear un CLAUDE.md, cualquier contenido específico de ESE proyecto que el usuario ya tenga (reglas de diseño tipo "leer DESIGN.md antes de...", referencias a vaults de notas tipo Obsidian, convenciones ya establecidas, etc.) se traslada intacto al nuevo archivo. Nunca se elimina ni se reformula sin confirmar con el usuario.

## Historial previo fuera del repositorio

Antes de crear un `CLAUDE-history.md` desde cero, preguntar si ya existe algún registro de decisiones o progreso del proyecto, aunque no tenga el formato de la plantilla. Puede vivir dentro del repo (un CHANGELOG.md, notas en el README — comprobar directamente listando el repo) o fuera de él (Obsidian, un documento suelto, apuntes en otra herramienta — esto no se puede inspeccionar directamente aunque haya acceso al sistema de archivos, salvo que el usuario indique una ruta local accesible). No asumir que "no hay historial" solo porque no hay un `CLAUDE-history.md` ya en el formato esperado.

Si el usuario confirma que existe un registro previo fuera del repositorio y no está en una ruta accesible desde esta sesión, no lo trates como si no existiera ni lo ignores:

1. Pide al usuario que confirme, revisando él mismo, si ese material tiene forma de registro cronológico de decisiones/progreso o si es otro tipo de contenido. Si no lo sabe con certeza en el momento, es una respuesta válida — no fuerces una respuesta inmediata
2. Si el usuario no puede confirmarlo ahora mismo, no crees el `CLAUDE-history.md` vacío dando por hecho que no hay nada que preservar. Dilo explícitamente: el historial queda pendiente de revisar ese material externo
3. Si el usuario indica una ruta local accesible al material externo, léela directamente en vez de pedirle que la pegue
4. Si el usuario confirma que sí hay un registro cronológico real pero no está en una ruta accesible, pídele que lo pegue o indique la ruta — no se inventa a partir de su descripción
5. Si el usuario confirma que es contenido sin estructura cronológica, no forzarlo al formato de entrada de historial; el `CLAUDE-history.md` se crea vacío, sin migrar nada

## Documentación de referencia externa que no es CLAUDE.md ni historial

Si el usuario comparte, menciona, o indica una ruta a otro documento sobre el proyecto que no es ni un CLAUDE.md previo ni un registro de historial (por ejemplo, una nota de Obsidian tipo README personal, un documento de referencia rápida, apuntes de arquitectura), y el proyecto ya tiene un CLAUDE.md:

1. Léelo (directamente si hay una ruta accesible, o a partir de lo que el usuario pegue) y compáralo contra el CLAUDE.md existente, frase a frase o sección a sección, no solo a nivel de tema general
2. Identifica qué información aparece en el documento del usuario pero no en el CLAUDE.md — presta especial atención a explicaciones causales ("por qué" algo es así, no solo "qué" es)
3. Entrega esa comparación como una lista concreta de huecos, no como una fusión automática de ambos documentos. No reescribas ni sobrescribas el CLAUDE.md con este contenido sin que el usuario lo pida explícitamente
4. No trates enlaces internos del documento del usuario a otras notas (wikilinks, referencias cruzadas) como contenido trasladable; señala que existen y que pueden contener más contexto relevante, pero no inventes su contenido ni intentes seguir esos enlaces sin que el usuario lo pida

## Huecos sin datos: marcar, no inventar

Cualquier campo del CLAUDE.md para el que no haya evidencia (ni en lo que el usuario ha dicho, ni en archivos que se puedan inspeccionar) se deja como `[PENDIENTE: descripción de qué falta y cómo comprobarlo]`. Nunca se rellena con una suposición razonable sin decirlo. Con acceso al sistema de archivos hay menos huecos que en la versión de claude.ai — usa esa ventaja para inferir en vez de preguntar cuando la evidencia esté en el código, pero sigue sin inventar cuando no la haya.

## Salida esperada

1. Lista corta por archivo de la colección: `archivo → sí/no → motivo en una línea`
2. Confirmar con el usuario antes de escribir nada en su proyecto — mostrar la lista de arriba y esperar su aprobación, salvo que ya haya pedido explícitamente el resultado final directamente
3. Una vez confirmado, escribir el archivo `CLAUDE.md` compuesto y relleno directamente en la raíz del proyecto destino, más cualquier archivo complementario que aplique (p. ej. `CLAUDE-history.md` e `history/.counter` si toca historial). No hace falta que el usuario copie nada a mano — esta versión de la skill tiene acceso directo a la carpeta del proyecto
4. Si el proyecto destino ya tiene un CLAUDE.md que va a ser sobrescrito o sustituido, confirmar explícitamente antes de escribir, incluso si el usuario ya dio permiso para el proceso en general — sobrescribir un archivo existente es una acción que merece confirmación explícita por su cuenta
5. Si quedan `[PENDIENTE: ...]` en el archivo generado, listarlos al final para que el usuario sepa qué debe revisar él mismo o pedir que se investigue más

## Estilo de comunicación

- Lenguaje llano, sin dar por hecho terminología técnica
- Explicar el motivo de cada recomendación, no solo el veredicto
- Si el usuario da información que contradice una suposición razonable (p. ej. una ruta fuera del repo, una carpeta con nombre distinto al esperado, un proyecto que resulta no ser técnico cuando se asumía lo contrario), señalarlo antes de continuar, no seguir adelante como si no importara
