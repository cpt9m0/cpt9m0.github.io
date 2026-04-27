# Ali Ayati - Personal Website

Source code for [ali-ayati.com](https://ali-ayati.com) — my personal academic webpage.

Built with [Jekyll](https://jekyllrb.com/) using the [Academic Website Template](https://github.com/sbryngelson/academic-website-template).

## Live URLs

1. [cpt9m0.github.io](https://cpt9m0.github.io)
2. [ali-ayati.com](https://ali-ayati.com)

## Usage

```sh
# Install JS dependencies and Ruby gems
npm run build:js
bundle install

# Build and serve with live reload
bundle exec jekyll serve --livereload

# Build JS, then Jekyll build
npm run build

# Full: build JS then serve
npm run serve
```

## Development

- Branch `dev` for development; merge to `main` for production.
- GitHub Actions on pushes to `main` builds and deploys to GitHub Pages.

## Features

- Personal profile and biography
- BibTeX-powered publications via Jekyll Scholar
- Research overview
- Teaching experience
- Technical blog
- Dark mode with system preference detection
- Site-wide search (Cmd+K)
- Copy BibTeX button

## Credits

Based on the [Academic Website Template](https://github.com/sbryngelson/academic-website-template) by S. Bryngelson.
