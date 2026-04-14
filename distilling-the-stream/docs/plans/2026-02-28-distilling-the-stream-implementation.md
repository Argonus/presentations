# Distilling the Stream: Kafka-Backed Elixir at Scale — Slides Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a 20-slide HTML presentation for Code BEAM / ElixirConf covering Fresha's Kafka-backed Elixir stack (Kayrock → KafkaEx → Kafkaesque) across three pillars: DevEx, Resilience, and Why Pure Elixir.

**Architecture:** Single HTML file with each slide as `<div class="slide" id="slide-N">`. Embedded CSS (no external deps). Speaker notes as HTML comments. Static code blocks — no JS. PowerPoint-transfer friendly.

**Tech Stack:** HTML5, embedded CSS, no JavaScript, no external fonts/CDNs

**Design doc:** `docs/plans/2026-02-28-kafka-presentation-design.md`

**Output file:** `distilling-the-stream.html`

---

## Color Palette & Theme

- Background (dark slides): `#0F1117`
- Primary accent: `#6C4BF6` (purple)
- Secondary accent: `#F6A623` (amber — used sparingly for highlights)
- Text primary: `#F0F0F0`
- Text secondary: `#A0A0B0`
- Code background: `#1A1B2E`
- Code keyword: `#C792EA`
- Code string: `#C3E88D`
- Code comment: `#546E7A`
- Code function: `#82AAFF`
- Code number: `#F78C6C`
- Section divider background: `#6C4BF6` (full purple)
- Light slide background: `#F7F7FB` with dark text (for takeaway slides)

---

### Task 1: HTML Shell + CSS Theme

**Files:**
- Create: `distilling-the-stream.html`

**Step 1: Create the file with full CSS theme and empty slide containers**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Distilling the Stream: Kafka-Backed Elixir at Scale</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background: #111;
    }

    .slide {
      width: 1280px;
      height: 720px;
      position: relative;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      justify-content: center;
      padding: 60px 80px;
      margin: 0 auto 4px auto;
      background: #0F1117;
      color: #F0F0F0;
      page-break-after: always;
    }

    /* Slide number */
    .slide-number {
      position: absolute;
      bottom: 24px;
      right: 40px;
      font-size: 13px;
      color: #555;
    }

    /* Footer */
    .slide-footer {
      position: absolute;
      bottom: 24px;
      left: 40px;
      font-size: 13px;
      color: #555;
      letter-spacing: 0.05em;
    }

    /* Title slide */
    .slide.title-slide {
      background: linear-gradient(135deg, #0F1117 60%, #1a1240);
      justify-content: center;
    }

    .slide.title-slide h1 {
      font-size: 54px;
      font-weight: 800;
      line-height: 1.1;
      color: #F0F0F0;
      max-width: 800px;
    }

    .slide.title-slide h1 span {
      color: #6C4BF6;
    }

    .slide.title-slide .subtitle {
      font-size: 24px;
      color: #A0A0B0;
      margin-top: 20px;
    }

    .slide.title-slide .speakers {
      font-size: 18px;
      color: #6C4BF6;
      margin-top: 40px;
      font-weight: 600;
    }

    .slide.title-slide .conference {
      font-size: 15px;
      color: #555;
      margin-top: 8px;
    }

    /* Section divider */
    .slide.divider {
      background: #6C4BF6;
      align-items: flex-start;
      justify-content: flex-end;
      padding-bottom: 80px;
    }

    .slide.divider .pillar-number {
      font-size: 160px;
      font-weight: 900;
      color: rgba(255,255,255,0.1);
      position: absolute;
      top: -20px;
      right: 60px;
      line-height: 1;
    }

    .slide.divider h2 {
      font-size: 52px;
      font-weight: 800;
      color: #fff;
      line-height: 1.1;
      max-width: 700px;
    }

    .slide.divider .divider-sub {
      font-size: 20px;
      color: rgba(255,255,255,0.7);
      margin-top: 16px;
    }

    /* Content slide */
    .slide h2 {
      font-size: 38px;
      font-weight: 700;
      color: #F0F0F0;
      margin-bottom: 32px;
      line-height: 1.2;
    }

    .slide h2 .accent {
      color: #6C4BF6;
    }

    .slide p {
      font-size: 22px;
      color: #C0C0D0;
      line-height: 1.6;
      max-width: 1000px;
    }

    .slide ul {
      list-style: none;
      margin-top: 8px;
    }

    .slide ul li {
      font-size: 22px;
      color: #C0C0D0;
      line-height: 1.5;
      padding: 10px 0;
      padding-left: 28px;
      position: relative;
    }

    .slide ul li::before {
      content: '→';
      position: absolute;
      left: 0;
      color: #6C4BF6;
    }

    /* Incident slide */
    .slide.incident {
      background: #0F1117;
      border-left: 6px solid #F6A623;
    }

    .slide.incident .incident-label {
      font-size: 13px;
      font-weight: 700;
      letter-spacing: 0.15em;
      color: #F6A623;
      text-transform: uppercase;
      margin-bottom: 20px;
    }

    .slide.incident h2 {
      font-size: 36px;
      color: #F0F0F0;
    }

    .slide.incident blockquote {
      font-size: 26px;
      color: #E0E0E0;
      font-style: italic;
      border-left: none;
      margin-top: 24px;
      line-height: 1.5;
    }

    /* Code slide */
    .slide.code-slide {
      padding: 40px 60px;
    }

    .slide.code-slide h2 {
      font-size: 28px;
      margin-bottom: 20px;
    }

    pre {
      background: #1A1B2E;
      border-radius: 8px;
      padding: 28px 32px;
      overflow: hidden;
      font-family: 'Fira Code', 'Source Code Pro', 'Courier New', monospace;
      font-size: 17px;
      line-height: 1.65;
      color: #C0C0D0;
    }

    .kw  { color: #C792EA; } /* keyword */
    .fn  { color: #82AAFF; } /* function */
    .st  { color: #C3E88D; } /* string */
    .cm  { color: #546E7A; font-style: italic; } /* comment */
    .nm  { color: #F78C6C; } /* number/atom */
    .hl  { background: rgba(108,75,246,0.25); border-radius: 3px; padding: 0 4px; } /* highlight */
    .op  { color: #89DDFF; } /* operator */

    /* Side-by-side code */
    .code-columns {
      display: flex;
      gap: 24px;
    }

    .code-col {
      flex: 1;
    }

    .code-col h3 {
      font-size: 16px;
      color: #A0A0B0;
      margin-bottom: 10px;
      font-weight: 600;
      letter-spacing: 0.05em;
      text-transform: uppercase;
    }

    .code-col pre {
      font-size: 14px;
    }

    /* Takeaway slide */
    .slide.takeaway {
      background: #F7F7FB;
      color: #1a1a2e;
    }

    .slide.takeaway h2 {
      color: #1a1a2e;
      font-size: 32px;
    }

    .slide.takeaway .takeaway-text {
      font-size: 32px;
      font-weight: 700;
      color: #6C4BF6;
      margin-top: 20px;
      line-height: 1.4;
      max-width: 900px;
    }

    .slide.takeaway p {
      color: #444;
    }

    /* Stack diagram */
    .stack-layers {
      display: flex;
      flex-direction: column;
      gap: 12px;
      margin-top: 16px;
    }

    .stack-layer {
      background: #1A1B2E;
      border-radius: 8px;
      padding: 18px 28px;
      display: flex;
      align-items: center;
      gap: 20px;
      border-left: 4px solid #6C4BF6;
    }

    .stack-layer.dim {
      border-left-color: #333;
      opacity: 0.6;
    }

    .stack-layer h3 {
      font-size: 20px;
      font-weight: 700;
      color: #F0F0F0;
      min-width: 140px;
    }

    .stack-layer p {
      font-size: 17px;
      color: #A0A0B0;
    }

    /* Three-up summary */
    .three-up {
      display: flex;
      gap: 24px;
      margin-top: 24px;
    }

    .summary-card {
      flex: 1;
      background: #1A1B2E;
      border-radius: 10px;
      padding: 28px 24px;
      border-top: 4px solid #6C4BF6;
    }

    .summary-card .card-num {
      font-size: 13px;
      font-weight: 700;
      letter-spacing: 0.15em;
      color: #6C4BF6;
      text-transform: uppercase;
      margin-bottom: 12px;
    }

    .summary-card h3 {
      font-size: 20px;
      font-weight: 700;
      color: #F0F0F0;
      margin-bottom: 12px;
      line-height: 1.3;
    }

    .summary-card p {
      font-size: 16px;
      color: #A0A0B0;
      line-height: 1.5;
    }

    /* CTA slide */
    .slide.cta {
      background: linear-gradient(135deg, #0F1117 50%, #1a1240);
    }

    .cta-items {
      margin-top: 28px;
      display: flex;
      flex-direction: column;
      gap: 16px;
    }

    .cta-item {
      font-size: 22px;
      color: #C0C0D0;
      padding-left: 28px;
      position: relative;
      line-height: 1.4;
    }

    .cta-item::before {
      content: '▸';
      position: absolute;
      left: 0;
      color: #6C4BF6;
    }

    .cta-item strong {
      color: #F0F0F0;
    }

    .cta-repo {
      margin-top: 36px;
      font-size: 20px;
      color: #6C4BF6;
      font-family: 'Fira Code', monospace;
    }

    .speakers-row {
      margin-top: 40px;
      display: flex;
      gap: 48px;
    }

    .speaker {
      font-size: 18px;
      color: #A0A0B0;
    }

    .speaker strong {
      display: block;
      color: #F0F0F0;
      font-size: 20px;
    }
  </style>
</head>
<body>
  <!-- slides go here, added in subsequent tasks -->
</body>
</html>
```

**Step 2: Open in browser and verify the CSS loads without errors**

Open `distilling-the-stream.html` in a browser. Expect: blank dark page, no console errors.

**Step 3: Commit**

```bash
git add distilling-the-stream.html
git commit -m "feat: presentation shell + CSS theme"
```

---

### Task 2: Opening Slides (slides 1–2)

**Files:**
- Modify: `distilling-the-stream.html` — add slides inside `<body>`

**Step 1: Add slide 1 — Title**

```html
<!-- SLIDE 1: Title -->
<div class="slide title-slide" id="slide-1">
  <h1>Distilling<br><span>the Stream</span></h1>
  <p class="subtitle">Kafka-Backed Elixir at Scale</p>
  <p class="speakers">Piotr Radacki &amp; [Colleague Name] — Fresha</p>
  <p class="conference">Code BEAM / ElixirConf 2026</p>
  <div class="slide-number">1 / 20</div>
  <!-- SPEAKER: No intro. Go straight to slide 2 after a beat. -->
</div>
```

**Step 2: Add slide 2 — Hook / Opening Incident**

```html
<!-- SLIDE 2: Hook -->
<div class="slide incident" id="slide-2">
  <div class="incident-label">Production. 2 AM.</div>
  <h2>We processed the same event<br>multiple times.</h2>
  <blockquote>"The customer was very confused."</blockquote>
  <p style="margin-top: 32px; font-size: 19px; color: #888;">
    This talk is about how we fixed that.<br>And everything else.
  </p>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">2 / 20</div>
  <!-- SPEAKER: Let the quote breathe. Pause after "very confused." Then: "This talk is about how we fixed that. And everything else." -->
</div>
```

**Step 3: Open in browser, verify slides render at correct dimensions**

Both slides should be 1280×720px, readable, no overflow.

**Step 4: Commit**

```bash
git add distilling-the-stream.html
git commit -m "feat: add title + hook slides"
```

---

### Task 3: Stack Overview Slides (slides 3–4)

**Step 1: Add slide 3 — The Stack**

```html
<!-- SLIDE 3: Stack Overview -->
<div class="slide" id="slide-3">
  <h2>Three Libraries. <span class="accent">One Stack.</span></h2>
  <div class="stack-layers">
    <div class="stack-layer">
      <h3>Kafkaesque</h3>
      <p>Framework: consumers, producers, observability, health checks, idempotency, DLQ, plugins</p>
    </div>
    <div class="stack-layer dim">
      <h3>KafkaEx</h3>
      <p>Kafka client + consumer group management — pure Elixir</p>
    </div>
    <div class="stack-layer dim">
      <h3>Kayrock</h3>
      <p>Kafka protocol implementation — binary encoding/decoding, follows the Kafka API spec</p>
    </div>
  </div>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">3 / 20</div>
  <!-- SPEAKER: Bottom-up stack. We live at the top — Kafkaesque. The lower layers are "how we got here." All three are open source. -->
</div>
```

**Step 2: Add slide 4 — Scale Context**

```html
<!-- SLIDE 4: Scale / Context -->
<div class="slide" id="slide-4">
  <h2>Why Does This Matter?</h2>
  <ul>
    <li>Fresha: global marketplace for salons &amp; spas</li>
    <li>Dozens of Elixir services, all producing and consuming Kafka events</li>
    <li>Every service used to roll its own consumer — 20+ different ways</li>
    <li>Today: one framework, one pattern, consistent across the org</li>
  </ul>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">4 / 20</div>
  <!-- SPEAKER: Set the stakes. This isn't a toy project — it's production infrastructure for a real marketplace at scale. -->
</div>
```

**Step 3: Open in browser, verify stack diagram and list render correctly**

**Step 4: Commit**

```bash
git add distilling-the-stream.html
git commit -m "feat: add stack overview slides"
```

---

### Task 4: Pillar 1 — DevEx & Velocity (slides 5–9)

**Step 1: Add slide 5 — Pillar 1 Section Divider**

```html
<!-- SLIDE 5: Pillar 1 Divider -->
<div class="slide divider" id="slide-5">
  <div class="pillar-number">1</div>
  <h2>DevEx &amp;<br>Velocity</h2>
  <p class="divider-sub">Convention over configuration. At scale.</p>
  <div class="slide-number">5 / 20</div>
  <!-- SPEAKER: Piotr takes over here. -->
</div>
```

**Step 2: Add slide 6 — Incident: The Copy-Paste Problem**

```html
<!-- SLIDE 6: Incident - boilerplate -->
<div class="slide incident" id="slide-6">
  <div class="incident-label">The Problem</div>
  <h2>How do I consume a Kafka topic?</h2>
  <ul style="margin-top: 24px;">
    <li>20+ services. Each one slightly different.</li>
    <li>No shared patterns. No shared monitoring.</li>
    <li>New engineer onboarding: "just look at how X service does it"</li>
    <li>Production incident: "which version of the consumer loop is this?"</li>
  </ul>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">6 / 20</div>
  <!-- SPEAKER: This is painful to maintain. When something goes wrong at 2 AM, you're reading five different consumer implementations to figure out which one applies here. -->
</div>
```

**Step 3: Add slide 7 — Before / After Code**

```html
<!-- SLIDE 7: Before / After -->
<div class="slide code-slide" id="slide-7">
  <h2>Before <span class="accent">→</span> After</h2>
  <div class="code-columns">
    <div class="code-col">
      <h3>Before (raw KafkaEx)</h3>
      <pre><span class="kw">defmodule</span> <span class="fn">MyApp.ConsumerSupervisor</span> <span class="kw">do</span>
  <span class="kw">use</span> Supervisor

  <span class="kw">def</span> <span class="fn">start_link</span>(opts) <span class="kw">do</span>
    Supervisor.start_link(<span class="nm">__MODULE__</span>, opts)
  <span class="kw">end</span>

  <span class="kw">def</span> <span class="fn">init</span>(_opts) <span class="kw">do</span>
    children <span class="op">=</span> [
      <span class="cm"># 15 more lines of boilerplate...</span>
      {KafkaEx.ConsumerGroup, [
        group: <span class="st">"my-group"</span>,
        topics: [<span class="st">"my-topic"</span>],
        handler: <span class="fn">MyHandler</span>
      ]}
    ]
    Supervisor.init(children, strategy: <span class="nm">:one_for_one</span>)
  <span class="kw">end</span>
<span class="kw">end</span></pre>
    </div>
    <div class="code-col">
      <h3>After (Kafkaesque)</h3>
      <pre><span class="kw">defmodule</span> <span class="fn">MyApp.ConsumerSupervisor</span> <span class="kw">do</span>
  <span class="kw">use</span> <span class="hl">Kafkaesque.ConsumerSupervisor</span>,
    consumer_group_identifier:
      Application.compile_env!(
        <span class="nm">:myapp</span>, <span class="nm">:consumer_group</span>),
    topics:
      Application.compile_env!(
        <span class="nm">:myapp</span>, <span class="nm">:topics</span>),
    message_handler: <span class="fn">MyApp.Handler</span>
<span class="kw">end</span>

<span class="cm"># Add to your supervision tree. Done.</span></pre>
    </div>
  </div>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">7 / 20</div>
  <!-- SPEAKER: The after version is what every engineer writes. The before version is what they no longer have to understand. -->
</div>
```

**Step 4: Add slide 8 — The Plugin System**

```html
<!-- SLIDE 8: Plugin System -->
<div class="slide code-slide" id="slide-8">
  <h2>Observability, <span class="accent">Free</span></h2>
  <pre><span class="kw">defmodule</span> <span class="fn">MyApp.ConsumerSupervisor</span> <span class="kw">do</span>
  <span class="kw">use</span> Kafkaesque.ConsumerSupervisor,
    consumer_group_identifier: ...,
    topics: ...,
    message_handler: <span class="fn">MyApp.Handler</span>,
    <span class="hl">topics_config: %{</span>
    <span class="hl">  "payments" =&gt; %{</span>
    <span class="hl">    plugins: [Kafkaesque.Plugins.Tracing,</span>
    <span class="hl">              Kafkaesque.Plugins.DeadLetterQueue]</span>
    <span class="hl">  }</span>
    <span class="hl">}</span>
<span class="kw">end</span></pre>
  <p style="margin-top: 20px; font-size: 18px; color: #A0A0B0;">
    Tracing, DLQ, custom decoders — as plugins. No bespoke code in every service.
  </p>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">8 / 20</div>
</div>
```

**Step 5: Add slide 9 — Pillar 1 Takeaway**

```html
<!-- SLIDE 9: Pillar 1 Takeaway -->
<div class="slide takeaway" id="slide-9">
  <h2>Pillar 1 Takeaway</h2>
  <p class="takeaway-text">Write the boilerplate once.<br>Let your team focus on business logic.</p>
  <p style="margin-top: 28px; font-size: 19px; color: #666;">
    Convention over configuration is not just a Rails idea.<br>
    It's how you scale a team's Kafka knowledge across an org.
  </p>
  <div class="slide-number">9 / 20</div>
</div>
```

**Step 6: Open in browser, verify all 5 slides in this section render correctly**

Pay attention to: code column alignment, highlight spans, before/after labels.

**Step 7: Commit**

```bash
git add distilling-the-stream.html
git commit -m "feat: add Pillar 1 DevEx slides"
```

---

### Task 5: Pillar 2 — Resilience & Idempotency (slides 10–14)

**Step 1: Add slide 10 — Pillar 2 Section Divider**

```html
<!-- SLIDE 10: Pillar 2 Divider -->
<div class="slide divider" id="slide-10">
  <div class="pillar-number">2</div>
  <h2>Resilience via<br>Inbox &amp; Idempotency</h2>
  <p class="divider-sub">At-least-once delivery is a feature. Duplicates are not.</p>
  <div class="slide-number">10 / 20</div>
  <!-- SPEAKER: Colleague takes over here. -->
</div>
```

**Step 2: Add slide 11 — Incident: The Duplicate Event**

```html
<!-- SLIDE 11: Incident - duplicates -->
<div class="slide incident" id="slide-11">
  <div class="incident-label">The Problem</div>
  <h2>Same event. Processed twice.<br>Real consequences.</h2>
  <ul style="margin-top: 24px;">
    <li>Consumer rebalancing mid-batch → message redelivered</li>
    <li>Broker restart → offset commit window missed</li>
    <li>Network blip → consumer assumed dead, group reassigned</li>
  </ul>
  <p style="margin-top: 28px; font-size: 18px; color: #888;">
    This is not a bug. This is Kafka. At-least-once delivery is the contract.
  </p>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">11 / 20</div>
  <!-- SPEAKER: The point here is: you cannot fix this at the broker level. You fix it at the application level. -->
</div>
```

**Step 3: Add slide 12 — Domain Idempotency First**

```html
<!-- SLIDE 12: Domain idempotency -->
<div class="slide" id="slide-12">
  <h2>Rule #1: <span class="accent">Domain-Level Idempotency First</span></h2>
  <ul>
    <li>Design business logic so processing the same event twice has no side effect</li>
    <li>Use natural keys + unique constraints in your domain</li>
    <li>Example: before creating a log entry, check if it already exists for that event ID</li>
  </ul>
  <div style="margin-top: 36px; background: #1A1B2E; border-radius: 8px; padding: 20px 28px; border-left: 4px solid #F6A623;">
    <p style="font-size: 19px; color: #F6A623; font-weight: 700;">Infrastructure patterns help. They do not guarantee idempotency.</p>
    <p style="font-size: 17px; color: #A0A0B0; margin-top: 8px;">Idempotency is a design decision, not a library feature.</p>
  </div>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">12 / 20</div>
</div>
```

**Step 4: Add slide 13 — Inbox Pattern Code**

```html
<!-- SLIDE 13: Inbox pattern code -->
<div class="slide code-slide" id="slide-13">
  <h2>When You Need It: <span class="accent">The Inbox Pattern</span></h2>
  <pre><span class="kw">defmodule</span> <span class="fn">MyApp.Handler</span> <span class="kw">do</span>
  <span class="kw">use</span> Kafkaesque.MessageHandler

  <span class="cm"># Wrap processing in idempotency check</span>
  <span class="kw">def</span> <span class="fn">handle_message</span>(message) <span class="kw">do</span>
    <span class="hl">Kafkaesque.Inbox.process(message,</span>
    <span class="hl">  strategy: Kafkaesque.Inbox.EctoStrategy,</span>
    <span class="hl">  fn -&gt;</span>
      do_the_work(message)
    <span class="hl">end)</span>
  <span class="kw">end</span>
<span class="kw">end</span>

<span class="cm"># EctoStrategy: persists processed message UUIDs</span>
<span class="cm"># Skips duplicates. Uses your existing DB.</span>
<span class="cm"># Redis strategy available for high-throughput scenarios.</span></pre>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">13 / 20</div>
  <!-- SPEAKER: This is not magic. It's a UUID check against a DB table. The key is it's extracted from every service into one place. Mention PgPartman for partition maintenance. -->
</div>
```

**Step 5: Add slide 14 — Pillar 2 Takeaway**

```html
<!-- SLIDE 14: Pillar 2 Takeaway -->
<div class="slide takeaway" id="slide-14">
  <h2>Pillar 2 Takeaway</h2>
  <p class="takeaway-text">Idempotency is not an afterthought.<br>Design for it from day one.</p>
  <p style="margin-top: 28px; font-size: 19px; color: #666;">
    Kafka guarantees at-least-once delivery.<br>
    Your application must guarantee at-most-once processing.
  </p>
  <div class="slide-number">14 / 20</div>
</div>
```

**Step 6: Open in browser, verify all 5 slides render correctly**

**Step 7: Commit**

```bash
git add distilling-the-stream.html
git commit -m "feat: add Pillar 2 Resilience slides"
```

---

### Task 6: Pillar 3 — Why Pure Elixir (slides 15–18)

**Step 1: Add slide 15 — Pillar 3 Section Divider**

```html
<!-- SLIDE 15: Pillar 3 Divider -->
<div class="slide divider" id="slide-15">
  <div class="pillar-number">3</div>
  <h2>Why Pure<br>Elixir</h2>
  <p class="divider-sub">A real decision. With real reasons.</p>
  <div class="slide-number">15 / 20</div>
</div>
```

**Step 2: Add slide 16 — The Decision**

```html
<!-- SLIDE 16: Why not Broadway / Brod / Kaffe -->
<div class="slide" id="slide-16">
  <h2>The Landscape (a few years ago)</h2>
  <ul>
    <li><strong style="color: #F0F0F0;">Broadway</strong> — great for concurrency control, not a full framework for what we needed</li>
    <li><strong style="color: #F0F0F0;">Brod</strong> — solid protocol implementation, but Erlang; pre-LLM, that gap mattered for our team</li>
    <li><strong style="color: #F0F0F0;">Kaffe</strong> — struggled with MSK (Amazon Managed Kafka) broker restarts</li>
    <li><strong style="color: #6C4BF6;">KafkaEx</strong> — pure Elixir, team could read and own the code</li>
  </ul>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">16 / 20</div>
  <!-- SPEAKER: This was not an ideological choice. We evaluated real options against a real production problem. -->
</div>
```

**Step 3: Add slide 17 — MSK Restarts + OTP**

```html
<!-- SLIDE 17: Let it crash = MSK restarts solved -->
<div class="slide code-slide" id="slide-17">
  <h2>MSK Restarts: <span class="accent">Let It Crash</span></h2>
  <pre><span class="cm"># When the broker goes away...</span>
<span class="cm"># KafkaEx consumer crashes. Supervisor restarts it.</span>
<span class="cm"># It reconnects. No special recovery code.</span>

<span class="kw">defmodule</span> <span class="fn">MyApp.Application</span> <span class="kw">do</span>
  <span class="kw">use</span> Application

  <span class="kw">def</span> <span class="fn">start</span>(_type, _args) <span class="kw">do</span>
    children <span class="op">=</span> [
      <span class="hl">MyApp.ConsumerSupervisor</span>  <span class="cm"># ← restarts on crash</span>
    ]
    Supervisor.start_link(children,
      strategy: <span class="nm">:one_for_one</span>)
  <span class="kw">end</span>
<span class="kw">end</span>

<span class="cm"># Kaffe needed special handling for MSK restarts.</span>
<span class="cm"># OTP supervision gave us this for free.</span></pre>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">17 / 20</div>
  <!-- SPEAKER: This is not clever engineering. This is OTP doing what OTP does. The supervisor is the recovery strategy. -->
</div>
```

**Step 4: Add slide 18 — Pillar 3 Takeaway**

```html
<!-- SLIDE 18: Pillar 3 Takeaway -->
<div class="slide takeaway" id="slide-18">
  <h2>Pillar 3 Takeaway</h2>
  <p class="takeaway-text">We chose pure Elixir because<br>"let it crash" solved a real problem.</p>
  <p style="margin-top: 28px; font-size: 19px; color: #666;">
    OTP supervision is not a trick. It's a production strategy.<br>
    And the team could read and own every line.
  </p>
  <div class="slide-number">18 / 20</div>
</div>
```

**Step 5: Open in browser, verify all 4 slides render correctly**

**Step 6: Commit**

```bash
git add distilling-the-stream.html
git commit -m "feat: add Pillar 3 Pure Elixir slides"
```

---

### Task 7: Closing Slides (slides 19–20)

**Step 1: Add slide 19 — Summary**

```html
<!-- SLIDE 19: Summary -->
<div class="slide" id="slide-19">
  <h2>Three Things We Learned</h2>
  <div class="three-up">
    <div class="summary-card">
      <div class="card-num">Pillar 1</div>
      <h3>Write the boilerplate once.</h3>
      <p>Let your team focus on business logic. Convention over configuration scales.</p>
    </div>
    <div class="summary-card">
      <div class="card-num">Pillar 2</div>
      <h3>Idempotency is a design decision.</h3>
      <p>Not a library feature. Domain-level first. Infrastructure patterns second.</p>
    </div>
    <div class="summary-card">
      <div class="card-num">Pillar 3</div>
      <h3>OTP supervision is a strategy.</h3>
      <p>"Let it crash" solved a real production problem. Not ideology — engineering.</p>
    </div>
  </div>
  <div class="slide-footer">Distilling the Stream</div>
  <div class="slide-number">19 / 20</div>
  <!-- SPEAKER: Photograph this slide. These are the three things worth keeping. -->
</div>
```

**Step 2: Add slide 20 — CTA + Q&A**

```html
<!-- SLIDE 20: CTA + Q&A -->
<div class="slide cta" id="slide-20">
  <h2>Take These Home</h2>
  <div class="cta-items">
    <div class="cta-item">Design for idempotency <strong>from day one</strong></div>
    <div class="cta-item">Let OTP do the recovery work — <strong>don't fight it</strong></div>
    <div class="cta-item">Kafkaesque is being <strong>open-sourced</strong> — watch the repo</div>
  </div>
  <div class="cta-repo">github.com/[org]/kafkaesque</div>
  <div class="speakers-row">
    <div class="speaker">
      <strong>Piotr Radacki</strong>
      @[handle]
    </div>
    <div class="speaker">
      <strong>[Colleague Name]</strong>
      @[handle]
    </div>
  </div>
  <div class="slide-number">20 / 20</div>
  <!-- SPEAKER: Open floor for questions. Both speakers on stage. -->
</div>
```

**Step 3: Replace the `[org]`, `[handle]`, and `[Colleague Name]` placeholders with real values before the talk**

**Step 4: Open in browser and do a full review pass — all 20 slides**

Check:
- Correct slide count (20 total)
- Each slide has one clear idea
- Code examples are syntactically correct Elixir
- Speaker notes (HTML comments) are present on complex slides
- Slide numbers are sequential
- Footer appears on content slides
- No text overflow on any slide

**Step 5: Final commit**

```bash
git add distilling-the-stream.html
git commit -m "feat: add closing slides, complete 20-slide deck"
```

---

## Placeholders to Fill In Before the Talk

| Placeholder | Where | What to put |
|------------|-------|-------------|
| `[Colleague Name]` | Slides 1, 20 | Co-presenter's full name |
| `@[handle]` | Slide 20 | Both speakers' social handles |
| `github.com/[org]/kafkaesque` | Slide 20 | Actual repo URL once open-sourced |
| `Code BEAM / ElixirConf 2026` | Slide 1 | Actual conference name + year |

---

## Execution Options

Plan complete and saved to `docs/plans/2026-02-28-distilling-the-stream-implementation.md`.

**Two execution options:**

**1. Subagent-Driven (this session)** — dispatch fresh subagent per task, review between tasks, fast iteration

**2. Parallel Session (separate)** — open new session with executing-plans, batch execution with checkpoints

Which approach?
