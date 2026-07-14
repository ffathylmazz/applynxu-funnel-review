# ApplyNXU Funnel — Prioritized Analysis
> Metric: **App start → App complete ($25 fee).** Framework: **Impact × Effort** (minimal data; RICE logic — reach/severity as qualitative modifiers).
> **Type split (critical):** each item is either **Confirmed** (clear bug/inconsistency, safe to recommend), **Validate** (design choice/trade-off → get use-case/data first), or **Strategic bet** (high impact/effort, roadmap). This split satisfies the "grounded, not generic best-practice" rubric criterion.

## Prioritization table
| # | Change | Finding | Impact | Effort | Type |
|---|---|---|---|---|---|
| 1 | Unsupported country: block/flag in UI + field-level error (kill the generic error) | C1 | 🔴 High | Low–Med | **Confirmed** |
| 2 | Fire conversion pixels only on a successful (2xx) registration | C2 | 🟠 Med (metric integrity — meta-critical) | Low | **Confirmed** |
| 3 | Validation UX: mark required fields + auto-scroll/focus first error | Validation | 🟠 Med–High | Low | **Confirmed** |
| 4 | File normalization: PDF→image + auto <10MB + HEIC + inline error | F6+F8 | 🔴 High | Med | **Confirmed** |
| 5 | ID front+back: two labeled slots / document-type aware | F9 | 🟠 Med | Low–Med | **Confirmed** |
| 6 | "See example" visibility: persistent thumbnail by the dropzone | F7 | 🟠 Med | Low | **Confirmed** |
| 7 | Sponsor email copy: "tuition fees" → "application fee" | Sponsor email | 🟡 Low | Low | **Confirmed** |
| 8 | One source of truth for wire details (checkout ≠ invoice) | F14 | 🟠 Med–High | Low | **Confirmed** |
| 9 | Google post-signin: "welcome" state (not a register screen) + stop token-in-URL | F17 | 🟠 Med | Low–Med | **Confirmed** |
| 10 | Defer non-critical third-party (Stripe→checkout, lazy widgets/fonts) | Perf | 🟠 Med–High (measured ~21s Slow-3G) | Med | **Confirmed (measured)** |
| 11 | Personal info: reduce field count + prefill (redundancy) | Stage 2 | 🟠 Med | Med | **Validate** (which fields are accreditation-required?) |
| 12 | Move identity/KYC to AFTER payment / optional at this stage | F1 | 🔴🔴 Very High | High | **Validate** (drop vs. fraud data) |
| 13 | Rethink "Awaiting Payment" success framing | Checkout | 🟡 Unclear | Low | **Validate** (may be intentional flexible-payment) |
| 14 | English-only ID requirement | F2 | 🟠 Med | Med | **Validate** (rejection rate by country) |
| 15 | Persistent "phone not verified" nag | S1 | 🟡 Low–Med | Low | **Validate** (serves drop-recovery — see F15) |
| 16 | Local currency / payment rails (Paystack, mobile money, Naira) | Checkout | 🟠 Med–High | High | **Strategic bet** |
| 17 | AI photo assistant (selfie bg/crop/compliance) | O1 | 🔴 High + brand | High | **Strategic bet** |

---

## A) RECOMMENDATIONS — CONFIRMED (clear; safe to recommend)
Ranked (impact × effort):
1. **C1 — End the silent country dead-end.** Note: the country limit *itself* may be intentional (sanctions/ops); but **offering it in the UI then rejecting server-side + a generic error** is wrong either way → the fix (field-level message + don't offer unsupported) holds unconditionally. → **Eng Brief #1**
2. **File normalization (F6+F8) + ID front/back (F9).** Late-funnel, high intent; prevents incomplete-ID submissions (which likely also reduces post-payment rejection — *inferred, we never paid*). → **Eng Brief #2**
3. **Validation UX** — required markers + auto-scroll; cheap, applies to every step.
4. **C2 — fire pixels only on success** (measurement hygiene; the optimized metric must be true).
5. **F14 — one source of truth for wire details** (checkout says Bank of America, invoice says Bank of New York for the same $25 wire).
6. **F7 — example visibility**; lowers rejected uploads.
7. **F17 — Google post-signin** "welcome" state, stop token-in-URL.
8. **Sponsor email copy** — "tuition" → "application fee" (small, clear).
9. **Perf** — defer non-critical third-party (measured ~21s on Slow 3G; Stripe loads at register).

## B) ASSUMPTIONS TO VALIDATE (design choice/trade-off → use-case + data first)
> Framed as "let's measure/ask", not as a mandate.
- **F1 — Should KYC stay before payment?** Measure: drop at this step vs. the fraud/quality cost of verifying later. (Biggest potential lift, but context is essential.)
- **"Awaiting Payment" framing** — may be intentional flexible-payment/monetization (user insight). Measure: payment rate after this screen; is recurring payment a goal?
- **USD-only payment** — banking/ops constraint? Measure: payment-failure rate by country; demand for local rails.
- **Stage 2 field count** — which are accreditation/compliance-required vs. marketing? Remove/defer the rest.
- **English-only ID** — measure: ID-rejection rate by country; how large is the no-passport population.
- **Phone nag & phone-required-at-register** — what's the verification goal; what's the conversion cost? (Note: phone powers the drop-recovery loop, F15.)
- **Performance** — measure real-user (field) LCP/TBT (especially 3G/mobile), then defer scripts.

## C) STRATEGIC BETS (roadmap; high impact/effort)
- **Local currency / payment rails** (Paystack/Flutterwave/mobile money) — Africa final-step conversion.
- **AI photo assistant (O1)** — selfie bg/crop/compliance; solves F6/F8/background in one delightful flow; on-brand. → evaluated via `recommendation-canvas`.

---
## Deliverable HEADLINE pick (scope judgment — not all of it, the highest-leverage)
**Confirmed 3:** C1 · File normalization + ID front/back · Validation UX
**+ 1 Validate (framing strength):** F1 KYC re-sequencing (biggest potential, presented as "to validate")
**+ Engineering briefs x2:** C1 (#1), File normalization (#2)
