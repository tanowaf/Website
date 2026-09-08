# Third-party notices

DevLab vendors a browser bundle at `static/vendor/beautiful-mermaid/beautiful-mermaid.min.js`. It is loaded only by pages that render a `devlab.diagram` component.

The bundle was produced with esbuild 0.25.9 from these unmodified packages:

| Package | Version | License | Source |
| --- | --- | --- | --- |
| beautiful-mermaid | 1.1.3 | MIT | [repository](https://github.com/lukilabs/beautiful-mermaid), [npm package](https://www.npmjs.com/package/beautiful-mermaid/v/1.1.3) |
| elkjs | 0.11.1 | EPL-2.0 | [repository](https://github.com/kieler/elkjs), [npm package](https://www.npmjs.com/package/elkjs/v/0.11.1) |
| entities | 7.0.1 | BSD-2-Clause | [repository](https://github.com/fb55/entities), [npm package](https://www.npmjs.com/package/entities/v/7.0.1) |

Copies of their licenses are stored under `third_party/`. Corresponding source is available from the linked repositories and from each exact package version in the npm registry.

The vendored bundle is not required to develop or build a DevLab site. Node.js and npm were used only by the theme maintainer to create this checked-in browser asset; theme consumers continue to build with Zola alone.
