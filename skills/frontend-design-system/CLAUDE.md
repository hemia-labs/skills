# Frontend Design System

Use `SKILL.md` as the source of truth.

When the user asks to create, review, or modify frontend UI in `apps/web`, follow the Next.js + Tailwind CSS v4 + shadcn React design system rules in this skill.

Read `SKILL.md` before acting on tasks that touch:

- Pages, layouts, dashboards, tables, forms, navigation, or product UI.
- Shared React components and shadcn primitives.
- Tailwind tokens in `apps/web/src/app/globals.css`.
- Light-only theme decisions, colors, spacing, typography, accessibility, or responsive behavior.

Core rules:

- Build product UI for a B2B document workspace: clear, dense, trustworthy, and scan-friendly.
- Use existing semantic Tailwind tokens and shadcn primitives instead of inventing a parallel design system.
- Keep the app light-only. Do not add dark mode, `dark:*` classes, theme toggles, or dark variants.
- Use Lucide icons for icon buttons and tool affordances.
- Keep controls at `h-12` or `min-h-12` by default; icon buttons use `size-12`.
- Avoid landing-page aesthetics, decorative gradients, blobs, or nested cards.
- Keep text readable, contained, and accessible on mobile and desktop.
- Use visible focus states, `aria-label` for icon-only buttons, and text/icons in addition to color for status.

Validate UI changes with the repo's existing build/lint/test commands when available. For visual changes, inspect the result in browser when possible.
