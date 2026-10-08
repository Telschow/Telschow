# Daniel Telschow

**Technical leader for autonomous systems and applied AI.** Munich.

I build products where a wrong answer has a cost: navigation that has to know how wrong it is, and AI agents that must not act without a human saying yes. My focus is making that kind of system measurable, auditable and shippable, and leading the engineers who build it.

[LinkedIn](https://linkedin.com/in/dtelschow) · [Featured work](#featured-work) · [How I lead](#how-i-lead-technical-work)

---

## Featured work

### [contested-nav](https://github.com/Telschow/contested-nav): is the filter right about how wrong it is?

A 21-state error-state Kalman filter (ESKF), fault detection (FDIR) and an evaluation harness for **GNSS-denied navigation**. It scores not just accuracy but whether the filter's reported uncertainty is honest.

| GNSS denied | Position error (ATE) | Claimed 1σ | Mean NEES (ideal 3) |
|---|---:|---:|---:|
| Vision off | 3.782 m | 0.567 m | 4.1 |
| Vision on | 2.541 m | 0.161 m | 419.4 |

Vision made the position better and the filter wildly overconfident. **The decision was to ship it off by default**, recorded in an [ADR](https://github.com/Telschow/contested-nav/blob/main/docs/adr/0003-ship-visual-disabled.md), with the bar for turning it on written down (mean NEES below 10, 2σ coverage above 90%). The negative result held across 10 noise seeds and 40 outage runs.

* 900+ tests, 92.9% line coverage, 12 architecture decision records, golden-snapshot benchmark, CI on 3 Python versions and 3 operating systems, CodeQL and secret scanning
* Every number in its README is generated, and CI fails if a table cell disagrees with the code
* Synthetic data only, with explicit scope and responsible-use limits for a dual-use domain. It states plainly that it is not field validated and not flight-ready.

`ESKF` · `GNSS-denied navigation` · `FDIR / spoofing detection` · `NEES / calibration` · `Python` · `reproducible evaluation`

### [personal-ai](https://github.com/Telschow/personal-ai): a local-first agent platform with a hard approval boundary

Ingests private documents into SQLite, retrieves evidence with provenance, and lets a local LLM call tools **only inside a policy engine** that returns `ALLOWED`, `DENIED` or `APPROVAL_REQUIRED`. Enforcement is in code, not in the prompt.

* Hybrid retrieval (BM25 + embeddings + RRF) with a measured eval gate in CI: recall@5 of 0.48 keyword vs 0.80 semantic on 64 labelled questions, and I say in the README why that set is a regression gate and not a real-world claim
* Agent evaluation that replays scripted model turns through the real loop, including a model that obeys an injected instruction, and fails CI if the approval boundary weakens
* Governed memory (scope, confidence, provenance, conflict handling), OpenAI-compatible API, MCP bridge, threat model
* 3,100+ hermetic tests, no network, no real personal data in the repo

`RAG` · `agent safety & evals` · `policy engine` · `human-in-the-loop` · `MCP` · `Ollama` · `Python`

---

## How I lead technical work

* **Decisions get written down.** Trade-offs go into ADRs with an exit test, so a team can disagree with a decision for the right reasons months later.
* **Claims are earned by measurement.** Numbers in docs are generated and checked in CI. A negative result stays in the headline.
* **AI-assisted engineering with gates, not trust.** Much of my code is written with AI coding assistants, and I say so in the READMEs. What keeps it correct is verification: property tests, finite-difference checks on Jacobians, golden snapshots, coverage ratchets and security scans. I set scope and priorities and decide what ships.
* **Safety and scope are part of the design.** Human-on-the-loop by default, documented non-goals, and a clear line on what a system must not do.
* **Product and engineering together.** I work from the problem and the decision it supports down to the implementation, not the other way round.

## Where I fit

Tech lead or staff-level roles in **autonomous driving, robotics, defense-tech and applied AI**, especially on:

* State estimation, sensor fusion, and degraded-sensor behaviour (GNSS denial, spoofing, fault handling)
* Evaluation, safety cases and uncertainty for systems that decide on their own
* Agent and LLM systems that need auditability, policy control and human oversight
* Turning research-grade prototypes into reproducible, testable products

## Stack

**Languages:** Python (primary), MATLAB
**Estimation & robotics:** ESKF, IMU/GNSS/visual fusion, FDIR, NEES/NIS consistency checks
**AI:** RAG, hybrid retrieval, agent loops and tool use, evaluation harnesses, Ollama, MCP
**Engineering:** pytest, property-based testing, GitHub Actions, CodeQL, uv, Docker, MkDocs, ADRs

## Connect

<a href="https://linkedin.com/in/dtelschow">LinkedIn</a> · <a href="https://github.com/Telschow">GitHub</a>
