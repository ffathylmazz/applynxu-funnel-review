# ApplyNXU — Application-Funnel Conversion Review
**Live site:** https://ffathylmazz.github.io/applynxu-funnel-review/
**Objective:** improve **App start → App complete** (pay the $25 application fee). Every recommendation ties back to that metric.

---

## 📦 The deliverables

**Start here → the focused brief.** It's the three highest-leverage changes; the full analysis and prototype are one click deeper.

| | What | Open |
|---|---|---|
| **① Focused brief** *(primary)* | The three highest-leverage changes — end the sign-up dead-end, accept the ID people actually have, move KYC after payment — each grounded in the walkthrough, plus an engineering brief. Tight, single-narrative. | **[🎯 Read the focused brief](https://ffathylmazz.github.io/applynxu-funnel-review/focused.html)** · or open `focused.html` |
| **② Full analysis** *(companion, for depth)* | All ~30 findings (with real screenshots), the off-funnel recovery loop, a developer-ready measurement plan, a quantified impact model, before/after mockups, and two engineering briefs. | **[▶ Full report](https://ffathylmazz.github.io/applynxu-funnel-review/deliverable.html)** · or open `deliverable.html` |
| **③ Interactive prototype** | A pixel-faithful, clickable recreation of the *proposed fixed* flow, with a **Broken ⟷ Fixed** toggle on every step (responsive). | **[▶ Play the prototype](https://ffathylmazz.github.io/applynxu-funnel-review/prototype.html)** · or open `prototype.html` |
| **④ Engineering briefs (standalone)** | Engineer-ready hand-offs: what changes on which surface, acceptance criteria, scope (build this / don't touch), 1-click links into the prototype. ENG-1 = register dead-end · ENG-2 = file normalization + Front/Back. | **[🔧 ENG-1](https://ffathylmazz.github.io/applynxu-funnel-review/eng-brief-1.html)** · **[🔧 ENG-2](https://ffathylmazz.github.io/applynxu-funnel-review/eng-brief-2.html)** · or open `eng-brief-1.html` / `eng-brief-2.html` |

All of them are self-contained HTML — no build step, no dependencies. `screens/` holds the source-of-truth walkthrough captures (organized by flow — see `screens/INDEX.md`).

## 🔍 Method
Registered, verified, and walked the entire application to the $25 checkout on **desktop and mobile**, watching the network and the inbox throughout. Findings are grounded in what was actually observed; inferences are labelled as such. **Confirmed** (safe to fix) vs **Validate** (needs the team's data) is applied throughout.

## 🎯 The headline
The flow is well-built and low-friction at the top. Losses cluster in two places: a **silent dead-end at sign-up** (rejected phone country → generic error) and a **heavy identity-verification wall placed right before payment**. Fix the confirmed sign-up + file-format bugs now (cheap) — and re-verify the English-proficiency dead-end (E1), potentially the single largest leak; test resequencing KYC to after payment (biggest bet — validate against fraud). And fix the tracking first: you can't optimize a number you're measuring wrong.

## 📁 `working-notes/` — supporting work
The report and prototype above are the submission; everything below is the working detail behind them.

**Engineer handoff**
- `ENG-BRIEF-1-supported-country.md`, `ENG-BRIEF-2-file-normalization.md` — build-ready briefs (Gherkin acceptance criteria)
- `TRACKING-PLAN.md` — event/instrumentation spec (per-step events, properties, success-gating, recovery events, dashboards)

**Analysis & prioritization**
- `FINDINGS.md` — full findings log + evidence
- `ANALYSIS.md` — Impact×Effort + Confirmed/Validate/Strategic
- `IMPACT-MODEL.md` — assumed-baseline funnel, per-fix sizing, targets, RICE, time-to-complete
- `DERISK-MEASUREMENT-PLAN.md` — de-risking + what to measure
- `OFF-FUNNEL-RECOVERY.md` — the email/call/WhatsApp recovery loop (with evidence)

**Discovery & framing (PM artifacts)**
- `COMPANY-RESEARCH.md`, `PROTO-PERSONA.md` (Amaka), `PROBLEM-STATEMENTS.md`, `OST.md` (opportunity-solution tree), `EPIC-HYPOTHESES.md`, `STAKEHOLDER-MAP.md`, `RECOMMENDATION-CANVAS-O1.md`

**Evidence**
- `invoice_2285.pdf`, `invoice-email.html` — payment evidence (F14)
