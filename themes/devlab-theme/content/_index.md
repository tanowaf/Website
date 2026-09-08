+++
title = "DevLab Theme"
description = "A modern Zola theme for developers, documentation and technical blogs."

[extra]
home_layout = "wide"
home_eyebrow = "Developer-first Zola theme"
home_hero_image = "/images/devlab-docs-preview.png"
home_hero_image_alt = "DevLab documentation layout with sidebar navigation, readable content and an on-page table of contents."
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
  { kicker = "Content", title = "Readable components", description = "Callouts, cards, code styling and copy buttons support technical writing without a frontend framework." },
  { kicker = "Runtime", title = "Minimal JavaScript", description = "The theme uses small vanilla scripts only where interaction needs them: search, theme mode and menus." },
]

[extra.home_workflow]
eyebrow = "Workflow"
title = "Built for maintainable documentation"
description = "DevLab favors reusable partials, Zola section trees and plain Markdown examples, so each feature stays easy to inspect and extend."
points = [
  { title = "Shared partials", description = "Docs navigation is rendered once and reused across desktop and mobile." },
  { title = "Live examples", description = "Feature docs show rendered output and the Markdown syntax behind it." },
  { title = "Short iterations", description = "Each workflow can evolve without a large template rewrite." },
]

[extra.home_cta]
title = "Ready to build your docs?"
description = "Start with the getting started guide or jump straight into the documentation overview."
primary_label = "Just start!"
primary_path = "/docs/getting-started/"
secondary_label = "View docs"
secondary_path = "/docs/"
+++

Build fast, readable and maintainable developer websites with Zola.
