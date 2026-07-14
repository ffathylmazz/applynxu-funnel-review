# Off-funnel recovery — the channel that pulls dropped applicants back
> The core review optimizes the in-product funnel (App start → App complete). But a big share of "lost" applicants aren't lost — Nexford runs a **human-assisted, multi-channel re-engagement loop** that brings them back to finish and pay. It moves the *same* metric, from outside the UI. It's a genuine strength — and today it's **un-instrumented and under-automated.**

## What we observed (evidence)
Testing with real accounts, every dropped/incomplete application was followed up across **three channels**:
1. **Email — always.** Across **3–4 separate test accounts**, each received a personalized follow-up email from a **different named "Nexford Application Specialist"** — suggesting a per-applicant ownership model (assigned or round-robin), not a generic blast.
2. **Phone call** — a next-day call (observed a missed call from a US number).
3. **WhatsApp** — when the call wasn't answered, a WhatsApp message from a named specialist with quick replies + a scheduling offer.

**Example email (verbatim, one specialist):**
> Hi Fatih,
> I'm curious to see if you are still interested in the Master of Business Administration. Let me know either way and I can amend your application over here. What makes sense as a next step, if any?
> If you're still unsure, check out these helpful links… [learner testimonials] … [alumni survey: promotions, leadership roles, higher earnings] … Still having doubts? Let's chat! Reply to this email or add me on WhatsApp …
> — Taufeeq Ajam, Nexford Application Specialist · Book a time with me | Chat on WhatsApp

## Why it's valuable (tie to the objective)
- It **re-captures drop-offs** → recovered applications complete and pay the $25 → it lifts the very metric under review, from outside the funnel.
- **Personal + named owner + soft, human tone** ("I can amend your application over here") builds trust for a fragile, emerging-market audience (Amaka) deciding if a US school is real.
- **Multi-channel** (email always, plus call/WhatsApp) matches the audience — WhatsApp is where this base actually replies.
- It also explains a UX choice: **phone is collected early** precisely to enable this loop.

## The gaps (opportunities — all tie back to conversion)
1. **It's unmeasured.** No visibility into recovery-attributed completions, best channel, best timing, or cost per recovered app. → instrument it (see `TRACKING-PLAN.md` §4: `recovery_email_sent/opened/clicked`, `application_resumed{resume_source}`, `app_completed{recovered,recovery_channel}`).
2. **The email CTA is high-friction.** It asks the applicant to *reply* or *book a time* — not a **1-tap "Resume & pay" deep link** back to their last step. Adding a resume-and-pay link (and a "Pay now" for identity-cleared applicants) removes the friction between "yes I'm interested" and "App complete."
3. **Human-only doesn't scale.** A named specialist per applicant is high-touch and great for quality, but caps volume. → **automate the sequence** (timed email + WhatsApp) with the human specialist as escalation for high-intent replies; A/B automated vs human to find the mix.
4. **Timing/sequence isn't optimized** because it isn't measured — once instrumented, test cadence (e.g., T+1h email, T+1d WhatsApp, T+3d call).

## Recommendation
- **[Keep]** the human-assisted, multi-channel, personalized loop — it's a real lever; don't dilute the personal tone.
- **[Confirmed]** add a **1-tap "Resume your application / Pay now" deep link** to the recovery email + WhatsApp (cheap, removes friction at the moment of intent).
- **[Confirmed]** **instrument the recovery loop** (TRACKING-PLAN §4) so it can be judged and scaled.
- **[Validate / Bet]** an **automated abandonment sequence** (email + WhatsApp) with human escalation; A/B automated vs human; measure recovery-attributed completions and cost per recovered app.

> Net: the funnel fixes stop people leaking out; the recovery loop pulls back the ones who do. Both raise App start → App complete — so both belong in the plan, and both need the same instrumentation to prove and scale.
