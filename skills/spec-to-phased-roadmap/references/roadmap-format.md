# Roadmap Format

Default path:

```txt
docs/roadmap/roadmap_desarrollo_<project>.md
```

Use Spanish headings by default when the repository or user uses Spanish.

Required order:

1. `# Roadmap de desarrollo - <Project>`
2. `## Fuentes analizadas`
3. `## Regla central`
4. `## Separacion correcta` when boundaries matter.
5. `## Archivo vivo de avance`
6. `## Fase 00 - Alineacion inicial`
7. Numbered implementation phases.

## Fuentes Analizadas

Use this format:

```md
## Fuentes analizadas

- `<file>`: one-line summary.
- `<file>`: one-line summary.
```

## Regla Central

One concise paragraph. State ownership and non-ownership.

Example shape:

```md
## Regla central

<System A> owns <domain>. <System B> owns <other domain>. <Vendor> handles <external responsibility>. <System A> must not store or decide <boundary>.
```

## Separacion Correcta

Use a table:

```md
| Sistema | Que debe manejar |
| --- | --- |
| ... | ... |
```

## Archivo Vivo De Avance

Always include the exact progress path:

````md
## Archivo vivo de avance

Durante todo el desarrollo debe existir y mantenerse actualizado:

```txt
docs/roadmap/<project-slug>-development-progress.md
```

Ese archivo debe registrar, por cada fase:

- Fase del roadmap.
- Fecha de inicio y cierre.
- Archivos creados o modificados.
- Entidades, DTOs, services, controllers y modulos implementados.
- Endpoints agregados.
- Migraciones requeridas o generadas.
- Seeds requeridos.
- Pruebas ejecutadas.
- Pendientes, decisiones y bloqueos.
- Referencia exacta al punto del roadmap que se completo.
````

## Phase Format

Use this structure:

```md
## Fase XX - Nombre

Objetivo: ...

Modulo:

- `module-name`

Entidades:

- `EntityName` -> `table_name`

Tareas:

- ...

Archivos esperados:

- `path/to/file.ts`

Endpoints:

- `METHOD /path`

Entregable:

- ...

Regla de separacion:

- ...

Registro obligatorio:

- Actualizar `docs/roadmap/<project-slug>-development-progress.md`.
- Referencia roadmap: "Fase XX - Nombre".
- Registrar ...
```

Omit optional sections when they do not apply.
