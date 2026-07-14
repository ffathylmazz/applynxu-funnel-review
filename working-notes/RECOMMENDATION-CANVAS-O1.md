# Recommendation Canvas — O1: AI Photo Assistant (ApplyNXU identity step)
> Metric north star: **App start → App complete ($25 fee).** Decision-maker: Head of Product / Growth.
> Scope guardrail: AI editing applies to the **selfie only**; the government ID gets **format/size normalization only** (no content alteration) to protect KYC/anti-fraud integrity.

## 1. Business Outcome
Increase App-start → App-complete conversion by lifting completion of **Step 3 (Identity)** — the heaviest wall, right before payment — without weakening identity verification or raising fraud/ops cost. Target: measurable Step-3 completion lift within one quarter of rollout.

## 2. Product Outcome (applicant's voice)
"I can take a photo on my phone, wherever I am, and still submit a compliant ID and selfie on the first try — without knowing anything about file formats, sizes, backgrounds, or cropping."

## 3. Problem Statement
Amaka is a working professional in Lagos applying for the MBA on her phone. She's minutes from paying the $25 fee, but the identity step wants a plain-background, passport-style selfie and an image-only ID under 10MB. Her ID is a PDF scan; her camera shoots 12MB HEIC; her selfie has a busy background. Each gets rejected by a small toast she barely notices. She's done everything else — and quietly gives up at the last step, at her highest intent.

## 4. Solution Hypothesis
**If we** add an in-browser AI photo assistant that auto-makes selfies compliant (plain background, passport crop, face/obstruction check) and normalizes any ID/selfie file (PDF→image, HEIC decode, compress &lt;10MB)
**for** mobile applicants in emerging markets at the identity step
**Then we will** raise Step-3 completion and reduce post-payment ID re-requests — because applicants stop hitting silent rejections.

### Tiny acts of discovery (cheap, fast, falsifiable)
- **Instrument first (1 week):** add analytics to Step 3 — upload attempts, rejection reasons (format/size/quality), and drop. Quantify the prize before building.
- **Normalizer MVP (concierge/thin):** ship only PDF→image + HEIC + auto-compress (no AI editing yet). A/B vs. current. Cheap, subsumes F6/F8.
- **AI-selfie prototype:** client-side background/crop/compliance on the selfie for a small cohort; measure first-try acceptance vs. control.
- **Wizard-of-Oz:** for a handful of applicants, manually "fix" submitted photos and see if approval/complete rate moves — before any model work.

### Proof-of-life
Within ~4–6 weeks of the cohort test: fewer Step-3 rejections per completing user, higher first-try acceptance, and a measurable Step-3 completion lift vs. control — with **no rise** in fraud/manual-review rate.

## 5. Positioning
**For** mobile applicants in 100+ countries **who need** to submit compliant identity photos without technical know-how, **the ApplyNXU photo assistant is a** built-in, in-browser capture helper **that** turns any phone photo into an accepted ID/selfie on the first try.
**Unlike** generic "upload only png/jpg &lt;10MB" forms (and unlike making the user find a third-party converter), **it** does the format, size, background, and framing work for them — server still receives a clean, verifiable image.

## 6. Assumptions & Unknowns
- **A1 (riskiest):** File/photo rejections are a *material* cause of Step-3 drop (not a rounding error). → Instrument before building.
- **A2:** Client-side AI (background removal, face check, HEIC, PDF raster) runs acceptably on low-end Android in-browser without bloating load (perf finding already flags heavy scripts).
- **A3:** AI-edited selfies still pass the downstream face-match/KYC vendor's quality checks (editing must help, not trip, verification).
- **A4:** Applicants trust and understand an auto-edit ("we adjusted your photo") rather than feeling something's wrong.
- **Unknown:** Whether the simple normalizer alone (no AI editing) captures most of the gain — if so, the AI layer is a fast-follow, not day one.

## 7. PESTEL risks
- **Legal (investigate):** Biometric/photo processing & storage — consent + data-residency across 100+ countries; keep processing client-side where possible to limit exposure.
- **Technological (investigate):** Model/lib bundle size vs. mobile performance; on-device inference variability; must **never** alter the government ID (integrity).
- **Social:** "AI changed my photo" distrust; be transparent and always allow manual override / re-take.
- **Economic:** Build/inference cost vs. the $25 fee economics — client-side keeps marginal cost near zero.
- **Political/Environmental (monitor):** AI-regulation shifts; energy of inference (negligible if client-side).

## 8. Value justification
**Yes — with sequencing.** (1) It attacks the most expensive drop (highest intent, pre-payment). (2) One capability collapses several confirmed frictions (F6 PDF, F8 size, F7 examples) plus the background/crop problem. (3) It's **on-brand** — Nexford grants AI degrees; using AI in its own funnel is a credible proof point. (4) Client-side keeps cost low. Caveat: the *AI-editing* layer is a bigger bet than the *normalizer*, so split them.

## 9. Success metrics (SMART)
1. Step-3 (Identity) completion rate **+X%** vs. control within one quarter (X set from the instrumentation baseline).
2. First-try upload acceptance **↑**; upload rejections per completing user **↓** measurably.
3. Post-payment ID re-request rate **↓** (protects won conversions; ties to F9).
4. **Guardrail:** fraud / manual-review / KYC-fail rate **flat or lower** — the feature must not weaken verification.

## 10. What's next
1. **Instrument Step 3** to size the prize (1 week) — go/no-go gate.
2. Ship the **file normalizer** as the MVP quick-win (PDF→image, HEIC, compress) — see Engineering Brief #2; A/B it.
3. If drop is background/quality-driven, prototype the **AI selfie** layer for one cohort; verify KYC still passes.
4. Legal review of photo processing/consent; confirm client-side keeps data exposure minimal.
5. Present results; if validated, roadmap the AI layer as a branded "take it at home, we make it compliant" capability.

---
**Verdict:** MVP = the **normalizer** (confirmed, medium effort — do now). The **AI selfie assistant** = a **strategic bet** — instrument and prototype before committing. Don't build the bet before the instrumentation proves the prize.
