+++
title = "Template extensions"
description = "Add integrations or override theme markup with explicit maintenance boundaries."
weight = 2
+++

Zola loads a site template before a theme template with the same path. DevLab's templates use unqualified parent and partial paths so a consumer site can extend a block, replace a partial or replace a complete layout.

## Extend the base template

Create `templates/base.html` in the site and extend the namespaced theme template:

{% raw %}
```html
{% extends "devlab-theme/templates/base.html" %}

{% block head_extra %}
<meta name="application-name" content="Acme Docs">
{% endblock head_extra %}

{% block extra_styles %}
<link rel="stylesheet" href="{{ get_url(path='custom.css') }}">
{% endblock extra_styles %}

{% block body_start %}
<div class="environment-banner">Staging</div>
{% endblock body_start %}

{% block extra_scripts %}
<script src="{{ get_url(path='js/custom.js') }}"></script>
{% endblock extra_scripts %}

{% block body_end %}
<!-- Site-owned body integration. -->
{% endblock body_end %}
```
{% endraw %}

Put referenced files under the site's `static/` directory. `get_url` keeps their URLs correct when the site is deployed below a path prefix.

## Use the extension hooks

| Block | Position | Intended use |
| --- | --- | --- |
| `head_extra` | After DevLab metadata and `main.css` | Additional metadata, verification tags and preload hints |
| `extra_styles` | Last in the page head | Site stylesheets |
| `body_start` | After the skip link, before the site shell | Environment banners and accessibility integrations |
| `extra_scripts` | After DevLab scripts | Site JavaScript that may use rendered theme markup |
| `body_end` | Last before `</body>` | Body-level integrations that must be last |

The structural `title`, `main_class` and `content` blocks are used by DevLab's own layouts. Override them in a specific layout rather than in the site-wide base wrapper.

## Override one partial

Create a file with the same relative path when a shared component needs different markup:

```text
templates/
└── partials/
    ├── header.html
    ├── footer.html
    └── footer-content.html
```

The site versions replace matching theme partials. Replacing `header.html` or `footer.html` is a complete component replacement, not a merge. Preserve keyboard behavior, accessible names and responsive controls. The footer partial is used below regular pages, in the desktop Docs sidebar and in the mobile Docs panel; a full override must preserve its `footer_compact` branch.

Prefer replacing `footer-content.html` when only the footer information or links need custom markup. The theme continues to own the outer separator, centered layout and compact mode. The content partial receives `footer_text`, `footer_year`, `footer_note`, `footer_links`, `footer_links_label` and `footer_compact` in its context. Reuse the `site-footer-note`, `site-footer-copyright`, `site-footer-links`, `site-footer-link` and `site-footer-link-icon` classes when the built-in responsive presentation is useful.

Header and navigation overrides should retain the `data-*` hooks expected by `site-menu.js`, `search.js` and `theme.js`, or replace the corresponding behavior deliberately.

DevLab's CSS classes and JavaScript DOM hooks are implementation details in `v0.7.0`, not a stable extension API. Treat a partial override as code that must be reviewed on theme upgrades.

## Override one layout block

To change only the generic page body, create `templates/page.html`:

{% raw %}
```html
{% extends "devlab-theme/templates/page.html" %}

{% block content %}
<article class="content custom-page">
  <header class="page-header">
    <p class="hero-eyebrow">Acme</p>
    <h1>{{ page.title }}</h1>
  </header>

  {{ page.content | safe }}
</article>
{% endblock content %}
```
{% endraw %}

Use the same pattern with `index.html`, `section.html`, `docs.html`, `doc-page.html`, `blog.html`, `blog-page.html` or `downloads.html`. Extending the namespaced file keeps untouched blocks connected to the installed theme; copying a complete template creates a larger maintenance surface.

Zola documents namespaced inheritance and site-template priority in [Customizing a theme](https://www.getzola.org/documentation/themes/extending-a-theme/).

## Validate an override

Run the native checks after every template change:

```bash
zola check
zola build
```

Then manually verify the affected layout at desktop and mobile widths, in light and dark modes, with keyboard navigation. For header, Docs or search overrides, also test the mobile menu, focus return, active navigation, stored Docs groups and search results.

Before upgrading DevLab, compare site overrides with the corresponding files in the new tag. A build can remain valid even when an override no longer includes newly added accessible markup or runtime hooks.
