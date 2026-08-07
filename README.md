# Duetto — project website

A static Jekyll site, published with GitHub Pages. Nothing is written twice:
navigation, people, research sections, events, featured links and playground
clips all live in `_data/` or `_events/`. Edit a YAML file and the site updates
wherever that content appears.

## Structure

```
_config.yml            site-wide settings: title, email, address, URLs,
                       the events collection and layout defaults
_data/
  nav.yml              the menu — one file for every page
  people.yml           the team
  research.yml         the research page, section by section
  highlights.yml       the featured grid on the home page
  playground.yml       creativity levels and their video clips
_events/               one Markdown file per event; the filename
                       becomes the URL (/events/<filename>/)
_includes/             reused fragments: head, nav, footer, event card
_layouts/
  default.html         inner pages
  home.html            the home page
  event.html           single event pages
index.md               home
research.html          \
team.html               |  a few lines each: the content
events.html             |  comes from _data/ and _events/
playground.html         |
contact.html           /
404.html
assets/
  style.css            all the CSS, one file
  hero-dark.svg        the dark artwork behind the home and page headers
  people/              portraits, cropped to 3/4 by the CSS
  research/            images and clips for the research page
  events/              logos and photos for events
  playground/          the creativity clips
```

## Publishing

1. Set `url` and `baseurl` in `_config.yml` — the comments explain which is
   which. `baseurl` must match the repository name exactly, capitals included.
2. Push to the `main` branch of a public repository.
3. Settings → Pages → Source: **Deploy from a branch**, branch `main`,
   folder `/ (root)`.

GitHub compiles the site on every push. You do not need Ruby installed, and
`_site/` must never be committed — it is the generated output.

## Everyday edits

**Add a person** — `_data/people.yml`:

```yaml
- name: Full Name
  role: Their role in Duetto
  subrole: Their job title
  photo: full-name.jpg
```

The portrait goes in `assets/people/`. `subrole`, `links` and `focus` are
optional; `focus` shifts the crop when a face sits off-centre, e.g. `30% center`.

**Add an event** — create a file in `_events/`. The filename becomes the URL, so
`thegate-festival-2026.md` is published at `/events/thegate-festival-2026/`.
Copy an existing file and change the front matter. Add `upcoming: true` to list
it under Upcoming, and remove that line once the event has passed.

**Add a research section** — `_data/research.yml`. Each entry becomes a section
with its own anchor, and the index at the top of the page updates itself. A
section can carry an `image`, a `video` or a `gallery`; `side: left` flips it to
the other side, `layout: centered` drops the two columns altogether, and
`collapsible: true` turns its sub-blocks into accordion entries.

**Change the featured grid** — `_data/highlights.yml`. Hand-picked, in the order
written. Six or nine entries fill the three-column grid cleanly; seven or eight
leave a gap.

**Add a menu item** — one entry in `_data/nav.yml`, then create the page with a
matching `permalink`. The current page highlights itself.

**Change colours or fonts** — `assets/style.css`, the `:root` block at the top.
Fonts also need updating in the Google Fonts link in `_includes/head.html`.

**Use the dark page header** — add `dark_head: true` to a page's front matter.

## Adding a page

Create an `.html` file in the root starting with:

```yaml
---
layout: default
title: Openings
permalink: /openings/
heading: Open positions
lede: One line under the title.
---
```

Then add it to `_data/nav.yml`. Nav, header and footer come from the layout, so
the file only holds its own content.

## Images and video

Keep the whole site well under 100 MB. Large files slow every deployment down,
and they stay in the git history even after you delete them.

- **Photos**: no wider than 2000 px, ideally under 400 KB.
- **Video**: MP4, muted, short and looping. Under 2 MB each.
- **Never use GIF** for animation. A ten-second GIF can weigh 30 MB where the
  same clip as MP4 weighs one. Convert with:

```bash
ffmpeg -i input.gif -movflags +faststart -pix_fmt yuv420p \
  -vf "fps=15,scale=1280:-2" -crf 26 output.mp4
```

To trim and compress a clip in one pass:

```bash
ffmpeg -ss 30 -to 50 -i input.mp4 -vf scale=1280:-2 \
  -c:v libx264 -crf 24 -preset slow -pix_fmt yuv420p \
  -an -movflags +faststart output.mp4
```

For `.avi` sources put `-ss` *after* `-i`, otherwise the first frames come out
corrupted.

## Local preview

Optional — you can work by pushing and checking the result online. If you want
instant feedback you need Ruby 3.3 (Jekyll does not run on Ruby 3.4 or later):

```bash
brew install ruby@3.3
export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"
bundle install
bundle exec jekyll serve --livereload --baseurl ""
```

Then open `http://localhost:4000`. The `export` only applies to that terminal
window, so it needs repeating each session.

`--baseurl ""` overrides the value in `_config.yml`, so the site answers at the
root instead of `/Duetto/`. Everything reloads on save except `_config.yml`,
which needs the server restarted.

## Troubleshooting

**The site loads but has no styling.** `baseurl` is wrong. It must match the
repository name exactly, capitals included.

**A change does not show up.** Check that the file is where Jekyll expects it —
layouts in `_layouts/`, includes in `_includes/`, data in `_data/`. A stray copy
in the root is ignored. If the source looks right, compare it against the
generated file: `grep -c "something" _site/research/index.html`.

**Jekyll reports a conflict.** Two files claim the same output path, usually
because both declare the same `permalink`. The server prints which ones.

**A page renders as plain text.** No layout was applied — check the `layout:`
line in the front matter, or the `defaults` block in `_config.yml` for events.

**The deployment stays queued and then times out.** Almost always a GitHub
outage rather than anything in the repository — check githubstatus.com. An empty
commit (`git commit --allow-empty -m "trigger rebuild"`) queues a fresh run once
the service recovers.