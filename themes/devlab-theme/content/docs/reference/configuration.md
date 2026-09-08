+++
title = "Configuration"
description = "Reference every DevLab setting, default and optional content contract."
weight = 1
updated = 2026-08-31
+++

DevLab Theme reads homepage options from `[extra]` and component settings from structured `[extra.devlab.*]` tables. The fields below are the theme's configuration contract.

## Recommended site settings

```toml
base_url = "https://example.com"
title = "DevLab"
description = "A modern Zola theme for developers, documentation and technical blogs."
author = "Your name"
default_language = "en"
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
```

DevLab uses these settings for the document language, Sass, search, syntax highlighting, Atom feed generation and SEO fallbacks. Keep `base_url` accurate before publishing, because canonical and sharing URLs use it. Set `default_language` to the site's content language and align non-English search index filenames with it.

## Theme settings

```toml
[extra]
home_layout = "default"
home_eyebrow = "Developer-first Zola theme"
home_hero_image = ""
home_hero_image_alt = ""
home_primary_action_label = "Get started"
home_primary_action_path = ""
home_secondary_action_label = "Learn more"
home_secondary_action_path = ""

[extra.devlab.brand]
logo_text = "DevLab"
footer_text = "DevLab"
show_logo_mark = false
logo_mark_path = ""

[extra.devlab.footer]
note = ""
links_label = "Footer links"
links = []

[extra.devlab.navigation]
links = [
  { name = "Home", path = "/" },
  { name = "Docs", path = "/docs/", kind = "docs" },
  { name = "Blog", path = "/blog/", kind = "blog" },
]

[extra.devlab.docs]
section = ""
path = ""
get_started_path = ""

# Optional: omit this complete table to hide page provenance actions.
[extra.devlab.docs.page_actions]
show_last_updated = false
show_copy_link = false
edit_url = ""
report_url = ""
date_format = "%Y-%m-%d"

[extra.devlab.blog]
path = ""
date_format = "%d.%m.%Y"

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

- `devlab.brand` controls the header logo text, optional mark and footer label.
- `devlab.footer` adds an optional note and project links around the centered footer label.
- `devlab.navigation.links` controls desktop and mobile navigation.
- `devlab.docs` defines the optional Docs section, public path, getting-started target and top-level navigation manifest.
- `devlab.docs.page_actions` optionally distributes source provenance and contribution controls across the Docs header, table of contents and article footer.
- `devlab.blog` defines the optional public Blog path and the displayed date format.
- `devlab.appearance` controls the initial color mode and toggle visibility.
- `devlab.i18n.rtl_languages` lists active Zola language codes that should render right-to-left.
- `devlab.search.index` optionally overrides the language-aware search index filename.
- `devlab.seo.og_image` controls the optional Open Graph and Twitter preview image.
- `home_layout` controls the homepage layout. Use `default` for the original constrained layout or `wide` for a full-width homepage shell.
- `home_eyebrow` controls the short label above the homepage title.
- `home_hero_image` and `home_hero_image_alt` add a right-side hero image in `wide` mode.
- `home_primary_action_*` and `home_secondary_action_*` control homepage hero buttons.

The header is text-only by default, so consumer sites do not inherit the DevLab mark. Set `show_logo_mark = true` to add a mark. With an empty `logo_mark_path`, the theme uses its built-in triangle; set the path to a local static asset, an absolute HTTP(S) URL or a protocol-relative URL to use your own image instead.

Set `logo_text = ""` for a mark-only header. The home link keeps `config.title` as its accessible name. If both the mark and text are disabled, the visible text safely falls back to `config.title` instead of leaving an empty link.

Use `kind = "docs"` for the link that should open the mobile docs panel and use `devlab.docs.path`. Use `kind = "blog"` for the optional blog link that should use `devlab.blog.path`. These special entries can omit their own `path`; Docs and Blog links are skipped while the corresponding configured path is empty.

Each navigation entry is a TOML table. `name` is required. A regular entry requires `path`; a special entry uses `kind = "docs"` or `kind = "blog"` and ignores its own path in favor of the configured Docs or Blog path. Other `kind` values have no special behavior.

Navigation `name`, `path` and `kind` values are strings. `devlab.brand.show_logo_mark`, `devlab.appearance.show_toggle` and other switches are booleans. `devlab.appearance.default_mode` accepts `system`, `light` or `dark`; any other value is normalized to `system`.

Set `devlab.navigation.links = []` to omit desktop and mobile navigation entirely. Search and the color mode toggle remain independent. When a Docs link has a configured path but no section tree, it behaves as a regular mobile link instead of opening an empty Docs panel.

Regular navigation links, `devlab.docs.get_started_path`, homepage actions, homepage CTA links, footer links and the `devlab.card` component accept local paths, absolute HTTP(S) URLs, protocol-relative URLs, `mailto:`, `tel:`, page fragments and query-only references. Local content routes are resolved through Zola so deployments under a subpath keep the correct base URL. Download channel and verification links use the same URL forms but remain artifact destinations rather than language-prefixed content routes.

## Multilingual routes

DevLab resolves local header, mobile-menu, homepage, CTA, footer, feed and Docs routes with the active Zola language. External URLs, protocol-relative URLs, email and telephone links, fragments and query-only references are preserved unchanged.

DevLab determines document direction from an explicit language list. List every right-to-left language so the theme can set the document `dir` attribute and mirror its interface:

```toml
[extra.devlab.i18n]
rtl_languages = ["ar", "he"]
```

Codes must match the active Zola language codes: `default_language` and any keys configured under `[languages]`. Unlisted languages use `ltr`.

The default-language Docs configuration remains strict: a configured missing Docs root or curated navigation section stops the build. In a secondary language, an untranslated Docs root hides the Docs destinations, while untranslated curated sectors are skipped. If the translated root exists but no navigation link can be rendered, the global Docs destination remains a direct link instead of opening an empty mobile panel. This supports partial translations without silently weakening validation of the primary site. Regular navigation routes assume their translated targets exist; define suitable language-specific configuration when a destination differs or is unavailable.

Content components do not inherit the page language implicitly. For a language-aware local card link, pass the active language explicitly:

```jinja
{% <devlab.card title="Fast setup" href="@/docs/getting-started/_index.md" content_lang={lang}> %}
Open the translated guide.
{% </devlab.card> %}
```

These routing and direction features do not provide a translation catalog. Configure the supported interface labels for each language and translate authored content with Zola's multilingual content model.

Footer link entries require `name` and `path`. An optional `icon` accepts `github`, `gitlab`, `codeberg`, `matrix`, `zulip`, `telegram`, `discord`, `rss`, `email` or `link`. A supported icon produces a compact icon link with `name` as its accessible label; an omitted or unsupported icon keeps the visible text. `note` is plain text, and `links_label` names the links for assistive technology. Empty values preserve the copyright-only footer.

`devlab.docs.section` is a Zola source path relative to `content/`, for example `docs/_index.md`; it is not a public URL or a filesystem path beginning with `content/`. A non-empty missing section stops the build. Each item in `devlab.docs.navigation` uses the same source-path format and must identify a section `_index.md`.

When `devlab.docs.navigation` contains entries, its array selects and orders the top-level sectors shared by the Docs sidebar, mobile panel and root catalog. Descendants are read automatically from the selected sections. Omitting the setting or using an empty array enables automatic discovery. A section omitted from a non-empty manifest remains published and directly addressable.

`devlab.docs.path` and `devlab.blog.path` do not create routes: they must match content already generated by Zola. Use root-relative prefixes with both slashes, such as `/docs/` and `/blog/`, so active states do not miss or overmatch routes.

## Page provenance actions

Docs pages can expose their authored update date and contribution paths without assuming GitHub, Codeberg, GitLab or another forge. Copy link appears as an icon beside the page title, Edit this page joins the desktop table of contents, and the feedback destination becomes a discussion card after the article. On narrow layouts the table of contents and its edit action are intentionally omitted. Add the optional table only when at least one action is useful:

```toml
[extra.devlab.docs.page_actions]
show_last_updated = true
show_copy_link = true
edit_url = "https://codeberg.org/acme/project/_edit/main/content/"
report_url = "https://codeberg.org/acme/project/issues/new"
date_format = "%Y-%m-%d"
```

`show_last_updated` renders a date only when the page defines Zola's native `updated` field. DevLab never substitutes the build time or a publication date:

```toml
+++
title = "Configuration"
updated = 2026-08-31
+++
```

`edit_url` is a repository-specific prefix. DevLab appends the page's `relative_path`, including a language suffix when the translated source file has one. Keep the trailing slash and include the repository's content directory in the prefix. `report_url` is used unchanged, so it can point to a new-issue page, an issue form or another feedback system. Both URLs should be absolute HTTP(S) destinations.

The Copy link control is progressively enhanced. It stays absent when the browser Clipboard API is unavailable and copies the current address, including an active heading fragment, when supported. A successful copy temporarily replaces the link icon with a check while the localized result is announced to assistive technology. Omit the complete `page_actions` table to render no provenance controls. Empty contribution URLs hide their respective links.

When non-empty, `devlab.search.index` is a local generated JavaScript asset path and must match the filename produced by Zola; an empty value uses the active language automatically. `devlab.seo.og_image` supports a local static path or an absolute HTTP(S) URL.

`home_hero_image` accepts a local static path, an absolute HTTP(S) image URL, a protocol-relative image URL or a Zola `@/` path to an asset colocated with content. Zola 0.23.4 resolves colocated assets through `get_url`, so a page bundle can keep its hero image beside its Markdown source.

The Downloads layout uses section-scoped front matter rather than global `zola.toml` settings. Its complete contract and release example are maintained in the [Downloads guide](@/docs/guides/downloads.md).

## Publishing

The theme includes the basic release-facing pieces: SEO metadata, feed autodiscovery, an SVG favicon and a custom 404 page. You usually only need to configure the site values:

```toml
base_url = "https://example.com"
title = "DevLab"
description = "Developer documentation and technical notes."
author = "Your name"
generate_feeds = true
feed_filenames = ["atom.xml"]

[extra.devlab.seo]
og_image = ""
```

Set `og_image` only when you have a real sharing image. Leave it empty to skip `og:image` and `twitter:image`.

More details are in the [publishing guide](@/docs/guides/publishing.md).

## Optional Docs

DevLab does not require `content/docs/_index.md`. A clean site can build without a docs section if the values under `extra.devlab.docs` stay empty.

To enable docs, create `content/docs/_index.md`:

```toml
+++
title = "Documentation"
description = "Guides and reference pages."
sort_by = "weight"
template = "docs.html"
page_template = "doc-page.html"
+++
```

Add nested directories with `_index.md` when you need documentation sectors and expandable descendant groups. Section landing pages expose one level at a time: direct pages and direct child sections become cards, while deeper entries remain on their own parent landing.

Then configure the site:

```toml
[extra.devlab.docs]
section = "docs/_index.md"
path = "/docs/"
get_started_path = "/docs/getting-started/"
navigation = [
  { section = "docs/getting-started/_index.md" },
  { section = "docs/guides/_index.md" },
  { section = "docs/reference/_index.md" },
]

[extra.devlab.navigation]
links = [
  { name = "Home", path = "/" },
  { name = "Docs", path = "/docs/", kind = "docs" },
]
```

## Optional Blog

DevLab does not require `content/blog/_index.md`. A clean site can build without a blog section if `extra.devlab.blog.path` stays empty.

To enable blog, create `content/blog/_index.md`:

```toml
+++
title = "Blog"
description = "Release notes and articles."
sort_by = "date"
paginate_by = 5
template = "blog.html"
page_template = "blog-page.html"
+++
```

Then configure the site:

```toml
[extra.devlab.blog]
path = "/blog/"
date_format = "%Y-%m-%d"

[extra.devlab.navigation]
links = [
  { name = "Home", path = "/" },
  { name = "Blog", path = "/blog/", kind = "blog" },
]
```

Blog posts use Zola's native `date` and `updated` fields. Add an optional release badge through page extra data:

```toml
+++
title = "Project v1.2.0"
date = 2026-07-01
updated = 2026-07-03

[extra]
release = "v1.2.0"
+++
```

`devlab.blog.date_format` accepts a date-format pattern. Use `%Y-%m-%d` for ISO-style dates or `%m/%d/%Y` for month-first dates. Omit it or leave it empty to keep the default day-first format `%d.%m.%Y`.

The starter configuration shows five posts per page. Omit `paginate_by` when all posts should remain on one page. The template supports both paginated and non-paginated sections.

## Homepage

The homepage always renders the hero from `content/_index.md`. The richer landing sections are opt-in: add `home_features`, `home_learn`, `home_workflow` or `home_cta` only when you want those sections.

```toml
+++
title = "DevLab Theme"
description = "A modern Zola theme for developers, documentation and technical blogs."

[extra]
home_layout = "wide"
home_eyebrow = "Developer-first Zola theme"
home_hero_image = ""
home_hero_image_alt = ""
home_primary_action_label = "Get started"
home_primary_action_path = "/docs/getting-started/"
home_secondary_action_label = "Learn more"
home_secondary_action_path = "#home-learn-more"
home_features = [
  { kicker = "Docs", title = "Structured documentation", description = "Build clean technical documentation with sections, pages and readable navigation." },
  { kicker = "Blog", title = "Technical writing", description = "Publish development notes, release posts, tutorials and engineering articles." },
  { kicker = "Projects", title = "Project showcase", description = "Present open-source work, internal tools and product experiments in one place." },
]

[extra.home_learn]
eyebrow = "Features"
title = "Why DevLab is different"
description = "DevLab keeps the theme small and Zola-native while giving documentation sites the navigation patterns readers expect."
cards = [
  { kicker = "Navigation", title = "One docs structure", description = "Sidebar, mobile menu, overview, breadcrumbs and prev/next links follow the same section tree." },
  { kicker = "Mobile", title = "Single site menu", description = "One mobile drawer handles global navigation and documentation links without adding a second burger menu." },
]

[extra.home_workflow]
eyebrow = "Workflow"
title = "Built for maintainable documentation"
description = "DevLab favors reusable partials, Zola section trees and plain Markdown examples."
points = [
  { title = "Shared partials", description = "Docs navigation is rendered once and reused across desktop and mobile." },
  { title = "Live examples", description = "Feature docs show rendered output and the Markdown syntax behind it." },
]

[extra.home_cta]
title = "Ready to build your docs?"
description = "Start with the getting started guide or jump straight into the documentation overview."
primary_label = "Get started"
primary_path = "/docs/getting-started/"
secondary_label = "View docs"
secondary_path = "/docs/"
+++
```

Use `home_layout = "default"` to keep the original homepage width. Use `home_layout = "wide"` when the homepage should span the same broad canvas as feature grids and richer landing pages.

Homepage values can live globally in `config.extra` or locally in the homepage front matter. The example above is valid in `content/_index.md`; to use the same settings globally, place its tables under `[extra]` in `zola.toml` or `config.toml` without the `+++` front matter delimiters.

Local values in `content/_index.md` win over global values. `home_features` is replaced as one complete array. The `home_learn`, `home_workflow` and `home_cta` tables resolve field by field, so an omitted local field continues to inherit its global value.

`home_layout` accepts `default` or `wide`; any other value renders the default layout. Hero and CTA buttons render only when their path is non-empty. With no explicit global hero path, `home_primary_action_path` falls back to `devlab.docs.get_started_path`; an explicitly defined local homepage value, including an empty string, has final priority.

If the optional homepage sections are omitted, DevLab does not render placeholder feature cards or a default call to action. This keeps clean external sites from inheriting DevLab marketing copy.

An explicitly empty local array has a narrower meaning:

- `home_features = []` hides the complete feature row.
- `home_learn.cards = []` hides the cards but keeps the learn-more heading and description.
- `home_workflow.points = []` hides the numbered points but keeps the workflow heading and description.

Once `home_learn`, `home_workflow` or `home_cta` is configured globally, that section remains enabled for the homepage. Keep a table in the homepage front matter instead of the global config when the entire section should be opt-in for that page.

`home_features` controls the feature row. Each entry supports:

- `kicker`
- `title` (required)
- `description`

`home_learn` controls the learn-more section:

- `eyebrow`
- `title`
- `description`
- `cards`, where each card requires `title` and optionally supports `kicker` and `description`

When the `home_learn` table exists but omits text fields, its defaults are `Features`, `Why DevLab is different` and the built-in theme description.

`home_workflow` controls the split workflow section:

- `eyebrow`
- `title`
- `description`
- `points`, where each point requires `title` and optionally supports `description`

When the `home_workflow` table exists but omits text fields, its defaults are `Workflow`, `Built for maintainable documentation` and the built-in theme description.

`home_cta` controls the final homepage call to action:

- `title`
- `description`
- `primary_label`
- `primary_path`
- `secondary_label`
- `secondary_path`

When the `home_cta` table exists, omitted values fall back to `Ready to build your docs?`, the built-in description, `Get started`, `devlab.docs.get_started_path`, `View docs` and `devlab.docs.path`, respectively. An empty resolved path hides its button.

## Defaults

| Setting                       | Default                       | Purpose                                                                |
| ----------------------------- | ----------------------------- | ---------------------------------------------------------------------- |
| `devlab.brand.logo_text`      | `config.title`                | Header logo text.                                                      |
| `devlab.brand.footer_text`    | `config.title`                | Footer copyright label.                                                |
| `devlab.brand.show_logo_mark` | `false`                       | Whether to render a compact image or the built-in theme mark.          |
| `devlab.brand.logo_mark_path` | Empty                         | Optional local or remote image that replaces the built-in mark.        |
| `devlab.footer.note`           | Empty                         | Optional plain-text footer information.                                |
| `devlab.footer.links`          | Empty array                   | Optional project, social or utility links.                             |
| `devlab.footer.links_label`    | `Footer links`                | Accessible name for the footer link group.                             |
| `devlab.seo.og_image`         | Empty                         | Optional Open Graph and Twitter preview image path or absolute URL.    |
| `devlab.appearance.default_mode` | `system`                   | Initial color mode before a visitor choice is saved.                   |
| `devlab.appearance.show_toggle`  | `true`                     | Whether to render the color mode toggle button.                        |
| `devlab.i18n.rtl_languages`    | Empty array                   | Active Zola language codes rendered right-to-left; unlisted languages use `ltr`. |
| `devlab.docs.section`         | Empty                         | Optional section used by docs sidebar, mobile docs menu and links.     |
| `devlab.docs.path`            | Empty                         | Optional public Docs prefix and fallback target for the homepage CTA secondary action. |
| `devlab.docs.get_started_path` | Empty                        | Optional fallback target for the hero primary action and homepage CTA primary action. |
| `devlab.docs.navigation`      | Not set                       | Optional ordered top-level section manifest for the Docs sidebar, mobile panel and root catalog; omission or an empty array enables automatic discovery. |
| `devlab.docs.page_actions.show_last_updated` | `false`          | Show `page.updated` below a Docs page when the field is present.        |
| `devlab.docs.page_actions.show_copy_link` | `false`               | Add a progressively enhanced control that copies the current page URL. |
| `devlab.docs.page_actions.edit_url` | Empty                       | Repository edit-URL prefix; `page.relative_path` is appended.           |
| `devlab.docs.page_actions.report_url` | Empty                     | Complete issue or feedback URL used without modification.               |
| `devlab.docs.page_actions.date_format` | `%Y-%m-%d`               | Display format for the Docs `updated` value.                             |
| `devlab.blog.path`            | Empty                         | Optional public URL prefix for blog links and active blog states.      |
| `devlab.blog.date_format`     | `%d.%m.%Y`                    | Display format for published and updated dates.                        |
| `home_layout`                 | `default`                     | Homepage layout mode.                                                  |
| `home_eyebrow`                | Developer-first Zola theme    | Homepage hero eyebrow.                                                 |
| `home_hero_image`             | Empty                         | Optional local path or absolute URL for the wide hero image.           |
| `home_hero_image_alt`         | Empty                         | Alt text for `home_hero_image`.                                        |
| `home_primary_action_label`   | Get started                   | Homepage hero primary button label.                                    |
| `home_primary_action_path`    | `devlab.docs.get_started_path`, then empty | Local path, absolute URL or fragment for the primary hero action. |
| `home_secondary_action_label` | Learn more                    | Homepage hero secondary button label.                                  |
| `home_secondary_action_path`  | Empty                         | Local path, absolute URL or fragment for the secondary hero action.    |
| `devlab.search.index`         | Empty                         | Optional generated-index filename override; empty selects `search_index.<lang>.js`. |
| `devlab.navigation.links`     | Home plus hidden Docs/Blog entries | Header and mobile links; special entries appear when their configured paths are non-empty. |

## Interface text

Supported global interface labels live directly under `[extra]`:

```toml
[extra]
docs_sidebar_title = "Documentation"
docs_section_toggle_label = "Toggle section: {title}"
breadcrumb_label = "Breadcrumb"
toc_label = "On this page"
previous_label = "Previous"
next_label = "Next"
search_label = "Search"
search_placeholder = "Search..."
menu_open_label = "Open menu"
menu_label = "Menu"
menu_close_label = "Close menu"
menu_back_label = "Back to main menu"
menu_docs_label = "Open documentation navigation"
```

| Setting | Used for |
| --- | --- |
| `docs_sidebar_title` | Visible desktop Docs sidebar title and its navigation label |
| `docs_section_toggle_label` | Accessible label for subsection disclosure buttons; keep the `{title}` placeholder |
| `breadcrumb_label` | Accessible label for breadcrumb navigation |
| `toc_label` | Visible and accessible title of the Docs table of contents |
| `previous_label`, `next_label` | Docs previous and next controls |
| `search_label`, `search_placeholder` | Search field accessible name and placeholder |
| `menu_open_label` | Mobile menu button accessible name |
| `menu_label`, `menu_close_label`, `menu_back_label`, `menu_docs_label` | Mobile menu title and controls |

`docs_overview_label` is no longer used. Docs navigation now takes landing-link text from each section's `title`; an older consumer configuration can leave the removed field in place without breaking a build, but it has no effect. To expose an item named `Overview`, create a real page or section with that title and position it with `weight`.

Search states, Blog metadata, Docs page actions and Copy feedback use `[extra.labels]`:

```toml
[extra.labels]
search_start = "Start typing to search."
search_no_results = "No results found."
search_unavailable = "Search is currently unavailable."
search_results = "Search results"
search_results_count = "Results found: {count}."
published = "Published"
updated = "Updated"
page_actions = "Page actions"
last_updated = "Last updated"
copy_link = "Copy link"
link_copied = "Link copied"
copy_link_failed = "Failed"
copy_link_success = "Page link copied to clipboard."
copy_link_error = "Unable to copy page link."
edit_page = "Edit this page"
feedback_prompt = "Questions or feedback?"
report_issue = "Report an issue"
copy = "Copy"
copied = "Copied"
copy_failed = "Failed"
copy_success = "Code copied to clipboard."
copy_error = "Unable to copy code."
```

| Setting | Used for |
| --- | --- |
| `search_start` | Empty search prompt |
| `search_no_results` | Visible message when no result matches |
| `search_unavailable` | Visible message when the search index or library is unavailable |
| `search_results` | Accessible results-list label |
| `search_results_count` | Search status announcement; keep the `{count}` placeholder |
| `published`, `updated` | Blog date labels |
| `page_actions` | Accessible name for the article-end Docs provenance area |
| `last_updated` | Docs page update-date label |
| `copy_link`, `link_copied`, `copy_link_failed` | Docs link-copy button states |
| `copy_link_success`, `copy_link_error` | Docs link-copy live-region announcements |
| `edit_page` | Docs source-edit link label |
| `feedback_prompt`, `report_issue` | Docs article-end discussion prompt and action label |
| `copy`, `copied`, `copy_failed` | Code-block button states |
| `copy_success`, `copy_error` | Clipboard live-region announcements |

Every interface label value is a string. These fields are the supported label contract, not a complete translation catalog. Static strings such as the skip link, theme-mode names and generic pagination labels remain theme-owned in the current release.

## Search

Search is optional. DevLab's browser adapter requires Zola's `elasticlunr_javascript` index format. Set it explicitly in new sites so a future Zola default or copied configuration cannot silently change the runtime contract:

```toml
build_search_index = true

[search]
index_format = "elasticlunr_javascript"
include_title = true
include_description = true
include_content = true
```

If `build_search_index` is `false`, the search field and search scripts are not rendered. They are also omitted when `search.index_format` is explicitly set to any other format, because the bundled adapter loads `elasticlunr.min.js` and expects the JavaScript index to define `window.searchIndex`. When `index_format` is absent, DevLab preserves compatibility with sites where the active Zola default already produces `elasticlunr_javascript`.

When search is enabled, DevLab selects `search_index.<lang>.js` from the active Zola language automatically:

```toml
build_search_index = true

[search]
index_format = "elasticlunr_javascript"

[extra.devlab.search]
index = ""
```

Set `devlab.search.index` only when the generated JavaScript file has a custom name. It changes the asset path, not the required index format.

If search is missing, confirm that `build_search_index = true` and that `search.index_format` is either `elasticlunr_javascript` or omitted while Zola still uses that default. If the control renders but reports that search is unavailable, check that the generated `elasticlunr.min.js` and `search_index.<lang>.js` files exist at the configured paths.

## Customization

Configuration is the preferred customization layer. When a site needs palette changes, additional assets or markup overrides, keep them outside `themes/devlab-theme/` and follow the [Customization overview](@/docs/customization/_index.md).

- [Brand and colors](@/docs/customization/branding.md) documents logo assets, CSS loading and supported tokens.
- [Template extensions](@/docs/customization/templates.md) documents base hooks, partial precedence and layout overrides.
