# Stakeholder Map — shipping the ApplyNXU funnel changes
> Two grids (Power×Interest for engagement, Impact×Power for who bears the consequences), then the gap comparison and an engagement plan. Metric: **App start → App complete ($25)**. Each stakeholder is tagged with the changes they **gate** and whether those are **Confirmed** (ship) or **Validate** (test first).

## Cast (who they are + stance)
| Stakeholder | Role in these changes | Likely stance |
|---|---|---|
| **Exec sponsor** (VP Growth/Enrollment or CPO) | Owns the conversion target; unblocks cross-team calls | Supportive — wants the number up |
| **Engineering** | Builds nearly everything (C1, files, sequencing, rails, Google, instrumentation) | Supportive but capacity-constrained |
| **Compliance / Risk** | Owns KYC policy + supported-country/sanctions list | **Cautious — the gate on the biggest bet** |
| **Finance / Payments** | Owns fee economics, refunds, PSP relationships | Cautious on fraud; positive on more paid apps |
| **Data / Analytics** | Owns funnel instrumentation + event definitions | Supportive — this is their leverage |
| **Marketing / Growth** | Owns conversion pixels (fire-on-success) + top-of-funnel | Highly interested; sensitive to attribution |
| **Admissions** | Owns country eligibility + runs drop-recovery outreach | Supportive — fewer stuck applicants |
| **Design / UX** | Errors, front/back slots, Google "welcome" | Supportive — their craft |
| **KYC / anti-fraud vendor** | Verification pipeline; constrains file handling + sequencing | Neutral/technical — API + SLA limits |
| **PSP (e.g. Paystack)** | Local currency/rails integration | Positive — new volume |
| **Enrollment counselors** | Frontline; run human drop-recovery (already works) | High impact, low power — **Q1** |
| **Applicants (Amaka)** | Bear every failure mode | High impact, zero formal power — **Q1** |

---

## Grid 1 — Power × Interest (engagement strategy)
**Manage closely** (high power, high interest)
- **Exec sponsor** — owns the number; unblocks the KYC bet.
- **Engineering** — builds all of it; sequencing depends on their capacity.
- **Compliance / Risk** — must sign off C1 country list + **F1 KYC-after-payment (Validate)**.
- **Data / Analytics** — instrumentation (C2) is the prerequisite for judging every item.

**Keep satisfied** (high power, lower day-to-day interest)
- **Finance / Payments** — approves local-rails (Validate) + fraud tolerance on the KYC bet.
- **KYC vendor** — technical gate on files (Confirmed) + re-sequencing (Validate).

**Keep informed** (lower power, high interest)
- **Marketing / Growth** — pixel fix (C2, Confirmed) affects their reporting; brief them so a *drop in counted starts* isn't misread as a loss.
- **Admissions** — country eligibility input; recovery context.
- **Design / UX** — delivery of the UX fixes.
- **PSP** — integration partner for rails.

**Monitor** (lower power, lower interest)
- (No one parked here who matters — enrollment counselors and applicants look like "monitor" here, which is exactly the trap Grid 2 catches.)

---

## Grid 2 — Impact × Power (who bears the consequences)
- **Q2 — high impact, high power (manage closely):** Exec sponsor, Engineering, Compliance/Risk, Finance/Payments.
- **Q1 — high impact, LOW power (elevate deliberately):** **Applicants/Amaka**, **Enrollment counselors**, **Data/Analytics** (their work is blocked/enabled by others' decisions but they don't own the roadmap).
- **Q4 — low impact, high power (gatekeepers, manage the relationship):** KYC vendor, PSP.
- **Q3 — low impact, low power (monitor):** Marketing (impacted only in reporting, not workflow).

---

## The gap the comparison reveals
- **Applicants & enrollment counselors** read as "monitor/keep-informed" on Grid 1 but are **Q1** on Grid 2 — they live every failure mode and have no roadmap seat. **Action:** put applicant evidence (our walkthrough + Amaka persona) into every decision, and give counselors a named slot: they see the real drop reasons daily and should feed the Step-3 rejection list and the recovery deep-link spec.
- **Data/Analytics** is high-interest *and* Q1: everyone needs their instrumentation but they can't force prioritization. **Action:** elevate — make "instrument first (C2 + step analytics)" an exec-sponsored prerequisite, not a nice-to-have, so they aren't blocked.
- **KYC vendor / PSP** are Q4 gatekeepers — manage the relationship (SLAs, API limits) but don't over-invest in buy-in.

---

## Who signs off the **KYC-after-payment** bet (F1 · Validate — the highest-leverage, highest-risk change)
This one change needs a small **decision council**, because it trades fraud exposure for conversion:
1. **Compliance / Risk — required sign-off** (KYC timing is their policy).
2. **Finance / Payments — required sign-off** (refund/chargeback + fee economics if fraud rises).
3. **Exec sponsor — arbiter** (owns the conversion vs. risk trade).
4. **KYC vendor — technical feasibility** (can verification run post-payment?).
- **Frame it as a bounded A/B with a kill switch:** move-after-payment for one cohort; ship only if **App-complete ↑ with fraud/refund flat or within an agreed tolerance.** That framing is what converts a "no" from Compliance into a "yes, for a measured test."

---

## Engagement plan (who does what)
| Stakeholder | Cadence | Ask / message | Gates (Confirmed/Validate) |
|---|---|---|---|
| Exec sponsor | Milestone + on-demand for the bet | "Instrument first; then a measured KYC-after-payment test" | Arbiter on F1 (Validate) |
| Engineering | Weekly | Sequenced backlog: C1 + files + pixel fix first | C1, files, C2 (Confirmed) |
| Compliance/Risk | Co-design on C1 list + bet | Canonical country list; bounded KYC test w/ guardrails | C1 (Confirmed), F1 (Validate) |
| Finance/Payments | Milestone | Fraud tolerance; PSP go-ahead | F1, local rails (Validate) |
| Data/Analytics | Weekly | Own funnel events; unblock as prerequisite | C2 + all measurement (Confirmed) |
| Marketing | Brief before pixel fix | "Counted starts will drop — that's accuracy, not loss" | C2 (Confirmed) |
| Admissions + counselors | Recurring input | Country eligibility; feed Step-3 reasons + recovery deep-link | C1; recovery (Keep) |
| Design/UX | Weekly | Errors, front/back, Google welcome | Confirmed UX set |
| KYC vendor / PSP | As-needed | API limits, SLAs, integration scope | files (Confirmed) / rails (Validate) |

> **On-objective:** every stakeholder is here because they gate a change that moves App start → App complete. The map's job is to get the **Confirmed** fixes shipped fast and the **KYC-after-payment Validate** bet properly sponsored and guard-railed.
