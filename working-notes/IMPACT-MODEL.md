# Impact Model — sizing each change against App start → App complete
> **Worked scenario.** We don't have live analytics access, so this model runs on an **assumed baseline** — read it as *“if Clarity/Mixpanel reported the funnel below.”* Every number is a stated assumption you swap for the real one; the value is the **method, the targets, and the single data point that validates each lever.** (Fixing tracking / C2 comes first precisely so these numbers become real.)

## Assumed baseline funnel (swap in real data)
| Stage | Assumed step conversion | Remaining (of 1,000 App starts) |
|---|---|---|
| App start (register success) | — | 1,000 |
| Email verification | 88% | 880 |
| Previous education | 90% | 792 |
| Personal information (14+ fields) | 80% | 634 |
| **Identity documents (heaviest wall)** | **62%** | **393** |
| **Application fee $25 (App complete)** | **31%** | **~120** |
| **Overall App start → App complete** | | **~12%** |

Two biggest leaks in this illustrative shape: the **identity step** and the **$25 payment** — which is exactly where our top fixes and bets are aimed.

## Target (the number to move)
- **Baseline (assumed):** ~12% App start → App complete (~120 completes per 1,000 starts / mo).
- **Target after the Confirmed set (one quarter):** **~13.5–15%** (+12–30 completes/mo) — non-additive.
- **Stretch, with validated bets (KYC-after-payment, local rails):** **~16–20%.**
- **Guardrail:** the bets ship only if App-complete rises with fraud/refund flat (KYC) and payment-success up (local rails).

## Secondary metric — time to complete (leading indicator)
A friction proxy for the primary metric, and a check against the product's own **"less than 10 minutes"** promise. Faster completion = less mid-flow abandonment, so it pulls conversion with it.
- **Assumed median today:** ~15–20 min. Where the minutes go: PDF/HEIC re-uploads (~3–6), dead-end + off-screen-error retries (~3–4), USD-card retries (~3–5), the 14-field step (~2–3).
- **Target after the Confirmed set:** ~7–9 min (**≈ −45–55%**) — back under the promised 10.
- **Data to make it real:** session-duration / step timing from Clarity/Mixpanel (replace the assumed medians).

## Sizing table (Confirmed)
| # | Change | What sizes it | Assumed range | Est. impact on start→complete | +completes/mo (illus., base 120) | Data to make it real |
|---|---|---|---|---|---|---|
| 1 | C1 supported-country + field errors | share of register attempts on an unsupported country | 2–6% of starts | **+2–6%** | +2–7 | 400s by PhoneCountryCode ÷ attempts |
| 2 | File normalization + Front/Back | Step-3 loss from format/size + post-pay re-uploads | 20–40% of Step-3 loss | **+4–10%** | +5–12 | Step-3 rejection reasons; first-try accept rate |
| 3 | Validation UX (markers, scroll-to-error) | mid-form abandon on silent errors | 1–3% of completers | **+1–3%** | +1–4 | field-level drop on Personal-info; rage-clicks |
| 4 | C2 fix tracking | — (measurement, not a lever) | — | **0 pp** (enables all others) | — | verify pixels are 2xx-gated; compare counts |
| 5 | Surface + localize examples (F7) | upload rejections from poor guidance | 0.5–2% | **+0.5–2%** | +1–2 | rejected-upload rate; example-modal opens |
| 6 | One source of truth for wire (F14) | failed/misrouted wire & sponsor payments | 0.2–1% | **+0.2–1%** | +0–1 | wire/sponsor failure rate |
| 7 | Defer third-party (Perf) | slow-network bounce before App start | 1–4% of starts | **+1–4%** | +1–5 | RUM LCP + bounce by geo/device |

**Confirmed set is not additive** (overlapping users) → roughly **+10–25% relative**, ≈ **+12–30 completes/mo** in this illustrative shape.

## Sizing table (Validate — the bets)
| Change | What sizes it | Assumed range | Est. impact | +completes/mo | Data / test |
|---|---|---|---|---|---|
| KYC after payment (F1) | heaviest wall, sitting before payment | up to ½ of Step-3 loss | **+8–20% (fraud-gated)** | +10–24 | A/B: complete ↑ vs fraud/refund flat |
| Local currency / rails | Nigerian USD-card failure | 20–40% of NG card attempts | **+5–15%** | +6–18 | payment-success by country/method |

## RICE priority (simplified 1–10 scales; Score = Reach × Impact × Confidence ÷ Effort)
| Change | Reach | Impact | Confidence | Effort | Score |
|---|---|---|---|---|---|
| C2 fix tracking | 10 | 5 | 9 | 2 | **225** |
| C1 supported-country | 8 | 9 | 7 | 3 | **168** |
| Validation UX | 9 | 5 | 6 | 2 | **135** |
| File normalization + F/B | 7 | 8 | 7 | 5 | **78** |
| Wire source of truth (F14) | 3 | 6 | 8 | 2 | **72** |
| Surface examples (F7) | 6 | 4 | 6 | 3 | **48** |
| Defer third-party (Perf) | 8 | 5 | 6 | 5 | **48** |
| KYC after payment (bet) | 8 | 9 | 4 | 6 | **48** |
| Local rails (bet) | 7 | 8 | 5 | 7 | **40** |

**Read the two tables together.** RICE rewards cheap certainty — so tracking (C2) and C1 top it. File-normalization scores only mid on RICE (more effort, moderate confidence) yet is among the **highest on raw conversion impact** in the sizing table. The sizing model tells you *how big*; RICE tells you *how efficient*. Ship the high-RICE quick wins now; the high-magnitude, higher-effort items and the bets follow with a measurement.
