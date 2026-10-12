# Design Doc — Dongxu Liu Personal Page

## Design Context

**Subject.** Dongxu Liu, undergraduate in the College of Artificial Intelligence at Jilin University, majoring in Artificial Intelligence (2024 – present). Research interests: LLM, MLLM, Optimization, Spiking Neural Network. Five papers across AAAI 2026, AISTATS 2026, CVPR 2026, and ICML 2026 — two accepted, three under review — all co-authored with Wenkang Wang, Wanli Shi, and Prof. Bin Gu. Course and practice work in neuromorphic LLM deployment, from-scratch machine learning, and SNNs on event-camera data.

**Audience.** Prospective advisors and lab collaborators, senior students and peers, and readers arriving from a paper or a conference. Desktop-first reading, mobile occasional. Chinese and international academic readers; page language stays English (matches the current site).

**Brand personality.** Early-career, technically serious, quietly confident. Should read as "a student with real output and no padding" rather than a founder-style personal brand. Nothing inflated: no invented awards, no invented news, no metrics that do not exist in the source files.

**Aesthetic direction.** *Editorial Grid* — user-selected. A Swiss-modernist 12-column grid carrying an editorial typographic hierarchy: sans-serif display headings, serif reading text, monospace metadata. One restrained accent color used only for structural signals (index numbers, links, active section marker). Flat surfaces, hairline rules, no shadows on content, generous vertical rhythm. Explicitly not a card-wall or glassmorphism page.

### Reference

Reference supplied by the user: the cloned `RuiHuangAI/RuiHuangAI.github.io` (whose own design doc calibrated to `waynejin0918.github.io/home`), plus the 13 page templates in the cloned `XY-Showing/pageclaw`.

Signals extracted from `RuiHuangAI.github.io/index.html` (CSS custom properties, verified):

- **Typography.** `Lato` (sans) for body/UI, `Merriweather` (serif) for headings and paper titles, `ui-monospace` for dates. Body 16px / line-height 1.7.
- **Color temperature.** Cool, near-monochrome, two restrained accents: `--primary: #0056b3`, `--accent: #d82644`. Dark mode flips to `--bg: #0f1115`, `--primary: #5aa9ff`.
- **Spatial density.** Airy. 960px max-width container, ~60px between sections, dashed item separators (`--border-dashed: #e6e6e6`).
- **Surface treatment.** Near-flat. Hairline `--border: #eeeeee`, `border-radius` limited to thumbnails/pills, exactly one avatar elevation (`0 8px 24px rgba(0,0,0,0.12)`).
- **Layout.** Two-column header (bio left, circular photo right), single column for every following section.
- **Animation.** Minimal: hover translate on social pills, `@media (prefers-reduced-motion: reduce)` respected, no scroll animation.
- **Responsive.** Single 768px breakpoint; header collapses to `column-reverse`.

This reference tilts the palette toward cool restraint and the density toward airy. The selected *Editorial Grid* aesthetic governs layout and typographic voice, so it departs from the reference in three deliberate ways: a 12-column grid instead of a single 960px column, sans-serif display headings instead of Merriweather, and numbered index rows instead of image thumbnails (the user's publications have no teaser images).

## Design System

Source: `ui-ux-pro-max` `--design-system` for `"academic personal homepage researcher editorial grid minimal typographic"`, then `--domain typography` and `--domain style` deep-dives. The generator's own style pick (`Vibrant & Block-based`, accent `#EC4899` pink) and its `Playfair Display` heading pick were both **overridden** — see Overrides.

### Tokens

| Token | Light | Dark |
|-------|-------|------|
| `--bg` | `#FAFAF9` | `#0C0D10` |
| `--bg-elev` | `#FFFFFF` | `#14161B` |
| `--text` | `#1C1D21` | `#E7E8EC` |
| `--text-muted` | `#6B6E76` | `#9BA0AB` |
| `--text-faint` | `#8E929B` | `#7C818C` |
| `--rule` | `#E3E3DF` | `#25282F` |
| `--rule-strong` | `#C9C9C4` | `#33373F` |
| `--accent` | `#1E40E0` | `#8AA4FF` |
| `--accent-quiet` | `#EEF1FE` | `#161B33` |
| `--status-accepted` | `#12704A` | `#4FD29B` |
| `--focus` | `#1E40E0` | `#8AA4FF` |

Single accent only. `--status-accepted` is a functional color for the "Poster / accepted" badge, not decoration; it never appears as a link or heading color.

### Typography

Triple stack, 8px base unit, 4px sub-unit.

- **Display / headings:** `Inter` (400, 600, 700) — H1 `clamp(2.75rem, 7vw, 4.5rem)` / 700 / `letter-spacing: -0.03em` / `line-height: 0.95`; H2 `1.5rem` / 650 / `-0.01em`.
- **Body:** `Source Serif 4` (300, 400, 600) — 17px / `line-height: 1.65`. Publication titles 1.0625rem / 600. Abstract-ish descriptions stay at 400.
- **Metadata:** `JetBrains Mono` (400, 500) — 0.6875rem / `letter-spacing: 0.08em` / uppercase for venue tags, status badges, index numbers, and years.
- **Fallbacks:** `Inter, ui-sans-serif, system-ui, -apple-system, 'Segoe UI', sans-serif`; `'Source Serif 4', Georgia, 'Times New Roman', serif`; `'JetBrains Mono', ui-monospace, SFMono-Regular, Menlo, monospace`.
- **Loading:** Google Fonts `@import` with `display=swap`, only the three families and the weights used.

### Aesthetic Implementation

- **Layout structure.** `<main>` is `display: grid; grid-template-columns: repeat(12, minmax(0, 1fr)); column-gap: 24px;` inside a `max-width: 1120px` shell. Every section spans the full 12 columns; section *content* is laid out on sub-grids: H2 sits in columns 1–3 as a sticky section label, content in columns 4–12. Publications and awards are index rows: a 3-column mono index number, a 9-column body, `border-top: 1px solid var(--rule)` per row. Header is asymmetric — name block spanning columns 1–8, avatar anchored to columns 10–12. Below 900px the grid collapses to a single column and the sticky labels unstick.
- **Surface treatment.** Flat. `border-radius: 0` on all containers and rows; `border-radius: 50%` on the avatar and the theme toggle only. No `box-shadow` anywhere. Cards do not exist — the only bounded surface with a background is the `--accent-quiet` news/status pill. Separation is carried by hairlines (`1px solid var(--rule)`) and by whitespace, never by elevation.
- **Typography expression.** Deliberate three-way split: sans display (700, tight tracking, oversized) vs. serif body (300–600, comfortable leading) vs. mono metadata (500, uppercase, wide tracking, small). Weight contrast is 700/400 for headings-vs-body; the mono layer is never larger than 12px and never used for prose.
- **Decorative rules.** Allowed: 1px hairline rules as row separators, a 2px accent underline on the currently-visible section in the top nav, mono index numbers (`01`, `02`, …) in front of publication and award rows, and a single 1px accent rule under the H1. Forbidden: gradients, glassmorphism, shadows, rounded card stacks, icons-as-decoration, emoji, colored section backgrounds, scroll-triggered animation.
- **Spatial rhythm.** Airy but systematic on the 8px unit: `--space-1: 8px`, `--space-2: 16px`, `--space-3: 24px`, `--space-4: 32px`, `--space-6: 48px`, `--space-8: 64px`, `--space-12: 96px`. 96px between top-level sections, 32px between publication rows, 24px between award rows, 16px inside the header block. Section labels align to the same 8px column edges as body content.
- **Signature CSS.**

```css
.shell {
  max-width: 1120px;
  margin-inline: auto;
  padding: 0 clamp(1.25rem, 4vw, 3rem);
}
.grid {
  display: grid;
  grid-template-columns: repeat(12, minmax(0, 1fr));
  column-gap: var(--space-3);
}
.section-label {
  grid-column: 1 / 4;
  position: sticky;
  top: var(--space-3);
  font-family: var(--font-sans);
  font-size: 0.8125rem;
  font-weight: 600;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  color: var(--text-muted);
}
.section-body { grid-column: 4 / -1; }
.row {
  display: grid;
  grid-template-columns: 3rem minmax(0, 1fr);
  column-gap: var(--space-3);
  padding-block: var(--space-4);
  border-top: 1px solid var(--rule);
}
.row-index {
  font-family: var(--font-mono);
  font-size: 0.6875rem;
  font-weight: 500;
  letter-spacing: 0.08em;
  color: var(--text-faint);
  padding-top: 0.35em;
}
```

### Decisions taken during synthesis

- **Index rows instead of thumbnails.** No publication in `_data/publications.yml` has an `image`, `pdf`, `code`, `page`, or `bibtex` value — all are `null`. Teaser thumbnails would be empty boxes, so the row leads with a mono index number and carries venue + status as metadata.
- **Status is text, not color alone.** `Poster` / `accepted` renders as a bordered mono badge with its own label; `Under review` renders as a muted mono badge. Color is redundancy, not the carrier, satisfying `color-not-only`.
- **Sections follow the page-story exactly.** About, Research Interests, Links, Education, Publications, Projects, Selected Awards, Skills. No News section is rendered because no news items exist in any source file; adding dates would mean inventing them.

### Overrides

- **Style: generator said `Vibrant & Block-based` (accent `#EC4899`); overridden to Swiss Modernism 2.0.** Reason: the generator's own `--domain style` result for a Swiss/editorial query recommends `Swiss Modernism 2.0` — 12-column grid, 8px base unit, single accent, no decoration — which matches the user-selected *Editorial Grid* and an academic audience. A vibrant block/pink palette is the wrong register for a research page and would read as a student portfolio.
- **Heading font: generator said `Playfair Display`; overridden to `Inter`.** Reason: the user's chosen aesthetic specifies sans-serif display headings. `Playfair Display` at 900 is a fashion/luxury signal and, in a page whose body is serif, would flatten the display-vs-reading contrast the aesthetic depends on. `Inter` 700 with tight tracking gives the grid its rational, modern voice.
- **Body font kept as `Source Serif 4`.** Retained from the generator — the serif body is the half of the pairing that carries editorial credibility and long-form readability, and it satisfies the user's "serif body" requirement.
- **Mono third layer added (`JetBrains Mono`).** From the generator's triple-stack recommendation; it gives venue strings, years, and index numbers a distinct semantic register instead of competing with body text.
- **Dark mode is required** by the PageClaw build spec. Implemented with CSS custom properties only, toggled by a sun/moon button in the top-right, persisted in `localStorage`, defaulting to `prefers-color-scheme`.
