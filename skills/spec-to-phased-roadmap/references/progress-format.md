# Progress Format

Default path:

```txt
docs/roadmap/<project-slug>-development-progress.md
```

The progress document is a single living log. It mirrors roadmap phases.

Use one section per phase:

```md
## Fase XX - Nombre

- Estado: no iniciada | en progreso | completada | bloqueada
- Referencia roadmap: Fase XX - Nombre
- Fecha:
- Archivos:
  - `path/to/file`
- Implementado:
  - ...
- Pendiente:
  - ...
- Migracion requerida: no aplica | si, ...
- Seeds requeridos: no aplica | si, ...
- Pruebas:
  - ...
- Notas:
  - ...
```

Use ASCII text unless the repository already uses accents consistently. Match the existing file style.

## Status Rules

- `no iniciada`: phase exists in roadmap but no work started.
- `en progreso`: implementation started or partial changes exist.
- `completada`: deliverable implemented and validation recorded.
- `bloqueada`: cannot continue without external decision, dependency, or credential.

## Required Fields

Never omit:

- Estado.
- Referencia roadmap.
- Fecha.
- Archivos.
- Implementado.
- Pendiente.
- Migracion requerida.
- Seeds requeridos.
- Pruebas.
- Notas.

Use `no aplica` when empty.
