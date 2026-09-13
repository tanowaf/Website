# TanoWAF - Website

This Git repository holds the contents of the https://tanowaf.org website.

The pages are built using the [Zola](https://www.getzola.org/) Static Site Generator with the [DevLab](https://codeberg.org/RiPetitor/devlab-theme)
theme.

The website itself is currently hosted on GitHub Pages. The GitHub Actions workflow in `deploy.yaml`, triggered on every
git push of the `main` branch to GitHub, triggers a rebuild and deployment.

## Local development

- install Zola on your workstation (see [www.getzola.org](https://www.getzola.org/documentation/getting-started/installation/))
- clone this repo
- in a terminal, position yourself at the root of dir of the repo, and run `zola serve`; the site will be available on
  port 1111 by default. In case of accessing the website using a different IP address than 127.0.0.1, you might have
  to use a command such as `zola serve -i 0.0.0.0 --base-url http://192.168.56.15` (the `--base-url` option is required
  to have working links to css and js files, as the Zola default is to use absolute links instead of relative ones)
