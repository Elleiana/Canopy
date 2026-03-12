# Canopy — Sitemap Toolkit

**Canopy** crawls a live website, maps its page structure, and generates an interactive visual sitemap. Useful for understanding a site before a redesign or migration, auditing information architecture, and sharing clear site structure with clients.

A sibling tool to [Waypoint](https://elleiana.github.io/Waypoint/) — use Canopy to map a site's structure, then pass the URLs to Waypoint to plan and manage a redirect migration.

**→ [Open Canopy](https://elleiana.github.io/Canopy/)**

---

## What it does

Canopy has three stages, each with its own tab:

**🕷️ Crawler** — point it at any URL and Canopy discovers all reachable internal pages by following links. Results appear in real time. When the crawl is done, hit **→ build diagram**.

**🌳 Diagram** — an interactive tree diagram laid out by section and depth. Sections spread horizontally under the root node; each section's child pages stack vertically beneath it with a connecting rail. Pan and zoom to explore, click any node for details, click sections to expand or collapse branches.

**🍂 Export** — download the sitemap as PNG, SVG, CSV, JSON, or sitemap XML. Send URLs directly to Waypoint's Mapper with one click.

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

### Setting up your own Cloudflare Worker proxy

1. Create a free account at [cloudflare.com](https://cloudflare.com)
2. Go to **Workers & Pages → Create → Create Worker**
3. In the Crawler Workbench, switch Proxy to **manual** and click **↓ Download canopy-worker.js**
4. Paste the downloaded code into the Worker editor, replacing the default
5. Edit `ALLOWED_ORIGIN` to match your Canopy deployment URL (or leave as `'*'` for open access)
6. Click **Deploy** and paste the Worker URL into the manual proxy field

---

## Importing a URL list

If you already have a URL list (from a previous crawl, a CMS export, or Waypoint), you can skip the crawler entirely:

1. Switch the Crawler tab to **↑ Import URLs** mode
2. Paste URLs into the text area (one per line), or upload a file
3. Accepted formats: plain URL list (`.txt`), CSV with a URL column, or `sitemap.xml`
4. Optionally set a Base URL to resolve relative paths
5. Click **→ Build sitemap**

The imported URLs feed into the same diagram and export pipeline as a live crawl.

---

## Diagram layout

The diagram uses a hybrid layout designed to make site hierarchy readable at a glance:

- The **root node** (homepage) sits at the top centre
- **Section nodes** (top-level URL segments like `/services`, `/about`) spread horizontally beneath it, connected by a bus bar
- **Child pages** stack vertically beneath their section, indented and connected by a vertical rail with horizontal ticks
- Deeper pages indent further right, so hierarchy depth is always spatially clear

Nodes are colour-coded: root (darker), section (mid), page (lighter). The legend in the top-left of the diagram canvas confirms the current mapping.

---

## Crawl tips

**The crawl is going very slowly** — lower concurrency if the site is rate-limiting you, or raise it (cautiously) on sites with no rate limits. The snail animation indicates an active crawl.

**Some pages aren't appearing** — check the Workbench filters. The dev & system filter strips `.js`, `.css`, and `/feed` URLs by default. The pagination filter strips `?page=` style URLs. If you need these, untick the relevant filters before crawling.

**The diagram is too wide / cluttered** — use the Diagram Workbench to reduce max depth (try setting it to 2 for a sections-only view) or lower the collapse threshold to auto-collapse large sections.

**A site returns mostly "blocked"** — the site is blocking the proxy's requests (rate limiting, bot detection, or server-side auth). Try switching to a manual proxy, or use Import mode with a URL list exported from another source.

---

## Architecture

Canopy is a single HTML file with no build step, no dependencies (beyond two Google Fonts), and no backend. Everything runs in the browser.

```
canopy.html
├── CSS          ~400 lines  — design system matching Waypoint, diagram themes
├── HTML         ~500 lines  — three page tabs + home + modals
└── JavaScript  ~1400 lines  — crawl engine, diagram layout, export, session storage
```

**Crawl engine** — `N` async workers run in parallel (configurable). Each worker dequeues a URL, fetches it via proxy, parses links from the returned HTML, and enqueues new same-origin URLs. Workers wait briefly before exiting if the queue is empty, to handle mid-flight URL additions from sibling workers.

**Diagram layout** — a recursive hybrid algorithm. Top-level sections are laid out horizontally using each subtree's computed width. Within each section column, children are placed vertically with consistent `V_GAP` spacing and a fixed `CHILD_INDENT` per depth level. All coordinates are computed in a single pass before rendering.

**Session storage** — crawl results are serialised to `localStorage` keyed by timestamp ID. A separate history index (max 8 entries, keyed by hostname) powers the home page recent sessions list. The most recent session is also stored under a fixed key for quick restore on load.

---

## Relationship to Waypoint

Canopy and Waypoint are designed to work together as a pre/post migration workflow:

```
Canopy  →  map current site structure  →  export URLs
Waypoint →  plan redirect mapping      →  validate migration
```

The **Send to Waypoint** export opens Waypoint with Canopy's crawled URLs pre-loaded as the existing site URL list. For large crawls that exceed URL bar limits, it copies the list to clipboard with paste instructions.

---

## Limitations

- **JavaScript-rendered pages** — Canopy fetches raw HTML via the proxy. Pages that require JavaScript to render their content (SPAs, heavily client-side sites) may return fewer links than a browser-based crawler would find.
- **Auth-gated pages** — Canopy cannot crawl pages behind login walls.
- **Rate limiting** — aggressive crawl settings may trigger 429 responses or IP blocks on the target site. You are responsible for crawling responsibly.
- **Large sites** — crawling thousands of pages is possible but will be slow and may exceed `localStorage` quota for session saving. Use the max crawl depth setting to limit scope.

---

## Licence

All rights reserved. Canopy may not be reproduced or redistributed without permission.

Canopy is provided as-is, without warranty of any kind. You are responsible for ensuring you have permission to crawl any site you use it on.
