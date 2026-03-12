# Canopy — Sitemap Toolkit

**Canopy** crawls a live website, maps its page structure, and generates an interactive visual sitemap.
Useful for understanding a site before a redesign or migration, auditing information architecture, and sharing clear site structure.

A sibling tool to [Waypoint](https://elleiana.github.io/Waypoint/) - use Canopy to map a site's structure, then pass the URLs to Waypoint to plan and manage a redirect migration.

**→ [Open Canopy](https://elleiana.github.io/Canopy/)**

---

## What it does

**🕷️ Crawler** - point it at any URL and Canopy discovers all reachable internal pages by following links. Results appear in real time. When the crawl is done, hit **→ build diagram**.

**🌳 Diagram** - an interactive tree diagram laid out by section and depth. Sections spread horizontally under the root node; each section's child pages stack vertically beneath it with a connecting rail. Pan and zoom to explore, click any node for details, click sections to expand or collapse branches.

**🍂 Export** - download the sitemap as PNG, SVG, CSV, JSON, or sitemap XML. Send URLs directly to Waypoint's Mapper with one click.

---

## Features

### Crawler
- Crawls any public website via a Cloudflare Worker proxy (handles CORS)
- Configurable **concurrency** (1–10 parallel requests) and **max crawl depth**
- **Workbench filters** strip out pagination URLs, media files, dev/system paths, query strings, hash links, and custom patterns before results appear
- Live results list with status badges, depth indicators, and URL search
- Filter results by: all / ok / errors / blocked
- **Import mode** — skip the crawl entirely and paste a URL list, upload a `.txt`, `.csv`, or `sitemap.xml` file, and build a diagram directly

### Diagram
- Hybrid layout: top-level sections spread horizontally, child pages stack vertically with indent rails
- **Workbench controls**: max display depth, collapse threshold (auto-collapses sections with too many children), show/hide child counts, show/hide error pages
- **Click any node** to open a detail panel showing full URL, path, depth, HTTP status, and child count
- **Click-to-collapse** sections directly from the detail panel, without re-drawing
- **Hover tooltips** show the full URL path on mouseover
- Ancestor highlighting — selected node's parent chain is highlighted in teal
- **Five colour themes**: Default, Teal, Slate, Forest, Ember — all with light and dark mode variants
- Pan (drag), zoom (scroll or +/− buttons), fit-to-view

### Export
- **PNG** — flat image, easy to drop into a doc or presentation
- **SVG** — vector, scalable for print or large diagrams
- **CSV** — URL list with depth and section columns, for spreadsheets
- **JSON** — full tree structure with crawl metadata, for developers
- **Sitemap XML** — standard `sitemap.xml` format, ready to submit to Search Console
- **Send to Waypoint** — opens Waypoint with crawled URLs pre-loaded into the Mapper

### Other
- **Session persistence** — crawl results are saved to `localStorage` and restored on next visit
- **Recent sitemaps** on the home screen — up to 8 sessions, click to reload
- **Light/dark mode** toggle
- Runs entirely in the browser — no server, no account, no tracking

---

## Using the proxy
Browsers block cross-origin requests (CORS), which means JavaScript in a browser can't directly fetch pages from other domains. Canopy routes crawl requests through a Cloudflare Worker proxy that fetches pages server-side and returns the HTML.
**Automatic** — uses the built-in Canopy proxy. Works immediately with no setup.
**Manual** — if you'd prefer to host your own proxy (for privacy, rate limit control, or custom `User-Agent`), Canopy can generate the Worker code for you.

---

## Crawl tips

**The crawl is going very slowly** — lower concurrency if the site is rate-limiting you, or raise it (cautiously) on sites with no rate limits. The snail animation indicates an active crawl.

**Some pages aren't appearing** — check the Workbench filters. The dev & system filter strips `.js`, `.css`, and `/feed` URLs by default. The pagination filter strips `?page=` style URLs. If you need these, untick the relevant filters before crawling.

**The diagram is too wide / cluttered** — use the Diagram Workbench to reduce max depth (try setting it to 2 for a sections-only view) or lower the collapse threshold to auto-collapse large sections.

**A site returns mostly "blocked"** — the site is blocking the proxy's requests (rate limiting, bot detection, or server-side auth). Try switching to a manual proxy, or use Import mode with a URL list exported from another source.

---

## Licence
All rights reserved. Canopy may not be reproduced or redistributed without permission.
Canopy is provided as-is, without warranty of any kind. You are responsible for ensuring you have permission to crawl any site you use it on.
