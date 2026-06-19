# Roadmap de desarrollo - <Project>

## Fuentes analizadas

- `<spec-file>`: <summary>.

## Regla central

<Core ownership rule. Include what this system does and must not do.>

## Separacion correcta

| Sistema | Que debe manejar |
| --- | --- |
| <System> | <Responsibility> |

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

## Fase 00 - Alineacion inicial

Objetivo: fijar alcance y decisiones base antes de implementar.

Tareas:

- Confirmar alcance MVP.
- Confirmar fuera de alcance.
- Confirmar sistemas externos y responsabilidades.
- Confirmar decisiones tecnicas necesarias.

Entregable:

- Decisiones de alcance documentadas.

Registro obligatorio:

- Crear o actualizar `docs/roadmap/<project-slug>-development-progress.md`.
- Registrar que la fase corresponde a "Fase 00 - Alineacion inicial".
- Guardar decisiones, pendientes y bloqueos.
