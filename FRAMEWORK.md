# The Four D's: A Working Mental Model for AI Product Management

> Traditional PM delivers a product. **AI PM delivers a product that learns.**

A real AI PM needs two kinds of knowledge, and they are not interchangeable:

1. **A process model** — how you *work*. What decisions do you own, in what order?
2. **A technical literacy map** — what you *understand*. What are the building blocks of an AI system, so you can make informed tradeoffs and have credible conversations with engineers?

Process without technical literacy makes you someone who runs meetings but can't engage on substance. Technical literacy without a process makes you someone who knows jargon but can't drive a product. This framework holds both — and connects them, so you know *when* each piece of technical knowledge becomes a decision you own.

It is organized in three layers:

- **Layer 1 — The Process:** Discover → Define → Deliver → Deepen
- **Layer 2 — The Technical Stack:** the pipeline every AI system flows through
- **Layer 3 — Cross-Cutting Concerns:** true at every stage

---

## Explain it to me like I'm a PM who's new to AI

Here's the whole thing in plain language, the way I'd explain it to a fellow PM over coffee.

**You already know how to ship products.** Discover the problem, define what you're building, deliver it. That muscle still works. AI doesn't throw it out — it adds wrinkles inside each step, and then adds one brand-new step at the end.

**Discover gets one new question: "is this even learnable?"** In normal product work, if users want it and it's feasible to build, you build it. In AI, there's a step before feasibility: does the data actually contain the pattern you're hoping the model will find? If you want to detect closed eyes, is there a measurable signal that separates "closed" from "open"? Sometimes the honest answer is no, and the best thing you can do is kill the idea early. That's a win, not a failure.

**Define is where the real AI work hides.** In normal software, "correct" is whatever the spec says — the logic either matches or it's a bug. In AI, *you have to define what correct even means*, and you define it with examples, not rules. You sit down and label a pile of real cases: this is a blink, this is not, this one's genuinely ambiguous. That labeled pile is your ground truth, and everything downstream depends on it being good. Then — and this is the part normal PM doesn't prepare you for — you accept that the system will never be 100% right. So you decide, up front, as a number, how good is good enough. And you decide which kind of mistake you can live with, because you can't minimize both. Missing a real problem and raising a false alarm are different pains for the user. You pick which one hurts less.

**Deliver looks familiar but you ship it on a leash.** Because the output is a probability, not a certainty, you don't just let it run wild. High-confidence answers can go through automatically; shaky ones get routed to a human. You watch it in production for "drift" — the model slowly getting worse as the real world drifts away from the examples you trained on. Normal monitoring tells you the server is up. AI monitoring tells you the model is still *right*.

**Deepen is the step that doesn't exist in normal PM.** This is the heart of it. A normal product is exactly as good the day you launch as it is a year later, unless you ship new features. An AI product can get *better on its own* — not by magic, but by a loop. The model makes a guess, a human corrects the wrong ones, those corrections become new labeled examples, you retrain, and it improves on exactly the cases your real users bring. Your job is to build that loop and to prove it's working by tracking the score going up over versions. If you can't show the number climbing, the loop isn't real.

**And underneath all of it, one hard-won truth:** the model is usually the least of your problems. When an AI product gives a dumb answer, people blame "the AI." But nine times out of ten the fault is upstream — messy data, a bad labeling decision, the wrong things being retrieved and fed in. The fancy model at the end is just stitching together whatever the pipeline handed it. So when something's wrong, look upstream first. That single habit will make you better at this than most people who've been doing it longer.

That's the whole framework. Four steps you mostly already know, one new step at the end, and one truth about where the real work lives. The diagram below just makes it visual.

---

## Why AI products are different (the three truths underneath everything)

1. **Probabilistic, not deterministic.** Traditional software works or has a bug. AI software is "right 86% of the time," and deciding whether 86% ships is your job.
2. **The intelligence lives upstream of the model.** When an AI system is wrong, the cause is usually the data, the labeling, the embeddings, or the retrieval — not the model. LLMs are only as good as what feeds them.
3. **It improves after launch — without new features.** Corrections from real use flow back and raise performance. Launch is the start, not the end.

---

# Layer 1 — The Process: the Four D's

The first three D's are the model every PM already knows. AI-specific work folds in as sub-parts. The fourth D is what AI adds.

## 1 · Discover — *the problem, and whether AI can solve it*

**Traditional:** Find the user problem worth solving.

**AI adds — Signal Check:** Is there a *learnable signal* in the data? Not every problem that sounds like AI is solvable with AI. Discovering that a problem lacks signal is a successful outcome, not a failure.

> *In practice:* On the support-case study, the discovery question was precise — "can a local system discover distinct customer-observed problems, including small ones, without seeing the answer labels?" That's a signal-check framed as a question, not an assumption.

## 2 · Define — *what "correct" means, and how good is good enough*

**Traditional:** Spec the solution and acceptance criteria.

**AI adds:**
- **Ground truth & data contract** — "correct" is defined by labeled examples a human made, not by logic. If ground truth is wrong, everything downstream is wrong.
- **De-risk** — baseline first; choose the metric that matches *user cost*; set the "good enough" bar as a number before optimizing.

> *In practice:* The support-case project set explicit product priorities before tuning — "preserve small behaviors, don't merge different failures, tolerate fragmentation before over-merging." Those priorities *are* the definition of correct, and they drove the metric choice (clean coverage, not raw accuracy).

## 3 · Deliver — *ship it into real use*

**Traditional:** Build, test, launch, monitor.

**AI adds:**
- **Guardrails** — confidence thresholds, human-in-the-loop for low-confidence cases, graceful degradation toward the safe default.
- **Monitor & observe** — drift detection, tracing, usage metrics. Monitoring tells you something changed; acting on it is the next stage.

> *In practice:* The severity classifier in the support project stayed "advisory and human-reviewed, not connected to alerts." That's a guardrail decision — a probabilistic output kept on a leash until it earns trust.

## 4 · Deepen — *the product learns* ★ the new stage

**Traditional:** No equivalent.

```
predict → user corrects → corrections become labeled data → retrain → measure the gain → repeat
```

- Capture corrections as **structured data**, not just "user clicked no."
- **Measure improvement** — version your metrics (v1: 0.78 → v2: 0.86 → v3: 0.90). If you can't show the gain, the loop isn't real.
- **Prioritize what to label next** with active learning — surface the model's most uncertain cases for human review instead of labeling randomly.

> *In practice:* The photo blink detector embodies this — a correction UI writes labels, one click retrains, F1 is tracked across versions. The product improves from use, not from new features.

---

# Layer 2 — The Technical Stack (PM-literacy level)

Every AI system — text, image, audio — flows through the same pipeline. You don't need to implement these. You need enough fluency to ask the right questions, understand the answers, and make tradeoffs. The right-hand column is the question a PM actually asks.

| Stage | What it is (plain English) | The PM question |
|---|---|---|
| **1. Data & Ingest** | Collect, clean, normalize raw inputs into a consistent form. | Is the data representative of real use? What's missing or biased? |
| **2. Represent (Embeddings)** | Convert data into numeric vectors that capture meaning, so a machine can compare similarity. | Does the embedding capture what *users* think is similar? |
| **3. Index & Cluster** | Organize vectors for fast search, or group them to discover patterns. | How do we search at scale? What groups emerge, and are they meaningful? |
| **4. Detect & Classify** | Spot anomalies or sort items into categories (blink/open, severity level). | What error type costs the user more — false positive or false negative? |
| **5. Retrieve & Reason (RAG)** | Pull the right context and feed it to an LLM to generate a grounded answer. | Is the retrieval returning relevant, complete context? Is the answer cited? |
| **6. Decide & Act** | Turn the output into an action — with guardrails and human approval where needed. | What's automated vs. human-approved? What's the cost of a wrong action? |
| **7. Evaluate & Monitor** | Measure quality (precision/recall/F1), watch for drift, close the feedback loop. | How do we know it works? How do we know it still works tomorrow? |

**The literacy that matters most, from hardest-won experience:**
- **Embeddings** turn meaning into math. Search and clustering quality are capped by embedding quality.
- **Precision vs. recall** is a product decision, not a math decision — pick based on which mistake hurts the user more.
- **Clustering is discovery, not truth.** A cluster is a candidate pattern, not automatically a defect or an incident.
- **The LLM is usually the last and smallest step.** It stitches together what the upstream pipeline produced. If the answer is wrong, look upstream first.

---

# Layer 3 — Cross-Cutting Concerns (true at every stage)

These are not a stage. They apply across all four D's and the entire technical stack. A mature AI PM is accountable for them from Discover through Deepen.

- **Privacy & data governance** — what data is used, where it lives, whether it leaves the device. (Both projects were deliberately local / zero-egress.)
- **Human-in-the-loop** — where a person stays in the decision path, especially for high-impact or low-confidence actions.
- **Cost & latency** — model size, inference cost, response time. The 3B-vs-14B tradeoff is a product decision with real user and budget impact.
- **Observability** — tracing, drift detection, evaluation harnesses that catch regressions before users do.
- **Honesty about limits** — synthetic data isn't production data; a dev-set score isn't a generalization claim. Stating boundaries is a PM credibility signal, not a weakness.

---

## The whole model on one page

```
LAYER 1 — PROCESS (how you work)
  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐
  │ DISCOVER  │─▶│  DEFINE   │─▶│  DELIVER  │─▶│  DEEPEN   │
  │ problem + │  │ ground    │  │ guardrails│  │ feedback  │
  │ signal    │  │ truth +   │  │ + monitor │  │ loop ★    │
  │ check     │  │ de-risk   │  │           │  │           │
  └───────────┘  └───────────┘  └───────────┘  └─────┬─────┘
        ▲                                             │
        └──── corrections become new ground truth ────┘

LAYER 2 — TECHNICAL STACK (what you understand)
  Data → Represent → Index/Cluster → Detect/Classify
       → Retrieve/Reason → Decide/Act → Evaluate/Monitor

LAYER 3 — CROSS-CUTTING (true everywhere)
  Privacy · Human-in-the-loop · Cost/Latency · Observability · Honesty
```

**How the layers connect — the insight most frameworks miss:** each process stage calls on specific parts of the technical stack. Define leans on *Represent* and *Evaluate*. Deliver leans on *Decide/Act*. Deepen leans on *Evaluate* and *Human-in-the-loop*. The process tells you *when* each piece of technical knowledge becomes a decision you own.

---

## The one thing to remember

Traditional PM asks: *did we build the right thing, and does it work?*

AI PM asks that — and then one more question that never stops:

**Is it still getting better?**

---

*This framework was developed through hands-on building across two AI PM studies: a privacy-first photo intelligence system (blink detection with a live feedback loop) and a local support-case discovery system (clustering evaluation without golden labels). Each stage maps to a real decision made and a lesson learned. It is a working mental model, not a theoretical one.*
