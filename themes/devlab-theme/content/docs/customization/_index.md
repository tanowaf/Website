+++
title = "Customization"
description = "Adapt DevLab without editing the installed theme."
weight = 3
sort_by = "weight"
template = "docs.html"
page_template = "doc-page.html"
+++

Keep project-specific work in the site's own `zola.toml`, `templates/` and `static/` directories. Zola gives site files priority over matching theme files, so DevLab can be updated or replaced without mixing custom changes into `themes/devlab-theme/`.

## Choose the smallest override

| Need | Preferred layer | Update cost |
| --- | --- | --- |
| Change text, paths or optional features | `zola.toml` and content front matter | Low |
| Change identity, colors, type or widths | Brand settings and a site stylesheet | Low |
| Add metadata, CSS, scripts or integrations | Base template extension hooks | Low |
| Change one shared component | Site-level partial override | Medium |
| Replace a page structure | Site-level layout override | High |

Start with [Brand and colors](@/docs/customization/branding.md). Use [Template extensions](@/docs/customization/templates.md) only when configuration and CSS cannot express the change.

## Keep updates predictable

Do not edit files inside the installed theme. Record every override in the site repository and review it when a release changes the corresponding theme template. Follow the tagged workflow in [Install and update](@/docs/getting-started/installation.md) before validating customizations against a new version.
