# Daniel Telschow

**Technical leader for autonomous systems and applied AI.** Munich.

I build products where a wrong answer has a cost: a drone that must know when its position is wrong, an operator who must know what to do about it, and AI agents that must not act without a human saying yes. I make that kind of system measurable, auditable and shippable, and I lead the engineers who build it.

[LinkedIn](https://linkedin.com/in/dtelschow) · [Featured work](#featured-work) · [How I lead](#how-i-lead-technical-work)

---

## Featured work

### [nav-trust-c2](https://github.com/Telschow/nav-trust-c2): which drone's position can the operator trust?

Human-on-the-loop mission control for a small team of simulated drones (PX4 SITL, Gazebo, MAVSDK, FastAPI, React). Two independent detectors watch each vehicle for GNSS loss and frozen positions. The console shows trust as GREEN, AMBER or RED with a reason and a position bound, and recommends landing or re-tasking a sector. **Nothing executes without a recorded operator approval**, through one gate that a test enforces. Released as v1.0.0, with a [3-minute demo video](https://youtu.be/L7DrhOYMQ5A).

* GNSS loss detected in 1.0 to 1.4 s (12 runs), a frozen position in 3.0 to 4.5 s, no alert in 9 nominal runs
* The displayed position bound first held in only 30.8% of reports. After recalibration it held in 99.72% of 5,308 reports on an independent set
* A frozen GPS position does not stall a vehicle, it flies away (5.3 km in one run). Only LAND ended the flight in a controlled stop, so that is what the console recommends
* With one of three vehicles lost, area covered was 100% when the operator approved the re-task and 87% when denied
* Held-out validation with a protocol committed before the runs: 7 of 8 expectations met, and the miss is reported, not excluded
* Usability test with two people: neither acted on the decision unaided. I redesigned the console and published the finding. The redesign is not yet re-tested, and round 2 is planned.
* Security hardening of the C2 backend: cross-site browser requests are refused, and a decision that names no operator is refused
* Simulation only, search-and-rescue framing, no weapons, targeting or effectors, with the guardrails written into the repo

`PX4 / Gazebo / MAVSDK` · `trust & integrity monitoring` · `human-on-the-loop` · `evaluation with ground truth` · `FastAPI` · `React / TypeScript`

### [contested-nav](https://github.com/Telschow/contested-nav): is the filter right about how wrong it is?

A 21-state error-state Kalman filter (ESKF), fault detection (FDIR) and an evaluation harness for **GNSS-denied navigation**. It scores not just accuracy but whether the filter's reported uncertainty is honest.

With GNSS denied and camera-based motion tracking switched on, the filter claims a position uncertainty of centimetres (0.156 m) while its error is metres (17.4 m; one synthetic run). It is confidently wrong, so **vision ships off by default**, recorded in an [ADR](https://github.com/Telschow/contested-nav/blob/main/docs/adr/0003-ship-visual-disabled.md). The overconfidence held in all 10 seeds and all 40 outage runs.

**I retracted my own earlier result.** The project first reported that vision made the position better. Then I found the benchmark started every run from the same fixed offset from the truth, which the filter's own prior did not describe. I wrote down the fix ([ADR-0027](https://github.com/Telschow/contested-nav/blob/main/docs/adr/0027-how-the-synthetic-runs-are-initialised.md)) before regenerating any number. After the fix the accuracy gain did not survive: vision was less accurate than no vision in 9 of 10 seeds (measured once, not checked by CI).

**The bar for shipping vision is pre-registered.** The gate ([ADR-0028](https://github.com/Telschow/contested-nav/blob/main/docs/adr/0028-the-b1-gate.md), [ADR-0030](https://github.com/Telschow/contested-nav/blob/main/docs/adr/0030-the-gate-for-the-next-visual-update-candidate.md)) fixes thresholds, scenarios and held-out seed blocks before a candidate is evaluated, and a failure is recorded as a result. A stochastic clone fixes independent visual errors but needs inflated noise against the errors measured on a real front end ([ADR-0023](https://github.com/Telschow/contested-nav/blob/main/docs/adr/0023-clone-against-a-measured-front-end.md)). An adaptive noise scale is the next candidate. The gate has not been passed, so the default is unchanged.

* 2,000+ tests, 94% line coverage, 32 architecture decision records, golden-snapshot benchmark, CI on 3 Python versions and 3 operating systems, CodeQL and secret scanning
* Tables in its README are generated, and CI fails if a cell disagrees with the code
* Headline benchmark is synthetic, extended to recorded EuRoC and TUM VI IMU data with GNSS still simulated. Not field validated, not flight-ready, and it says so.

`ESKF` · `GNSS-denied navigation` · `FDIR / spoofing detection` · `NEES / calibration` · `visual odometry` · `Python`

### [personal-ai](https://github.com/Telschow/personal-ai): a local-first agent platform with a hard approval boundary

Ingests private documents into SQLite, retrieves evidence with provenance, and lets a local LLM call tools **only inside a policy engine** that returns `ALLOWED`, `DENIED` or `APPROVAL_REQUIRED`. Enforcement is in code, not in the prompt.

* Hybrid retrieval (BM25 + embeddings + RRF) with a measured eval gate in CI: recall@5 of 0.48 keyword vs 0.80 semantic on 64 labelled questions. The README says why that set is a regression gate and not a real-world claim.
* Agent evaluation that replays scripted model turns through the real loop, including a model that obeys an injected instruction, and fails CI if the approval boundary weakens
* Governed memory (scope, confidence, provenance, conflict handling), OpenAI-compatible API, MCP bridge, threat model
* 3,100+ hermetic tests, no network, no real personal data in the repo
* v0.1.0 after a hardening pass: cross-site browser requests refused, approval decisions tightened, untrusted prompt blocks escaped

`RAG` · `agent safety & evals` · `policy engine` · `human-in-the-loop` · `MCP` · `Ollama` · `Python`

### Also: [mia-san-miete](https://github.com/Telschow/mia-san-miete) ([project page](https://telschow.github.io/mia-san-miete/))

A personal agent for the German rental market. An LLM writes one or two sentences, a validator checks them against an allow-list of facts, and a fixed template takes over if the check fails. The score is deterministic. Sending is off by default: shadow mode, daily caps, quiet hours, a kill switch and a content-free audit trail. Its own benchmark report states that the reply classifier scores 100% on the sets it was tuned on and 57% on a fresh held-out set.

---

## How I lead technical work

* **Decisions get written down.** Trade-offs go into ADRs with an exit test, so a team can disagree with a decision for the right reasons months later.
* **Claims are earned by measurement.** Numbers in docs are generated and checked in CI. Negative results stay in the headline, and so do the misses in a validation report.
* **I retract my own results and pre-register the bar.** When I found a flaw in my own benchmark, I fixed the method before regenerating numbers, and the gate for the next claim is fixed before it is run.
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
