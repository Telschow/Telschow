<img src="assets/banner.svg" alt="Daniel Telschow. Autonomy and applied AI. Systems that know when they are wrong." width="100%">

**Technical leader for autonomous systems and applied AI.** Munich.

I build products where a wrong answer has a cost: a drone that must know when its position is wrong, an operator who must know what to do about it, and AI agents that must not act without a human saying yes. I make that kind of system measurable, auditable and shippable, and I lead the engineers who build it.

**The question behind my projects:** when does adding a sensor, a camera or an AI component make a system worse, and does the system know it?

[LinkedIn](https://linkedin.com/in/dtelschow) · [Featured work](#featured-work) · [How I lead](#how-i-lead-technical-work) · [Where I fit](#where-i-fit)

## Results, not activity

<p align="center">
  <a href="https://github.com/Telschow/mosaic-perception/blob/main/artifacts/benchmark/report.md"><img src="assets/tile-mosaic.svg" width="49%" alt="14 of 16 scenarios where adding a camera made tracking worse, from mosaic-perception"></a>
  <a href="https://github.com/Telschow/nav-trust-c2/blob/main/docs/validation/heldout-report.md"><img src="assets/tile-nav-trust.svg" width="49%" alt="7 of 8 held-out expectations met, the miss is reported, from nav-trust-c2"></a>
  <a href="https://github.com/Telschow/contested-nav"><img src="assets/tile-contested-nav.svg" width="49%" alt="17.4 metres actual error when the filter claimed 49 centimetres, one synthetic run, from contested-nav"></a>
  <a href="https://github.com/Telschow/contested-nav/blob/main/docs/adr/index.md"><img src="assets/tile-adr.svg" width="49%" alt="42 architecture decisions on the record, from contested-nav"></a>
</p>

Each tile links to its source. All results are from simulation or synthetic data, and each project says so.

## Featured work

<table>
  <tr>
    <td width="300" valign="top"><a href="https://github.com/Telschow/nav-trust-c2"><img src="assets/nav-trust-c2.jpg" alt="Operator console showing three drones, one flagged not trusted, with a decision waiting" width="300"></a></td>
    <td valign="top">
      <h3><a href="https://github.com/Telschow/nav-trust-c2">nav-trust-c2</a>: which drone's position can the operator trust?</h3>
      <p>Human-on-the-loop mission control for a small team of simulated drones. Two independent detectors watch each vehicle for GNSS loss and frozen positions. The console shows trust as GREEN, AMBER or RED with a reason and a position bound, and recommends landing or re-tasking a sector. <b>Nothing executes without a recorded operator approval</b>, through one gate that a test enforces. v1.0.0, with a <a href="https://youtu.be/L7DrhOYMQ5A">3-minute demo video</a>.</p>
      <p><sub><code>PX4 / Gazebo / MAVSDK</code> · <code>trust and integrity monitoring</code> · <code>FastAPI</code> · <code>React / TypeScript</code></sub></p>
      <details><summary>Evidence</summary>
        <ul>
          <li>GNSS loss detected in 1.0 to 1.4 s (12 runs), a frozen position in 3.0 to 4.5 s, no alert in 9 nominal runs</li>
          <li>The displayed position bound first held in only 30.8% of reports. After recalibration it held in 99.72% of 5,308 reports on an independent set</li>
          <li>A frozen GPS position does not stall a vehicle, it flies away (5.3 km in one run). Only LAND ended the flight in a controlled stop, so that is what the console recommends</li>
          <li>With one of three vehicles lost, area covered was 100% when the operator approved the re-task and 87% when denied</li>
          <li>Held-out validation with a protocol committed before the runs: 7 of 8 expectations met, and the miss is reported, not excluded</li>
          <li>A lost-link contingency is partly built, in simulation only: the operator approves LAND before the mission, the vehicle may act on nothing else, and 3 of 3 recorded runs behaved that way (<a href="https://github.com/Telschow/nav-trust-c2/blob/main/docs/adr/0004-authority-under-lost-link.md">ADR 0004</a>)</li>
          <li>Usability test with two people: neither acted on the decision unaided. I redesigned the console and published the finding. The redesign is not yet re-tested, and round 2 is planned</li>
          <li>Backend hardening: cross-site browser requests are refused, and a decision that names no operator is refused</li>
          <li>Simulation only, search-and-rescue framing, no weapons, targeting or effectors, with the guardrails written into the repo</li>
        </ul>
      </details>
    </td>
  </tr>
  <tr>
    <td width="300" valign="top"><a href="https://github.com/Telschow/contested-nav"><img src="assets/contested-nav.jpg" alt="Figure: the filter claims it is within 49 centimetres and is 17.4 metres off, one synthetic run" width="300"></a></td>
    <td valign="top">
      <h3><a href="https://github.com/Telschow/contested-nav">contested-nav</a>: is the filter right about how wrong it is?</h3>
      <p>A 21-state error-state Kalman filter, fault detection and an evaluation harness for <b>GNSS-denied navigation</b>. It scores not just accuracy but whether the filter's reported uncertainty is honest. With GNSS denied and camera aiding on, the filter claims centimetres while its error is metres, so <b>vision ships off by default</b> (<a href="https://github.com/Telschow/contested-nav/blob/main/docs/adr/0003-ship-visual-disabled.md">ADR-0003</a>).</p>
      <p><sub><code>ESKF</code> · <code>FDIR / spoofing detection</code> · <code>NEES / calibration</code> · <code>visual odometry</code> · <code>Python</code></sub></p>
      <details><summary>Evidence</summary>
        <ul>
          <li>The overconfidence held in all 10 seeds and all 40 outage runs</li>
          <li><b>I retracted my own earlier result.</b> The project first reported that vision made the position better. Then I found the benchmark started every run from the same fixed offset from the truth, which the filter's own prior did not describe. I wrote down the fix (<a href="https://github.com/Telschow/contested-nav/blob/main/docs/adr/0027-how-the-synthetic-runs-are-initialised.md">ADR-0027</a>) before regenerating any number. After the fix the accuracy gain did not survive: vision was less accurate than no vision in 9 of 10 seeds (measured once, not checked by CI)</li>
          <li><b>The bar for shipping vision was pre-registered, and I stopped when it was not met.</b> The gate (<a href="https://github.com/Telschow/contested-nav/blob/main/docs/adr/0028-the-b1-gate.md">ADR-0028</a>, <a href="https://github.com/Telschow/contested-nav/blob/main/docs/adr/0030-the-gate-for-the-next-visual-update-candidate.md">ADR-0030</a>) fixed thresholds, scenarios and held-out seed blocks before any candidate was evaluated. The first candidate failed the held-out gate. A stochastic clone passes when visual errors are independent and fails when they are correlated. An adaptive noise scale and a Gauss-Markov error model each failed on development seeds. I then closed the question as <b>characterised, not solved</b> and stopped building candidates (<a href="https://github.com/Telschow/contested-nav/blob/main/docs/adr/0036-b1-is-closed-as-characterised-not-solved.md">ADR-0036</a>). The default is unchanged</li>
          <li>2,700+ tests, 95% line coverage, 42 architecture decision records, golden-snapshot benchmark, CI on 3 Python versions and 3 operating systems, CodeQL and secret scanning</li>
          <li>Tables in its README are generated, and CI fails if a cell disagrees with the code</li>
          <li>Headline benchmark is synthetic. It is extended to recorded EuRoC and TUM VI IMU data with GNSS simulated, and to UrbanNav with the receiver's own GNSS fixes. Not field validated, not flight-ready, and it says so</li>
        </ul>
      </details>
    </td>
  </tr>
  <tr>
    <td width="300" valign="top"><a href="https://github.com/Telschow/mosaic-perception"><img src="assets/mosaic-perception.jpg" alt="Three tracks of one synthetic target: range sensor only, camera added (worse), bias estimated" width="300"></a></td>
    <td valign="top">
      <h3><a href="https://github.com/Telschow/mosaic-perception">mosaic-perception</a>: when does fusing sensors make tracking worse?</h3>
      <p>A deterministic workbench for multi-sensor object tracking. Three methods run on the same synthetic worlds and random streams: a range-bearing sensor alone, the same plus a camera-like bearing sensor, and a third that also estimates and removes the camera's bearing bias. Comparisons are paired, so differences are not luck of the draw. <a href="https://telschow.github.io/mosaic-perception/">Project site</a> with a 44-second explainer.</p>
      <p><sub><code>multi-target tracking</code> · <code>sensor fusion</code> · <code>EKF / Hungarian association</code> · <code>GOSPA / MOTA / NEES</code> · <code>Python / Streamlit</code></sub></p>
      <details><summary>Evidence</summary>
        <ul>
          <li>Adding the camera made precision, MOTA and GOSPA worse in 14 of 16 scenarios (25 paired seeds, corrected for multiple comparisons). Position error was inconclusive</li>
          <li>Position uncertainty was overconfident in 28 of 30 scenario-method pairs</li>
          <li>An ablation shows the losses largely vanish when the camera may not refresh tracks the range sensor missed. So it is a property of this tracker design, not of fusion in general</li>
          <li>Reserved seeds were run once and frozen, and the README says that run is a historical result and not a generalisation claim. The best-case hero figure is labelled as best case</li>
          <li>360+ tests, CI that regenerates a benchmark and compares it with committed golden files, artifact provenance checks, secret scan. All synthetic</li>
        </ul>
      </details>
    </td>
  </tr>
</table>

### Also

* **[personal-ai](https://github.com/Telschow/personal-ai)**, a local-first agent platform with a hard approval boundary. A local LLM calls tools only inside a policy engine that returns `ALLOWED`, `DENIED` or `APPROVAL_REQUIRED`, enforced in code and not in the prompt. Hybrid retrieval with an eval gate in CI (recall@5 of 0.48 keyword vs 0.80 semantic on 64 labelled questions, a regression gate and not a real-world claim), agent evals that include a model obeying an injected instruction, governed memory, MCP bridge, 3,100+ hermetic tests. v0.1.0 after a hardening pass. `RAG` · `agent safety & evals` · `policy engine` · `MCP` · `Ollama`
* **[mia-san-miete](https://github.com/Telschow/mia-san-miete)** ([project page](https://telschow.github.io/mia-san-miete/)), a personal agent for the German rental market. An LLM writes one or two sentences, a validator checks them against an allow-list of facts, and a fixed template takes over if the check fails. Sending is off by default: shadow mode, daily caps, quiet hours, a kill switch and a content-free audit trail. Its own benchmark report states that the reply classifier scores 100% on the sets it was tuned on and 57% on a fresh held-out set.

---

## How I lead technical work

* **Decisions get written down.** Trade-offs go into ADRs with an exit test, so a team can disagree with a decision for the right reasons months later.
* **Claims are earned by measurement.** Numbers in docs are generated and checked in CI. Negative results stay in the headline, and so do the misses in a validation report.
* **I retract my own results and pre-register the bar.** When I found a flaw in my own benchmark, I fixed the method before regenerating numbers, and the gate for the next claim is fixed before it is run. When the gate was not met, I stopped.
* **I test with users early and publish what failed.** The first console version did not work for the two people I tested it with.
* **AI-assisted engineering with gates, not trust.** Most of the code in these repos is written by an AI coding agent, and each README says so. I set scope and requirements, make the decisions, review and merge. Verification keeps it correct: property tests, finite-difference checks on Jacobians, golden snapshots, coverage ratchets, held-out protocols fixed before the runs, and security scans.
* **Safety and scope are part of the design.** Human-on-the-loop by default, one approval gate with a test, documented non-goals, and a clear line on what a system must not do.
* **Product and engineering together.** I work from the problem and the decision it supports down to the implementation, not the other way round.

## Where I fit

Tech lead or staff-level roles in **autonomous driving, robotics, defense-tech and applied AI**, especially on:

* State estimation, sensor fusion and tracking, and degraded-sensor behaviour (GNSS denial, spoofing, fault handling)
* Trust, integrity and human oversight for systems that decide on their own
* Evaluation and safety cases with ground truth, held-out protocols and honest uncertainty
* Agent and LLM systems that need auditability, policy control and human approval
* Turning research-grade prototypes into reproducible, testable products

## Stack

**Languages:** Python (primary), TypeScript
**Estimation & robotics:** ESKF, IMU/GNSS/visual fusion, multi-target tracking, FDIR, NEES/NIS consistency checks, PX4 SITL, Gazebo, MAVSDK
**AI:** RAG, hybrid retrieval, agent loops and tool use, evaluation harnesses, Ollama, MCP
**Engineering:** pytest, property-based testing, FastAPI, React, GitHub Actions, CodeQL, uv, Docker, MkDocs, ADRs

## Connect

<a href="https://linkedin.com/in/dtelschow">LinkedIn</a> · <a href="https://github.com/Telschow">GitHub</a>
