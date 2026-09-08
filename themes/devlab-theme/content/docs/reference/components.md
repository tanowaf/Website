+++
title = "Tera components"
description = "Reusable, namespaced Tera 2 components for Markdown content and templates."
weight = 2
+++

Zola 0.23.4 renders page and section bodies as Tera 2 templates before Markdown. DevLab exposes its content building blocks as global `devlab.*` components: they need no import and cannot collide with generic names from your site.

## Component syntax

Self-closing components render inline:

{% raw %}
```jinja
{{<devlab.icon name="github" />}}
```
{% endraw %}

Components that accept Markdown use an opening and closing tag:

{% raw %}
```jinja
{% <devlab.callout type="info" title="Good to know"> %}
The body supports **Markdown**.
{% </devlab.callout> %}
```
{% endraw %}

Arguments written as strings use quotes. Pass variables and non-string literals as Tera expressions in braces, for example `item={page}` or `enabled={true}`.

## Code blocks

Markdown fenced code blocks are styled automatically. Their toolbar identifies the language and includes a Copy button when the Clipboard API is available. The button shows a visible success or failure state while a polite live region announces the same result to assistive technology.

````md
```toml
theme = "devlab-theme"
compile_sass = true
```
````

```toml
theme = "devlab-theme"
compile_sass = true
```

Add `linenos` for line numbers and `hl_lines` for individual lines or ranges:

````md
```rust,linenos,hl_lines=2
fn main() {
    println!("this line is highlighted");
    println!("this one is not");
}
```
````

```rust,linenos,hl_lines=2
fn main() {
    println!("this line is highlighted");
    println!("this one is not");
}
```

These annotations are native Zola highlighting features. DevLab styles them and strips line numbers when copying.

## Tabs

Tabs group related Markdown or code examples while keeping every panel readable when JavaScript is unavailable. These two groups share `sync="local-shell"`: selecting a platform in either group updates the other and remembers the choice across pages.

{% <devlab.tabs sync="local-shell" label="Development server command"> %}
{% <devlab.tab name="Unix" value="unix" selected={true}> %}
```bash
zola serve
```
{% </devlab.tab> %}
{% <devlab.tab name="Windows" value="windows"> %}
```powershell
zola.exe serve
```
{% </devlab.tab> %}
{% </devlab.tabs> %}

{% <devlab.tabs sync="local-shell" label="Site check command"> %}
{% <devlab.tab name="Unix" value="unix" selected={true}> %}
```bash
zola check
```
{% </devlab.tab> %}
{% <devlab.tab name="Windows" value="windows"> %}
```powershell
zola.exe check
```
{% </devlab.tab> %}
{% </devlab.tabs> %}

{% raw %}
````jinja
{% <devlab.tabs sync="local-shell" label="Development server command"> %}
{% <devlab.tab name="Unix" value="unix" selected={true}> %}
```bash
zola serve
```
{% </devlab.tab> %}
{% <devlab.tab name="Windows" value="windows"> %}
```powershell
zola.exe serve
```
{% </devlab.tab> %}
{% </devlab.tabs> %}
````
{% endraw %}

`devlab.tabs` parameters:

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `sync` | No | Empty | Shared persistence key. Groups with the same key synchronize matching tab values. |
| `label` | No | `Tabs` | Accessible name for the generated tab list. |

`devlab.tab` parameters:

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `name` | Yes | - | Visible tab label. |
| `value` | No | `name` | Stable value used for synchronization and persistence. Values must be unique inside a group. |
| `selected` | No | `false` | Initial tab when no saved selection matches. The first tab is the final fallback. |

Use at least two `devlab.tab` children directly inside `devlab.tabs`. An empty `sync` value keeps the group independent and does not write a preference. A non-empty key stores only the selected `value` in `localStorage`. Explicit values are recommended when labels are translated or may change.

The enhanced control follows the horizontal tabs keyboard pattern: Left and Right move and select, while Home and End jump to the first or last tab. Arrow direction mirrors in RTL layouts.

## Diagrams

Diagrams turn a compact Mermaid-compatible source into a responsive SVG. The renderer is bundled with the theme, loaded only on pages that use `devlab.diagram`, and follows DevLab's live color tokens without a remote font or CDN request.

{% <devlab.diagram id="distribution-architecture" label="Distribution architecture" description="Arch packages and the Lycoris repository converge into one system with two desktop editions."> %}
flowchart TD
  upstream["Arch Linux repositories"]
  lycoris["Lycoris repository"]
  packages["Arch packages"]
  system["Lycoris system"]
  plasma["Plasma edition"]
  lxqt["LXQt edition"]

  upstream --> lycoris
  upstream --> packages
  lycoris --> system
  packages --> system
  system --> plasma
  system --> lxqt
{% </devlab.diagram> %}

{% raw %}
```jinja
{% <devlab.diagram id="distribution-architecture" label="Distribution architecture" description="How repositories become desktop editions."> %}
flowchart TD
  upstream["Arch Linux repositories"]
  lycoris["Lycoris repository"]
  packages["Arch packages"]
  system["Lycoris system"]
  plasma["Plasma edition"]
  lxqt["LXQt edition"]

  upstream --> lycoris
  upstream --> packages
  lycoris --> system
  packages --> system
  system --> plasma
  system --> lxqt
{% </devlab.diagram> %}
```
{% endraw %}

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `id` | Yes | - | Unique, HTML-safe identifier used by the accessible caption. |
| `label` | Yes | - | Visible diagram title and accessible name. |
| `description` | No | Empty | Visible explanation associated with the generated graphic. |
| `error_message` | No | `Diagram could not be rendered.` | Fallback message for disabled JavaScript or a rendering error. |

The current renderer supports flowcharts, state, sequence, class, ER and XY diagrams. Treat diagram source like the rest of the site's authored content: it is rendered from files in the repository, not from visitor input. Keep the description meaningful because it remains available when the visual cannot be rendered.

The self-contained renderer is about 1.6 MB minified (495 KB gzip). This cost is isolated: neither the component loader nor the renderer is requested on pages without a diagram.

## File tree

File trees explain a project layout without depending on hand-drawn connector characters. Folders use the browser's native disclosure control, so they remain collapsible without JavaScript.

{% <devlab.file_tree label="Theme structure"> %}
{% <devlab.folder name="devlab-theme" open={true}> %}
{{<devlab.file name="theme.toml" note="Theme metadata" />}}
{% <devlab.folder name="templates" open={true}> %}
{{<devlab.file name="base.html" />}}
{{<devlab.file name="components.html" note="Public components" />}}
{% </devlab.folder> %}
{% <devlab.folder name="sass" open={false}> %}
{{<devlab.file name="main.scss" />}}
{% </devlab.folder> %}
{{<devlab.file name="zola.toml" note="Demo configuration" />}}
{% </devlab.folder> %}
{% </devlab.file_tree> %}

{% raw %}
```jinja
{% <devlab.file_tree label="Theme structure"> %}
{% <devlab.folder name="devlab-theme" open={true}> %}
{{<devlab.file name="theme.toml" note="Theme metadata" />}}
{% <devlab.folder name="templates" open={true}> %}
{{<devlab.file name="base.html" />}}
{{<devlab.file name="components.html" note="Public components" />}}
{% </devlab.folder> %}
{% <devlab.folder name="sass" open={false}> %}
{{<devlab.file name="main.scss" />}}
{% </devlab.folder> %}
{{<devlab.file name="zola.toml" note="Demo configuration" />}}
{% </devlab.folder> %}
{% </devlab.file_tree> %}
```
{% endraw %}

Component parameters:

| Component | Parameter | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `devlab.file_tree` | `label` | No | `File tree` | Visible caption for the complete structure. |
| `devlab.folder` | `name` | Yes | - | Folder name. |
| `devlab.folder` | `open` | No | `true` | Initial state of the native disclosure control. |
| `devlab.file` | `name` | Yes | - | File name. |
| `devlab.file` | `note` | No | Empty | Short annotation aligned with the file. |

Place `devlab.folder` and `devlab.file` directly inside a tree or another folder. Folder and file names use `dir="auto"`, so localized names remain readable inside both LTR and RTL pages.

## Badges

Badges add compact status text without turning the surrounding sentence into a callout.

Release {{<devlab.badge text="Stable" tone="success" />}}
API {{<devlab.badge text="Beta" tone="info" />}}
Migration {{<devlab.badge text="Required" tone="warning" />}}
Version {{<devlab.badge text="Unsupported" tone="danger" />}}
Label {{<devlab.badge text="Metadata" />}}

{% raw %}
```jinja
Release {{<devlab.badge text="Stable" tone="success" />}}
```
{% endraw %}

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `text` | Yes | - | Visible badge text. |
| `tone` | No | `neutral` | `neutral`, `info`, `success`, `warning`, or `danger`; invalid values fall back to `neutral`. |

Tone communicates supporting status, but the visible text must carry the meaning on its own. Do not rely on color to distinguish states.

## Cards

Cards can link to another resource or present standalone information.

{% <devlab.card title="Fast setup" href="@/docs/getting-started/_index.md" content_lang={lang}> %}
Start building your documentation site with Zola.
{% </devlab.card> %}

{% raw %}
```jinja
{% <devlab.card title="Fast setup" href="@/docs/getting-started/_index.md" content_lang={lang}> %}
Start building your documentation site with Zola.
{% </devlab.card> %}
```
{% endraw %}

Omit `href` for a static card:

{% <devlab.card title="Project status"> %}
DevLab Theme is under active development.
{% </devlab.card> %}

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `title` | No | Empty | Card heading. |
| `href` | No | Empty | Local path, `@/` content path, fragment, query, or external URL. |
| `content_lang` | No | Empty | Active Zola language for a language-aware local link; pass `{lang}` on multilingual content. |

## Callouts

Callouts support `info`, `warning`, `error`, and `tip` variants.

{% <devlab.callout type="info"> %}
**Note:** DevLab requires Zola 0.23.4 and tracks the current Zola release.
{% </devlab.callout> %}

{% <devlab.callout type="warning"> %}
Restart `zola serve` after changing settings that affect the whole site.
{% </devlab.callout> %}

{% <devlab.callout type="error"> %}
Do not publish a site that has not passed `zola check`.
{% </devlab.callout> %}

{% <devlab.callout type="tip" title="Fast feedback"> %}
Use `zola serve` while writing and keep the production build in CI.
{% </devlab.callout> %}

{% raw %}
```jinja
{% <devlab.callout type="tip" title="Fast feedback"> %}
Use `zola serve` while writing.
{% </devlab.callout> %}
```
{% endraw %}

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `type` | No | `info` | Visual variant and fallback title. |
| `title` | No | Empty | Custom title; replaces the type label. |

## Details

Details hide supplementary Markdown behind the browser's native, no-JavaScript disclosure widget.

{% <devlab.details summary="What Zola version do I need?"> %}
DevLab Theme requires Zola `0.23.4`; check the installed version with `zola --version`.
{% </devlab.details> %}

{% raw %}
```jinja
{% <devlab.details summary="What Zola version do I need?"> %}
DevLab Theme requires Zola `0.23.4`.
{% </devlab.details> %}
```
{% endraw %}

The required `summary` argument is the always-visible label.

## Steps

Steps turn a Markdown ordered list into a connected walkthrough.

{% <devlab.steps> %}
1. Install Zola 0.23.4.
2. Add `devlab-theme` under `themes/devlab-theme`.
3. Run `zola serve` and open the printed address.
{% </devlab.steps> %}

{% raw %}
```jinja
{% <devlab.steps> %}
1. Install Zola.
2. Add the theme.
3. Run `zola serve`.
{% </devlab.steps> %}
```
{% endraw %}

`devlab.steps` takes no arguments. Each top-level list item becomes one step; nested Markdown remains available inside it.

## Icons

DevLab ships a small SVG library and exposes it through `devlab.icon`.

GitHub {{<devlab.icon name="github" />}} Codeberg {{<devlab.icon name="codeberg" />}} Matrix {{<devlab.icon name="matrix" />}}

{% raw %}
```jinja
GitHub {{<devlab.icon name="github" />}}
```
{% endraw %}

Icons are decorative by default. Add `label` when an icon communicates meaning without adjacent text.

{{<devlab.icon name="check" size="sm" label="Small icon" />}}
{{<devlab.icon name="check" size="inline" label="Inline icon" />}}
{{<devlab.icon name="check" size="md" label="Medium icon" />}}
{{<devlab.icon name="check" size="lg" label="Large icon" />}}

| Parameter | Required | Default | Description |
| --- | --- | --- | --- |
| `name` | Yes | - | Icon name without `.html`. |
| `size` | No | `inline` | `sm`, `inline`, `md`, or `lg`; invalid values fall back to `inline`. |
| `label` | No | Empty | Accessible label for a meaningful standalone icon. |
| `extra_class` | No | Empty | Additional class on the wrapper. |

Available names:

- Theme: `sun`, `moon`
- Social and brand: `github`, `gitlab`, `codeberg`, `matrix`, `zulip`, `telegram`, `discord`, `rss`, `email`, `link`
- Interface: `search`, `chevron-right`, `external-link`, `copy`, `check`, `info`, `warning`, `menu`, `x`, `arrow-left`, `arrow-right`, `folder`, `file`

### Use an icon from a template

The lower-level SVG component is also global:

{% raw %}
```html
<a href="https://github.com/you" aria-label="GitHub">
  {{<devlab.svg_icon name="github" />}}
</a>
```
{% endraw %}

To extend the set, add an SVG fragment under `templates/components/icons/` and a matching branch in `templates/components.html`. Keep filenames lowercase and add an accessible label at the wrapper level when the icon is not decorative.

## Literal Tera in Markdown

Zola 0.23.4 templates all Markdown content, including fenced code blocks. Wrap examples that must be displayed rather than executed in a Tera raw block:

<pre><code>&#123;% raw %&#125;
```jinja
&#123;&#123;&lt;project.example value="literal" /&gt;&#125;&#125;
```
&#123;% endraw %&#125;</code></pre>

For files that should never execute Tera, add their paths to `skip_content_templating` in `zola.toml`. Do not disable templating on pages that use DevLab components.
