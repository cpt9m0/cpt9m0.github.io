# AGENTS.md

## Repo Shape

- This is a personal Academic Pages / Minimal Mistakes Jekyll site, not a JS app; Jekyll source files live at the repo root.
- Site-wide settings, author metadata, collections, plugins, Sass output, and excludes are in `_config.yml`; restart `jekyll serve` after changing it.
- Main editable content is in `_pages/`, collection folders such as `_publications/`, `_talks/`, `_teaching/`, `_portfolio/` when present, and `_data/navigation.yml` for menu links.
- Styling flows from `assets/css/main.scss` into `_sass/`; Jekyll compiles SCSS during `bundle exec jekyll build`.
- `assets/js/main.min.js` is generated from `node_modules/jquery/dist/jquery.min.js`, plugin files in `assets/js/plugins/`, and `assets/js/_main.js` by `npm run build:js`; edit `_main.js` or plugin sources, then rebuild the minified file.

## Commands

- First Ruby setup: `npm run install` (runs `bundle install --path vendor/bundle`).
- Install JS tooling before rebuilding JS: `npm install` because `package-lock.json` is intentionally ignored.
- Local dev server: `npm run serve` or `npm start` (`bundle exec jekyll serve --livereload`).
- Build verification: `npm run build` (`bundle exec jekyll build`).
- Rebuild bundled JS after JS source changes: `npm run build:js`; watch with `npm run watch:js`.

## Generated And Ignored Files

- Do not commit `_site/`, `vendor/`, `.bundle/`, `node_modules/`, `local/`, or `package-lock.json`; they are ignored local/build artifacts.
- `_config.yml` excludes `assets/js/_main.js`, `assets/js/plugins/`, `package.json`, `Gemfile`, and `vendor/` from the generated site, so verify source changes with a local build rather than by expecting those files in `_site/`.

## Content Generators

- `markdown_generator/publications.py` reads `markdown_generator/publications.tsv` and writes markdown into `../_publications/`; run it from `markdown_generator/`.
- `markdown_generator/talks.py` reads `markdown_generator/talks.tsv` and writes markdown into `../_talks/`; run it from `markdown_generator/`.
- `markdown_generator/pubsFromBib.py` expects `proceedings.bib` and `pubs.bib` in `markdown_generator/`, but those files are not present by default.
- `talkmap.py` says to run from `_talks/`; it scrapes talk markdown `location` fields and writes the standalone map under `../talkmap/`.

## Deployment Notes

- `_config.yml` sets `url: "https://ali-ayati.com"`, `baseurl: ""`, and repository `cpt9m0/cpt9m0.github.io`; keep absolute URL changes consistent with `CNAME` and README live URLs.
