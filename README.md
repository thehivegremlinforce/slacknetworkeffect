# Slack — The Network Effect (V2)

An interactive visualization of how the Slack platform creates compounding network effects across customers, partners, vendors, AI agents, and apps & data — all sharing one fabric of channels.

Toggle **Before Slack** to see scattered, friction-ridden work, then flip to **With Slack** and watch the network resolve into one orbiting fabric around a Slack hub. Move the **Network Scale** slider from Startup to Global to grow the graph. Turn on **Slackbot** to see Slack's native assistant ring the hub. Switch scenarios to focus on a single deal room, the AI agent layer, or the data plane. Tap any node to read what it does.

## What's new in V2

- **Refreshed UI** — glassmorphic panels, refreshed colour system (WCAG-AA text), a segmented Before/With control, and a cleaner topbar that collapses gracefully on mobile.
- **Mobile & touch first** — tap-to-inspect tooltips (the node story layer now works on phones), `devicePixelRatio` scaling so the graph is crisp on Retina/4K, 44px touch targets, and a topbar that never overflows.
- **Fit-to-canvas** — orbit radii adapt to the viewport with a header safe-zone, so nothing clips at any scale or screen size.
- **Offline-safe** — all 32 brand logos are inlined as data-URI SVGs (no Simple Icons / Clearbit / slack-edge dependency); the deck renders fully behind a locked-down corporate network or from a USB stick.
- **Illustrative & defensible** — real brand logos are kept but clearly labelled "Illustrative model"; fabricated compliance claims (HIPAA/FedRAMP/SOC 2) are removed; the stats panel is titled "Modeled Estimates" with per-metric tooltips; the Before-Slack friction figures carry named, dated public sources (Asana, McKinsey, Microsoft WTI, Forrester TEI).
- **Persistence & share links** — your configuration survives a refresh (sessionStorage) and a **Share** button copies a link that reopens the exact setup, including renames, custom nodes, scale, scenario, and Before/With mode.
- **Accessibility** — keyboard-operable legend/scene buttons, `:focus-visible` rings, `prefers-reduced-motion` support, aria labels, and a `role="alert"` login error.
- **Performance** — the canvas render loop idles when nothing is animating and pauses in background tabs; node glow gradients are cached instead of rebuilt every frame.

## What it shows

- **Employees, Customers, Partners, Vendors** — the human side of the network, connected through shared Slack Connect channels
- **AI Agents** — autonomous workers operating inside the same channels as the humans they support
- **Apps & Data** — the integrations that turn channels into systems of record
- **Slackbot** — Slack's always-on native assistant, governed by your enterprise policies (the Saturn rings around the hub)

The point: every node added doesn't just connect to one other thing — it multiplies the value of everything already on the network.

## Built by

**Brian D'Souza** — using [Claude Code](https://claude.com/claude-code), my own Slack knowledge, and my own ideas about where the Slack platform, AI agents, and the network are heading. The numbers shown are an illustrative model, not actual customer data.

## Running locally

It's a single-file static app. Open `index.html` in a browser, or deploy via Vercel (`vercel.json` is included). No build step, no server, no network required.

`index.v1.backup.html` is the original V1, kept for reference. `V2_PLAN.md` documents the full set of findings and decisions behind this rebuild.
