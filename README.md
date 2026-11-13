# Batch ETA Darts

**What finish time should an async AI batch promise the moment it's submitted?** A Monte Carlo dart board picks the ETA percentile, then a director memo rewrites itself from the result.

- **Live demo:** https://shriyashekatkar25-byte.github.io/batch-eta-darts/
- **APM Lab gallery:** https://shriyashekatkar25-byte.github.io/apm-lab/

Part of **APM Lab**, a series of product-decision prototypes by Shriya Shekatkar (UCLA, Statistics & Data Science minor), built with methods from UCLA Stats 102C, Monte Carlo Methods.

## The launch that inspired it

On November 9, 2026, Databricks release notes listed the **AI Functions Asynchronous API (Beta)**. It runs `ai_parse_document`, `ai_extract` or `ai_classify` asynchronously on up to 50,000 requests from a JSONL file in a Unity Catalog volume, and writes one result per request to a file in a volume.

- Source: [Databricks release notes, November 2026: AI Functions Asynchronous API (Beta)](https://docs.databricks.com/aws/en/release-notes/product/2026/november#ai-functions-asynchronous-api-beta)
- API reference linked from the note: [AI Functions batch API](https://docs.databricks.com/api/ai-functions/v1/batch)

Everything else in this prototype (durations, queue behavior, throttling, prices) is **simulated** and is not a claim about how the real product behaves.

## The PM decision

> When a user submits a batch, which finish time goes on the screen: P50, P75, P90 or P95 of simulated runs? And does the ETA need to read the live queue before it ships?

- **Customer problem:** an ops lead submits 40,000 claim PDFs before a 9 a.m. review. With no ETA she polls and over-plans; with a wrong ETA she loses trust.
- **Proposed change:** a headline ETA (a chosen percentile of simulated runs of *this* batch) plus a smaller "likely by" P50.
- **Success metric:** on-time rate ≥ 90% (batches finishing before the ETA shown at submit).
- **Guardrails:** padding (ETA ÷ median actual) ≤ 1.6; bad surprises P(actual > 1.5 × ETA) ≤ 2%; throttled retries ≤ 1% of requests; cost per 1k requests ≤ +5% vs. no-retry baseline.
- **Stakeholders:** AI Functions engineering and serving capacity, design/docs, field and support, cost-conscious admins.

What the simulation shows (seed 42, 40k requests, 45% parsing, 48 slots): a P90 ETA keeps about 90% of promises in a typical week, but a **queue-blind** ETA keeps only about two-thirds of them in busy hours. Making the forecast read the live queue is the real launch blocker. A P50 headline misses half the time.

## What this shows about product judgment

| Databricks APM skill | Where it shows up |
|---|---|
| Customer problem framing | A specific persona and moment (a submit screen before a deadline), not "add an ETA" |
| Prototyping | A working, seeded simulator with sliders, live KPI badges and an animated dart board |
| SQL / Python | Illustrative SQL to score past promises and a NumPy ETA engine (Board 5) |
| Data-driven decisions | Guardrails with thresholds, a statistical pass rule (target inside the 95% band), and a memo that changes its recommendation when the evidence changes |
| Clear writing | A director memo with decision, evidence, risks, rollout plan, next step and a one-sentence VP summary |

## Methods

| Course idea (Stats 102C, Ch1) | Used for |
|---|---|
| Seeded pseudorandom numbers | Reproducible runs (mulberry32, seed control) |
| Fraction of darts inside = probability; hit count is Binomial | On-time rate and bad-surprise rate |
| Variance shrinks like 1/n; ±1.96·SE band | The live convergence chart |
| Integral = width × average height; expectation = sample mean | Each run's finish time = (N / C) × mean of 240 sampled request durations, plus queue wait and straggler |
| Strong Law of Large Numbers | Why estimates settle as darts accumulate |

## Design baseline

Four pins picked at random from a Pinterest home feed, redrawn from scratch in SVG/CSS. No pin images are used, embedded or linked as images.

- **Hero** ← [“My birthday” by Tanisihaly](https://www.pinterest.com/pin/1073264154971341246/): cream frosting background, blue piped scallop border with pink and yellow pearls, glossy red/yellow/blue/green balloons, red script-style headline, star sprinkles.
- **Cards and buttons** ← [“Cherry Doll” by VOODOO DOLL](https://www.pinterest.com/pin/714242822132261155/): golden cookie-dough cards with chocolate-chip dots, thick chocolate outlines, blush-pink "cheek" buttons.
- **KPI badges** ← [“My heart” (pink heart gem)](https://www.pinterest.com/pin/1078752917011309345/): faceted pink gem gradients with light glints, black calligraphic labels on a pale mist background.
- **Simulation room** ← [“Paid in full” letter by Select Student Loan](https://www.pinterest.com/pin/350295677242390381/): warm grey paper with soft vignette, typewriter monospace, red rubber-stamp "PAID IN FULL / OUTSTANDING" ledger.

## Files

- `index.html`: the whole prototype (single self-contained file; Google Fonts only).
- `README.md`: this write-up.

---

Unofficial concept prototype by Shriya Shekatkar. All data is simulated. Not affiliated with any company.
