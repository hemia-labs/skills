# Frontend Page UI

Use `SKILL.md` as source of truth.

When user asks to create, review, or modify Hemia Docs app pages, follow this
skill before changing UI in `frontend/src/app` or related page components.

Read `SKILL.md` before tasks touching:

- Page headings, breadcrumbs, titles, descriptions, metadata, or i18n labels.
- Page actions, panels, forms, tables, lists, or layout spacing.
- Shared page components, Tailwind tokens, or responsive page behavior.

Core rules:

- Reuse `PageHeading` and `AppBreadcrumb` when possible.
- Use one `h1`; source UI text from `frontend/src/i18n/messages.ts`.
- Use semantic Tailwind tokens and existing shadcn-style primitives.
- Keep actions accessible: Lucide icons, visible labels, and `aria-label` on icon-only buttons.
- Protect long content with truncation, wrapping, or horizontal table scroll.
- Avoid `dark:*`, inline hex, decorative gradients, and nested cards.

Validate UI changes with existing repo checks; inspect visually when possible.
