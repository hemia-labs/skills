---
name: frontend-page-ui
description: Hemia Docs frontend page UI conventions for Next.js/React with Tailwind and shadcn-style primitives. Use when creating, reviewing, or modifying app pages, page headers, breadcrumbs, titles, descriptions, actions, section headings, tables, panels, forms, layout spacing, i18n page labels, or metadata in `frontend/src/app` and related components.
---

# Frontend Page UI

## Overview

Apply Hemia Docs page conventions before changing or reviewing frontend UI. Prefer existing components and tokens over bespoke markup.

Read local files first when changing code:

- `frontend/src/components/page/page-heading.tsx`
- `frontend/src/components/page/app-breadcrumb.tsx`
- `frontend/src/components/page/stat-card.tsx`
- `frontend/src/components/dashboard/panel-title.tsx`
- `frontend/src/app/globals.css`
- `frontend/src/i18n/messages.ts`

## Page Heading

Use `PageHeading` for app pages whenever possible.

```tsx
<PageHeading
  actions={
    <Button asChild className="h-11 w-fit gap-2 whitespace-nowrap rounded-lg px-4 text-sm font-semibold">
      <Link href={localizePath("/spaces/new", locale)}>
        <Plus className="size-5" />
        {t("spaces.newSpace")}
      </Link>
    </Button>
  }
  breadcrumb={
    <AppBreadcrumb
      items={[
        { label: "Hemia" },
        { label: t("spaces.breadcrumb") },
      ]}
    />
  }
  description={t("spaces.description")}
  title={t("spaces.title")}
/>
```

`PageHeading` shape:

- wrapper: `mb-6 flex flex-col justify-between gap-4 xl:flex-row xl:items-end`
- `h1`: `text-3xl font-bold tracking-normal md:text-4xl`
- description: `mt-2 max-w-3xl text-sm leading-6 text-supporting md:text-base`

Rules:

- Use one `h1` per page.
- Keep title short: section/resource name.
- Keep description brief: what user can review or do.
- Put primary page actions in `actions`, not inside copy.
- For long dynamic titles, use `truncate` and ensure parent has `min-w-0`.

## Breadcrumbs

Use `AppBreadcrumb` above page title.

```tsx
<AppBreadcrumb
  items={[
    { label: "Hemia" },
    { href: localizePath("/spaces", locale), label: t("spaces.breadcrumb") },
    { label: space.name },
  ]}
/>
```

`AppBreadcrumb` shape:

- wrapper: `mb-3 text-sm font-semibold text-primary`
- list: `gap-0 text-sm font-semibold text-primary`
- separator: `/` with `mx-2 text-muted-foreground`
- current page: `truncate font-semibold text-muted-foreground`
- links: `text-primary hover:underline hover:text-primary`

Rules:

- First item is `"Hemia"` inside app shell.
- Intermediate items get `href`.
- Last item has no `href`.
- UI labels come from i18n.
- Dynamic labels may be resource names like `space.name` or `collection.name`.

## Type Scale

Use:

- Page title: `text-3xl font-bold tracking-normal md:text-4xl`
- Compact page/error title: `text-2xl font-bold`
- Section/panel title: `text-lg font-bold`
- Dense card title: `text-base font-bold`
- Row/list title: `text-sm font-bold` or `text-sm font-semibold`
- Metadata/help: `text-xs text-muted-foreground`
- Micro label/badge: `text-2xs font-bold uppercase tracking-normal text-muted-foreground`

Rules:

- Do not use `tracking-tight` or negative tracking.
- Do not scale text with viewport units.
- Reserve `text-3xl`/`md:text-4xl` for page titles and major metrics.
- Use `truncate`, `line-clamp-*`, intentional wrapping, or horizontal scroll for long text.

## Descriptions

Page descriptions:

```tsx
<p className="mt-2 max-w-3xl text-sm leading-6 text-supporting md:text-base">
  {description}
</p>
```

Panel/card descriptions:

```tsx
<p className="mt-2 text-sm leading-6 text-supporting">{description}</p>
```

Form helper text:

```tsx
<p className="text-sm leading-5 text-supporting">{help}</p>
```

Use `text-supporting` for important explanatory copy. Use `text-muted-foreground` for metadata, timestamps, and secondary labels.

## Actions

Page header primary action:

```tsx
<Button className="h-11 w-fit gap-2 whitespace-nowrap rounded-lg px-4 text-sm font-semibold">
  <Plus className="size-5" />
  Crear
</Button>
```

Secondary action:

```tsx
<Button className="h-11 w-fit gap-2 whitespace-nowrap rounded-lg px-4 text-sm font-semibold" variant="outline">
  <CalendarDays className="size-5" />
  Rango
</Button>
```

Rules:

- Use `lucide-react` icons.
- Header buttons use `h-11`, `rounded-lg`, `px-4`, `text-sm font-semibold`.
- Add `whitespace-nowrap` for compact actions.
- Icon-only buttons require `aria-label`.
- Internal links use `Button asChild` with `next/link`.

## Layout And Surfaces

Page sections:

- Main content uses `bg-background`.
- Separate major sections with `mt-4`, `mt-5`, or `mt-6`.
- Metric grids: `grid gap-3 sm:grid-cols-2 xl:grid-cols-*`.
- Panel grids: `grid gap-4`.
- Wide tables need horizontal scroll plus stable min-width class.

Stable table classes:

- `spaces-table`
- `collections-table`
- `collection-documents-table`
- `space-detail-docs-table`
- `dashboard-table-search`
- `dashboard-table-consulted`
- `dashboard-table-updates`

Cards/panels:

```tsx
<section className="rounded-lg border border-border bg-card p-5 shadow-sm">
  ...
</section>
```

Table/list surface:

```tsx
<section className="rounded-lg border border-border bg-card shadow-sm">
  <div className="flex items-center justify-between border-b px-5 py-4">
    <h2 className="text-lg font-bold">Todas las areas</h2>
  </div>
  ...
</section>
```

Rules:

- Use `rounded-lg border border-border bg-card shadow-sm` for panels.
- Normal padding: `p-5` or `p-6`.
- Table/panel header: `px-5 py-4`.
- Do not create cards inside cards.
- Do not add decorative gradients or dark content backgrounds.

## Colors

Use semantic tokens:

- Main text: `text-foreground`
- Supporting text: `text-supporting`
- Secondary text: `text-muted-foreground`
- Action/link: `text-primary`
- Page background: `bg-background`
- Panel background: `bg-card`
- Soft backgrounds: `bg-muted`, `bg-secondary`, `bg-surface-soft`
- Borders: `border-border`, `border-surface-line`
- Errors: `text-destructive`, `border-destructive`, `bg-red-50 text-red-700`

Do not use inline hex, `dark:*`, or `slate-*` as visual base. Violet only as minor accent.

## i18n

Put UI text in `frontend/src/i18n/messages.ts`.

```ts
spaces: {
  breadcrumb: "Areas",
  title: "Areas",
  description: "Administra areas de documentacion, visibilidad y propiedad editorial.",
  actions: {
    create: "Nueva area",
  },
}
```

Guidance:

- `breadcrumb`: short route label.
- `title`: main screen label.
- `description`: context sentence.
- `actions`: button/menu verbs.
- `table`: table headers and states.
- `filters`: filter labels/placeholders.
- Avoid hardcoded UI strings except brand names such as `"Hemia"` or temporary sample data.

## Next Metadata

For public/indexable or stable route pages, define `metadata` or `generateMetadata` near `page.tsx`.

```tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Areas | Hemia Docs",
  description: "Administra areas de documentacion, visibilidad y propiedad editorial.",
};
```

Private pages may prioritize i18n/UI; add metadata when title is stable.

## Checklist

Before finalizing a page:

- Use `PageHeading` or match its scale for detail/form exceptions.
- Use `AppBreadcrumb`; last item has no `href`.
- Ensure one `h1`.
- Source title/description from i18n.
- Put primary actions in heading `actions`.
- Use Lucide icons where actions benefit from icons.
- Use tokens: `bg-card`, `border-border`, `shadow-sm`, `text-supporting`.
- Protect long text with `min-w-0`, `truncate`, wrapping, or scroll.
- Give wide tables stable width and horizontal overflow.
- Avoid `dark:*`, inline hex, decorative gradients, and nested cards.
