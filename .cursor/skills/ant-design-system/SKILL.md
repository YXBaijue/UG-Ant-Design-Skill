---
name: ant-design-system
description: Applies the project's Ant Design design system specification for AI-generated Ant Design frontend prototypes and B-end admin pages. Use when generating, reviewing, or adjusting Ant Design UI layouts, Design Tokens, component styling, spacing, colors, typography, tables, forms, modals, and page prototypes.
---

# Ant Design System

## Directory Structure

```text
project-root/
├── ant-design-system-prompt.md
└── .cursor/
    └── skills/
        └── ant-design-system/
            └── SKILL.md
```

This is a project-level Cursor Skill. From this `SKILL.md`, the design specification is located at `../../../ant-design-system-prompt.md`.

## Usage

Before generating, reviewing, or adjusting any Ant Design based UI, read `../../../ant-design-system-prompt.md`.

Treat `../../../ant-design-system-prompt.md` as the authoritative design specification. Do not rewrite, summarize, relax, or override its rules unless the user explicitly asks to update the specification.

Apply the specification in this priority order:

1. Brand configuration
2. Global Design Tokens
3. B-end page layout guidelines
4. Component-level design parameters

If the referenced specification file is unavailable, ask the user for the current Ant Design design system specification before proceeding.
