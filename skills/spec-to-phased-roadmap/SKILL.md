---
name: spec-to-phased-roadmap
description: Use when converting one or more specification documents into a single phased development roadmap plus one living progress document. Outputs should match docs/roadmap/roadmap_desarrollo_<project>.md and docs/roadmap/<project>-development-progress.md style, with phase-by-phase implementation tracking and update rules.
---

# Spec To Phased Roadmap

Use this skill when the user wants a development roadmap from specs, a phase-by-phase plan, or a living progress document.

## Outputs

Create or update exactly two main documents unless the user asks otherwise:

- `docs/roadmap/roadmap_desarrollo_<project>.md`
- `docs/roadmap/<project-slug>-development-progress.md`

Do not create separate phase files by default.

## Workflow

1. Locate spec source files from the user's request.
2. Read existing roadmap and progress files if present.
3. Extract requirements using `references/extraction-rules.md`.
4. Build one roadmap using `references/roadmap-format.md` and `references/phase-rules.md`.
5. Create or update one progress document using `references/progress-format.md`.
6. When updating progress after implementation work, inspect repo state and follow `references/update-rules.md`.

## Roadmap Rules

- Start with analyzed sources.
- Define the central system rule.
- Define system boundaries in a table when multiple systems, vendors, or products interact.
- Name the single live progress document.
- Split work into numbered phases: `Fase 00`, `Fase 01`, etc.
- Keep phases independently shippable when possible.
- Every phase must include `Objetivo`, `Tareas`, `Entregable`, and `Registro obligatorio`.
- Add `Modulo`, `Entidades`, `Endpoints`, `Archivos esperados`, or `Regla de separacion` only when useful.

## Progress Rules

- Keep one section per phase in the single progress document.
- Append or update phase sections. Do not scatter status across files.
- Before starting a new phase, read the progress document first.
- Do not mark a phase completed unless acceptance and validation are done.
- Record exact roadmap reference, files changed, implemented work, pending work, migrations, seeds, tests, and notes.

## Safety

- Do not invent requirements.
- Mark uncertainty as decisions, assumptions, or open questions.
- Separate inferred implementation tasks from explicit spec requirements.
- Respect existing repo conventions over this skill when they conflict.
- If a required spec detail is missing, create an explicit question instead of guessing silently.
