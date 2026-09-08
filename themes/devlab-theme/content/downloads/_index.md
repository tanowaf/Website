+++
title = "Downloads"
description = "A reusable release page for packages, installation images and verification details."
template = "downloads.html"

[extra.downloads]
eyebrow = "Distribution layout"
channels_label = "Release channels"
unavailable_label = "Not published"
channels = [
  { name = "Stable package", status = "Not published", description = "The recommended channel for tested release artifacts." },
  { name = "Preview package", status = "Planned", description = "Pre-release builds for validating an upcoming version." },
  { name = "Minimal package", status = "Planned", description = "A smaller artifact for custom installation workflows." },
]

[extra.downloads.status]
label = "Release status"
title = "No artifacts published"
description = "This Demo intentionally provides no files or download links."
items = [
  { label = "Availability", value = "Not published" },
  { label = "Links", value = "Disabled until a file exists" },
  { label = "Verification", value = "Added when artifacts are published" },
]

[extra.downloads.verification]
label = "Verification"
title = "Verify published artifacts"
description = "SHA-256 checksums and signatures should be published beside real artifacts. No verification data is shown until the corresponding files exist."
+++

DevLab itself does not publish downloadable artifacts. This page demonstrates how another project can present release channels before its first public build. Buttons remain disabled until a channel has a real URL.
