---
description: Este documento contiene todas las reglas de desarrollo y directrices para este proyecto, aplicables a todos los agentes de IA (Claude, Cursor, Codex, Gemini, etc.).
alwaysApply: true
---

## 1. Principios Fundamentales

- **Tareas pequeñas, una a la vez**: Trabaja siempre en pasos pequeños, uno a la vez. Nunca avances más de un paso.
- **Desarrollo Guiado por Pruebas (TDD)**: Comienza con pruebas que fallen para cualquier funcionalidad nueva (TDD), según los detalles de la tarea.
- **Seguridad de Tipos (Type Safety)**: Todo el código debe estar completamente tipado.
- **Nombres Claros**: Usa nombres claros y descriptivos para todas las variables y funciones.
- **Cambios Incrementales**: Prefiere cambios incrementales y enfocados en lugar de modificaciones grandes y complejas.
- **Cuestionar Suposiciones**: Cuestiona siempre las suposiciones e inferencias.
- **Detección de Patrones**: Detecta y resalta patrones de código repetidos.

## 2. Estándares de Idioma
- **Español con términos técnicos en Inglés**: La documentación y los artefactos técnicos se escribirán en español, pero se mantendrán en inglés los términos técnicos comunes que no tienen una traducción directa o que son más usados en inglés (ej. *framework*, *middleware*, *hook*, *payload*, *props*, *render*, etc.).
    - El código (variables, funciones, clases) se escribirá preferiblemente en inglés para mantener la consistencia con las librerías y el ecosistema.
    - Los comentarios se escribirán en español (manteniendo los términos técnicos en inglés).
    - La documentación (README, guías, docs de API) se escribirá en español.
    - Los mensajes de commit se escribirán en español.

## 3. Estándares Específicos

Para directrices y estándares detallados específicos para diferentes áreas del proyecto, consulta:

- [Estándares de Backend](./backend-standards.md) - Desarrollo de API, patrones de base de datos, pruebas, seguridad y mejores prácticas de backend.
- [Estándares de Frontend](./frontend-standards.md) - Componentes de React, directrices de UI/UX y arquitectura de frontend.
- [Estándares de Documentación](./documentation-standards.md) - Estructura de documentación técnica, formato y directrices de mantenimiento, incluyendo estándares de IA como este documento.
- [Pasos Obligatorios para Tareas de OpenSpec](./openspec-tasks-mandatory-steps.md) - Lista de verificación requerida y reglas de ejecución al crear o actualizar archivos `tasks.md` de OpenSpec.

## 4. Skills del Proyecto

- Las skills viven en `ai-specs/skills`.
- Cuando una solicitud coincida con una skill, cárgala y sigue el archivo `SKILL.md` correspondiente automáticamente antes de continuar.
- También carga cualquier archivo referenciado en la carpeta de la skill (por ejemplo, `references/*.md`) cuando la skill lo requiera.

## 5. Requisito de Modelo de Planificación

Los workflows de planificación deben ejecutarse con Opus high reasoning.

Este requisito aplica a:
- `enrich-us`
- `openspec-ff-change`
- `openspec-continue-change`

Antes de comenzar cualquiera de estos workflows, verifica que la sesión esté usando Opus high reasoning. Si no es así, **auto-corrígete** agregando `"model": "claude-opus-4-7"` a `.claude/settings.json` (usa la skill `update-config` o edítalo directamente), luego continúa; no te detengas a preguntar al usuario. Haz lo mismo para volver a sonnet medium para cualquier otro paso.

## 6. Integridad de Symlinks y Portabilidad Multi-Agente

- **Fuente Canónica**: Mantén los artefactos reutilizables en `ai-specs` como la fuente canónica. Las rutas específicas de agentes (como `.claude` y `.cursor`) deben referenciarlos a través de symlinks cuando sea posible.
- **Seguridad en Actualizaciones**: Cada vez que un archivo se renombre, mueva o cambie su sufijo, verifica y actualiza todos los symlinks que lo apuntan antes de considerar el cambio completo.
- **Enlace de Nuevos Artefactos**: Cada vez que crees un nuevo artefacto que requiera exposición multi-agente (por ejemplo, nuevos agentes o skills en `ai-specs`), crea los symlinks correspondientes desde las rutas de referencia esperadas por el agente.
- **Revisión de Personalización Externa**: Cada vez que se introduzca una personalización fuera de `ai-specs`, evalúa si debe moverse a `ai-specs` y reemplazarse con symlinks desde las ubicaciones originales.
- **Puerta de Finalización**: Un cambio está incompleto si deja symlinks rotos, objetivos obsoletos o artefactos canónicos duplicados entre carpetas específicas de agentes.

## 7. Actualizaciones Obligatorias de Artefactos OpenSpec para Cambios Post-Apply

Cuando aparezca una nueva solicitud de corrección/cambio después de `opsx:apply` (o `/apply`) y antes de `opsx:archive` (or `/archive`), los agentes deben tratarla primero como una actualización de especificación, no como un arreglo informal de "corrige esto rápido". Es el principio central de openspec: la documentación es la fuente de verdad.

Orden requerido:

1. Actualiza los artefactos de cambio de OpenSpec actuales que se ven afectados (por ejemplo: escenarios, requerimientos/specs y `tasks.md`). No agregues tareas como "bugfixes" sino como parte del diseño inicial, por lo tanto, en la sección adecuada.
2. Si se necesita regenerar artefactos, ejecuta el paso correspondiente de OpenSpec (`opsx:continue`, `opsx:ff` o equivalente) antes de codificar.
3. Implementa el código solo después de que los artefactos reflejen la nueva solicitud.
4. Vuelve a ejecutar la verificación contra los artefactos actualizados antes de archivar.

No apliques correcciones directas solo en código en esta ventana sin actualizar los artefactos de OpenSpec.
