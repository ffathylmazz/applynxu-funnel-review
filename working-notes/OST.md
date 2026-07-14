# Opportunity Solution Tree — ApplyNXU funnel
> Teresa Torres OST. One outcome → drop-points (opportunities, in the applicant's voice) → solutions → experiments. Tags: **Confirmed** (ship), **Validate** (test first), **Bet** (strategic), **Keep** (already works).

## OUTCOME
**Increase App start → App complete (pay the $25 application fee).**

## Opportunities → Solutions → Experiments

### O1 — "I can't get past sign-up"
- **[Confirmed]** Prevent/flag unsupported countries + field-level error (C1)
- **[Confirmed]** Fire conversion pixels only on success (C2)
- 🧪 Instrument register drop by country · verify 2xx-gated pixels

### O2 — "I can't clear the identity step" (heaviest wall)
- **[Confirmed]** Client-side file normalization (PDF→image, HEIC, <10MB) + Front/Back slots (F6/F8/F9)
- **[Confirmed]** Surface + localize the upload example (F7)
- **[Validate]** Move KYC to after payment (F1)
- **[Bet]** AI photo assistant for the selfie (O1)
- 🧪 Step-3 rejection-reason analytics · A/B KYC-after-payment (watch fraud/refund) · normalizer-MVP recovery

### O3 — "I give up on the long/confusing forms"
- **[Confirmed]** Mark required fields + auto-scroll/focus first error
- **[Validate]** Trim / prefill the 14+ Personal-info fields (redundancy)
- 🧪 Field-level drop analytics · A/B a trimmed form

### O4 — "I can't pay at the finish line"
- **[Confirmed]** One source of truth for wire details — checkout ≠ invoice (F14)
- **[Bet]** Local currency / rails (Paystack, mobile money) for the Africa-heavy base
- 🧪 Payment-success by country/method (Nigeria USD-card failure %)

### O5 — "I don't feel signed in / lose trust"
- **[Confirmed]** Google post-signin "Welcome — 2 details" state, out of the register look (F17)
- **[Confirmed / security]** Stop passing the OAuth token in the URL
- 🧪 Post-Google completion rate vs. the manual path

### O6 — "I leave and don't come back"
- **[Keep]** Resume-to-last-step (F12) + human drop-recovery outreach (F15) already work
- **[Bet]** Automated abandonment sequence + resume deep-link ("continue in 1 tap")
- 🧪 Recovery-rate measurement · automated vs. human outreach

## Cross-cutting
- **[Confirmed]** Performance — defer Stripe → checkout, lazy-load widgets/fonts (measured ~21s Slow-3G). 🧪 real-user LCP by device/geo.

## Prerequisite for every experiment
Instrument the funnel first: fix tracking (C2) + step-level analytics. You can't judge any bet on a metric that counts failed starts.
