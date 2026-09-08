+++
title = "Brand and colors"
description = "Configure project identity and override the theme's CSS tokens."
weight = 1
+++

Brand settings cover the header and footer without a template override. A small stylesheet loaded after `main.css` is enough for palettes, fonts and layout widths.

## Configure the identity

Set the project title for metadata and accessible fallbacks, then configure the visible brand:

```toml
title = "Acme Docs"

[extra.devlab.brand]
logo_text = "Acme"
footer_text = "Acme Engineering"
show_logo_mark = true
logo_mark_path = "/brand/acme-mark.svg"
```

Place the image at `static/brand/acme-mark.svg`. Local paths are resolved through Zola and remain correct under a deployment subpath. The mark is rendered at `32 × 32` pixels and treated as decorative; `logo_text`, or `config.title` for a mark-only header, supplies the accessible home-link name.

Leave `logo_mark_path` empty while `show_logo_mark = true` to use DevLab's built-in triangle. Set `show_logo_mark = false` for a text-only header.

## Compose the footer

The copyright label stays geometrically centered even when optional content is present. Add a short note, project links or social icons without replacing the footer template:

```toml
[extra.devlab.brand]
footer_text = "Acme Engineering"

[extra.devlab.footer]
note = "Documentation for the Acme platform."
links_label = "Acme links"
links = [
  { name = "Codeberg", path = "https://codeberg.org/acme/docs", icon = "codeberg" },
  { name = "Contact", path = "mailto:docs@example.org", icon = "email" },
  { name = "Privacy", path = "/privacy/" },
]
```

Supported icons are `github`, `gitlab`, `codeberg`, `matrix`, `zulip`, `telegram`, `discord`, `rss`, `email` and `link`. A link without a supported icon keeps its visible name. Local paths pass through Zola, so they remain valid below a deployment subpath. The full footer uses left, center and right regions; narrow screens and the Docs footer stack the same content into one centered column.

For arbitrary markup, override only `templates/partials/footer-content.html`. The theme keeps the outer footer, separator and compact layout, while the site owns the inner content.

## Load a site stylesheet

Create `static/custom.css`, then create `templates/base.html` in the site:

{% raw %}
```html
{% extends "devlab-theme/templates/base.html" %}

{% block extra_styles %}
<link rel="stylesheet" href="{{ get_url(path='custom.css') }}">
{% endblock extra_styles %}
```
{% endraw %}

The `extra_styles` hook is rendered after the theme's `main.css`, so selectors with the same specificity can override theme values without `!important`.

Do not create `sass/main.scss` in the site: it has the same output path as the theme entrypoint and replaces DevLab's complete stylesheet. If Sass is useful for site code, use a distinct entrypoint such as `sass/site.scss`, which compiles to `site.css`, and load that file through the same hook. A plain `static/custom.css` keeps the workflow smaller.

## Override both color modes

DevLab exposes palette values as CSS custom properties. Define the light fallback, the system-dark palette and the explicit dark palette so the result also works when JavaScript is disabled:

```css
:root,
:root[data-theme="light"] {
  --color-accent: #6d28d9;
  --color-accent-contrast: #ffffff;
  --color-surface: #faf8ff;
  --color-border: #e7e0f3;
}

@media (prefers-color-scheme: dark) {
  :root {
    --color-accent: #c4b5fd;
    --color-accent-contrast: #1e1b4b;
    --color-surface: #171327;
    --color-border: #332b4d;
  }
}

:root[data-theme="dark"] {
  --color-accent: #c4b5fd;
  --color-accent-contrast: #1e1b4b;
  --color-surface: #171327;
  --color-border: #332b4d;
}
```

Test contrast in both explicit modes and in `system` mode. Accent contrast affects filled buttons and should remain readable against `--color-accent`.

## Available CSS tokens

The stable site-level tokens are grouped by purpose:

| Group | Tokens |
| --- | --- |
| Core palette | `--color-bg`, `--color-surface`, `--color-fg`, `--color-muted`, `--color-border`, `--color-accent`, `--color-accent-contrast` |
| Code | `--color-code-bg`, `--color-code-fg`, `--color-pre-bg`, `--color-pre-fg` |
| Callouts | `--color-callout-{info,warning,error,tip}-{bg,border,fg}` |
| Typography | `--font-sans`, `--font-mono` |
| Widths | `--site-width`, `--content-width`, `--header-width`, `--wide-width`, `--wide-gutter`, `--wide-gutter-compact` |

Use ordinary CSS font stacks or load a local webfont from `static/`; DevLab does not fetch external fonts. Keep `--header-height` and `--sticky-offset` unchanged unless the custom header is tested at every responsive breakpoint, because Docs and anchored headings use both values.

Spacing, radii and responsive breakpoints are currently component-level Sass values rather than public tokens. A full visual redesign can override component selectors, but it carries a higher review cost than changing the documented palette, font and width tokens.

## Replace static assets

Zola lets a site replace a theme asset by creating the same relative path under its own `static/` directory. For example, `static/favicon.svg` replaces DevLab's default favicon. Prefer a new path for unrelated assets and reference it from configuration or a template hook.

Do not shadow `main.css` or DevLab's runtime files under `static/js/` unless the site deliberately takes ownership of the corresponding styles or behavior. Static-file replacement is silent and the theme version will no longer be published at that path.

The official Zola guide documents the same [site-over-theme file precedence](https://www.getzola.org/documentation/themes/installing-and-using-themes/#customizing-a-theme).
