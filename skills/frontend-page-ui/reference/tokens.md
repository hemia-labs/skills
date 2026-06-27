# Tokens, Type Scale & Measurements

Companion reference for [SKILL.md](../SKILL.md). All values assume a Tailwind
theme exposing shadcn-style semantic tokens. Use these instead of raw colors or
ad-hoc sizes so every page stays consistent.

## Type scale

| Use | Classes |
| --- | --- |
| Page title (`h1`) | `text-3xl font-bold tracking-normal md:text-4xl` |
| Compact / error title | `text-2xl font-bold` |
| Section / panel title (`h2`) | `text-lg font-bold` |
| Dense card title | `text-base font-bold` |
| Big metric value | `text-3xl font-bold` |
| Row / list title | `text-sm font-bold` or `text-sm font-semibold` |
| Body / cell text | `text-sm` (add `leading-5`/`leading-6` for multi-line) |
| Metadata / help / timestamp | `text-xs text-muted-foreground` |
| Micro label / badge | `text-2xs font-bold uppercase tracking-normal text-muted-foreground` |

Rules:

- Don't use `tracking-tight` or negative tracking.
- Don't scale text with viewport units.
- Reserve `text-3xl` / `md:text-4xl` for the page title and big metric values.
- For long text use `truncate`, `line-clamp-*`, intentional wrapping, or
  horizontal scroll — never let it overflow its container.

## Color tokens

Use semantic tokens; never inline hex. Standard shadcn tokens:

| Role | Token |
| --- | --- |
| Main text | `text-foreground` |
| Secondary / metadata text | `text-muted-foreground` |
| Action / link | `text-primary` |
| Page background | `bg-background` |
| Panel / card background | `bg-card` |
| Soft fill | `bg-muted` / `bg-secondary` |
| Borders | `border-border` |
| Destructive | `text-destructive`, `border-destructive` |

If your theme defines extra tokens (e.g. a `text-supporting` for emphasized
explanatory copy, or a `border-surface-line` for inner table borders), prefer
them; otherwise fall back to `text-muted-foreground` / `border-border`.

Rules:

- No inline hex, no `dark:*` utilities, no `slate-*` as a visual base.
- Use a single accent color sparingly; don't introduce decorative gradients.

## Status badges

Status / category cells use a `Badge` with one soft background + matching text
per value. Use the `50/700` step of any Tailwind hue and keep the hover the same
as the base so the badge doesn't shift on hover:

```tsx
const badgeClasses: Record<Status, string> = {
  A: "bg-blue-50 text-blue-700 hover:bg-blue-50",
  B: "bg-emerald-50 text-emerald-700 hover:bg-emerald-50",
  C: "bg-orange-50 text-orange-700 hover:bg-orange-50",
  D: "bg-violet-50 text-violet-700 hover:bg-violet-50",
  E: "bg-cyan-50 text-cyan-700 hover:bg-cyan-50",
};
```

Assign hues by meaning, not order (e.g. green = healthy/internal, orange =
warning/private). Reuse the same map everywhere a given status appears.

## Measurements

Concrete sizes used across the four page areas:

| Element | Size |
| --- | --- |
| Heading primary button | `h-11 rounded-lg px-4 text-sm font-semibold` |
| Filter / search controls | `h-12 rounded-lg px-4` |
| Page-size / footer button | `h-10 px-4` |
| Icon-only action button | `size="icon-sm"` (ghost), icon `size-4` |
| Stat card icon chip | `size-14 rounded-full`, icon `size-7` |
| Primary-cell icon chip | `size-9 rounded`, icon `size-5` |
| Panel count badge | `size-7 rounded-full` |
| Avatar in row | `size-8`, fallback `text-xs font-bold` |
| Inline action / filter icon | `size-4` |
| Sort glyph | `ArrowUpDown size-3` |
| Search field icon | `size-5`, inset `left-4`, input `pl-12` |

Spacing:

| Context | Value |
| --- | --- |
| Page heading bottom margin | `mb-6` |
| Between page areas | `mt-5` (or `mt-4` / `mt-6`) |
| Panel / card padding | `p-5` (or `p-6`) |
| Filter bar padding | `p-4` |
| Panel/table header padding | `px-5 py-4` |
| Metric grid gap | `gap-3` |
| Panel grid gap | `gap-4` |
| Panel header bottom margin | `mb-5` |
| Table footer top margin | `mt-4` |

Surfaces:

- Panels: `rounded-lg border border-border bg-card shadow-sm`.
- Card primitives that ship with default padding/gap: reset with `gap-0 py-0`
  and control padding on the inner `CardContent`.
- Table header row: `bg-background`; table wrapper: `ScrollArea` +
  `rounded-lg border border-border` + a stable `min-w-[…]` on the `Table`.

## Icons

Use a single icon set (the examples use [lucide-react](https://lucide.dev)).
Common roles: `Plus` (create), `Search`, `SlidersHorizontal` (filter),
`RotateCcw` (reset/restore), `Check` (active option), `Eye` (view), `Edit3`
(edit), `Shield` (permissions), `MoreVertical` (overflow menu), `Copy`
(duplicate), `Archive`, `Trash2` (delete), `ArrowUpDown` (sortable column).
Every icon-only control needs an `aria-label`.

## Internationalization (optional)

If the project uses i18n, source every visible string (titles, descriptions,
labels, table headers, filter labels, action verbs, empty/loading states) from
the message catalog rather than hardcoding it. The only literals that stay inline
are brand names and temporary sample data. If there's no i18n layer, plain
strings are fine — just keep them in one place per page.
