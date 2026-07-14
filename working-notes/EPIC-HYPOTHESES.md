# Epic Hypotheses — ApplyNXU top changes
> Tim Herbig if/then format. Persona: Amaka (Lagos, mobile-first). Each states the expected conversion direction + a cheap validation (from the de-risk plan). Tagged Confirmed / Validate. Metric: App start → App complete.

## EH1 — Supported-country sign-up (C1) · **Confirmed**
- **If we** stop offering unsupported phone countries (and return a clear field-level error instead of a generic one)
- **for** Amaka and every applicant from a country the UI currently offers but the backend rejects
- **then we will** recover the sign-ups now lost at App start to a silent "An error occurred."
- **Validate by:** register drop-by-country before/after; zero 400s reaching the user; pixels fire only on 2xx.
- **Valid if** (2 weeks): register-completion for affected countries rises toward the baseline of accepted countries.

## EH2 — File normalization + Front/Back (F6/F8/F9) · **Confirmed**
- **If we** auto-convert/compress any ID/selfie (PDF→image, HEIC, >10MB) and enforce front+back
- **for** Amaka uploading on her phone at the identity step
- **then we will** raise Step-3 completion and cut post-payment re-uploads.
- **Validate by:** instrument Step-3 rejection reasons; ship the normalizer MVP first; measure first-try acceptance ↑ and rejections/​completing-user ↓.
- **Valid if** (2–4 weeks): Step-3 completion rises and format/size rejections drop materially.

## EH3 — Re-sequence KYC to after payment (F1) · **Validate (biggest bet)**
- **If we** move identity verification to after the $25 payment (or make it optional at that stage)
- **for** Amaka, who faces heavy KYC before committing
- **then we will** raise App-complete conversion — *if* fraud/quality doesn't rise past the fee economics.
- **Validate by:** A/B move-after-payment for a cohort; compare App-complete AND fraud/refund/no-show.
- **Valid if:** App-complete ↑ meaningfully with fraud/refund flat or within tolerance. (Kill if fraud rises.)

## EH4 — Local currency / rails at checkout · **Validate (bet)**
- **If we** offer local currency/rails (Paystack, mobile money) alongside USD
- **for** Nigerian applicants whose cards are restricted for USD/international
- **then we will** recover final-step drops from payment failures.
- **Validate by:** instrument payment-success by country/method first (Nigeria USD-card failure %); build only if failure is material and integration effort is reasonable.
- **Valid if:** payment-success for NG rises after adding a local rail.

> Prerequisite for all: instrument the funnel (fix tracking C2 + step-level analytics) — you can't judge a hypothesis on a metric that counts failed starts.
