# Dongxu Liu Academic Homepage Implementation Plan

> **For agentic workers:** Execute this plan task-by-task and use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the minimal-light Jekyll homepage at the repository root with a single self-contained `index.html` implementing the *Editorial Grid* aesthetic described in `docs/plans/dongxu-liu-design.md`.

**Architecture:** One static HTML file with an inline `<style>` block and one inline `<script>` block. No build step, no framework, no CDN CSS. Theme is driven entirely by CSS custom properties, with light values in `:root` and dark values in `html[data-theme="dark"]`, so a `data-theme` attribute flip is the only theme mechanism. Content is transcribed verbatim from `page-story-dongxu-liu.md`; the page-story is the authoritative content source.

**Tech Stack:** HTML5, CSS (custom properties, grid, `clamp()`), vanilla ES5-compatible JavaScript, Google Fonts (`Inter`, `Source Serif 4`, `JetBrains Mono`) loaded via `<link>`, inline SVG for all icons.

## Global Constraints

- Single file: all CSS and JS inline in `index.html`; no external stylesheets or scripts except the Google Fonts `<link>`.
- All colors must be referenced through CSS custom properties; no raw hex in a rule body. The only exceptions are inside the `:root` / `html[data-theme="dark"]` token blocks and the two inline SVG `currentColor` fills.
- `border-radius: 0` on every container; only the avatar and the theme toggle may be `50%`.
- No `box-shadow` anywhere in the stylesheet.
- One accent color (`--accent`). `--status-accepted` is the only other chromatic token and may only decorate status badges.
- The page-story content is rendered faithfully — no invented news, dates, awards, metrics, or paper links. Sections in order: About Me, Research Interests, Links, Education, Publications, Projects, Selected Awards, Skills.
- Interactive elements must define both `:hover` and `:focus-visible` states; transitions 150–250ms; `@media (prefers-reduced-motion: reduce)` disables them.
- No layout shift: the avatar carries explicit `width`/`height` attributes; fonts load with `display=swap`.
- Output `index.html` lives at the repository root; asset paths resolve relative to the repository root.
- Avatar asset: `assets/img/avatar-dongxu.jpg` (already generated, 101 KB, 640×640).

---

### Task 1: Document shell, tokens, and SEO head

**Files:**
- Create: `index.html`
- Delete: nothing yet (old `index.md` is handled in Task 8)

**Interfaces:**
- Consumes: nothing.
- Produces: `index.html` exists with `:root` / `html[data-theme="dark"]` token blocks (names fixed for all later tasks: `--bg`, `--bg-elev`, `--text`, `--text-muted`, `--text-faint`, `--rule`, `--rule-strong`, `--accent`, `--accent-quiet`, `--status-accepted`, `--focus`, `--font-sans`, `--font-serif`, `--font-mono`, `--space-1`…`--space-12`), `.shell`, `.grid`, `.section-label`, `.section-body`, `.row`, `.row-index` classes, and a `<script>` that applies the stored theme before first paint.

- [ ] **Step 1: Write the head and token layer**

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Dongxu Liu — Undergraduate, Jilin University</title>
<meta name="description" content="Dongxu Liu is an undergraduate in the College of Artificial Intelligence at Jilin University working on optimization, spiking neural networks, and large language models.">
<meta name="author" content="Dongxu Liu">
<meta name="keywords" content="Dongxu Liu, Jilin University, spiking neural network, LLM, MLLM, optimization, zeroth-order optimization, SNN">
<link rel="canonical" href="https://ldx825.github.io/liudongxu825.github.io/">
<meta property="og:type" content="profile">
<meta property="og:title" content="Dongxu Liu — Undergraduate, Jilin University">
<meta property="og:description" content="Undergraduate in the College of Artificial Intelligence at Jilin University. Optimization, spiking neural networks, and large language models.">
<meta property="og:url" content="https://ldx825.github.io/liudongxu825.github.io/">
<link rel="icon" href="assets/img/favicon.png" type="image/png">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&family=Source+Serif+4:opsz,wght@8..60,300;8..60,400;8..60,600&family=JetBrains+Mono:wght@400;500&display=swap">
<script>
(function () {
  var stored = null;
  try { stored = localStorage.getItem('theme'); } catch (e) {}
  var theme = stored || (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
  document.documentElement.setAttribute('data-theme', theme);
})();
</script>
<style>
  :root {
    --bg: #FAFAF9; --bg-elev: #FFFFFF;
    --text: #1C1D21; --text-muted: #6B6E76; --text-faint: #8E929B;
    --rule: #E3E3DF; --rule-strong: #C9C9C4;
    --accent: #1E40E0; --accent-quiet: #EEF1FE;
    --status-accepted: #12704A; --focus: #1E40E0;
    --font-sans: Inter, ui-sans-serif, system-ui, -apple-system, 'Segoe UI', sans-serif;
    --font-serif: 'Source Serif 4', Georgia, 'Times New Roman', serif;
    --font-mono: 'JetBrains Mono', ui-monospace, SFMono-Regular, Menlo, monospace;
    --space-1: 8px; --space-2: 16px; --space-3: 24px; --space-4: 32px;
    --space-6: 48px; --space-8: 64px; --space-12: 96px;
  }
  html[data-theme="dark"] {
    --bg: #0C0D10; --bg-elev: #14161B;
    --text: #E7E8EC; --text-muted: #9BA0AB; --text-faint: #7C818C;
    --rule: #25282F; --rule-strong: #33373F;
    --accent: #8AA4FF; --accent-quiet: #161B33;
    --status-accepted: #4FD29B; --focus: #8AA4FF;
  }
</style>
```

- [ ] **Step 2: Verify the theme bootstrap has no FOUC**

Run: open `index.html` in a browser with a dark OS preference and hard-reload five times.
Expected: the first paint is already dark; there is no white flash.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat(home): scaffold single-file homepage with theme tokens"
```

---

### Task 2: Base stylesheet, grid primitives, and page chrome

**Files:**
- Modify: `index.html` (append to the inline `<style>` block)

**Interfaces:**
- Consumes: token names from Task 1.
- Produces: global reset, `.skip`, `.shell`, `.grid`, `.section-label`, `.section-body`, `.row`, `.row-index`, `.mono`, `.badge`, `.badge--accepted`, `.badge--review`, link and focus rules, and the `prefers-reduced-motion` guard used by every later task.

- [ ] **Step 1: Append the primitives**

```css
*, *::before, *::after { box-sizing: border-box; }
html { -webkit-text-size-adjust: 100%; }
body {
  margin: 0; background: var(--bg); color: var(--text);
  font-family: var(--font-serif); font-size: 17px; line-height: 1.65;
  font-weight: 400; -webkit-font-smoothing: antialiased;
}
h1, h2, h3 { font-family: var(--font-sans); margin: 0; }
a { color: var(--accent); text-decoration: none; }
a:hover { text-decoration: underline; text-underline-offset: 3px; }
a:focus-visible, button:focus-visible {
  outline: 2px solid var(--focus); outline-offset: 3px; border-radius: 0;
}
.skip {
  position: absolute; left: -9999px; top: 0; z-index: 10;
  background: var(--bg-elev); color: var(--text);
  padding: var(--space-1) var(--space-2); border: 1px solid var(--rule-strong);
}
.skip:focus { left: var(--space-3); top: var(--space-3); }
.shell { max-width: 1120px; margin-inline: auto; padding-inline: clamp(1.25rem, 4vw, 3rem); }
.grid { display: grid; grid-template-columns: repeat(12, minmax(0, 1fr)); column-gap: var(--space-3); }
.section { padding-block: var(--space-6); border-top: 1px solid var(--rule); }
.section-label {
  grid-column: 1 / 4; align-self: start;
  position: sticky; top: var(--space-3);
  font-family: var(--font-sans); font-size: 0.8125rem; font-weight: 600;
  letter-spacing: 0.14em; text-transform: uppercase; color: var(--text-muted);
}
.section-body { grid-column: 4 / -1; }
.row {
  display: grid; grid-template-columns: 3rem minmax(0, 1fr);
  column-gap: var(--space-3); padding-block: var(--space-4);
  border-top: 1px solid var(--rule);
}
.row:first-of-type { border-top: 0; padding-top: 0; }
.row-index {
  font-family: var(--font-mono); font-size: 0.6875rem; font-weight: 500;
  letter-spacing: 0.08em; color: var(--text-faint); padding-top: 0.4em;
}
.mono { font-family: var(--font-mono); font-size: 0.6875rem; font-weight: 500; letter-spacing: 0.08em; }
.badge {
  display: inline-block; font-family: var(--font-mono); font-size: 0.625rem;
  font-weight: 500; letter-spacing: 0.1em; text-transform: uppercase;
  padding: 3px 7px; border: 1px solid var(--rule-strong); color: var(--text-muted);
}
.badge--accepted { border-color: var(--status-accepted); color: var(--status-accepted); }
.badge--review { border-style: dashed; }
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { transition: none !important; animation: none !important; scroll-behavior: auto !important; }
}
```

- [ ] **Step 2: Verify the grid math**

Run: `index.html` with DevTools open at 1440px. Inspect `.grid`.
Expected: 12 tracks, `24px` column gap, no track narrower than 0, no horizontal scrollbar.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat(home): add editorial grid primitives and focus styles"
```

---

### Task 3: Top bar, name block, and avatar

**Files:**
- Modify: `index.html` (append to `<style>`, then insert `<body>` markup)

**Interfaces:**
- Consumes: `.shell`, `.grid`, token names.
- Produces: `<header class="masthead">`, `#theme-toggle` button (wired in Task 7), and `<img class="avatar" src="assets/img/avatar-dongxu.jpg" width="640" height="640" alt="Portrait of Dongxu Liu">`.

- [ ] **Step 1: Add masthead styles**

```css
.masthead {
  display: flex; align-items: center; justify-content: space-between;
  gap: var(--space-3); padding-block: var(--space-3);
  border-bottom: 1px solid var(--rule);
  font-family: var(--font-sans); font-size: 0.8125rem;
}
.masthead__name { font-weight: 700; letter-spacing: -0.01em; }
.masthead__nav { display: flex; gap: var(--space-3); }
.masthead__nav a { color: var(--text-muted); position: relative; padding-bottom: 3px; }
.masthead__nav a:hover { color: var(--accent); text-decoration: none; }
.masthead__nav a[aria-current="true"] {
  color: var(--text);
  box-shadow: inset 0 -2px 0 0 var(--accent);
}
.hero {
  display: grid; grid-template-columns: repeat(12, minmax(0, 1fr));
  column-gap: var(--space-3); row-gap: var(--space-4);
  padding-block: var(--space-8) var(--space-6);
}
.hero__text { grid-column: 1 / 9; }
.hero__photo { grid-column: 10 / -1; justify-self: end; }
.hero h1 {
  font-size: clamp(2.75rem, 7vw, 4.5rem); font-weight: 700;
  letter-spacing: -0.03em; line-height: 0.95; margin-bottom: var(--space-3);
}
.hero__rule { width: 64px; height: 1px; background: var(--accent); border: 0; margin: 0 0 var(--space-3); }
.hero__role { font-family: var(--font-sans); font-size: 0.9375rem; color: var(--text-muted); }
.avatar { width: clamp(140px, 22vw, 208px); height: auto; border-radius: 50%; display: block; }
#theme-toggle {
  width: 34px; height: 34px; display: grid; place-items: center;
  background: transparent; border: 1px solid var(--rule-strong);
  border-radius: 50%; color: var(--text-muted); cursor: pointer;
  transition: color 180ms ease, border-color 180ms ease;
}
#theme-toggle:hover { color: var(--accent); border-color: var(--accent); }
```

- [ ] **Step 2: Insert the markup**

```html
<body>
<a class="skip" href="#main">Skip to content</a>
<header class="shell">
  <div class="masthead">
    <span class="masthead__name">Dongxu Liu</span>
    <nav class="masthead__nav" aria-label="Sections">
      <a href="#about">About</a>
      <a href="#publications">Publications</a>
      <a href="#projects">Projects</a>
      <a href="#awards">Awards</a>
    </nav>
    <button id="theme-toggle" type="button" aria-label="Switch to dark theme" aria-pressed="false"></button>
  </div>
  <div class="hero">
    <div class="hero__text">
      <h1>Dongxu Liu</h1>
      <hr class="hero__rule">
      <p class="hero__role">Undergraduate Student · College of Artificial Intelligence, Jilin University</p>
    </div>
    <div class="hero__photo">
      <img class="avatar" src="assets/img/avatar-dongxu.jpg" width="640" height="640" alt="Portrait of Dongxu Liu">
    </div>
  </div>
</header>
```

- [ ] **Step 3: Verify**

Run: open at 375px and 1440px.
Expected: photo sits right of the name on desktop; no horizontal scroll at either width; `Avatar` has non-zero layout box before the image bytes arrive.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat(home): add masthead, hero name block, and avatar"
```

---

### Task 4: About, Research Interests, and Links sections

**Files:**
- Modify: `index.html` (append to `<style>`, then insert inside `<main id="main">`)

**Interfaces:**
- Consumes: `.section`, `.section-label`, `.section-body`.
- Produces: `.interest-list`, `.link-list`, `.link-list svg` used by Task 5's footer only (no other consumer).

- [ ] **Step 1: Add styles**

```css
.interest-list { list-style: none; margin: 0; padding: 0; display: flex; flex-wrap: wrap; gap: var(--space-1) var(--space-2); }
.interest-list li {
  font-family: var(--font-mono); font-size: 0.75rem; font-weight: 500;
  letter-spacing: 0.06em; text-transform: uppercase;
  padding: 5px 10px; border: 1px solid var(--rule-strong); color: var(--text-muted);
}
.lede { font-size: 1.125rem; margin: 0 0 var(--space-4); }
.link-list { list-style: none; margin: 0; padding: 0; display: flex; flex-wrap: wrap; gap: var(--space-2); }
.link-list a {
  display: inline-flex; align-items: center; gap: 7px;
  font-family: var(--font-sans); font-size: 0.8125rem; font-weight: 600;
  padding: 7px 12px; border: 1px solid var(--rule-strong); color: var(--text);
  transition: border-color 180ms ease, color 180ms ease, background-color 180ms ease;
}
.link-list a:hover { border-color: var(--accent); color: var(--accent); background: var(--accent-quiet); text-decoration: none; }
.link-list svg { width: 15px; height: 15px; fill: currentColor; flex: none; }
```

- [ ] **Step 2: Insert the markup** (icon SVG paths are inline; `Email` uses an envelope path, `CV` a document path, `Google Scholar` and `GitHub` use Simple Icons paths)

```html
<main id="main" class="shell">
  <section class="section grid" id="about">
    <h2 class="section-label">About</h2>
    <div class="section-body">
      <p class="lede">I am currently a second-year undergraduate student in <a href="https://sai.jlu.edu.cn/">the College of Artificial Intelligence at Jilin University</a>, majoring in Artificial Intelligence.</p>
      <ul class="link-list">
        <li><a href="mailto:liudx9924@mails.jlu.edu.cn"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M2 5.5A2.5 2.5 0 0 1 4.5 3h15A2.5 2.5 0 0 1 22 5.5v13a2.5 2.5 0 0 1-2.5 2.5h-15A2.5 2.5 0 0 1 2 18.5v-13Zm2.7-.5 7.3 5.4L19.3 5H4.7ZM20 6.9l-7.4 5.5a1 1 0 0 1-1.2 0L4 6.9V19h16V6.9Z"/></svg>Email</a></li>
        <li><a href="https://scholar.google.com/citations?hl=en&amp;user=053eRGQAAAAJ"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 24a7 7 0 1 1 0-14 7 7 0 0 1 0 14Zm0-4a3 3 0 1 0 0-6 3 3 0 0 0 0 6Zm-1.6-10.4L4.2 5.5 12 1l7.8 4.5-6.2 4.1a8.7 8.7 0 0 0-3.2 0Z"/></svg>Google Scholar</a></li>
        <li><a href="https://github.com/ldx825"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 .3a12 12 0 0 0-3.8 23.4c.6.1.8-.3.8-.6v-2c-3.3.7-4-1.6-4-1.6-.6-1.4-1.4-1.8-1.4-1.8-1-.7.1-.7.1-.7 1.2.1 1.8 1.2 1.8 1.2 1 1.8 2.8 1.3 3.5 1 .1-.8.4-1.3.7-1.6-2.7-.3-5.5-1.3-5.5-6 0-1.2.5-2.3 1.2-3.1-.1-.3-.5-1.5.1-3.2 0 0 1-.3 3.3 1.2a11.5 11.5 0 0 1 6 0C17.3 4.7 18.3 5 18.3 5c.6 1.7.2 2.9.1 3.2.8.8 1.2 1.9 1.2 3.1 0 4.7-2.8 5.7-5.5 6 .4.4.8 1.1.8 2.2v3.3c0 .3.2.7.8.6A12 12 0 0 0 12 .3Z"/></svg>GitHub</a></li>
        <li><a href="assets/files/ldx_26_3_1_Academic_CV_Template.pdf"><svg viewBox="0 0 24 24" aria-hidden="true"><path d="M6 2h8l6 6v12a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2Zm7 1.5V9h5.5L13 3.5ZM8 13h8v1.5H8V13Zm0 4h8v1.5H8V17Z"/></svg>CV</a></li>
      </ul>
    </div>
  </section>

  <section class="section grid" id="interests">
    <h2 class="section-label">Research Interests</h2>
    <div class="section-body">
      <ul class="interest-list">
        <li>LLM</li><li>MLLM</li><li>Optimization</li><li>Spiking Neural Network</li>
      </ul>
    </div>
  </section>
```

- [ ] **Step 3: Verify**

Run: keyboard-tab through the four link pills.
Expected: each shows a 2px accent outline with 3px offset; icons scale with text and inherit the hover color.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat(home): add about, research interests, and profile links"
```

---

### Task 5: Education, Publications, Projects, Awards, Skills

**Files:**
- Modify: `index.html` (append to `<style>`, then insert inside `<main>`)

**Interfaces:**
- Consumes: `.row`, `.row-index`, `.badge`, `.badge--accepted`, `.badge--review`, `.mono`.
- Produces: nothing consumed by later tasks.

- [ ] **Step 1: Add styles**

```css
.entry-title { font-family: var(--font-serif); font-size: 1.0625rem; font-weight: 600; line-height: 1.45; margin: 0 0 var(--space-1); }
.entry-authors { margin: 0 0 var(--space-1); color: var(--text-muted); font-size: 0.9375rem; }
.entry-authors strong { color: var(--text); font-weight: 600; }
.entry-meta { display: flex; flex-wrap: wrap; align-items: center; gap: var(--space-1) var(--space-2); }
.entry-venue { font-family: var(--font-mono); font-size: 0.6875rem; font-weight: 500; letter-spacing: 0.08em; text-transform: uppercase; color: var(--text-muted); }
.entry-note { margin: var(--space-1) 0 0; font-size: 0.9375rem; }
.entry-year { font-family: var(--font-mono); font-weight: 500; color: var(--text-muted); }
.skills { list-style: none; margin: 0; padding: 0; display: flex; flex-wrap: wrap; gap: var(--space-2); font-family: var(--font-sans); font-size: 0.875rem; color: var(--text-muted); }
.skills li { padding-right: var(--space-2); border-right: 1px solid var(--rule); }
.skills li:last-child { border-right: 0; padding-right: 0; }
```

- [ ] **Step 2: Insert Education**

```html
  <section class="section grid" id="education">
    <h2 class="section-label">Education</h2>
    <div class="section-body">
      <div class="row">
        <span class="row-index">01</span>
        <div>
          <p class="entry-title">Jilin University, College of Artificial Intelligence</p>
          <p class="entry-authors">B.Eng. in Artificial Intelligence</p>
          <p class="entry-meta"><span class="entry-year">2024 – Present</span></p>
        </div>
      </div>
    </div>
  </section>
```

- [ ] **Step 3: Insert Publications** (five rows in `_data/publications.yml` order; status comes from each entry's `notes` field, and `Under Review` maps to the dashed review badge)

```html
  <section class="section grid" id="publications">
    <h2 class="section-label">Publications</h2>
    <div class="section-body">
      <div class="row">
        <span class="row-index">01</span>
        <div>
          <p class="entry-title">Towards Nonlinear Sparse AUC Maximization via Compositional Stochastic Hard Thresholding</p>
          <p class="entry-authors">Wenkang Wang, <strong>Dongxu Liu</strong>, Bin Gu*</p>
          <p class="entry-meta"><span class="entry-venue">AAAI 2026</span><span class="badge badge--accepted">Poster</span></p>
        </div>
      </div>
      <!-- rows 02–05 follow the same structure: AISTATS 2026 (Poster),
           CVPR 2026 (Under review), ICML 2026 (Under review) ×2 -->
    </div>
  </section>
```

- [ ] **Step 4: Insert Projects, Selected Awards, Skills**

```html
  <section class="section grid" id="projects">
    <h2 class="section-label">Projects</h2>
    <div class="section-body">
      <div class="row"><span class="row-index">01</span><div>
        <p class="entry-title">Neuromorphic LLM Deployment</p>
        <p class="entry-note">Deploying spiking large language models on neuromorphic hardware, focusing on model spiking, operator mapping, on-chip inference, and algorithm–hardware co-design.</p>
      </div></div>
      <!-- 02 Machine Learning from Scratch, 03 Spiking Neural Networks on Event-Camera Datasets -->
    </div>
  </section>

  <section class="section grid" id="awards">
    <h2 class="section-label">Selected Awards</h2>
    <div class="section-body">
      <div class="row"><span class="row-index">01</span><div>
        <p class="entry-title">Jilin University First-Class Scholarship</p>
        <p class="entry-meta"><span class="entry-year">2025</span></p>
      </div></div>
      <!-- 02 Jilin University Excellent Student, 03 Second Prize, Jilin Province College Students Mathematics Competition -->
    </div>
  </section>

  <section class="section grid" id="skills">
    <h2 class="section-label">Skills</h2>
    <div class="section-body">
      <ul class="skills"><li>Python</li><li>PyTorch</li><li>C</li><li>C++</li><li>Linux</li><li>Git</li><li>tmux</li></ul>
    </div>
  </section>
</main>
```

- [ ] **Step 5: Verify content fidelity**

Run: compare every rendered string against `page-story-dongxu-liu.md` section by section.
Expected: identical author lists and paper titles; every publication carries exactly one status badge; no `null` leaks into the DOM.

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "feat(home): add education, publications, projects, awards, skills"
```

---

### Task 6: Footer

**Files:**
- Modify: `index.html` (append to `<style>`, insert after `</main>`)

**Interfaces:**
- Consumes: `.shell`, `.mono`.
- Produces: nothing consumed by later tasks.

- [ ] **Step 1: Add footer markup**

```html
<footer class="shell">
  <div class="footer">
    <span class="mono">© 2026 Dongxu Liu</span>
    <a class="mono" href="https://github.com/ldx825/liudongxu825.github.io">Source</a>
  </div>
</footer>
```

- [ ] **Step 2: Add footer styles**

```css
.footer {
  display: flex; justify-content: space-between; align-items: center; gap: var(--space-2);
  padding-block: var(--space-6) var(--space-8); border-top: 1px solid var(--rule);
  color: var(--text-faint);
}
.footer a { color: var(--text-faint); }
.footer a:hover { color: var(--accent); }
```

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat(home): add footer"
```

---

### Task 7: Theme toggle wiring

**Files:**
- Modify: `index.html` (insert one `<script>` before `</body>`)

**Interfaces:**
- Consumes: `#theme-toggle`, `html[data-theme]`, `localStorage` key `"theme"` established in Task 1.
- Produces: nothing consumed by later tasks.

- [ ] **Step 1: Add the script**

```html
<script>
(function () {
  var root = document.documentElement;
  var button = document.getElementById('theme-toggle');
  var SUN = '<svg viewBox="0 0 24 24" width="15" height="15" aria-hidden="true"><path fill="currentColor" d="M12 17a5 5 0 1 1 0-10 5 5 0 0 1 0 10Zm0 2.2v2.3h0v-2.3Zm0-18.5V3h0V.7ZM22.3 12h-2.3H22.3ZM4 12H1.7 4Zm15.3-7.3-1.6 1.6 1.6-1.6ZM6.3 17.7l-1.6 1.6 1.6-1.6ZM19.3 19.3l-1.6-1.6 1.6 1.6ZM6.3 6.3 4.7 4.7l1.6 1.6Z"/></svg>';
  var MOON = '<svg viewBox="0 0 24 24" width="15" height="15" aria-hidden="true"><path fill="currentColor" d="M12.9 2.2a1 1 0 0 0-1.2 1.3 7 7 0 0 1-8.9 8.9 1 1 0 0 0-1.3 1.2 10 10 0 1 0 11.4-11.4Z"/></svg>';

  function render() {
    var dark = root.getAttribute('data-theme') === 'dark';
    button.innerHTML = dark ? SUN : MOON;
    button.setAttribute('aria-label', dark ? 'Switch to light theme' : 'Switch to dark theme');
    button.setAttribute('aria-pressed', String(dark));
  }

  button.addEventListener('click', function () {
    var next = root.getAttribute('data-theme') === 'dark' ? 'light' : 'dark';
    root.setAttribute('data-theme', next);
    try { localStorage.setItem('theme', next); } catch (e) {}
    render();
  });

  render();
})();
</script>
```

- [ ] **Step 2: Verify persistence and default**

Run: toggle to dark, reload (stays dark); clear `localStorage`, reload with OS dark (starts dark); reload with OS light (starts light).
Expected: all three behave as described, and the icon matches the active theme in every case.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat(home): wire light/dark theme toggle with persistence"
```

---

### Task 8: Responsive, active-section nav, and retirement of the Jekyll homepage

**Files:**
- Modify: `index.html` (append media queries and a small `IntersectionObserver`)
- Modify: `index.md` → move to `_archive/index.md`

**Interfaces:**
- Consumes: `.grid`, `.section-label`, `.section-body`, `.hero`, `.masthead`.
- Produces: final deliverable.

- [ ] **Step 1: Add breakpoints**

```css
@media (max-width: 900px) {
  .grid { grid-template-columns: minmax(0, 1fr); row-gap: var(--space-2); }
  .section-label { position: static; grid-column: 1 / -1; }
  .section-body { grid-column: 1 / -1; }
  .hero__text { grid-column: 1 / -1; }
  .hero__photo { grid-column: 1 / -1; justify-self: start; order: -1; }
}
@media (max-width: 640px) {
  body { font-size: 16px; }
  .masthead { flex-wrap: wrap; }
  .masthead__nav { order: 3; width: 100%; gap: var(--space-2); font-size: 0.75rem; }
  .row { grid-template-columns: 2rem minmax(0, 1fr); column-gap: var(--space-2); padding-block: var(--space-3); }
  .hero { padding-block: var(--space-6) var(--space-4); }
}
```

- [ ] **Step 2: Add the active-section marker script and finish the page**

```html
<script>
(function () {
  var links = Array.prototype.slice.call(document.querySelectorAll('.masthead__nav a'));
  var map = {};
  links.forEach(function (link) {
    var target = document.querySelector(link.getAttribute('href'));
    if (target) { map[target.id] = link; }
  });
  if (!('IntersectionObserver' in window)) { return; }
  var observer = new IntersectionObserver(function (entries) {
    entries.forEach(function (entry) {
      var link = map[entry.target.id];
      if (!link) { return; }
      if (entry.isIntersecting) {
        links.forEach(function (l) { l.removeAttribute('aria-current'); });
        link.setAttribute('aria-current', 'true');
      }
    });
  }, { rootMargin: '-45% 0px -50% 0px' });
  Object.keys(map).forEach(function (id) { observer.observe(document.getElementById(id)); });
})();
</script>
</body>
</html>
```

- [ ] **Step 3: Archive the Jekyll homepage**

Jekyll renders `index.md` to the same destination as a root `index.html`, which is a conflict. Move the old homepage out of Jekyll's processing scope (directories starting with `_` are ignored by Jekyll) so the original content stays in the repository.

```bash
git mv index.md _archive/index.md
```

- [ ] **Step 4: Verify the site builds as a static page**

Run: `git status --short` and a local static server (`python -m http.server 8000`) then open `http://localhost:8000/`.
Expected: `/` serves the new page; `/index.md` is not reachable as a rendered page; no broken asset requests in the Network tab except the Google Fonts request.

- [ ] **Step 5: Commit**

```bash
git add index.html _archive/index.md
git commit -m "feat(home): responsive breakpoints, active-section nav, archive Jekyll homepage"
```

---

### Task 9: Quality pass

**Files:**
- Modify: `index.html` (fixes arising from the pass only)

**Interfaces:**
- Consumes: the finished page.
- Produces: the audited deliverable.

- [ ] **Step 1: Run the `audit` checklist**

Verify each: contrast ≥ 4.5:1 for body text and ≥ 3:1 for large display text in **both** themes; every interactive element reachable by `Tab`; `alt` on the avatar; `aria-label` on the theme toggle; sequential heading order `h1 → h2 → h3`; no emoji used as an icon; no `box-shadow`; no horizontal scroll at 375px and 1200px.

- [ ] **Step 2: Run the `polish` checklist**

Verify each: optical alignment of `.section-label` against `.section-body`; consistent 8px rhythm; hover states on every link, pill, badge-bearing row, and button; no orphaned single-word lines in the H1 at 375px; dark-mode hairline `--rule` is visible but not heavy; `prefers-reduced-motion` confirmed by emulating it in DevTools.

- [ ] **Step 3: Check performance**

Run: DevTools Network tab on a cold load.
Expected: one HTML document, one Google Fonts CSS request, three font files, one 101 KB avatar image; no render-blocking JS.

- [ ] **Step 4: Fix whatever the three checklists surface, then commit**

```bash
git add index.html
git commit -m "fix(home): quality pass — contrast, focus states, spacing rhythm"
```
