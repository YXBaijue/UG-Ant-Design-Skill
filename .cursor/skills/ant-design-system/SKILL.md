---
name: ant-design-system
description: Applies the project's Ant Design design system for B-end admin pages. Default output is app-shell (Layout + left Menu + Header + Content) with PAGE_REGISTRY; read app-shell.md first, then business templates. Use when generating, reviewing, or adjusting Ant Design UI, tables, forms, modals, or page prototypes. Skip app-shell only if user explicitly asks for content-only page without sidebar.
---

# Ant Design System

## Directory Structure

```text
project-root/
├── ant-design-system-prompt.md
├── templates/
│   ├── README.md
│   ├── list-table.md
│   ├── detail-basic.md
│   ├── form-modal.md
│   └── app-shell.md
└── .cursor/
    └── skills/
        └── ant-design-system/
            └── SKILL.md
```

This is a project-level Cursor Skill. From this `SKILL.md`, the design specification is located at `../../../ant-design-system-prompt.md`.

## Usage

Before generating, reviewing, or adjusting any Ant Design based UI, read `../../../ant-design-system-prompt.md`.

### Default generation mode (mandatory)

When generating **B-end admin pages**, **always default to the app-shell template** unless the user explicitly asks for a standalone content-only page (no sidebar menu).

1. **Always read first**: `../../../templates/app-shell.md` — `Layout` + left `Sider` + `Menu` + `Header` + `Content`, with `PAGE_REGISTRY` for menu switching.
2. **Then read** the business page template (`list-table.md`, `detail-basic.md`, etc.).
3. Register new pages in `PAGE_REGISTRY` and wire menu items; menu clicks must switch pages.
4. HTML prototypes: extend `../../../admin-shell.html` or use single-file component registry pattern.
5. **Skip app-shell only if** user explicitly says: 仅内容页 / 不要菜单 / 独立页面 / no sidebar.

For fixed page templates, also read `../../../templates/README.md` and the matching template file:

- **Default — admin app shell**: read `../../../templates/app-shell.md` (Layout + Menu + page registry).
- Query table pages, list pages, table with search: read `../../../templates/list-table.md`.
- Detail pages, basic detail pages, profile pages: read `../../../templates/detail-basic.md`.
- Form modals, add/edit dialogs, basic forms: read `../../../templates/form-modal.md` (must use `Modal` + `Form`).

For every generated page, also follow:

- `../../../rules/components.md`: use real Ant Design components; default to **React + TypeScript + antd**; when the user asks for HTML, load React + antd via CDN and use real components (not div simulation); use Vue only when the user asks.
- `../../../rules/interaction.md`: pages must be interactive and clickable (not static); Tailwind is layout-only and must not replace antd components. Icons: prefer Lucide (`https://unpkg.com/lucide@1.17.0/dist/umd/lucide.js` for HTML CDN), then Ant Design Icons.

Treat `../../../ant-design-system-prompt.md` as the authoritative design specification. Do not rewrite, summarize, relax, or override its rules unless the user explicitly asks to update the specification.

Apply the specification in this priority order:

1. Brand configuration
2. Global Design Tokens
3. B-end page layout guidelines
4. Component-level design parameters
5. **Default app shell** (Layout + Menu) in `../../../templates/app-shell.md` — unless user opts out
6. Component usage rules (React + antd default) in `../../../rules/components.md`
7. Business page templates in `../../../templates/` (list-table, detail-basic, form-modal)
8. Query table page constraints
9. Page generation technical rules (interactive + Tailwind auxiliary) in `../../../rules/interaction.md`

If the referenced specification file is unavailable, ask the user for the current Ant Design design system specification before proceeding.
