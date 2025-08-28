---
title: Theme Components
---

# Theme Components

Several Vue components are provided by the product site themes. They can be used within Markdown via Vue inline syntax.

## Global Components

### `<Badge />`

Source: `storage_redirect/.vuepress/theme/global-components/Badge.vue`

Props:

- `type` (string, default `tip`): visual style. Supported values: `tip` (alias `green`), `warning` (aliases `warn`, `yellow`), `error`.
- `text` (string, optional): displayed text. If not provided, slot content is used.
- `vertical` (string, default `top`): applied to `vertical-align` CSS style.

Usage examples:

```md
<Badge text="Stable" type="tip" />

<Badge type="warning">Deprecated</Badge>

<Badge text="Breaking" type="error" vertical="middle" />
```

Result renders a small colored badge inline with text.

## Layout and Navigation

Each site has a theme under its `.vuepress/theme` directory. Components like `Navbar`, `Sidebar`, and `Home` are standard VuePress theme items and not intended as public API, but can be referenced for customization.

