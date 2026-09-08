+++
title = "Docs navigation"
description = "Select top-level documentation sectors and derive their descendants from the Zola content tree."
weight = 1
+++

DevLab connects documentation to the section configured by `extra.devlab.docs.section`. An optional `extra.devlab.docs.navigation` manifest selects the top-level sectors shared by the desktop sidebar, mobile Docs panel and root Docs catalog. Descendants inside each selected sector continue to come from the Zola content tree.

Breadcrumbs keep following the real content hierarchy. Previous and next links use the curated manifest's scope and top-level order when it is present, or the complete Docs tree when it is omitted. This Demo uses `content/docs/`, but a consumer site can choose another source directory and public path.

## Build the content tree

Use pages for regular links and directories with `_index.md` for expandable groups:

```txt
content/docs/
├── _index.md
├── getting-started/
│   ├── _index.md
│   ├── installation.md
│   └── quick-start.md
├── guides/
│   ├── _index.md
│   └── navigation.md
├── customization/
│   ├── _index.md
│   └── branding.md
└── reference/
    ├── _index.md
    └── configuration.md
```

Each subsection title is a link to that section's authored landing page. When the subsection has children, a separate chevron button expands them in the sidebar and mobile Docs menu. Child subsections are nested automatically. This Demo keeps installation and the complete first-site example together below the `Getting started` landing link.

DevLab does not generate synthetic `Overview` items. The global Docs link opens the root landing page, and every subsection link opens its own landing page. If an information architecture needs a visible item named `Overview`, add it as a normal page or section with that title.

## Configure the Docs root

Connect the source section, public URL prefix and navigation entry in `zola.toml`:

```toml
[extra.devlab.docs]
section = "docs/_index.md"
path = "/docs/"
get_started_path = "/docs/getting-started/"
navigation = [
  { section = "docs/getting-started/_index.md" },
  { section = "docs/guides/_index.md" },
  { section = "docs/customization/_index.md" },
  { section = "docs/reference/_index.md" },
]

[extra.devlab.navigation]
links = [
  { name = "Docs", kind = "docs" },
]
```

Merge the Docs entry into the site's complete `links` array. The settings have different roles:

- `section` is the source path Zola passes to `get_section`;
- `path` is the public URL prefix used for links and active states;
- `navigation` selects and orders the top-level documentation sectors;
- `kind = "docs"` opens the documentation panel in the mobile menu.

`get_started_path` is optional and supplies the default target for the homepage primary action.

## Select the top-level sectors

Each `navigation` entry requires one `section` source path relative to `content/`. The path must identify a section `_index.md`, not its public URL:

```toml
[extra.devlab.docs]
navigation = [
  { section = "docs/users/_index.md" },
  { section = "docs/packaging/_index.md" },
  { section = "docs/developers/_index.md" },
]
```

The manifest order is the rendered top-level order and the top-level order used by previous and next links. DevLab takes each entry's title, description and link from the target section. Its pages and child sections are discovered automatically, so the manifest does not repeat the complete tree.

Keep manifest entries unique and non-overlapping. Previous and next links defensively de-duplicate page paths in first-occurrence order, but repeated or nested entries would still repeat top-level sectors in the sidebar and root catalog.

If `navigation` is omitted or empty, DevLab discovers top-level navigation and previous or next targets automatically from the configured Docs root. Omitting one section from a non-empty manifest hides that generated top-level entry and excludes its pages from previous and next links; it does not unpublish the section or its pages.

`extra.devlab.docs.navigation` is separate from `extra.devlab.navigation.links`: the first structures documentation, while the second adds global header and mobile-menu destinations.

## Control nesting and order

Set `transparent = true` in a subsection when its pages should belong to the parent navigation level. DevLab follows Zola's transparent-section behavior: the subsection wrapper is hidden, its pages appear once under the effective parent, and nested non-transparent sections remain visible as groups.

The explicit manifest controls only the top-level sector order. Set `sort_by = "weight"` in each sector's `_index.md` and add `weight` to its pages when you need a custom descendant order. Give sibling subsections unique `weight` values as well, because Zola uses section weight to order them.

The root Docs catalog renders one card for each selected top-level sector, without copying its descendants onto the root page. A sector landing renders its direct pages and direct child sections as cards; a child section exposes its own direct entries on its landing page. This one-level-at-a-time structure keeps deep pages out of higher-level catalogs.

The card grid chooses the number of columns from the available content width and returns to one column on small screens; source order remains the keyboard and screen-reader order. On the root Docs page, the authored introduction is rendered before the catalog so readers get context before choosing a sector.

The generated HTML keeps every child link visible and hides disclosure buttons, so the complete navigation remains usable without JavaScript. When JavaScript loads, inactive branches collapse and the buttons become available. The branch containing the current page remains open, so the active link stays visible.

Opening or closing a branch saves that choice under `devlab-docs-nav-state-v1` in the browser's `localStorage`, and the same state is reused by the desktop sidebar and mobile Docs menu. Saved open groups remain open across page changes. The active branch always reopens on a direct page load, even when an earlier saved state closed it. Clearing site data resets non-active groups to closed after JavaScript initializes.

## Follow the same tree

Breadcrumbs always use the real section hierarchy, for example `Documentation / Guides / Docs navigation`, independently of the curated manifest.

With a non-empty curated manifest, previous and next links include only pages below the listed sections. They follow manifest order between top-level sectors and depth-first content-tree order inside each sector: direct pages first, then subsection pages. Without a manifest, they use the complete configured Docs root in the same depth-first order.
They are rendered by Zola, so this essential navigation remains available when JavaScript is disabled.

The `On this page` panel groups third-level headings below their second-level parent with native disclosure controls. Groups start closed in the generated HTML. As the reader moves through the page, scroll tracking closes inactive groups and opens the branch that contains the active heading; the parent title remains a regular anchor link.

On Docs pages, the compact site footer sits at the bottom of the desktop sidebar and the mobile Docs panel. Other layouts keep the footer below the main page content.
