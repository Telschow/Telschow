# Daniel Telschow

**Technical leader for autonomous systems and applied AI.** Munich.

I build products where a wrong answer has a cost: a drone that must know when its position is wrong, an operator who must know what to do about it, and AI agents that must not act without a human saying yes. I make that kind of system measurable, auditable and shippable, and I lead the engineers who build it.

[LinkedIn](https://linkedin.com/in/dtelschow) · [Featured work](#featured-work) · [How I lead](#how-i-lead-technical-work)

---

## Featured work

### [nav-trust-c2](https://github.com/Telschow/nav-trust-c2): which drone's position can the operator trust?

Human-on-the-loop mission control for a small team of simulated drones (PX4 SITL, Gazebo, MAVSDK, FastAPI, React). Two independent detectors watch each vehicle for GNSS loss and frozen positions. The console shows trust as GREEN, AMBER or RED with a reason and a position bound, and recommends landing or re-tasking a sector. **Nothing executes without a recorded operator approval**, through one gate that a test enforces.

* GNSS loss detected in 1.0 to 1.4 s (12 runs), a frozen position in 3.0 to 4.5 s, no alert in 9 nominal runs
* The displayed position bound first held in only 30.8% of reports. After recalibration it held in 99.72% of 5,308 reports on an independent set
* A frozen GPS position does not stall a vehicle, it flies away (5.3 km in one run). Only LAND ended the flight in a controlled stop, so that is what the console recommends
* With one of three vehicles lost, area covered was 100% when the operator approved the re-task and 87% when denied
* Held-out validation with a protocol committed before the runs: 7 of 8 expectations met, and the miss is reported, not excluded
* Usability test with two people: neither acted on the decision unaided. I redesigned the console and published the finding. The redesign is not yet re-tested.
* Simulation only, search-and-rescue framing, no weapons, targeting or effectors, with the guardrails written into the repo

`PX4 / Gazebo / MAVSDK` · `trust & integrity monitoring` · `human-on-the-loop` · `evaluation with ground truth` · `FastAPI` · `React / TypeScript`

### [contested-nav](https://github.com/Telschow/contested-nav): is the filter right about how wrong it is?

A 21-state error-state Kalman filter (ESKF), fault detection (FDIR) and an evaluation harness for **GNSS-denied navigation**. It scores not just accuracy but whether the filter's reported uncertainty is honest.

| GNSS denied | Position error (ATE) | Claimed 1σ | Mean NEES (ideal 3) |
|---|---:|---:|---:|
| Vision off | 3.760 m | 0.567 m | 4.1 |
| Vision on | 2.322 m | 0.162 m | 286.2 |

Vision made the position better and the filter wildly overconfident. **The decision was to ship it off by default**, recorded in an [ADR](https://github.com/Telschow/contested-nav/blob/main/docs/adr/0003-ship-visual-disabled.md), with the bar for turning it on written down (mean NEES below 10, 2σ coverage above 90%).

I then tested a fix, a stochastic clone of the previous pose. It is calibrated when visual errors are independent and fails when they are correlated over a couple of seconds. I measured a real feature-tracking front end on EuRoC camera frames and replayed its errors: NEES 27 and 55% coverage, so the clone still stays opt-in. With four times the measured noise it recovers to NEES 4.3 and 91% coverage ([ADR-0023](https://github.com/Telschow/contested-nav/blob/main/docs/adr/0023-clone-against-a-measured-front-end.md)). One sequence, so I call the factor of four a trial value and not a result.

* 1,300+ tests, 94% line coverage, 23 architecture decision records, golden-snapshot benchmark, CI on 3 Python versions and 3 operating systems, CodeQL and secret scanning
* Every number in its README is generated, and CI fails if a table cell disagrees with the code
* Headline benchmark is synthetic, extended to recorded EuRoC IMU data with GNSS still simulated. Not field validated, not flight-ready, and it says so.

`ESKF` · `GNSS-denied navigation` · `FDIR / spoofing detection` · `NEES / calibration` · `visual odometry` · `Python`

### [personal-ai](https://github.com/Telschow/personal-ai): a local-first agent platform with a hard approval boundary

Ingests private documents into SQLite, retrieves evidence with provenance, and lets a local LLM call tools **only inside a policy engine** that returns `ALLOWED`, `DENIED` or `APPROVAL_REQUIRED`. Enforcement is in code, not in the prompt.

* Hybrid retrieval (BM25 + embeddings + RRF) with a measured eval gate in CI: recall@5 of 0.48 keyword vs 0.80 semantic on 64 labelled questions. The README says why that set is a regression gate and not a real-world claim.
* Agent evaluation that replays scripted model turns through the real loop, including a model that obeys an injected instruction, and fails CI if the approval boundary weakens
* Governed memory (scope, confidence, provenance, conflict handling), OpenAI-compatible API, MCP bridge, threat model
* 3,100+ hermetic tests, no network, no real personal data in the repo

`RAG` · `agent safety & evals` · `policy engine` · `human-in-the-loop` · `MCP` · `Ollama` · `Python`

### Also: [mia-san-miete](https://github.com/Telschow/mia-san-miete)

A personal agent for the German rental market. An LLM writes one or two sentences, a validator checks them against an allow-list of facts, and a fixed template takes over if the check fails. The score is deterministic. Sending is off by default: shadow mode, daily caps, quiet hours, a kill switch and a content-free audit trail. Its own benchmark report states that the reply classifier scores 100% on the sets it was tuned on and 57% on a fresh held-out set.

---

## How I lead technical work

* **Decisions get written down.** Trade-offs go into ADRs with an exit test, so a team can disagree with a decision for the right reasons months later.
* **Claims are earned by measurement.** Numbers in docs are generated and checked in CI. Negative results stay in the headline, and so do the misses in a validation report.
* **I test with users early and publish what failed.** The first console version did not work for the two people I tested it with.
* **AI-assisted engineering with gates, not trust.** Most of the code in these repos is written by an AI coding agent, and each README says so. I set scope and requirements, make the decisions, review and merge. Verification keeps it correct: property tests, finite-difference checks on Jacobians, golden snapshots, coverage ratchets, held-out protocols fixed before the runs, and security scans.
* **Safety and scope are part of the design.** Human-on-the-loop by default, one approval gate with a test, documented non-goals, and a clear line on what a system must not do.
* **Product and engineering together.** I work from the problem and the decision it supports down to the implementation, not the other way round.

## Where I fit

Tech lead or staff-level roles in **autonomous driving, robotics, defense-tech and applied AI**, especially on:

* State estimation, sensor fusion, and degraded-sensor behaviour (GNSS denial, spoofing, fault handling)
* Trust, integrity and human oversight for systems that decide on their own
* Evaluation and safety cases with ground truth, held-out protocols and honest uncertainty
* Agent and LLM systems that need auditability, policy control and human approval
* Turning research-grade prototypes into reproducible, testable products

## Stack

**Languages:** Python (primary), TypeScript
**Estimation & robotics:** ESKF, IMU/GNSS/visual fusion, FDIR, NEES/NIS consistency checks, PX4 SITL, Gazebo, MAVSDK
**AI:** RAG, hybrid retrieval, agent loops and tool use, evaluation harnesses, Ollama, MCP
**Engineering:** pytest, property-based testing, FastAPI, React, GitHub Actions, CodeQL, uv, Docker, MkDocs, ADRs

## Connect

<a href="https://linkedin.com/in/dtelschow">LinkedIn</a> · <a href="https://github.com/Telschow">GitHub</a>
