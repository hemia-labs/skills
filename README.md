# HEMIA AI Skills

Repositorio de skills compartidas para agentes AI del equipo HEMIA. Cada skill vive en
`skills/<skill-name>/` y define instrucciones reutilizables para Codex, Claude u otros agentes.

## Skills disponibles

| Skill | Para que sirve |
| --- | --- |
| `spec-to-phased-roadmap` | Convierte uno o mas documentos de especificacion en un roadmap por fases y un documento vivo de progreso. |
| `nestjs-backend-architecture` | Define arquitectura, estandares y flujos para backend NestJS en `apps/api`: modulos, TypeORM, DTOs, permisos, migraciones, seeds, auth, config y uploads. |
| `frontend-design-system` | Define el sistema visual frontend para `apps/web`: Next.js, Tailwind CSS v4, shadcn, componentes, layouts, tablas, formularios, tokens y UI light-only. |

## Estructura esperada

Cada skill compatible debe incluir:

- `SKILL.md`: fuente principal para Codex/OpenAI.
- `CLAUDE.md`: entrada resumida para Claude.
- `agents/openai.yaml`: metadata de instalacion/descubrimiento para Codex/OpenAI.
- `references/`: guias extendidas cuando la skill necesite mas contexto.
- `assets/`: templates u otros recursos, cuando aplique.

## Instalar en Codex

En Codex Desktop, pedir:

```text
Instala la skill de Codex desde https://github.com/hemia-labs/skills/tree/main/skills/<skill>
```

Invocar en Codex Desktop:

```text
Use $spec-to-phased-roadmap ...
Use $nestjs-backend-architecture ...
Use $frontend-design-system ...
```

## Instalar en Claude

En Claude Desktop, pedir:

```text
Instala la skill de Claude desde https://github.com/hemia-labs/skills/tree/main/skills/<skill>
```

Invocar en Claude Desktop:

```text
Usa la skill spec-to-phased-roadmap ...
Usa la skill nestjs-backend-architecture ...
Usa la skill frontend-design-system ...
```

Claude debe leer `CLAUDE.md` y usar `SKILL.md` como fuente de verdad.
