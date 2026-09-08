+++
title = "Install and update"
description = "Pin a tagged DevLab release and upgrade it without following development changes."
weight = 1
+++

Install DevLab under the Zola site's `themes/devlab-theme/` directory and pin a release tag. Tagged installations are reproducible; the `main` branch is development work and can change between releases.

DevLab follows the current Zola release instead of maintaining a compatibility range. This iteration requires Zola `0.23.4`; confirm the local tool before installing the theme:

```bash
zola --version
```

Run the commands on this page from the root of the Zola site. Create the theme directory before choosing an installation method:

```bash
mkdir -p themes
```

## Choose an installation method

| Method | Use it when | How the site records the theme |
| --- | --- | --- |
| Git submodule | The site is stored in Git and the theme should remain a separate repository. | The parent repository records one exact theme commit. |
| Tagged clone | Deployment fetches the theme separately or the site repository does not track `themes/`. | The nested repository records the checked-out tag locally. |
| Vendored copy | The complete theme should live directly in the site repository. | Theme files are committed with the site. |

Use one method for a site. Do not place a tagged clone inside a tracked repository and commit it as an embedded Git repository; use a submodule or a vendored copy instead.

## Install as a Git submodule

From the root of the Zola site, add the repository and check out the current stable tag:

```bash
git submodule add https://codeberg.org/RiPetitor/devlab-theme themes/devlab-theme
git -C themes/devlab-theme fetch --depth 1 origin tag v0.7.0
git -C themes/devlab-theme checkout --detach v0.7.0
```

Commit both `.gitmodules` and the `themes/devlab-theme` entry in the site repository. The parent repository pins the exact theme commit selected by the tag.

After cloning the site elsewhere, initialize the pinned theme with:

```bash
git submodule update --init --recursive
```

Alternatively, clone the site and its submodules in one command with `git clone --recurse-submodules`.

## Install as a tagged clone

Use a standalone clone when the surrounding site does not track the theme directory:

```bash
git clone --branch v0.7.0 --depth 1 https://codeberg.org/RiPetitor/devlab-theme themes/devlab-theme
```

The clone starts at the release tag in detached HEAD state. That is expected for a pinned dependency.

## Install as a vendored copy

Download the source archive for `v0.7.0` from the repository's [Tags](https://codeberg.org/RiPetitor/devlab-theme/tags) page and extract it into `themes/devlab-theme/`.

Check the final directory shape before building:

```text
themes/
└── devlab-theme/
    ├── theme.toml
    ├── templates/
    ├── sass/
    └── static/
```

Avoid an additional archive directory between `devlab-theme/` and `theme.toml`. Commit the extracted theme files with the site.

## Enable the theme

Set the theme name and Sass compilation in the site's `zola.toml`:

```toml
theme = "devlab-theme"
compile_sass = true
```

Then verify that Zola can load the theme:

```bash
zola check
zola serve
```

Continue with [Quick start](@/docs/getting-started/quick-start.md) for a complete configuration and content tree.

## Update a Git installation

Read the matching post in [Updates](@/blog/_index.md) before changing a tag. Replace `vX.Y.Z` below with the release you selected:

```bash
git -C themes/devlab-theme status --short
DEVLAB_VERSION=vX.Y.Z
git -C themes/devlab-theme fetch --depth 1 origin tag "$DEVLAB_VERSION"
git -C themes/devlab-theme checkout --detach "$DEVLAB_VERSION"
git -C themes/devlab-theme describe --tags --exact-match
```

Stop before fetching if the status command reports local changes. Move site customizations outside the theme directory or commit them separately before switching releases.

For a submodule, commit the changed `themes/devlab-theme` pointer in the parent site repository. A tagged clone needs no parent-repository change unless deployment records the selected version elsewhere.

Do not use `git submodule update --remote` or `git pull` as the upgrade procedure. Both follow branch state instead of expressing an intentional release selection.

## Update a vendored copy

Download the archive for the new tag and replace the existing `themes/devlab-theme/` directory as one change. Do not overlay the new archive on the old directory: files removed by a release would otherwise remain in the site.

Keep site-specific templates, styles and assets outside the vendored theme directory so replacing the theme does not discard custom work.

## Validate an update

Run the native checks after every upgrade:

```bash
zola check
zola build
```

Review the release notes for configuration changes, then manually check the routes enabled by the site: Home, Docs, Blog, Downloads, search and both color modes. If a release does not work for the site, return the Git installation to the previous tag or restore the previous vendored directory from the site repository.
