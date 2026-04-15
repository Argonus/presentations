# Presentation Improvements: Distilling the Stream

**Date:** 2026-04-14
**Scope:** Structural and content improvements to the 29-slide HTML presentation
**Target:** ~28 slides, 40-45 min slot, Code BEAM / ElixirConf 2026

---

## Speaker Assignment

| Section | Speaker |
|---------|---------|
| Opening + Context (slides 1-5) | Piotr Rybarczyk |
| Pillar 1: DevEx & Velocity | Piotr Rybarczyk |
| Pillar 2: Resilience via Outbox, Inbox & Idempotency | Piotr Rybarczyk |
| Pillar 3: Why Pure Elixir / Let It Crash | Anton Borisov |
| Pillar 4: Infrastructure Optimization | Anton Borisov |
| Closing + Q&A | Both |

Two handoffs: Piotr → Anton at Pillar 3 divider, Anton → Both at closing.

---

## Change 1: Cut "The Postgres Mistake" slide

**Current:** Slide 4 — "We tried to build our own message broker." Postgres as message queue, polling loops, table bloat.

**Action:** Delete this slide entirely.

**Reason:** Interesting history but never pays off — the talk never returns to it. Cutting it tightens the opening from 4 context slides to 3, preserving the hook's momentum.

**Resulting context section:**
1. Title
2. Hook (duplicate notifications incident)
3. What is Kafka (diagram)
4. How the Pieces Fit Together (architecture — was slide 5)
5. Why Does This Matter (scale — was slide 6)

---

## Change 2: Add Kafkaesque to the architecture diagram

**Current:** Slide 5 ("How the Pieces Fit Together") shows: kpro_schema → kayrock → kafka_ex → Client Notifications → Kafka Broker. Kafkaesque is absent.

**Action:** Add a Kafkaesque layer between kafka_ex and Client Notifications. Style it as the most visually prominent layer (it's the main subject of the talk and the thing being open-sourced).

**Layer content:**
- Name: `kafkaesque` (matching the mono-font style of other layers)
- Description: "(framework — plugins, DLQ, observability, health checks, idempotency)"
- Sub-boxes: `Consumer Supervisor`, `Plugin Pipeline`, `Dead Letter Queue`, `Telemetry`
- Color: use a distinct accent — warm accent (amber/gold) with a stronger weight than kafka_ex, or a brighter purple with a thicker border and elevated background. Must be visually distinct from both kafka_ex (amber) and kayrock (purple). The key requirement is that Kafkaesque is the most prominent layer.

**Stack order (top to bottom):**
1. Client Notifications (neutral border, as-is)
2. **Kafkaesque** (new, purple/accent, prominent)
3. kafka_ex (amber, as-is)
4. kayrock (purple, as-is)
5. kpro_schema (green, as-is)

---

## Change 3: Beef up Pillar 3 (Why Pure Elixir / Let It Crash)

**Current:** 2 content slides — comparison matrix + basic `Supervisor.start_link` code.

**Action:** Add 1 new slide between the comparison matrix and the "Let It Crash" code slide: a concrete MSK incident story.

**New slide: "MSK Restarts: The Real Problem"**
- Type: incident slide (amber left border)
- Content:
  - MSK broker restarts during maintenance windows
  - Kaffe consumers left in wrong state after restart — no visibility into what happened
  - Consumer lag growing silently, no alerts because the process was technically "alive" but not consuming
  - With KafkaEx + OTP supervision: consumer process crashes cleanly, supervisor restarts it, reconnects automatically, telemetry reports the restart
- Speaker: Anton Borisov

**Updated "Let It Crash" code slide:**
- Keep the existing `Supervisor.start_link` code
- Add a caption or annotation making the point more concrete: "Kaffe needed special recovery handlers for MSK restarts. We wrote zero recovery code."

**Pillar 3 slide sequence:**
1. Divider — "Why Pure Elixir" (byline: Anton Borisov)
2. Comparison matrix (as-is)
3. **MSK incident** (new)
4. Let It Crash code (enhanced)
5. Takeaway

**Speaker byline:** Anton Borisov (changed from "Piotr Rybarczyk & Anton Borisov")

---

## Change 4: Reframe Pillar 4 (Infrastructure Optimization)

**Current:** 1 content slide listing Kafka ecosystem trends (KRaft, tiered storage, diskless brokers, shared-nothing rebalancing). Generic and not actionable.

**Action:** Add 1 new slide before the existing trends slide: "Jumping Two Major Versions" — the generator/metaprogramming payoff story.

**New slide: "The Payoff: Jumping Two Major Versions"**
- Type: content slide with a before/after + diff breakdown (based on reference image)
- Left side — Before/After:
  - Before: kafka_ex + kayrock on old kpro_schema. Produce V0-V5 only. No flexible versions (KIP-482). No compact types. Stuck on older Kafka clusters.
  - After: updated kpro_schema + generator fixes. Produce V0-V8. Full KIP-482 support. compact_string, compact_array, tagged_fields. Works with latest Kafka.
- Right side — What actually changed:
  - serialize.ex: +60 lines (compact_type functions)
  - deserialize.ex: +50 lines (compact_type parsing)
  - generate.ex: +40 lines (flexible header logic)
  - record_batch.ex: ~0 lines (format unchanged!)
  - produce.ex: regenerated (automatic from schema)
  - Total: ~150 lines of manual changes → hundreds of structs regenerated
- Bottom tagline: "The generator design made a 2-version jump tractable."
- Speaker: Anton Borisov

**Updated existing trends slide:**
- Keep the content (KRaft, tiered storage, diskless brokers, rebalancing)
- Reframe the callout: now it has a concrete lead-in from the version-jumping slide — "the generator design is *why* we can adopt these changes painlessly"

**Pillar 4 slide sequence:**
1. Divider — "Infrastructure Optimization" (byline: Anton Borisov)
2. **Jumping Two Major Versions** (new)
3. Kafka infrastructure trends (existing, reframed)
4. Takeaway

**Speaker byline:** Anton Borisov (changed from "Piotr Rybarczyk & Anton Borisov")

---

## Change 5: Build idempotency two-path diagram

**Current:** Slide 15 ("Domain-Level Idempotency First") has a dashed-border placeholder where a diagram should be.

**Action:** Replace the placeholder with an HTML/CSS two-path diagram:

- **Left path (red):** Message arrives → Naive handler → Side effect executes → Same message arrives again → Side effect executes AGAIN → "Duplicate effect" (red)
- **Right path (green):** Message arrives → Idempotent handler (checks natural key / unique constraint) → Side effect executes → Same message arrives again → Handler detects duplicate → Skips → "Same result" (green)

Style: use `--accent-red` for the naive path, `--accent-green` for the idempotent path. Keep it simple — two vertical flows side by side, connected at the top by "Message arrives twice."

---

## Change 6: Fix 4-card summary layout

**Current:** Summary slide uses CSS class `three-up` (flexbox) with 4 cards. Cards are cramped at ~270px each.

**Action:** Adjust the layout so 4 cards have proper spacing:
- Reduce card padding slightly
- Reduce font sizes in cards by 1-2px
- Ensure the `three-up` flex container accommodates 4 children without overflow
- Test that all card text is fully visible at 1280x720

---

## Change 7: Update speaker bylines on divider slides

| Pillar | Current byline | New byline |
|--------|---------------|------------|
| 1: DevEx & Velocity | Piotr Rybarczyk | Piotr Rybarczyk (no change) |
| 2: Resilience | Anton Borisov | Piotr Rybarczyk |
| 3: Why Pure Elixir | Piotr Rybarczyk & Anton Borisov | Anton Borisov |
| 4: Infrastructure Optimization | Piotr Rybarczyk & Anton Borisov | Anton Borisov |

---

## Resulting Slide Structure (~28 slides)

```
[Title]                              slide 1    — Piotr
[Hook: duplicate notifications]      slide 2    — Piotr
[What is Kafka]                      slide 3    — Piotr
[How the Pieces Fit Together]        slide 4    — Piotr
[Why Does This Matter]               slide 5    — Piotr
[P1 Divider: DevEx]                  slide 6    — Piotr
[P1 Incident: boilerplate chaos]     slide 7    — Piotr
[P1 Before/After code]              slide 8    — Piotr
[P1 One Module]                      slide 9    — Piotr
[P1 Plugin System]                   slide 10   — Piotr
[P1 Takeaway]                        slide 11   — Piotr
[P2 Divider: Resilience]             slide 12   — Piotr
[P2 Incident: duplicates]            slide 13   — Piotr
[P2 Domain Idempotency + diagram]    slide 14   — Piotr
[P2 Inbox: Two Strategies]           slide 15   — Piotr
[P2 Outbox + CDC]                    slide 16   — Piotr
[P2 Takeaway]                        slide 17   — Piotr
[P3 Divider: Why Pure Elixir]        slide 18   — Anton
[P3 Comparison Matrix]               slide 19   — Anton
[P3 MSK Incident]                    slide 20   — Anton (NEW)
[P3 Let It Crash code]               slide 21   — Anton
[P3 Takeaway]                        slide 22   — Anton
[P4 Divider: Infra Optimization]     slide 23   — Anton
[P4 Jumping Two Versions]            slide 24   — Anton (NEW)
[P4 Kafka Infra Trends]              slide 25   — Anton
[P4 Takeaway]                        slide 26   — Anton
[What Changes Monday]                slide 27   — Both
[Summary: 4 cards]                   slide 28   — Both
[Future]                             slide 29   — Both
[CTA + QR codes]                     slide 30   — Both
```

**Net change:** -1 (cut Postgres mistake) +2 (MSK incident, version jump) = +1 slide. 30 slides total. ~1.5 min/slide for a 45-min slot.
