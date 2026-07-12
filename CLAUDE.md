# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static marketing site for **Volli** (an app to find and book pickleball/tennis/basketball courts), served via **GitHub Pages** from the `main` branch of `troy-herbison.github.io`. There is no build system, package manager, or test suite — the repo is plain HTML plus image assets.

## Workflow

- **Deploy:** push to `main`. GitHub Pages serves the repo root; the live URL is the repo's Pages domain. There is no build step.
- **Preview locally:** open the `.html` files directly in a browser, or run `python3 -m http.server` from the repo root and visit `localhost:8000`.
- Tailwind is loaded at runtime via the `@tailwindcss/browser@4` CDN script (see `<head>`), so utility classes work with no install or config. There is no `tailwind.config` and no compiled CSS.

## Architecture

Each page is a standalone HTML file (`index.html` = Home, `about.html` = About). There are **no shared/partial files** — the design system and navigation are duplicated inline in every page:

- A `:root` block defines the brand palette (`--volli-dark`, `--volli-mid`, `--volli-bright`, `--volli-bright-hover`) and the `body` gradient background.
- The `.tab-nav` component (pill-style top navigation) and its `.active` state are re-declared in each file's `<style>` block.

**Consequences when editing:**
- A change to the palette, background, footer, or `.tab-nav` styling must be applied to **every** HTML file to stay consistent.
- Adding a new page means adding its `<a>` link to the `.tab-nav` of every existing page, and copying the shared `<style>`/`:root` block into the new file. Mark the current page's nav link with `class="active"` and `aria-current="page"`.

Assets: `logo.jpg` is the shared logo/favicon/OG image; `about_page_1.jpg`…`about_page_6.jpg` are the About page's content images (referenced in order). External links (social, surveys, YouTube embed) are hardcoded in the markup.
