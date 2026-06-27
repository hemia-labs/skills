---
name: frontend-page-ui
description: Conventions for building app pages in React + Tailwind with shadcn-style primitives (Button, Card, Input, Badge, DropdownMenu, Table, Avatar, Tabs, Pagination). Use when creating or reviewing a resource list/index page (heading, breadcrumbs, stat cards, filter bar, data table) or a resource detail page (identity header, metrics, tabs, body panels), plus layout spacing, type scale, and tokens.
---

# Frontend List Page UI

A blueprint for the most common app screen: a **list/index page** for a resource
(spaces, users, invoices, projects…). It stacks four areas, top to bottom, in a
single vertical flow. Use it to build a new listing or to review an existing one
for consistency.

This skill is framework-agnostic about your component library: it assumes
[shadcn/ui](https://ui.shadcn.com)-style primitives (`Button`, `Card`, `Input`,
`Badge`, `DropdownMenu`, `Table`, `Avatar`, `Pagination`, `ScrollArea`) and a
Tailwind theme exposing semantic tokens (`bg-card`, `border-border`,
`text-primary`, `text-muted-foreground`, …). Substitute your project's own
primitives where names differ — the structure and measurements are what matter.

## Index

1. [Page anatomy](#page-anatomy) — the four areas at a glance
2. [Area 1 — Page heading](#area-1--page-heading)
3. [Area 2 — Stat cards](#area-2--stat-cards-optional)
4. [Area 3 — Filter bar](#area-3--filter-bar-optional)
5. [Area 4 — Data table panel](#area-4--data-table-panel)
6. [Vertical rhythm & container](#vertical-rhythm--container)
7. [Tokens, type scale & measurements](reference/tokens.md) — companion file
8. [Detail page anatomy](reference/detail-page.md) — for single-resource pages
9. [Checklist](#checklist)

## Page anatomy

```
┌──────────────────────────────────────────────────────────────┐
│ AREA 1  Page heading                                          │
│   breadcrumb                                                  │
│   H1 title                          [ + Primary action ]      │
│   description                                                 │
├──────────────────────────────────────────────────────────────┤
│ AREA 2  Stat cards          (optional)                        │
│   [ card ] [ card ] [ card ] [ card ]                         │
├──────────────────────────────────────────────────────────────┤
│ AREA 3  Filter bar          (optional)                        │
│   [ 🔍 search........ ] [ filter ▾ ] [ filter ▾ ] [ reset ]   │
├──────────────────────────────────────────────────────────────┤
│ AREA 4  Data table panel                                      │
│   title  (count)                                              │
│   ┌────────────────────────────────────────────────────────┐ │
│   │ table header                                           │ │
│   │ rows…                                                  │ │
│   └────────────────────────────────────────────────────────┘ │
│   showing N            [ ‹ 1 › ]   [ page size ▾ ]            │
└──────────────────────────────────────────────────────────────┘
```

Each area is a sibling block separated by top margin (see
[Vertical rhythm](#vertical-rhythm--container)). Areas 2 and 3 are optional;
1 and 4 are the minimum viable listing.

---

## Area 1 — Page heading

The identity of the page: where you are, what it is, and the one primary thing
you can do here. Wrap it in a reusable `PageHeading` component so every page
shares the exact scale.

**Components:** breadcrumb, `<h1>`, description `<p>`, and an `actions` slot
(usually a single primary `Button`).

**Distribution:** breadcrumb/title/description stack on the left; primary action
sits to the right and drops below the text on narrow screens.

```tsx
<section className="mb-6 flex flex-col justify-between gap-4 xl:flex-row xl:items-end">
  <div>
    {breadcrumb}
    <h1 className="text-3xl font-bold tracking-normal md:text-4xl">{title}</h1>
    <p className="mt-2 max-w-3xl text-sm leading-6 text-muted-foreground md:text-base">
      {description}
    </p>
  </div>
  {actions}
</section>
```

**Breadcrumb** (above the title):

```tsx
<nav className="mb-3 text-sm font-semibold text-primary">
  {/* first crumb = app/brand name; intermediates get href; last crumb has NO href */}
  <ol className="flex gap-0">
    <li className="text-primary hover:underline">Brand</li>
    <li><span className="mx-2 text-muted-foreground">/</span></li>
    <li className="truncate font-semibold text-muted-foreground">Current page</li>
  </ol>
</nav>
```

**Primary action** (in the `actions` slot):

```tsx
<Button asChild className="h-11 w-fit gap-2 whitespace-nowrap rounded-lg px-4 text-sm font-semibold">
  <Link href={createHref}>
    <Plus className="size-5" />
    {createLabel}
  </Link>
</Button>
```

**Optional:** the `actions` slot (read-only pages have none); a second
secondary action (`variant="outline"`, same sizing) beside the primary.

**Rules:**

- Exactly one `<h1>` per page.
- Title is the short resource/section name; description is one sentence on what
  the user can review or do here. Never put actions inside the description copy.
- First breadcrumb crumb is the app/brand name; the last crumb is the current
  page and carries no `href`.
- For long dynamic titles add `truncate` and give the parent `min-w-0`.

---

## Area 2 — Stat cards (optional)

A row of metric cards summarizing the list (totals, counts, deltas). Drop this
area entirely when the resource has no meaningful aggregate metrics.

**Component:** a `StatCard` built on `Card` — icon chip + label + big value, plus
an optional delta line.

**Distribution:** a responsive grid that collapses to a single column on mobile.

```tsx
<section className="grid gap-3 sm:grid-cols-2 2xl:grid-cols-4">
  {metrics.map((m) => <StatCard key={m.label} {...m} />)}
</section>
```

`StatCard` internals:

```tsx
<Card className="gap-0 rounded-lg border-border bg-card py-0 shadow-sm">
  <CardContent className="p-5">
    <div className="flex items-center gap-5">
      <div className="grid size-14 shrink-0 place-items-center rounded-full bg-secondary text-primary">
        <Icon className="size-7" />
      </div>
      <div className="min-w-0">
        <p className="truncate text-sm font-bold">{label}</p>
        <p className="mt-2 text-3xl font-bold">{value}</p>
      </div>
    </div>
    {/* optional delta row */}
    <div className="mt-4 flex items-center gap-3 text-xs font-semibold">
      <span className={negative ? "text-red-600" : "text-emerald-600"}>
        {negative ? "↓" : "↑"} {delta}
      </span>
      <span className="text-muted-foreground">{periodLabel}</span>
    </div>
  </CardContent>
</Card>
```

**Measurements:** icon chip `size-14` rounded-full, icon `size-7`, value
`text-3xl font-bold`, card padding `p-5`, grid gap `gap-3`.

**Optional inside the card:** the delta row (omit when there's no period
comparison); the icon tone (e.g. a `tone` prop swapping the chip color).

**Rules:**

- Reserve `text-3xl` for the metric value — it's the largest text besides the
  page title.
- Match column count to metric count: 4 metrics → `2xl:grid-cols-4`, 3 →
  `lg:grid-cols-3`. Always fall back to one column on mobile.
- Don't nest cards inside cards.

---

## Area 3 — Filter bar (optional)

Search + faceted filters for the table below. Omit when the list is always short
and unfiltered.

**Components:** a `Card` wrapping a search `Input` (with a leading icon) and a row
of filter `DropdownMenu`s, a reset `Button`, and an optional active-filter
count `Badge`.

**Distribution:** search takes the remaining width (`flex-1`); filter buttons
wrap on the right. Stacks vertically on narrow screens, goes horizontal at the
wide breakpoint.

```tsx
<Card className="gap-0 rounded-lg border-border bg-card py-0 shadow-sm">
  <CardContent className="flex flex-col gap-3 p-4 2xl:flex-row 2xl:items-center">
    <form action={listHref} className="relative min-w-0 flex-1">
      <Search className="pointer-events-none absolute left-4 top-1/2 size-5 -translate-y-1/2 text-muted-foreground" />
      <Input name="search" placeholder={searchPlaceholder}
        className="h-12 rounded-lg pl-12 text-sm font-medium shadow-none" />
    </form>

    <div className="flex flex-wrap items-center gap-3">
      {filterGroups.map((group) => (
        <DropdownMenu key={group.label}>
          <DropdownMenuTrigger asChild>
            <Button variant="outline" className="h-12 w-auto gap-3 whitespace-nowrap rounded-lg px-4">
              <SlidersHorizontal className="size-4" />
              {group.label}
            </Button>
          </DropdownMenuTrigger>
          <DropdownMenuContent align="end" className="w-48">
            {group.options.map((o) => (
              <DropdownMenuItem asChild key={o.label}>
                <Link href={o.href} className="flex items-center justify-between gap-3">
                  <span>{o.label}</span>
                  {o.active ? <Check className="size-4" /> : null}
                </Link>
              </DropdownMenuItem>
            ))}
          </DropdownMenuContent>
        </DropdownMenu>
      ))}

      <Button asChild variant="outline" className="h-12 gap-2 rounded-lg px-4">
        <Link href={resetHref}><RotateCcw className="size-4" />{resetLabel}</Link>
      </Button>
    </div>
  </CardContent>
</Card>
```

**Measurements:** controls are `h-12` (taller than the heading's `h-11` button),
search icon `size-5` inset at `left-4` with `pl-12` on the input, filter icons
`size-4`, dropdown width `w-48`.

**Optional:** the active-filter count badge (show only when `activeFilters > 0`);
any individual filter group; URL-driven filters via `<Link href>` vs. client
state — both work, the markup is identical.

**Rules:**

- Drive filters through the URL (links / form GET) when results are
  server-rendered, so state is shareable and back-button-safe.
- A checkmark (`Check`) marks the active option inside each dropdown.
- Keep filter triggers `whitespace-nowrap` so labels don't wrap.

---

## Area 4 — Data table panel

The list itself, inside a bordered panel with a titled header and a paginated
footer. This is the only non-optional area besides the heading.

**Components:** `Card` → header (`<h2>` + count badge) → `ScrollArea` wrapping a
`Table` → footer (`showing N` + `Pagination` + page-size `DropdownMenu`).

```tsx
<Card className="gap-0 rounded-lg border-border bg-card py-0 shadow-sm">
  <CardContent className="p-5">
    {/* panel header */}
    <div className="mb-5 flex items-center gap-3">
      <h2 className="text-lg font-bold">{allLabel}</h2>
      <span className="grid size-7 place-items-center rounded-full bg-muted text-sm font-bold text-muted-foreground">
        {rows.length}
      </span>
    </div>

    {/* table — horizontal scroll + stable min-width */}
    <ScrollArea className="w-full rounded-lg border border-border">
      <Table className="min-w-[64rem]">
        <TableHeader>
          <TableRow className="bg-background">
            <TableHead>
              <span className="flex items-center gap-2">{nameLabel}<ArrowUpDown className="size-3" /></span>
            </TableHead>
            {/* …other headers; numeric cols text-center, actions col text-right… */}
          </TableRow>
        </TableHeader>
        <TableBody>{/* rows */}</TableBody>
      </Table>
    </ScrollArea>

    {/* footer */}
    <div className="mt-4 flex flex-col justify-between gap-3 text-sm text-muted-foreground md:flex-row md:items-center">
      <span>{showingLabel}</span>
      <div className="flex flex-wrap items-center gap-2">
        <Pagination className="w-auto">{/* prev / pages / next */}</Pagination>
        <DropdownMenu>{/* page size */}</DropdownMenu>
      </div>
    </div>
  </CardContent>
</Card>
```

**Row anatomy** — common cell patterns:

- **Primary cell** (name): small icon chip + bold linked label.
  ```tsx
  <div className="flex items-center gap-3">
    <span className="grid size-9 place-items-center rounded bg-secondary text-primary">
      <Icon className="size-5" />
    </span>
    <Link href={detailHref} className="font-bold text-primary underline-offset-4 hover:underline">
      {row.name}
    </Link>
  </div>
  ```
- **Status / category cell:** a colored `Badge` (one soft background + matching
  text per value — see [tokens reference](reference/tokens.md#status-badges)).
- **Person cell:** `Avatar` (initials fallback) + name.
  ```tsx
  <div className="flex items-center gap-3">
    <Avatar className="size-8"><AvatarFallback className="bg-foreground text-xs font-bold text-white">{initials}</AvatarFallback></Avatar>
    <span className="whitespace-nowrap text-sm">{name}</span>
  </div>
  ```
- **Numeric cell:** `text-center font-medium`.
- **Timestamp cell:** `whitespace-nowrap text-muted-foreground`.
- **Actions cell** (`text-right`): a row of `size="icon-sm"` ghost buttons for the
  top 1–3 actions, then a `MoreVertical` dropdown for the rest. Icon-only buttons
  **must** have `aria-label`. Destructive items use `variant="destructive"`.

**Measurements:** panel padding `p-5`, header bottom margin `mb-5`, count badge
`size-7` rounded-full, primary-cell icon chip `size-9 rounded`, sort glyph
`ArrowUpDown size-3`, footer top margin `mt-4`.

**Optional:** the count badge in the header; sort glyphs (only on sortable
columns); the page-size selector; any individual action button (collapse rare
actions into the `MoreVertical` menu).

**Rules:**

- Always wrap wide tables in `ScrollArea` and give the `Table` a stable
  `min-w-[…]` so columns don't crush on small viewports.
- Header row gets `bg-background`; the panel body is `bg-card`.
- Provide an empty state when there are no rows (a centered message replacing the
  table body).

---

## Vertical rhythm & container

The four areas are plain siblings separated by top margin — no extra wrappers:

```tsx
<>
  <PageHeading … />                         {/* has its own mb-6 */}
  <section className="grid gap-3 …">…</section>   {/* stat cards */}
  <div className="mt-5"><FilterBar … /></div>
  <div className="mt-5"><TablePanel … /></div>
</>
```

- Page heading carries `mb-6`; subsequent areas use `mt-5` (or `mt-4`/`mt-6`).
- Page background is `bg-background`; every panel sits on `bg-card`.
- Don't introduce decorative gradients, dark content backgrounds, or cards
  nested inside cards.

---

## Checklist

Before finalizing a list page:

- [ ] Heading uses the shared `PageHeading` scale; exactly one `<h1>`.
- [ ] Breadcrumb present; first crumb = brand, last crumb has no `href`.
- [ ] Primary action lives in the heading `actions` slot, not in body copy.
- [ ] Stat cards (if any) in a responsive grid that collapses to 1 column.
- [ ] Filter bar drives state through the URL; active option shows a check.
- [ ] Table wrapped in `ScrollArea` with a stable `min-w-[…]`.
- [ ] Icon-only action buttons have `aria-label`; destructive uses the
      destructive variant.
- [ ] Empty state handled when the list has zero rows.
- [ ] Long text protected with `min-w-0` + `truncate` / wrapping / scroll.
- [ ] Only semantic tokens & the type scale from
      [reference/tokens.md](reference/tokens.md); no inline hex, no `dark:*`,
      no nested cards.
