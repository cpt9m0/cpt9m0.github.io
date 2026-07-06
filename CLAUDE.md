# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repo Shape

This is a Jekyll-based personal academic website (Ali Ayati's site, deployed at ali-ayati.com and cpt9m0.github.io), based on the "Academic Website Template" — not the Academic Pages template, despite similar naming. Jekyll source lives at the repo root; there are no `_publications`/`_talks`/`_teaching` collections. Site content (research, teaching, projects, publications) is authored directly as single pages in `_pages/`, with front matter `layout:` selecting one of the `_layouts/*.html` templates (`gridlay`, `homelay`, `piclay`, `textlay`, `research`, `team`, `publications`, `bibtemplate`, `post`, `page`).

- `_config.yml` holds identity/links/site settings (name, title, institution, social links, accent color, nav pages) plus Jekyll Scholar config. Restart `jekyll serve` after changing it.
- `_data/pi.yml` and `_data/news.yml` hold small structured content (education list, homepage news items) pulled into pages via Liquid.
- Publications are rendered by `jekyll-scholar` from `assets/ref.bib` (see `_pages/publications.md`'s `{% bibliography --query @* %}` tag and the `citesty.csl` style); per-entry detail pages go to `bibliography_details/details_dir` per `_config.yml`.
- Blog posts are normal Jekyll posts in `_posts/` (layout `post`), listed on `_pages/blogs.md`.
- Styling: `assets/main.scss` imports from `_sass/{base,components,layouts,utilities}/`; Jekyll compiles SCSS during `bundle exec jekyll build`.
- JS: `assets/js/site.js` is the source, bundled/minified by esbuild into `assets/js/site.min.js` via `npm run build:js`. Edit `site.js`, then rebuild — do not hand-edit `site.min.js`.
- `_plugins/ext.rb` just requires `jekyll/scholar`; `_plugins/markdown.rb` adds a custom `{% markdown <file> %}` Liquid tag that renders an `_includes/` file through Liquid+Kramdown.
- `talkmap.py` / `talkmap.ipynb` scrape `location` front matter from talk markdown and generate the standalone Leaflet map under `talkmap/` (referenced by `_pages/talkmap.html`). There is currently no `_talks/` collection in this repo, so this generator is not actively wired up to content.
- `markdown_generator/` (Python/Jupyter, run from that directory) can generate page markdown from TSV/BibTeX sources (`publications.py`, `talks.py`, `pubsFromBib.py`), but generated output is not currently used since pages are hand-authored in `_pages/`.

## Commands

- Install: `npm install` (JS deps; `package-lock.json` is gitignored) and `bundle install` (Ruby/Jekyll, gem versions pinned in `Gemfile`, Jekyll 4.3.3).
- Local dev server: `npm run serve` (builds JS then `bundle exec jekyll serve --livereload`), or `npm start` (alias).
- Build only: `npm run build` (`npm run build:js` then `bundle exec jekyll build`).
- Rebuild bundled JS only: `npm run build:js`.
- There is no lint or test suite in this repo; verify changes with a local `serve`/`build` and visual check.

## Deployment

- Branch `dev` is for development; `main` is production. Merge `dev` → `main` to ship.
- `.github/workflows/deploy.yml` builds with `bundle exec jekyll build` (`JEKYLL_ENV=production`, Ruby 3.2) and deploys to GitHub Pages on every push to `main`.
- `_config.yml` sets `url: "https://ali-ayati.com"`, `baseurl: ""`, and GitHub repo `cpt9m0/cpt9m0.github.io`. `CNAME` and README live URLs must stay consistent with any URL changes.
- `_config.yml` `exclude:` keeps `Gemfile`, `package.json`, `vendor/`, `markdown_generator/`, `talkmap/`, `AGENTS.md`, etc. out of the built `_site/` — don't expect source-only files to appear there.
