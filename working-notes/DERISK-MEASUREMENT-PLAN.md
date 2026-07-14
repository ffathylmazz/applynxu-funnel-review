# De-risking & Measurement Plan — "Validate" items & strategic bets
> For changes I would NOT ship blindly (possible intentional trade-offs). Each: the risky assumption, what to measure first (cheap), a success threshold, and act-now vs. track. Lenses: DUFV (Desirability/Usability/Feasibility/Viability) + PESTEL. Answers the interview question: *"How would you validate this?"*

> **Precondition for all of this:** fix conversion tracking (C2) and instrument the funnel step-by-step first — you can't de-risk anything if the metric counts failed starts. **Zero-cost prerequisite.**

---

## 1. F1 — Move identity/KYC to AFTER payment (biggest potential lift)
- **Risky assumption:** KYC-before-payment is pure friction (not deliberate fraud/quality gating).
- **Lens:** Viability + Legal (fraud/compliance) vs. Desirability (drop).
- **Measure first (cheap):**
  1. Instrument Step 3: exact drop rate at identity vs. steps 1/2/4 (1 week of data — free).
  2. Ask Admissions/Compliance/Finance: *why* is KYC here? (fraud rate, chargeback rate, cost of verifying-after). One meeting.
  3. **A/B (real test):** move KYC to after the $25 payment for a small cohort; compare App-complete rate AND downstream fraud/no-show/refund rate.
- **Success threshold:** App-complete ↑ meaningfully (e.g. +X pts at Step 3) with **fraud/refund flat or within tolerance**. If fraud rises past the fee economics, keep KYC before.
- **Triage:** **ACT** (instrument + stakeholder ask now; A/B next).

## 2. "Awaiting Payment" success-framing (sponsor path)
- **Risky assumption:** the green ✓ / "Thank you for applying" reduces payment urgency (vs. it being an intentional flexible-payment play).
- **Lens:** Viability (monetization) + Desirability.
- **Measure first:** cohort the "Awaiting Payment" screen → **payment-completion rate within 24h/7d** after it; segment self-pay vs. sponsor. Read the intent: is recurring/sponsor funding a stated goal? (ask Growth/Finance).
- **Success threshold:** if post-screen payment rate is healthy (≥ benchmark) → it's working as designed, leave it. If it's low → A/B a more urgent framing ("Secure your seat — pay the $25 to complete").
- **Triage:** **TRACK** (measure the rate; only act if low).

## 3. USD-only payment → add local currency/rails (Paystack, mobile money)
- **Risky assumption:** USD card/PayPal is a final-step barrier for the Africa-heavy base.
- **Lens:** Viability + Economic + Feasibility (integration).
- **Measure first:**
  1. **Payment-attempt → success rate by country/method** (instrument checkout — free). Nigeria USD-card failure rate is the key number.
  2. Count Bank-Transfer/Sponsor selections as a signal that cards fail.
  3. Feasibility spike: Paystack/Flutterwave integration effort + fees.
- **Success threshold:** if USD card-failure in Nigeria is materially high (e.g. >X%) and local-rail effort is reasonable → build. If failure is low → deprioritize.
- **Triage:** **ACT** (instrument payment failure by country now — cheap, high-signal); build = **Strategic bet** gated on the data.

## 4. Stage-2 Personal-info — 14+ fields, redundancy
- **Risky assumption:** several fields are unnecessary for "App complete" (marketing, not accreditation).
- **Lens:** Usability (form fatigue) + Feasibility (what's truly required).
- **Measure first:**
  1. **Field-level analytics:** per-field drop, time-to-complete, corrections (instrument — free).
  2. Ask Registrar/Compliance which fields are accreditation-mandatory vs. which are marketing (LinkedIn, "how did you hear", gender?).
  3. **A/B:** remove/defer the non-mandatory ones (or prefill from Stage 0) → measure Step-2 completion.
- **Success threshold:** Step-2 completion ↑ with no loss of data the business truly needs.
- **Triage:** **ACT** (instrument + the required-vs-marketing audit are cheap).

## 5. English-only ID requirement
- **Risky assumption:** it blocks a material share of applicants (non-English-ID countries).
- **Lens:** Usability + Social (digital literacy / document availability) + Legal (KYC vendor needs).
- **Measure first:** **ID-rejection rate by country/reason** (how many rejected for language / no-passport). Ask the KYC vendor whether non-English IDs + OCR/translation are supportable.
- **Success threshold:** if a meaningful share is rejected for language in non-English markets → invest in translation/OCR or accept national IDs. If small → track only.
- **Triage:** **TRACK** (until rejection-by-reason data says otherwise).

## 6. AI photo assistant (O1 — strategic bet)
- **Risky assumption (riskiest):** photo/selfie rejections are a *material* cause of Step-3 drop (worth an AI build).
- **Lens:** Desirability + Feasibility (client-side AI on low-end Android) + Legal (biometric processing) + Technological (bundle/perf).
- **Measure first:**
  1. **Size the prize:** instrument Step-3 rejection reasons (format/size/quality/background). If "background/quality" is a small slice, the AI layer isn't justified yet.
  2. Ship the **normalizer MVP** first (PDF→image, HEIC, compress — Confirmed, low effort) and measure how much it recovers before building AI editing.
  3. Wizard-of-Oz: manually fix a few users' photos → does approval/complete move?
- **Success threshold:** normalizer recovers most of the drop → AI editing is a fast-follow only if a residual background/quality gap remains AND KYC still passes edited selfies.
- **Triage:** **TRACK / gated bet** (instrument first; MVP normalizer is the ACT).

## 7. Performance (~21s Slow-3G, Stripe at register)
- **Risky assumption:** first-screen weight causes drop before App-start (already measured synthetically).
- **Lens:** Usability + Technological + Feasibility.
- **Measure first:** real-user (field) LCP/TBT by device/geo (RUM) to confirm the synthetic ~21s in the wild; confirm Stripe/Warmly are non-critical on register.
- **Success threshold:** deferring Stripe→checkout + lazy widgets/fonts cuts LCP by a target (e.g. −X s on mobile) → ship. Low risk regardless.
- **Triage:** **ACT** (the defer is confirmed/low-risk; validate lift with RUM).

---

## Consolidated register

### Act now (instrument / cheap tests before committing)
| # | Item | First step |
|---|---|---|
| 0 | **C2 tracking + funnel instrumentation** | Gate pixels on 2xx; add step-level analytics — prerequisite for everything |
| 1 | **F1 KYC sequencing** | Measure Step-3 drop + ask Compliance the fraud rationale; then A/B move-after-payment |
| 3 | **USD-only payment** | Instrument payment-success by country/method (Nigeria USD-card failure %) |
| 4 | **Stage-2 fields** | Field-level analytics + required-vs-marketing audit; A/B trim/prefill |
| 6 | **AI photo (via normalizer)** | Instrument Step-3 rejection reasons; ship normalizer MVP; measure recovery |
| 7 | **Performance** | RUM LCP by device/geo; defer Stripe→checkout |

### Track (watch with a threshold/cadence)
| # | Item | Watch | Cadence |
|---|---|---|---|
| 2 | "Awaiting Payment" framing | post-screen payment rate (self-pay vs sponsor) | monthly |
| 5 | English-only ID | ID-rejection rate by country/reason | monthly |
| — | Local-rail build (Paystack) | gated on #3 payment-failure data | quarterly |

**Biggest gap:** measurement itself. Almost every "Validate" answer starts with *instrument the funnel* — so **C2 + step-level analytics is the true first move**; without it, none of these bets can be judged.
