# Proto-Persona — "Ambitious Amaka" (primary ApplyNXU applicant)
> ⚠️ **Proto-persona** — assumption-based, built from public research signals (Nexford's Africa/Nigeria-heavy, mobile-first, working-professional base) + our funnel walkthrough. To be validated with real applicant data. Grounds the review; every finding maps to her. Tied to App start → App complete.

## Who she is
- **~30, Lagos, Nigeria.** Works full-time (e.g. ops/marketing/analyst at a mid-size firm).
- Applies **on her phone, after work**, on mobile data.
- **Non-technical**, cost-sensitive: the $25 app fee matters; tuition is ~$200/mo.
- Wants a **US-accredited MBA without leaving her job or country.**

## Quotes (representative, from walkthrough behavior)
- "If it says less than 10 minutes, I'll do it now on my phone."
- "I filled everything and it just says 'an error occurred' — is this even real?"
- "My ID is a PDF and my selfie's too big — why won't it just take them?"
- "I'm ready to pay, but my card won't do dollars."

## Goals / Jobs-to-be-done
- **Advance my career** with a credible, affordable, flexible degree.
- **Apply quickly** on my phone, without documents I don't have ready.
- **Trust** that this US school and payment are legitimate.

## Context & constraints
- Mobile-first; slow/expensive data → **page weight & long forms hurt** (Perf, Stage-2).
- National ID often **not in English**; ID stored as a **PDF scan**; phone shoots **HEIC**, photos **>10MB** (F2, F6, F8).
- **Nigerian card restrictions** on USD/international payments (checkout USD-only).
- **Low friction tolerance** (applying after a long day); **fragile trust** (is this real?).
- Reachable and responsive on **WhatsApp** (verification + drop-recovery).

## Frustrations (mapped to findings)
- Sign-up dead-ends on a generic error when her country is rejected → **C1**.
- Can't upload her ID (format/size) and faces heavy KYC before paying → **F6/F8/F9/F1**.
- Can't pay in a way her bank allows → **USD-only**.
- After Google sign-in she's bounced to a register-looking screen → **F17**.

## What would make her convert
- Accept her country + tell her clearly if something's wrong.
- Take whatever ID photo she has (auto-convert/compress); don't front-load KYC before payment.
- Let her pay the way her bank allows; keep the payment form unobstructed on mobile.
- Fast first screen; short forms; and if she leaves, an easy 1-tap way back (resume + recovery already exist).

## Assumptions to validate
- Device/OS mix (Android/HEIC prevalence), % on slow networks, USD-card failure rate, how many drop for language/format reasons. → the de-risk plan's instrumentation.
