# Detail Page UI

Companion to [SKILL.md](../SKILL.md) (which covers list/index pages). This file
covers the **detail page** for a single resource (a collection, project, user,
invoice…): the screen you land on after clicking a row in a list.

Shares the same primitives, tokens, and type scale —
see [tokens.md](tokens.md). The difference is structure: a detail page leads with
an **identity header panel**, summarizes with **metrics**, then exposes the
resource's facets through **tabs**, each rendering its own **body panel**.

## Index

1. [Page anatomy](#page-anatomy)
2. [Area 1 — Header panel](#area-1--header-panel)
3. [Area 2 — Metric row](#area-2--metric-row-optional)
4. [Area 3 — Tab navigation](#area-3--tab-navigation-optional)
5. [Area 4 — Tab body panels](#area-4--tab-body-panels) — catalog of panel patterns
6. [Vertical rhythm](#vertical-rhythm)
7. [Checklist](#checklist)

## Page anatomy

```
┌──────────────────────────────────────────────────────────────┐
│ AREA 1  Header panel  (bordered card)                         │
│   breadcrumb                                                  │
│   [←] [icon]  H1 title  [badge] [badge]   [Edit][Config][⋮]   │
│              description                                       │
│              meta: linked parent resource                     │
├──────────────────────────────────────────────────────────────┤
│ AREA 2  Metric row          (optional)                        │
│   [ metric ] [ metric ] [ metric ] [ metric ] [ metric ]      │
├──────────────────────────────────────────────────────────────┤
│ AREA 3  Tab navigation      (optional)                        │
│   [ Tab A ] [ Tab B ] [ Tab C ] [ Tab D ]                     │
├──────────────────────────────────────────────────────────────┤
│ AREA 4  Tab body — one panel pattern per tab                  │
│   ┌────────────────────────────────────────────────────────┐ │
│   │  panel title       …content (table / grid / list)…     │ │
│   └────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────┘
```

Unlike a list page (whose areas are bare siblings), a detail page wraps the
whole thing in a `space-y-5` stack and makes the **header an actual card**, so
the identity block reads as one object. Areas 2 and 3 are optional.

---

## Area 1 — Header panel

The resource's identity and the actions that operate on the whole resource. It's
a bordered panel (not a bare heading) so it visually owns the page.

**Components:** `Card`/`section`, breadcrumb, a back `Button` (icon), an icon
chip, `<h1>`, status/parent `Badge`s, description `<p>`, an optional meta line
linking the parent resource, and an action cluster (primary + secondary +
overflow `DropdownMenu`).

**Distribution:** identity on the left (back button, icon, title block all in a
`flex … gap-4`); actions on the right. Stacks vertically below the wide
breakpoint (`xl:flex-row xl:items-start`).

```tsx
<section className="rounded-lg border border-border bg-card p-5 shadow-sm">
  {breadcrumb /* mb-4; last crumb = this resource's name, no href */}
  <div className="flex flex-col justify-between gap-5 xl:flex-row xl:items-start">
    <div className="flex min-w-0 items-start gap-4">
      <Button asChild aria-label={backLabel} variant="outline" size="icon-lg" className="mt-1 size-12 rounded-lg">
        <Link href={listHref}><ArrowLeft className="size-5" /></Link>
      </Button>
      <span className="mt-1 grid size-12 shrink-0 place-items-center rounded-lg bg-secondary text-primary">
        <Icon className="size-6" />
      </span>
      <div className="min-w-0">
        <div className="flex flex-wrap items-center gap-3">
          <h1 className="truncate text-3xl font-bold tracking-normal md:text-4xl">{name}</h1>
          <Badge variant="secondary" className={statusBadge}>{status}</Badge>
          <Badge variant="secondary" className="bg-secondary text-primary">{parentName}</Badge>
        </div>
        <p className="mt-2 max-w-3xl text-sm leading-6 text-muted-foreground md:text-base">{description}</p>
        <p className="mt-2 text-sm font-semibold text-muted-foreground">
          {parentLabel}: <Link href={parentHref} className="text-primary hover:underline">{parentName}</Link>
        </p>
      </div>
    </div>

    <div className="flex flex-wrap gap-3">
      <Button asChild className="h-12 gap-2 rounded-lg px-4 font-semibold">
        <Link href={editHref}><Edit3 className="size-5" />{editLabel}</Link>
      </Button>
      <Button asChild variant="outline" className="h-12 gap-2 rounded-lg px-4 font-semibold">
        <Link href={`${baseHref}?tab=settings`}><Settings2 className="size-5" />{configLabel}</Link>
      </Button>
      <DropdownMenu>{/* archive, delete (destructive)… in a size-12 icon-lg trigger */}</DropdownMenu>
    </div>
  </div>
</section>
```

**Measurements:** panel padding `p-5`, breadcrumb `mb-4`, back button & icon chip
& overflow trigger all `size-12 rounded-lg`, chip icon `size-6`, action buttons
`h-12`, gap between identity and actions `gap-5`.

**Optional:** the back button (skip when the app shell already provides
navigation); the icon chip; any badge; the meta/parent line; the secondary
"configure" action; the overflow menu.

**Rules:**

- One `<h1>` — the resource name; give it `truncate` and the parent `min-w-0`.
- Badges go inline with the title in a `flex-wrap gap-3` so they wrap under it on
  narrow screens.
- Whole-resource actions (edit, archive, delete) live here. Per-row/per-item
  actions belong inside the tab body, not here.
- Destructive items (`delete`) use `variant="destructive"` inside the overflow
  menu, never as a bare primary button.

---

## Area 2 — Metric row (optional)

A compact version of the list page's stat cards — summarizes the resource's
contents (counts by status, last-updated, totals). Drop it when there's nothing
to aggregate.

**Component:** a compact `Metric` card — horizontal icon chip + label + value,
**no delta row** (that's the difference from the list-page `StatCard`).

```tsx
<section className="grid gap-3 sm:grid-cols-2 xl:grid-cols-5">
  {metrics.map((m) => (
    <Card key={m.label} className="gap-0 rounded-lg border-border bg-card py-0 shadow-sm">
      <CardContent className="flex items-center gap-4 p-5">
        <span className="grid size-12 shrink-0 place-items-center rounded-full bg-secondary text-primary">
          <m.icon className="size-6" />
        </span>
        <div className="min-w-0">
          <p className="truncate text-sm font-bold text-muted-foreground">{m.label}</p>
          <p className="mt-1 truncate text-2xl font-bold">{m.value}</p>
        </div>
      </CardContent>
    </Card>
  ))}
</section>
```

**Measurements:** chip `size-12 rounded-full`, icon `size-6`, value `text-2xl`
(smaller than the list page's `text-3xl` so it doesn't compete with the title).
Column count matches metric count (`xl:grid-cols-5` for five), always one column
on mobile.

---

## Area 3 — Tab navigation (optional)

Splits the resource's facets (documents, structure, permissions, activity,
settings…) into tabs. Skip entirely for simple resources that fit on one panel.

**Components:** `Tabs` + `TabsList` (line variant) + `TabsTrigger`, in a
horizontally-scrollable bordered strip.

```tsx
<Tabs className="gap-5" value={activeTab}>
  <div className="w-full overflow-x-auto rounded-lg border border-border bg-card px-2 py-3 shadow-sm">
    <TabsList variant="line" className="h-auto w-max justify-start gap-2 bg-card p-0">
      {tabs.map((tab) => (
        <TabsTrigger asChild key={tab} value={tab}
          className="h-12 flex-none rounded-md px-4 text-sm font-semibold text-muted-foreground after:hidden data-[state=active]:!bg-primary data-[state=active]:!text-primary-foreground data-[state=active]:shadow-sm hover:bg-accent hover:text-foreground">
          <Link href={tab === defaultTab ? baseHref : `${baseHref}?tab=${tab}`} replace scroll={false} prefetch={false}>
            {tabLabel(tab)}
          </Link>
        </TabsTrigger>
      ))}
    </TabsList>
  </div>
  <TabsContent className="mt-0" value={activeTab}>{activeTabContent}</TabsContent>
</Tabs>
```

**Measurements:** triggers `h-12 rounded-md px-4`, strip padding `px-2 py-3`, tab
gap `gap-2`, content gap from strip `gap-5`.

**Rules:**

- Drive the active tab through the URL (`?tab=…`) so it's deep-linkable and
  back-button-safe; the default tab uses the bare `baseHref`.
- Active trigger is a solid `bg-primary` pill; inactive is muted text with an
  `accent` hover. Use `scroll={false}` + `replace` so switching tabs doesn't jump
  the scroll position or pollute history.
- Wrap the list in `overflow-x-auto` so many tabs scroll instead of wrapping.

---

## Area 4 — Tab body panels

Each tab renders one panel. Lead every panel with a `PanelTitle` (icon chip +
`h2`) and pick the pattern that fits the content:

```tsx
function PanelTitle({ icon: Icon, title }) {
  return (
    <div className="flex items-center gap-3">
      <span className="grid size-10 place-items-center rounded-md bg-secondary text-primary">
        <Icon className="size-5" />
      </span>
      <h2 className="text-lg font-bold">{title}</h2>
    </div>
  );
}
```

### a. Table panel with inline controls

For a list facet. Same table mechanics as the list page
([SKILL.md Area 4](../SKILL.md#area-4--data-table-panel)), but filters sit in the
panel header and the footer offers a "view all" link instead of full pagination.

```tsx
<Card className="gap-0 rounded-lg border-border bg-card py-0 shadow-sm">
  <CardContent className="p-0">
    <div className="flex flex-col justify-between gap-3 p-5 lg:flex-row lg:items-center">
      <PanelTitle icon={FileText} title={title} />
      <div className="grid gap-3 sm:grid-cols-[minmax(14rem,1fr)_12rem_12rem]">
        {/* search Input (h-12) + Select filters (h-12) */}
      </div>
    </div>
    <ScrollArea className="w-full border-y border-border">
      <Table className="min-w-[64rem]">{/* … */}</Table>
    </ScrollArea>
    <div className="flex items-center justify-between gap-3 p-5 text-sm text-muted-foreground">
      <span>{showingLabel}</span>
      <Button variant="outline" className="h-12 rounded-lg px-4 font-semibold">{viewAllLabel}</Button>
    </div>
  </CardContent>
</Card>
```

Note `CardContent` is `p-0` here so the header, table, and footer each manage
their own padding and the table borders run edge-to-edge.

### b. Card-grid (grouped items)

For structure/grouping facets: groups as cards, items stacked inside.

```tsx
<div className="mt-5 grid gap-4 xl:grid-cols-3">
  {groups.map((g) => (
    <section key={g.label} className="rounded-lg border border-border bg-background p-4">
      <div className="flex items-center justify-between gap-3">
        <h3 className="font-bold">{g.label}</h3>
        <Badge variant="secondary" className="bg-secondary text-primary">{g.items.length}</Badge>
      </div>
      <div className="mt-4 space-y-2">
        {g.items.map((it) => (
          <div key={it.id} className="flex items-center justify-between gap-3 rounded-md border border-border bg-card p-3">
            <span className="flex min-w-0 items-center gap-2 text-sm font-semibold">
              <FileText className="size-4 shrink-0 text-primary" />
              <span className="truncate">{it.title}</span>
            </span>
            <MoreVertical className="size-4 shrink-0 text-muted-foreground" />
          </div>
        ))}
      </div>
    </section>
  ))}
</div>
```

### c. Main + aside (two-column)

For facets with a primary list and contextual sidebar (permissions + inheritance,
content + metadata). A fixed-width aside on the right collapses below the main on
narrow screens.

```tsx
<div className="grid gap-5 xl:grid-cols-[minmax(0,1fr)_22rem]">
  <Card>{/* main panel */}</Card>
  <Card>{/* aside: ~22rem context card */}</Card>
</div>
```

### d. Activity timeline

For an event/history facet: avatar rail with a connecting line, event text, and a
timestamp.

```tsx
<div className="mt-5 space-y-5">
  {items.map((item, i) => (
    <div key={item.id} className="grid grid-cols-[2rem_minmax(0,1fr)_auto] gap-4">
      <div className="relative flex justify-center">
        {i < items.length - 1 && <span className="absolute top-8 bottom-[-1.25rem] w-px bg-border" />}
        <Avatar className="relative size-8 border border-border">
          <AvatarFallback className="bg-foreground text-2xs font-bold text-white">{item.actorInitials}</AvatarFallback>
        </Avatar>
      </div>
      <div className="min-w-0">
        <p className="text-sm font-bold">{item.actor}</p>
        <p className="mt-1 text-sm leading-6 text-muted-foreground">{item.event}</p>
      </div>
      <span className="whitespace-nowrap text-sm text-muted-foreground">{item.time}</span>
    </div>
  ))}
</div>
```

The connecting line is the absolutely-positioned `w-px` span, hidden on the last
item via the `i < length - 1` guard.

### e. Definition list (settings / key-value)

For a settings/metadata facet: label/value rows separated by dividers.

```tsx
<div className="mt-5 divide-y divide-border rounded-lg border border-border bg-background px-4">
  {rows.map((row) => (
    <div key={row.label} className="grid gap-2 py-4 md:grid-cols-[14rem_minmax(0,1fr)] md:items-center">
      <p className="text-sm font-semibold text-muted-foreground">{row.label}</p>
      <p className="text-sm font-bold">{row.value}</p>
    </div>
  ))}
</div>
```

---

## Vertical rhythm

```tsx
<div className="space-y-5">
  <section className="rounded-lg border border-border bg-card p-5 shadow-sm">{/* header */}</section>
  <section className="grid gap-3 …">{/* metrics */}</section>
  <Tabs className="gap-5">{/* tabs + body */}</Tabs>
</div>
```

- Wrap the whole page in `space-y-5` (vs. the list page's per-area `mt-5`).
- Inner panels reuse the page surface tokens: `rounded-lg border border-border
  bg-card shadow-sm`; nested sub-cards sit on `bg-background` so they read as
  recessed, not stacked cards-in-cards.

---

## Checklist

Before finalizing a detail page:

- [ ] Header is a bordered card with one `<h1>` (the resource name, truncated).
- [ ] Breadcrumb's last crumb is this resource and has no `href`.
- [ ] Whole-resource actions in the header; destructive action in the overflow
      menu with `variant="destructive"`.
- [ ] Metric row (if any) uses the compact `Metric` (`text-2xl`, no delta).
- [ ] Tabs driven by `?tab=…`; active tab is a solid pill; `scroll={false}`.
- [ ] Each tab body leads with a `PanelTitle` and picks one body pattern (a–e).
- [ ] Sub-cards sit on `bg-background`; no decorative gradients, no cards nested
      directly in cards.
- [ ] Tokens & scale from [tokens.md](tokens.md); icon-only buttons have
      `aria-label`; long text protected with `min-w-0` + `truncate`.
