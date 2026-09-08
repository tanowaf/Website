+++
title = "Troubleshooting"
description = "Fix common setup issues in a DevLab Theme site."
weight = 3
+++

Use this page when the theme builds but a specific area is missing or empty.

## Docs sidebar is empty

Check that the docs section exists at the path configured by `extra.devlab.docs.section`:

```toml
[extra.devlab.docs]
section = "docs/_index.md"
```

The file should exist as `content/docs/_index.md`.

## Docs pages use the wrong layout

Set the docs section templates in `content/docs/_index.md`:

```toml
+++
template = "docs.html"
page_template = "doc-page.html"
+++
```

Without `page_template = "doc-page.html"`, pages inside the docs section will not use the documentation layout.

## A docs subsection is missing

Every docs directory that should appear as a navigation group needs an `_index.md` file:

```txt
content/docs/Linux/_index.md
content/docs/Linux/opensuse.md
```

Set the same docs templates on nested sections when they should render like docs pages:

```toml
+++
title = "Linux"
sort_by = "weight"
template = "docs.html"
page_template = "doc-page.html"
+++
```

If the subsection appears in the wrong place, check `sort_by` in the parent section and `weight` on pages or child sections.

## Search does not appear

Search is rendered only when Zola search index generation is enabled with the JavaScript Elasticlunr format expected by DevLab:

```toml
build_search_index = true

[search]
index_format = "elasticlunr_javascript"
include_title = true
include_description = true
include_content = true
```

An explicitly configured different `search.index_format` hides the search control and skips its scripts. Omitting the field remains compatible only while Zola's active default produces `elasticlunr_javascript`.

By default, DevLab loads `search_index.<lang>.js` for the active content language. Override it only when your generated filename is different:

```toml
[extra.devlab.search]
index = "custom-search-index.js"
```

## Atom feed is missing

Enable Zola's feed generator:

```toml
generate_feeds = true
feed_filenames = ["atom.xml"]
```

Then run `zola build` and check that `public/atom.xml` exists.

## Sharing image does not appear

`og:image` and `twitter:image` are rendered only when `extra.devlab.seo.og_image` is not empty:

```toml
[extra.devlab.seo]
og_image = "/images/social-preview.png"
```

Use an absolute URL or a path that exists in `static/`. Leave the value empty if you do not have a real preview image yet.

## Code highlighting uses the wrong colors

Use the current Zola highlighting format:

```toml
[markdown]

[markdown.highlighting]
light_theme = "github-light"
dark_theme = "github-dark"
```

Older single-theme highlighting config will not match DevLab's light and dark modes as cleanly.

## Mobile menu does not show docs links

The mobile menu uses the docs navigation partial. Confirm that the docs link in `extra.devlab.navigation.links` has `kind = "docs"`:

```toml
[extra.devlab.navigation]
links = [
  { name = "Docs", path = "/docs/", kind = "docs" },
]
```

That marker tells the mobile menu which item should open the documentation panel.

## Blog pages use the wrong layout

Set the blog section templates in `content/blog/_index.md`:

```toml
+++
template = "blog.html"
page_template = "blog-page.html"
+++
```

Blog posts should also include a date:

```toml
+++
title = "Hello DevLab"
date = 2026-06-01
+++
```
