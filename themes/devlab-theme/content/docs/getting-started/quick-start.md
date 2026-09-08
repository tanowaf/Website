+++
title = "Quick start"
description = "Create a minimal site with optional docs, blog, search and feed support."
weight = 2
+++

This guide creates a small but complete demo site. It assumes you already have a Zola site and followed [Install and update](@/docs/getting-started/installation.md) to place the theme under `themes/devlab-theme`.

## Configure Zola

Use this as a starting `zola.toml`:

```toml
base_url = "https://example.com"
title = "DevLab"
description = "Developer documentation and technical notes."
author = "Your name"
theme = "devlab-theme"

compile_sass = true
build_search_index = true
generate_feeds = true
feed_filenames = ["atom.xml"]
exclude_paginated_pages_in_sitemap = "all"

[search]
index_format = "elasticlunr_javascript"
include_title = true
include_description = true
include_content = true

[markdown]

[markdown.highlighting]
light_theme = "github-light"
dark_theme = "github-dark"

[extra.devlab.brand]
logo_text = "DevLab"
footer_text = "DevLab"
show_logo_mark = false
logo_mark_path = ""

[extra.devlab.footer]
note = ""
links = []

[extra.devlab.navigation]
links = [
  { name = "Home", path = "/" },
  { name = "Docs", path = "/docs/", kind = "docs" },
  { name = "Blog", path = "/blog/", kind = "blog" },
]

[extra.devlab.docs]
section = "docs/_index.md"
path = "/docs/"
get_started_path = "/docs/getting-started/"

[extra.devlab.blog]
path = "/blog/"

[extra.devlab.appearance]
default_mode = "system"
show_toggle = true

[extra.devlab.i18n]
rtl_languages = []

[extra.devlab.search]
index = ""

[extra.devlab.seo]
og_image = ""
```

Do not need docs or blog yet? Leave the corresponding values under `extra.devlab.docs` or `extra.devlab.blog` empty and remove the matching navigation link.

## Create content folders

Create the folders used by the default navigation:

```bash
mkdir -p content/docs content/blog
```

The minimal structure should look like this:

```text
content/
  _index.md
  docs/
    _index.md
    getting-started.md
  blog/
    _index.md
    hello.md
```

Directories under `content/docs/` become expandable documentation groups when they include `_index.md`.

## Home page

Create `content/_index.md`:

```md
+++
title = "DevLab"
description = "A developer documentation site."
+++

Build technical documentation and blogs with Zola.
```

The homepage uses `templates/index.html` automatically for the root section.

## Docs section

Create `content/docs/_index.md`:

```md
+++
title = "Documentation"
description = "Guides and reference pages."
sort_by = "weight"
template = "docs.html"
page_template = "doc-page.html"
+++

Start here.
```

The `template` setting controls the docs overview page. The `page_template` setting controls every page inside the docs section.

Create `content/docs/getting-started.md`:

````md
+++
title = "Getting started"
description = "Start using the project."
weight = 1
+++

This page verifies that documentation rendering works.

## Requirements

- Zola
- Git

## Local development

Run:

```bash
zola serve
```
````

## Blog section

Create `content/blog/_index.md`:

```md
+++
title = "Blog"
description = "Release notes and technical articles."
sort_by = "date"
paginate_by = 5
template = "blog.html"
page_template = "blog-page.html"
+++
```

Create `content/blog/hello.md`:

```md
+++
title = "Hello DevLab"
description = "First blog post."
date = 2026-06-01
+++

This post verifies that blog rendering works.
```

## Run the site

Start the development server:

```bash
zola serve
```

Check these pages:

- `/`
- `/docs/`
- `/docs/getting-started/`
- `/blog/`
- `/blog/hello/`

If search is enabled, type at least two characters in the search field. If search is disabled, the search box and search scripts are not rendered.

The build also creates `/atom.xml`, `/404.html` and `favicon.svg`. Search engines and feed readers discover the feed and metadata from the page head.
