# AGENTS.md

## Repo Shape

- This is a Jekyll site built on the "Academic Website Template" (not Academic Pages), not a JS app; Jekyll source files live at the repo root.
- Site-wide settings, author metadata, plugins, and Jekyll Scholar config are in `_config.yml`; restart `jekyll serve` after changing it.
- Main editable content is single hand-authored pages in `_pages/` (about, home, research, publications, teaching, projects, blogs, allnews, talkmap, 404) — there are no `_publications/`/`_talks/`/`_teaching/`/`_portfolio/` collections or `_data/navigation.yml` in this repo. Each page's front matter `layout:` picks a template from `_layouts/` (`gridlay`, `homelay`, `piclay`, `textlay`, `research`, `team`, `publications`, `bibtemplate`, `post`, `page`).
- `_data/pi.yml` (education list) and `_data/news.yml` (homepage news items) are small structured data pulled into pages via Liquid.
- Publications are rendered by `jekyll-scholar` from `assets/ref.bib` via `{% bibliography --query @* %}` in `_pages/publications.md`, styled with `citesty.csl`.
- Blog posts are normal Jekyll posts in `_posts/` (layout `post`), listed on `_pages/blogs.md`.
- Styling flows from `assets/main.scss` into `_sass/{base,components,layouts,utilities}/`; Jekyll compiles SCSS during `bundle exec jekyll build`.
- `assets/js/site.min.js` is generated from `assets/js/site.js` by esbuild via `npm run build:js`; edit `site.js`, then rebuild — do not hand-edit `site.min.js`.
- `_plugins/ext.rb` requires `jekyll/scholar`; `_plugins/markdown.rb` adds a custom `{% markdown <file> %}` Liquid tag that renders an `_includes/` file through Liquid+Kramdown.

## Commands

- Install: `npm install` (JS deps; `package-lock.json` is intentionally ignored) and `bundle install` (Ruby/Jekyll 4.3.3, versions pinned in `Gemfile`).
- Local dev server: `npm run serve` or `npm start` (builds JS, then `bundle exec jekyll serve --livereload`).
- Build verification: `npm run build` (`npm run build:js` then `bundle exec jekyll build`).
- Rebuild bundled JS after JS source changes: `npm run build:js`.
- There is no `npm run install` or `npm run watch:js` script in `package.json` — only `build:js`, `build`, `serve`, `start` exist.
- No lint or test suite exists; verify changes with a local `serve`/`build` and visual check.

## Generated And Ignored Files

- Do not commit `_site/`, `vendor/`, `.bundle/`, `node_modules/`, `local/`, or `package-lock.json`; they are ignored local/build artifacts.
- `_config.yml` `exclude:` keeps `Gemfile`, `Gemfile.lock`, `package.json`, `package-lock.json`, `vendor`, `.bundle`, `markdown_generator`, `talkmap`, and `AGENTS.md` out of the generated site, so verify source changes with a local build rather than expecting those files in `_site/`.

## Content Generators (not currently wired to live content)

- `markdown_generator/publications.py` reads `markdown_generator/publications.tsv` and writes markdown into `../_publications/`; run it from `markdown_generator/`. There is no `_publications/` collection in this repo currently, so its output is not consumed by any page.
- `markdown_generator/talks.py` reads `markdown_generator/talks.tsv` and writes markdown into `../_talks/`; same caveat — no `_talks/` collection exists.
- `markdown_generator/pubsFromBib.py` expects `proceedings.bib` and `pubs.bib` in `markdown_generator/`, but those files are not present by default.
- `talkmap.py` / `talkmap.ipynb` say to run from `_talks/`; they scrape talk markdown `location` fields and write the standalone Leaflet map under `../talkmap/` (referenced by `_pages/talkmap.html`). Since no `_talks/` collection exists, this generator has no current input.

## Deployment Notes

- Branch `dev` is for development; merge to `main` for production. `.github/workflows/deploy.yml` builds (`bundle exec jekyll build`, Ruby 3.2, `JEKYLL_ENV=production`) and deploys to GitHub Pages on every push to `main`.
- `_config.yml` sets `url: "https://ali-ayati.com"`, `baseurl: ""`, and repository `cpt9m0/cpt9m0.github.io`; keep absolute URL changes consistent with `CNAME` and README live URLs.
