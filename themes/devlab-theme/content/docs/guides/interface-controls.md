+++
title = "Interface controls"
description = "Configure search, keyboard shortcuts and color mode behavior."
weight = 2
+++

DevLab keeps the interface small, but a few controls are worth knowing before you publish: search shortcuts, keyboard navigation, search messages and color mode behavior.

## Search index contract

DevLab's search UI uses Zola's JavaScript Elasticlunr index. New sites should make that contract explicit:

```toml
build_search_index = true

[search]
index_format = "elasticlunr_javascript"
include_title = true
include_description = true
include_content = true
```

The theme does not render the search control or load search scripts when `search.index_format` is explicitly set to an incompatible format. If the field is absent, DevLab remains compatible while the active Zola default produces `elasticlunr_javascript`.

The optional `extra.devlab.search.index` setting changes only the generated JavaScript filename. It does not convert another index format for the bundled adapter.

## Search shortcuts

When search is enabled, the header search field supports a keyboard shortcut:

| Platform      | Shortcut |
| ------------- | -------- |
| Windows/Linux | `Ctrl+K` |
| macOS         | `Cmd+K`  |

The static HTML fallback shows `Ctrl+K`. When JavaScript loads, the shortcut hint is updated on Apple platforms by checking `navigator.userAgentData.platform` first and falling back to `navigator.platform`.

The shortcut hint is rendered next to the search input and hidden on small screens so the mobile header layout stays compact.

## Search states

Search shows three non-result states:

| State              | Fallback text                    |
| ------------------ | -------------------------------- |
| Empty input        | Start typing to search.          |
| No results         | No results found.                |
| Search unavailable | Search is currently unavailable. |

The messages are prepared for localization through `extra.labels`:

```toml
[extra.labels]
search_start = "Start typing to search."
search_no_results = "No results found."
search_unavailable = "Search is currently unavailable."
```

The empty-input message appears when the field receives focus with no query. The no-results message appears only after a search query returns no matches. The unavailable message appears when the generated index or search library cannot be loaded or queried.

## Search keyboard navigation

When the search input is focused and result links are visible, the keyboard can move through results without changing the search algorithm or index format:

| Key         | Behavior                                      |
| ----------- | --------------------------------------------- |
| `ArrowDown` | Select the next result.                       |
| `ArrowUp`   | Select the previous result.                   |
| `Enter`     | Open the selected result.                     |
| `Escape`    | Close results; press again to clear and leave search. |

Mouse clicks still use normal anchor behavior.

## Color modes

The color mode model has three modes:

| Mode     | Icon    | Behavior                                                |
| -------- | ------- | ------------------------------------------------------- |
| `system` | Desktop | Follows `prefers-color-scheme`.                         |
| `light`  | Sun     | Forces the light palette.                               |
| `dark`   | Moon    | Forces the dark palette.                                |

The selected mode is stored as `data-theme-mode`. The resolved palette is stored as `data-theme`, so existing light and dark CSS variables continue to work.

The toggle cycles in this order:

```text
system -> light -> dark -> system
```

The button label and title describe the current mode:

- `System theme`
- `Light theme`
- `Dark theme`

## Theme settings

The default color mode is configured with `devlab.appearance.default_mode`:

```toml
[extra.devlab.appearance]
default_mode = "system"
```

Valid values are `system`, `light` and `dark`. If a visitor has already chosen a mode, the saved localStorage value takes priority over `default_mode`.

The toggle button can be disabled without changing theme resolution:

```toml
[extra.devlab.appearance]
show_toggle = false
```

When disabled, the theme toggle partial does not render the button, but the configured default mode and saved visitor preference still apply.

## Reduced motion

DevLab respects `prefers-reduced-motion: reduce`. Navigation controls stop animating and the mobile drawer opens without a sliding transition, while positioning transforms remain intact.
