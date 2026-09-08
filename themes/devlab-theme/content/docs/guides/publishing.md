+++
title = "Publishing"
description = "Set up metadata, feeds, favicon, 404 and syntax highlighting."
weight = 5
+++

This page covers the pieces that matter once the site is public. DevLab keeps them simple and uses Zola defaults where possible.

## SEO metadata

The theme writes the common tags for search and sharing:

- canonical URL
- page description
- Open Graph title, description, type and URL
- Twitter Card title and description
- theme color for light and dark mode

Titles and descriptions come from the current page or section first. If those are missing, the theme falls back to `config.title` and `config.description`.

Set an image only when you have one:

```toml
[extra.devlab.seo]
og_image = "/images/social-preview.png"
```

If `og_image` is empty, image tags are not rendered.

## Atom feed

DevLab uses Zola's built-in feed generator. Enable it in `zola.toml`:

```toml
author = "Your name"
generate_feeds = true
feed_filenames = ["atom.xml"]
```

The theme adds an autodiscovery link in the page head only when feeds are enabled, using the first configured feed filename. Set `author` so generated entries do not fall back to `Unknown`. Most RSS readers can subscribe to Atom feeds without extra setup. There is no custom feed template in the theme.

Zola 0.23.4 can exclude an individual page while leaving feeds enabled for its section:

```toml
+++
title = "Internal release note"
include_in_feeds = false
+++
```

This is useful for utility pages or updates that should remain published without being delivered to subscribers.

## Favicon

The theme ships a small SVG favicon:

```text
static/favicon.svg
```

It is linked from the page head as an SVG icon. Add PNG or ICO variants only if your project needs them.

## 404 page

The theme includes `templates/404.html`. It uses the same header, footer and theme colors as the rest of the site.

If search is enabled, the page also points visitors back to the header search field.

## Syntax highlighting

Use Zola's dual theme highlighting so code blocks match the color mode:

```toml
[markdown]

[markdown.highlighting]
light_theme = "github-light"
dark_theme = "github-dark"
```

The theme adds the code block frame, language label and Copy button styling. Syntax colors still come from Zola.
