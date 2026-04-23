# Presentation Design: Distilling the Stream — Kafka-Backed Elixir at Scale

**Date:** 2026-02-28
**Conference:** Code BEAM / ElixirConf
**Slot:** 40–45 min
**Format:** Static code slides
**Speakers:** Piotr + 1 colleague from Fresha (co-presented)

---

## Key Takeaways (what the audience leaves with)

1. Inbox/Outbox + idempotency patterns are how you build resilient event-driven systems — and Elixir makes them elegant
2. Kafkaesque (and its patterns) are ready to adopt in your own Elixir services

---

## Audience

Mixed room: Elixir-familiar, not Kafka experts. Introduce Kafka concepts via the stack and incidents — no dedicated Kafka 101 section.

---

## Narrative Approach

**Three Pillars (Approach 3)** as the outer structure, with **incident-driven narrative (Approach 2)** as the engine inside each pillar.

Each pillar follows the same internal rhythm: **incident → lesson → solution → Elixir code → takeaway**

---

## Slide Structure (~20 slides, ~2 min each)

```
[Title + Hook]              ~3 min   (2 slides)
[Stack Overview]            ~3 min   (2 slides)
[Pillar 1: DevEx]           ~11 min  (5 slides)
[Pillar 2: Resilience]      ~11 min  (5 slides)
[Pillar 3: Why Pure Elixir] ~9 min   (4 slides)
[Closing + CTA]             ~4 min   (2 slides)
─────────────────────────────────────────────────
Total: ~41 min  /  20 slides
```

---

## Speaker Split

| Section | Speaker |
|---------|---------|
| Title + Hook | Piotr |
| Stack Overview | Piotr |
| Pillar 1: DevEx & Velocity | Piotr |
| Pillar 2: Resilience | Colleague |
| Pillar 3: Why Pure Elixir | Colleague (or shared) |
| Closing + CTA | Both |

---

## Opening (slides 1–2)

**Do NOT open with "Hi I'm Piotr and today I'll talk about..."**

Open with a specific incident — a number or a consequence that makes the audience lean in. Example framing: *"We once processed the same event multiple times in production. The customer was very confused."* Then: *"This talk is about how we fixed that. And everything else."*

Slide 2: Brief orientation — Fresha, Kafka at scale, three libraries in the stack.

---

## Stack Overview (slides 3–4)

Two slides to orient the audience without turning into a library tour:

- **Kayrock** — pure Elixir Kafka protocol implementation (binary encoding/decoding, follows the Kafka API spec)
- **KafkaEx** — Kafka client and consumer group management built on Kayrock
- **Kafkaesque** — Fresha's framework on top of KafkaEx: observability, health checks, consumer supervisors, producers, dead letter queues, decoders, plugin system, idempotency

Frame it as a stack, not a product catalog. The rest of the talk lives at the Kafkaesque layer — the lower layers are "how we got here."

---

## Pillar 1: DevEx & Velocity (slides 5–9)

**Speaker: Piotr**

### Incident slide
Engineers copy-pasting the same consumer boilerplate across 20+ services. Each slightly different. No standards. A new joiner asks "how do I consume a Kafka topic?" and gets 5 different answers.

### Solution slides
- **Before/after code:** raw `kafka_ex` setup (~15 lines of supervision boilerplate) vs `use Kafkaesque.ConsumerSupervisor` (~5 lines)
- **Plugin system:** add observability, tracing, dead-letter-queue as plugins — not bespoke code in every service. Topics config for per-topic behavior.
- **Standardization at scale:** same pattern applied across the company → new engineers productive faster, incidents easier to diagnose

### Takeaway slide
Convention over configuration. The best boilerplate is the boilerplate you write once.

---

## Pillar 2: Resilience via Inbox/Outbox & Idempotency (slides 10–14)

**Speaker: Colleague**

### Incident slide
Same event processed multiple times due to consumer rebalancing. Real production consequences (duplicate bookings, double charges, or similar). This is not a Fresha bug — it's how Kafka works: at-least-once delivery.

### Solution slides
- **Why it happens:** brief, one diagram — Kafka at-least-once delivery + consumer rebalancing
- **Domain-level idempotency first:** design business logic so processing the same event twice has no side effect. Natural keys, unique constraints. Infrastructure patterns help but don't guarantee it.
- **Inbox pattern:** `Kafkaesque.Inbox` with Ecto strategy — persists processed message UUIDs, skips duplicates. Code slide showing the strategy in use. Mention: partition pruning gotcha (PgPartman maintenance), Redis strategy for high-throughput scenarios.

### Takeaway slide
Idempotency is not an afterthought. Design for it from day one.

---

## Pillar 3: Why Pure Elixir (slides 15–18)

**Speaker: Colleague (or shared)**

### Decision slide
The landscape a few years ago: Broadway existed but wasn't a full framework for what Fresha needed. Brod was solid under the hood — but it's Erlang, and pre-LLM, that gap mattered for a team writing Elixir day-to-day. Kaffe had issues with MSK (Amazon Managed Streaming for Kafka) restarts.

### The MSK problem slide
MSK restarts are hard on Kafka clients. Kaffe struggled. KafkaEx, built on OTP supervision and "let it crash", handled it naturally — consumer dies, supervisor restarts it, it reconnects. No special recovery code needed. This was the decisive moment.

### Code slide
Show the ConsumerSupervisor supervision tree. The point: this isn't clever engineering — it's OTP doing what OTP does. The Elixir community's "let it crash" philosophy maps directly to how you want a Kafka consumer to behave under broker instability.

### Takeaway slide
We didn't choose pure Elixir for ideological reasons. We chose it because "let it crash" solved a real production problem, and the team could read and own the code.

---

## Closing & CTA (slides 19–20)

### Summary slide
Three punchy lines — one per pillar. Designed to be photographed.

1. **DevEx:** Write the boilerplate once. Let your team focus on business logic.
2. **Resilience:** Idempotency is a design decision, not a library feature.
3. **Elixir:** OTP supervision isn't a trick — it's a production strategy.

### CTA + Q&A slide
- Kafkaesque is being open-sourced — watch the repo
- The patterns work even if you don't use the library
- `[link to kafkaesque repo]`
- Speaker names + handles

---

## What NOT to do

- Don't open with speaker bios
- Don't do a Kafka 101 detour — introduce concepts inline via incidents
- Don't turn the stack overview into a library feature tour
- Don't use live demos (coordination risk with two speakers)
- Don't end without the open-source announcement — it's the natural payoff

---

## Next Step

Invoke `writing-plans` skill to create a detailed implementation plan for the HTML slide deck.
