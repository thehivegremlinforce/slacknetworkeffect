# Slack Network Effect — V2 Build Plan

Status: proposed spec, not yet implemented.
Basis: full read of `index.html` (1,918 lines) plus six rendered states (Before Slack, With Slack, Slackbot+Enterprise, Deal scene, Global, mobile 414px), reviewed across six lenses with every finding verified against source. 41 findings confirmed, 8 partial, 3 rejected.

Owner decisions locked for V2:
1. Branding: keep real brand logos, but make them clearly **illustrative** (option B), not fully fictional.
2. Scope of this document: written plan only. No code changes yet.

---

## BLUF

The app is strong: the radial network metaphor lands, the Before/With Slack contrast is persuasive, and Configure is more thoughtful than most sales toys. Three things gate a credible V2, in priority order: content credibility and brand safety, touch support (the whole story layer is mouse only today), and canvas fit/quality (clipping plus blur). Everything else is polish or future enabling refactor.

---

## Workstream 1 — Content credibility and brand safety (do first)

Decision applied: real logos stay, but the view must read as illustrative and must not assert invented facts about regulated data or specific deals.

1.1 **Add a persistent "Illustrative" treatment.**
   - Add a small always visible banner or chip near the canvas subtitle: "Illustrative model. Not actual customer data."
   - Carry the same line into the PDF/print output and the footer credibility line.
   - Acceptance: the disclaimer is visible in every screenshot state and in exported PDF.

1.2 **Strip fabricated compliance and regulated-data claims** tied to named brands. Source: `POOL.customer` lines 655 to 666.
   - Remove HIPAA (Pfizer c12), FedRAMP (Goldman c9), SOC 2 sharing (Stripe c6 / InfoSec v3), data-residency specifics.
   - Replace with generic capability language ("security review handled in channel") with no named-brand attribution.
   - Acceptance: grep for HIPAA, FedRAMP, SOC, FedRAMP, "data residency" returns no brand-coupled assertion.

1.3 **Restrict retained real logos to brands with published Slack case studies.**
   - Keep brands you can point to a public Slack story for; relabel the rest as generic ("Global Bank", "EV Manufacturer", "Pharma Co").
   - Customer pool ids c5 to c16 are the ones to audit.
   - Acceptance: a short note in this file lists each retained brand and its case-study URL.

1.4 **Genericise invented per-deal events and the named individual.** Source: feed arrays `FEED_I` (1765 to 1774), `FEED_N` (1781 to 1790), tooltip copy.
   - Remove "Sarah Chen" (line 1772). Use a role, not a person.
   - Move brand-specific event strings (Stripe security review, Tesla MEDDIC, Verizon QBR) to the fictional set (Acme/Hooli/Initech/Globex).
   - Add a "Sample activity" label to the feed header so it does not read as live telemetry.

1.5 **Reframe the stats so they survive exec scrutiny.** Source: `refreshStats` (983 to 1024), `refreshFrictionStats` (1219 to 1229).
   - Rename "Live Network Stats" to "Modeled estimates" (the values are coefficients, not telemetry).
   - Add a per-metric tooltip (reuse the Network Value pattern at line 577) that shows the formula and its basis.
   - Drive the friction dollar figure off user-entered headcount (`headcountMap`, already summed at 999 to 1005) rather than the hardcoded `{1:50...5:8000}` table.
   - Surface the three friction assumptions (recoverable hrs/wk, working weeks, $/hr) as editable inputs with citable defaults.
   - Fix the Forrester anchor mismatch: today only `emp*1.6` maps to the cited ~97 min/wk; the `ag*12`, `app*3`, `+20` terms are unsourced. Either cite them or move them behind a labelled "with AI agents" path.
   - Relabel "Network Value Nx vs baseline": the Metcalfe square compounds with the slider (Startup ~7x to Global ~5000x) and never equals the "1.0 baseline" the tooltip claims. Call it "relative connectivity index (illustrative)".
   - Add per-stat citations (study, year) for the Before Slack friction cards (588 to 592). Drop figures with no current named source (2 min interruptions, 4:1 read:sent).

---

## Workstream 2 — Touch and mobile (your stated priority; currently broken)

2.1 **Tap to inspect nodes (critical).** Source: only `mousemove` (1735) and `mouseleave` (1761) exist; zero touch/pointer/click handlers.
   - Add a `pointerdown`/`touchstart` hit test reusing the existing `nPos` + `Math.hypot` logic to pin the tooltip; tap empty space or a close affordance to dismiss.
   - Enlarge hit radius on coarse pointers (`nt.r + 16` when `matchMedia('(pointer:coarse)')`).
   - Clamp the tooltip to the visible canvas so it cannot fall off small screens.
   - Acceptance: every node's `tip.b`/`tip.s` copy is reachable on a phone.

2.2 **Fix topbar overflow below ~600px.** Source: topbar 319 to 347; only handling is `flex-wrap` at 900px.
   - Icon-only buttons under 600px (keep `title`, add matching `aria-label`).
   - Collapse Reset/Print/PDF into a "More" menu; promote Before/With Slack to one primary segmented control.
   - Give `.t-title-input` `min-width:0; flex:1` so it shrinks gracefully.
   - Acceptance: nothing clips at 360px and 414px.

2.3 **devicePixelRatio scaling (kills blur).** Source: `resize()` 1315 to 1320 sizes the bitmap in CSS px; `#mc{width:100%}` stretches it.
   - In `resize()`: `dpr = min(devicePixelRatio, 2)`, set `canvas.width/height` to `r.width/height * dpr`, set `canvas.style.width/height` in CSS px, `ctx.setTransform(dpr,0,0,dpr,0,0)`, keep `W/H/CX/CY` in logical px so all draw math and hit-testing are unchanged.
   - Cap dpr at 2 to protect low-GPU laptops at Global scale.

2.4 **Bigger tap targets on touch.** Source: `.del-btn` (148), `.emoji-btn` (139), `.leg-toggle` (75), `.sb-toggle` (110), `.q-slider` thumb (93) all well under 44px.
   - Add `@media (pointer:coarse)` enforcing >=44px hit areas, priority on the per-node delete and emoji buttons.

2.5 **Mobile canvas height.** Source: `minmax(420px,55vh)` (24), `.canvas-wrap{min-height:420px}` (27). Raise to ~65vh and recompute orbit radii from canvas size (see 3.1).

---

## Workstream 3 — Canvas fit and visual quality

3.1 **Fit to canvas (high).** Source: `ORB_R_BASE` up to 345 (746), `orbR = base * sc` with `sc` up to 1.48 (809 to 810). Outer ring reaches ~510px and clips all four edges; it clips even at the default "Scale" level on 1440x900.
   - Derive radii from available size: `fit = min(W,H)/2 - labelMargin; orbR = ORB_R_BASE.map(r => r/345 * fit)`.
   - Recompute on resize. Cap so it never upscales past design size.
   - Acceptance: no orbit clips at 1280x720, 1440x900, 1920x1080, and 414x896.

3.2 **Reserve a header safe zone.** Source: title at top:22 (169), subtitle at top:62 (176); nodes orbit `CY = H/2` (1318), so top nodes collide with the headline at scale.
   - Set `CY = headerH + (H - headerH)/2`; move the `.pr` pulse rings to the same centre.
   - Optional subtle backdrop behind `.canvas-title` for any residual overlap.

3.3 **First-run reveal (the payoff is currently hidden).** Source: boot calls `toggleBeforeSlack()` (1863); all six legend types ship `off` (720), so "With Slack" shows only the bare hub until the user manually toggles.
   - On first switch to With Slack, auto-enable customer/agent/app so a recognisable graph appears immediately.
   - Optional 3-step first-run hint ("1. See the friction. 2. Tap With Slack. 3. Scale it up"), gated on a sessionStorage flag.

3.4 **Label collisions on dense rings.** Source: centered labels at 1721 to 1724, no collision avoidance; 14 employees on a 125px-radius ring overlap.
   - Lowest cost: dark pill behind each label (reuse 1514 to 1515 pattern).
   - Better: hide labels on dense rings, reveal on hover/spotlight (state already tracked).
   - Set `ctx.textAlign='center'` explicitly at 1721 (today it leaks from the emoji branch).

3.5 **Before to With Slack transition.** Source: hard branch at 1574 into a separate renderer, instant cut.
   - Cheap: opacity crossfade on the canvas (~10 lines).
   - High fidelity: shared id-keyed position map; tween each dept card from grid cell to orbit slot over ~600ms. This is the single most persuasive moment; worth the effort.

3.6 **Scenario clarity.** Source: scenes dim non-spotlight to alpha 0.22 (1634) with only a button highlight as feedback.
   - Add a caption near the subtitle ("Active Deal Room: 14 participants across 6 node types"; count = `spotlightIds.size`).
   - Lift dimmed alpha toward 0.30 or desaturate instead of near-hiding.

---

## Workstream 4 — Accessibility

4.1 Raise `--muted` `#6b6b78` (fails AA at ~3.2:1) to ~`#9a9aa8`; reserve the dark grey for borders/dots only. Doubles as projector/exec readability. (15, used at 81/196/206/612.)
4.2 Bump smallest body copy (`.ft` 9px line 200; `.leg-sub`/`.fi`/`.bot-note` 10px) to >=12px.
4.3 Make legend/category toggles real buttons (or role=button + tabindex + Enter/Space + aria-pressed). Source 360 to 389.
4.4 `prefers-reduced-motion`: gate the rAF loop and the pulse/comm intervals; init `paused` from the media query. Hooks already exist via `togglePause`.
4.5 Login inputs need `aria-label`; `#lg-err` needs `role="alert"`; Slackbot toggle needs an accessible name; decorative emoji spans `aria-hidden`.
4.6 Add `:focus-visible` ring (inputs set `outline:none` with only low-contrast tints; buttons keep the native ring so this is input-scoped).

---

## Workstream 5 — Performance and offline reliability

5.1 **Idle the render loop.** Source: `requestAnimationFrame(draw)` is unconditional (1729); only angle integration is paused (1576 to 1588), the full redraw runs at 60fps even when paused or backgrounded.
   - Schedule rAF only while animating; redraw once on state change. Add a `visibilitychange` listener that cancels rAF and the four spawners when hidden.
5.2 **Cache node gradients.** Source: `ctx.createRadialGradient` per node per frame (1636 to 1639), top GC hotspot at ~65 nodes. Pre-build per-type gradients at origin, translate per node.
5.3 **Replace per-pulse `shadowBlur`** (1620 to 1622) with a pre-rendered glow sprite blitted via `drawImage`.
5.4 **Inline all brand logos.** Source: `getLogo` falls back to `cdn.simpleicons.org` then `logo.clearbit.com` (1410 to 1413) at render time, and the slackbot PNG is remote (1404). These blank out on locked-down customer/offline networks.
   - Extend `INLINE_LOGOS` (1381) to cover all ~23 remaining slugs as data-URI SVGs (Simple Icons are MIT). Drop Clearbit (paid third party plus privacy hop). Inline the slackbot sprite. Self-host/inline the Lato font (8).
   - Acceptance: the file renders fully from `file://` with networking disabled.
5.5 Compute each node position once per frame into an id->pos map; reuse for connection lines, pulses, node draw, and the mousemove hit-test (currently recomputed 2x+ per node per frame; 1600/1604/1628/1740).

---

## Workstream 6 — Architecture (V2 enablers, do after Tier 1 to 3)

6.1 **Persist the full config.** Source: only `_slk_company`/`_slk_org` survive (1043/1056); a refresh wipes customNodes, labels, emojis, headcount, hiddenTypes, quality, sbOn, hub, scene.
   - Single serialisable `appState` plus `persist()`/`hydrate()`, debounced on existing mutators. Store the hub logo data URL under its own key with quota guard.
6.2 **Shareable URL state.** No `location.hash`/`URLSearchParams` today.
   - Encode compact non-logo state (hiddenTypes bitmask, quality, sbOn, scene, customNodes, label/emoji overrides, names) into the hash; hydrate on load (precedence over sessionStorage); add a "Copy share link" button by Print/PDF.
   - Enables "send a colleague the Acme version."
6.3 **NODE_TYPES registry.** Source: per-type logic duplicated across 6+ JS functions and 12 hardcoded HTML blocks (legend 360 to 389, sections 465 to 541; plus `NT`, `ORBIT`, `ORB_R_BASE`, `SPD`, `EMOJI_POOLS`, `refreshLegend`, `refreshQSummary`, `refreshInputs`, `refreshStats`).
   - One array of `{key,label,plural,color,glow,stroke,radius,orbit,speed,emojiPool,tooltipLabel,legendSub}`; render legend and configure sections by mapping over it; derive the rest from it. Adding/recoloring a type becomes a one-line edit.
6.4 **Single `state` object** instead of ~21 free `let` globals (717 to 731, 1134 to 1139). Separate raw config from derived render data; route mutations through a small set of mutators.
6.5 **Extract the duplicated Saturn-ring block.** Source: 1642 to 1669 and 1692 to 1719 are byte-for-byte identical except the cull line. One `drawSaturnRings(ctx,pos,r,...,cull)` helper plus a hoisted `SATURN_RINGS` constant.
6.6 Optional: light Vite + ES modules layout that still ships one deployable artifact (data/pools, data/typeRegistry, core/state, core/stats, render/network, render/beforeSlack, ui/configure, export/pdf). Preserves zero-server Vercel deploy.

---

## Security note (decided: keep as welcome screen)

The SHA-256 login gate (286 to 312) is cosmetic: the full app and the credential hash ship in source, and the gate only sets `display:none`. Fine as a welcome screen, but do not label it security and consider dropping the shared hash. If real access control is needed, enforce it at the host layer (Vercel password protection / Cloudflare Access). Two earlier worries were checked and cleared: the footer "everything resets on close" claim is actually true for sessionStorage, and the PDF CORS-tainting fear is a non-issue because `crossOrigin='anonymous'` is already set (1393).

---

## Recommended sequencing

1. Workstream 1 (content/brand) — low effort, removes the only items that could embarrass you in front of an exec.
2. Workstream 2 (touch/mobile) — where most of your audience actually views it.
3. Workstream 3.1 to 3.3 (fit, header safe zone, first-run reveal) — so it never clips and the payoff is immediate.
4. Workstream 6 (architecture) — the foundation that makes every future edit cheap; then 4 and 5 as polish.

## Rejected during verification (not in scope, for the record)
- "Reset is a dead end": false. Reset returns to the populated Before Slack view, not a bare hub.
- "motFor NaN for short ids": false. `charCodeAt` beyond index 0 is guarded with `||0`.
- "PDF CORS tainting": false. `crossOrigin='anonymous'` prevents tainting; logos fail closed to the emoji fallback.
