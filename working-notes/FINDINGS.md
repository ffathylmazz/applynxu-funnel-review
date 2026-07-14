# NexFord ApplyNXU — Funnel Walkthrough Notes
> Metric: **App start → App complete conversion rate.** Every observation ties back to this.
> Test persona: Fatih Yilmaz · Odessa, Ukraine (desktop) / Lagos, Nigeria (mobile) · MBA · onyilmazllc@gmail.com
> 📸 Screenshots: `screens/` — flow-order folders (01-desktop / 02-mobile / 03-edge / 04-payments / 05-drop-recovery / 06-qa) + map: `screens/INDEX.md`

## STAGE 0 — Entry / Registration (`/v2/register`) = "App start"
Screenshots: `screens/step1.png`, `step1-mobile.png`, `live-06-register-final.png`

### Two entry paths (user's observation)
- **A) "Continue with Google"** — 1 tap, name+email auto, no password, skips email verification → low friction.
- **B) Manual form** — 6–7 fields + phone + consent + email verification → higher friction.
- Question: is each path's conversion measured? Does Google skip phone/consent/city (consistency)? Is Google advantaged by skipping email verification?

### F17 — "Continue with Google": useful but the post-signin experience is confusing (user completed it → OBSERVED)
Screens: `03-edge-and-errors/google-oauth-entry.png` (OAuth), `google-post-signin.png` (return), `google-landing.png`.
- **OAuth screen:** real "Sign in with Google → nexford.edu", **minimal scope openid/profile/email**, `prompt=select_account`. 1 tap. ✅
- ✅ **Benefit (confirmed end-to-end):** name+email **prefilled** from Google; fill phone/city/program + Register Now → lands **straight on `/application/education`, no email-OTP screen at all** (network: no `send-verification-code`, `learners/self/new` 201, OTP screen: false). Manual = register+OTP+Step1; Google = 1 tap + 3 fields + Step1.
- 🔴 **But the post-signin is confusing (user experienced it: "didn't feel signed in"):** after Google auth the user is returned to the **same register-styled form**, headed *"We need some additional information to complete your registration with Google."* Phone/City/Program + consent still empty → "Register Now" again. Because the screen still reads "Register / Start your education journey / Continue with Google", it feels like sign-in didn't take. **Fix:** a clear "Welcome, Fatih — just 2 more details" state, out of the register look.
- 🔒 **Security:** the OAuth access token is exposed in the return URL query (`/v2/register?token=ya29...&auth_type=google`) → leak risk via history/logs/referrer. Tokens shouldn't travel in URLs.
- 🟡 Name prefilled in ALL CAPS (FATIH YILMAZ) — should be normalized. Phone still defaults to **+1** (C1).

### What's good (for conversion)
- "Less than 10 minutes to apply" — clear expectation-setting.
- Consolidated on one screen, clean design, strong hero.
- Google sign-up option (fast path).
- Selecting a program shows a description + specialization → decision support.

### Friction hypotheses (to validate)
1. **Phone country default = +1 (US)** but Nexford is global/emerging-market focused → every user must change it manually.
2. **Country selector: 245 countries, NO search box** → finding your country (especially on mobile) is tedious.
3. **Consent checkbox is a custom element, not a native `<input>`** → accessibility risk (keyboard/screenreader).
4. **Is phone required at "App start"?** If so, friction + triggers sales calls; some may hesitate.
5. **City/locality required + async search** → extra effort; is it really needed at registration?
6. **Program of Interest + Specialization BEFORE registration** → decision load before an account exists (abandonment risk) vs. higher lead quality (trade-off).
7. Progressive disclosure lengthens the form: Type → Program → (description) → Specialization.

### To measure / open questions
- Are phone/city/program "required"? (validation test)
- Step difference between the Google path and the manual path?
- What exactly is the "App start" event? (form view, Register Now click, or account creation?)

## 🔴 CRITICAL FINDINGS (Register Now submit) — screens: `step2-after-register.png`, `step2b-register-error.png`

### C1 — Ukraine phone code rejected by backend, generic error to the user
- Endpoint: `POST /api/public/learner-profile/learners/self/new/validate` → **400**
- Payload error: `PhoneCountryCode "380" → "The specified condition was not met for 'Phone Country Code'"`
- UI lists 245 countries but the backend rejects certain codes (possibly allowlist/sanctions).
- User sees: **"An error occurred. Please try again."** — no field, no reason, no next step.
- **Conversion impact:** a user who completed the whole form drops hard at App start. The most expensive drop (lost at highest intent). Affects: every country offered in the dropdown but rejected server-side.
- **Fix directions:** (a) don't offer / clearly flag unsupported countries, (b) field-level message ("This phone country isn't supported yet — try..."), (c) remove the generic error.

### C2 — Conversion pixels fire on failed submit
- Register Now (despite a 400) triggered: Google Ads form-data/rmkt (multiple accounts), TikTok pixel, Mixpanel `track`, Ahrefs.
- **Impact:** the "App start / lead" conversion event counts even on a failed attempt → the metric they optimize inflates/corrupts. The data optimization relies on is wrong.
- **Fix:** fire the conversion event only after a 200/successful registration.

## 🗺️ FUNNEL MAP (App start → App complete)
```
[App start]  /v2/register  ──►  Email verification gate (/verification-code/verify)
      │                              (the Google path skips this gate)
      ▼
  APPLICATION (4 steps, top progress bar):
  1) Previous education   /application/education
  2) Personal information
  3) Identity documents          ← document upload (high friction)
  4) Application fee checkout     ← PAYMENT = "App complete" (paywall)
```
- Endpoint evidence: `POST /api/learner-profile/learners/self/new` → 201 (profile created at App start).
- Note: to continue the walkthrough the test phone was set to Nigeria +234 (due to C1); persona stays Odessa.

## STAGE 1 — Previous education (`/application/education`) — screen: `step3-after-verify.png`
### Good
- Left progress bar (4 steps clearly visible) → expectation-setting.
- Only **one required field** here: "highest completed education level"; transcript upload is **optional** → low friction.
### Friction / observation
- S1. A persistent orange **"+234... not verified – Verify now"** banner (throughout the funnel, including payment) → phone verification is pushed while email suffices; anxiety/distraction, and unnecessary if it isn't a gate.
  - "Verify now" → modal **"Verify Phone Number"** (screen: `step9-verify-phone.png`): code sent **via WhatsApp only**. ✅ Audience-appropriate (WhatsApp is huge in Africa) but 🟡 **WhatsApp-only, no SMS/voice fallback** → a number without WhatsApp is stuck. Also a radio-selector for a single option is needless UX. Verification is **optional** (doesn't block the funnel) but the persistent nag insists.
- S2. The **"Finish faster and pay less"** (prior-learning credit) box on a critical form step → value prop but a distraction; its placement in the flow to checkout is debatable.
- Open question: is education level "required"? Test an empty "Save & Continue".

### Stage 1 extra finding (progressive disclosure)
- Selecting education level = "Bachelor's Degree" reveals **3 required fields**: Degree Name, Graduation Year, Institution. Continuing empty → field-level validation ("...is required") appears (good: clear error messages). Note: Institution is a **searchable dropdown** (pick from a list), and selecting one reveals "Institution Country".
- Endpoint: `POST /api/learner-profile/v2/self/education` → 200 (step save).

## STAGE 2 — Personal information (`/application`) — screen: `step4-personal-info.png`
### Observation
- **Heaviest step in the funnel: 14+ fields** → First/Last Name, DoB (3 dropdowns), Address(+Line2), City, State, Postal, Country, Phone, Nationality, Gender, Native English speaker?, How did you hear about us, LinkedIn URL.
- **Data redundancy:** Name, Phone, Country/City were already collected at Stage 0 (register) → asked again here (redundancy = friction). Country "Ukraine" prefilled (derived from city, good).
- This step alone is a high drop-off candidate (form fatigue). Which fields are truly needed for "App complete"? (e.g., is LinkedIn / "how did you hear" / gender for enrollment or marketing?)
### Bug (monitor.log)
- `CONSOLE.ERR [react-phone-number-input] Expected the initial value to be a E.164 phone number. Got +234 8031234567` → the phone field's prefill is malformed; potential phone-field bug / tech debt.

### Validation/error UX (user's observation — screen: `step5b-error-state.png`)
- **Required fields aren't marked upfront** — no asterisk (*)/"required" label. A user learns a field is required only by failing "Save & Continue" (trial-and-error) → friction.
- **Errors are inline only** (red box + "X is required"), **no summary at top, no auto-scroll to the first error.** On a long form the error can sit off-screen → the user thinks "the button's broken" and bails (silent drop).
- Upside: the message text is clear ("Postal/zip code is required") and the field is outlined red — it's only a discoverability/placement issue.
- **Confirmed (empty-submit test, `03-edge-and-errors/register-empty-validation.png`):** submitting register empty shows field-level messages ("First name is required", "Phone number is required", "Country is required", "Program type is required") **but no label has an asterisk (*)** → required fields aren't marked upfront, learned only on error. On short forms the errors are visible; on the long Personal-info step they fall below the fold (the real problem is there).
- **Fix:** (a) mark required fields, (b) on submit, auto-scroll + focus the first errored field, (c) optional: an error summary at top.

### Performance finding — NOW MEASURED (screen: `03-edge-and-errors/perf-register-slow3g.png`)
- **Register (App start), Slow-3G / mobile / cache off:** load event **~21.5s**, DOMContentLoaded **~10.6s**, **516 KB**, **16 of 21 requests (~76%) third-party** (only 5 first-party).
- Top third-party: **getwarmly.com — 7 requests** (Warmly sales/visitor widget), Google Fonts (3), **Stripe SDK (2)**, Mixpanel (3). (Other steps also: Snapchat, LinkedIn Ads, HubSpot, App Insights.)
- 🔑 **New insight:** the **Stripe payment SDK loads on the REGISTER screen** — yet payment is 4 steps away (checkout). Unnecessary library weight at App-start.
- **Impact:** for a mobile-first, slow-network emerging-market audience, a **~20s first screen** = drop risk before App-start even happens + Core Web Vitals.
- **Fix (Confirmed, now with data):** defer/lazy non-critical third-party — especially **load Stripe only at checkout**, lazy-load Warmly + fonts. Validate with real RUM/LCP field data.

## STAGE 3 — Identity documents (`/application/identity`) — screen: `step6-identity-documents.png`
Endpoint (Stage 2 save): `POST /api/applynxuaggregator/v2/self/personal-information` → 200
### This step is the funnel's HEAVIEST friction wall (full KYC BEFORE payment)
- Asked: **Legal Name** + **Government ID upload** (English; photo+name+DOB+valid+clear; passport if not English; front+back as separate images) + **Selfie** (take/upload; no glasses/hat/filter).
### 🔴 Critical frictions (conversion)
- **F1 — Identity verification is BEFORE payment (App complete).** The user must do high-effort tasks (find ID, take a selfie) before committing anything. Strongest *scope/sequencing* insight: move this after payment / after admission, or make it optional at this stage → shortens the path to checkout.
- **F2 — "English-language ID" requirement.** Nexford's core audience (non-English emerging markets) → national ID often not in English → passport required; many don't have one = hard barrier.
- **F3 — Selfie required.** Privacy friction + awkward with a desktop webcam (easy on mobile). Extra drop.
- **F4 — Documents not on hand.** Many users can't produce ID/selfie right then → session abandonment. "Save & resume" exists (via Login) but isn't visible/reminded on this step.
- **F5 — Mobile load:** front+back+selfie = 3 separate uploads; possible via phone camera but long.
### Open question
- Is this step really needed to count an application "complete", or does it belong to admission/enrollment? Is "App complete" defined here or at checkout?

### 🔴 F6 — File-format inconsistency + weak error (user experienced it) — screen: `step6b-pdf-rejected.png`
- **ID & Selfie accept only `.png,.jpg,.jpeg`** (`accept` attr confirmed). **PDF rejected.** But the **Transcript (Step 1) accepts PDF/doc** → inconsistency.
- Rejection feedback: a **toast in the top-right corner** "The following files are not supported: test-id.pdf" → far from the dropzone, auto-dismisses, **no why/how-to-fix**.
- **Conversion impact:** a user whose ID is a PDF (scan) must convert PDF→image; hard barrier for low-digital-literacy/mobile audiences. Silent drop at the highest-intent, closest-to-payment step.
- **User insight:** "PNG is fine; the real problem is people with other formats — not everyone can convert a PDF to PNG."
- **WHY no PDF (deliberate constraint — evidence-based hypothesis):** ID+selfie feed an automated **KYC pipeline**: (1) selfie↔ID **face matching** needs raster images; (2) name/DOB **OCR** (English-ID + "type it exactly" hints); (3) "we combine front+back" = image processing; (4) "Take photo"/camera-first flow yields JPEG; (5) PDF security/parse risk. Transcripts are human-reviewed so PDF is fine → the source of the inconsistency.
- **Reframed fix (more mature):** keep the constraint, remove the friction → **auto-convert first page → image if a PDF is uploaded** (client or server). The user does nothing; the KYC pipeline still gets an image. Alternative/addition: field-level error + "how to convert" help. → **Engineering brief candidate (strong).**

### F7 — "See example" guidance is strong but hidden (user suggestion) — screens: `step6c-example-government-id.png`, `step6d-example-selfie.png`
- Modal content is quality: ID examples (driver's license, ID card, passport) + a passport-style selfie example → clear guidance for correct upload.
- **Problem:** only a small "See example" text link; most users don't click → wrong/blurry/non-English/wrong-crop uploads → rejection → retry → drop.
- **Fix (low effort / medium impact):** make the example persistently visible — a small thumbnail beside the dropzone + a prominent "See example". Lowers rejection → raises completion.
- Extra: ID examples are entirely US-style ("HOMETOWN, USA", US passport) → for a global audience, add passport/national-ID variety.
- 🔴 **Personalization gap (user finding):** the system **knows** the applicant's country (phone +234 + Lagos = Nigeria) but still shows a **US-specific** Government ID example — not adapted to the user's country (e.g. Nigerian NIN/passport). Country-based examples/copy → higher correct-upload, lower rejection. Low-med effort, high relevance for a 100+ country audience. "Confirmed" improvement.

### 🔴 F8 — 10MB size limit, no auto-compression (user experienced it) — screen: `step6e-oversize-rejected.png`
- Test: 11MB .png → toast (top-right): **"The following files exceed the maximum size of 10 MB: big.png"**. (Same weak placement pattern as the PDF error.)
- **Conversion impact:** modern phone camera/scan photos easily exceed 10MB → the user shoots their ID/selfie, gets rejected. Most can't compress manually → drop at the highest-intent step.
- **Fix:** client-side auto resize/compress (shrink the image to ~<10MB before upload) → the user does nothing. Same "auto-normalize" family as F6 (PDF→image) → **Engineering brief candidate (combined: file normalization).**

### F9 — Front/back not enforced (Confirmed) + post-payment rejection (Inferred, WE NEVER PAID)
> Honesty correction (user challenge): separating observation from inference; we never paid.
- ✅ **OBSERVED (Confirmed):** the copy says "Upload front and back as separate images" but we uploaded a **single image** (`dummy-id.png`) and could **continue to checkout** — no enforcement. (Evidence: `step7-checkout.png`, `step6f-identity-filled`.) Also at submit `POST /learner-identity-api/submit → 201`, and **dummy images weren't rejected in real time → verification is async.**
- ⚠️ **INFERRED (not observed):** since the ID is incomplete + verification is async, rejection likely comes **later (perhaps after payment)** → refund/support/churn. **BUT we didn't pay and didn't observe a rejection** → this is a risk/hypothesis, not evidence. To confirm: when does incomplete-ID rejection actually fire relative to payment?
- **Confirmation (input attrs):** Government ID input is `multiple:true` (front+back can both be added) but proceeds with one = not enforced; Selfie is `multiple:false`.
- **"Take photo" (user question):** **couldn't be determined in emulation** — inputs have no `capture` attr, and clicking produced no in-browser camera (getUserMedia) UI or error. The mechanism can't be observed without a real device/camera (no overclaim). Mobile toasts (F6/F8) also confirmed on mobile: `m10-pdf-rejected`, `m11-oversize-rejected`.
- **Fix (medium impact / low-med effort):** (1) two labeled slots "Front of ID" + "Back of ID" (both required); or (2) document-type aware: Passport=1 slot, national ID/license=2 slots; (3) at minimum: single-file warning. "combine" already implies server-side image processing → low added effort. → Recommendation/engineering-brief candidate.

## STAGE 4 — Application fee checkout (`/application/checkout`) = "APP COMPLETE" — screens: `step7-checkout.png`, `step7b-payment-options.png`
Endpoint (Stage 3 save): `POST /api/learner-identity-api/submit` → 201. **Note: dummy images were accepted → NO real-time ID verification (async/later).**
### Findings
- **"App complete" = paying the $25 non-refundable application fee** (company research confirms). Balance $25.
- Billing info is **prefilled** (name/email/address/city/zip) → no re-entry (GOOD).
- Coupon code field (good for fee waivers/promos).
- **Payment options:** USD Debit/Credit Card · USD PayPal · Request Sponsor Payment · Bank Transfer.
  - 🔴 **All USD.** No local currency/local payment (Nigeria: Paystack/Flutterwave/mobile money/Naira). In Africa, USD/international card restrictions are common → **drop risk at the final (payment) step.** Tuition is country-adjusted but the application fee is USD-only = inconsistency/opportunity.
  - **Payment UI changes by option** (screens: `step7c-pay-paypal`, `step7d-pay-sponsor`, `step7e-pay-banktransfer`, `step7f-pay-card`):
    - ✅ **Request Sponsor Payment** (sponsor name+email → "Send Invoice to Sponsor") = very audience-appropriate (parent/employer pays), smart feature.
    - 🔴 **Bank Transfer** = an **international USD wire** to a US bank (routing/SWIFT) + **manual**: the user emails the receipt+transaction hash to billings@nexford.edu. For $25, wire fees often exceed the fee itself + slow + manual reconciliation → "App complete" isn't instant.
    - Card (Stripe) & PayPal = USD.
  - Net: option variety is good but **no local currency/automatic local rail**.
- ⚠️ The "phone not verified" banner persists **even at the payment step** (a nag throughout the funnel).
### Sponsor flow + "Awaiting Payment" state (screens: `step7g-sponsor-filled`, `step7h-sponsor-sent`)
- ✅ **Sponsor invoice** (name+email → Send Invoice) works (`POST /api/sponsor-api/v1/self/requests` 200) → very audience-appropriate. Good.
- 🟡 **"Awaiting Payment" + success styling — probably INTENTIONAL (trade-off, not clearly a bug):**
  - The screen shows a green ✓ + "Thank you for applying!" but the status is "Awaiting Payment" (not App complete).
  - **One reading (risk):** the "done" feeling could reduce payment urgency.
  - **Other reading (user insight — stronger):** it may be a deliberate flexible-payment/monetization mechanism — "Pay today, sponsor's payment applied to **future fees**" = turns a one-time $25 into recurring tuition funding; captures more payment in one flow without sending links repeatedly. Consistent with the monthly-subscription model.
  - **Conclusion:** which reading holds depends on **use-case + data** (payment rate after this screen? is recurring payment a goal?). → Present as an assumption to validate, not a prescription.
- Note: the "App complete" definition matters — this screen says "applied" but payment hasn't happened. The metric definition must be clear (applied vs paid).
- **Sponsor email evaluation** (arrived at ffathylmazz, "University Tuition Request from Fatih"):
  - ✅ Clear amount+purpose (Amount $25.00 / Purpose: Application Fee), one clear CTA (personalized payment link), **PDF invoice attached**, mobile-responsive, WhatsApp support.
  - 🟡 Copy inconsistency: subject+body say "tuition fees" but it's the $25 **application fee** → a sponsor could be confused. "Fatih " trailing space (polish).
  - The email button → a **separate payment portal** `paynxu.nexford.edu/sponsor/<token>` (screen: `step7i-sponsor-payment-portal`). Amount **$25 prefilled** ✅. Payment: USD Card + PayPal (again USD-only). Invoice PDF attached (invoice_2285.pdf) but no download on the portal. 🆕 The portal has an **"Ask NXU" AI chatbot** (different assistant from the main funnel — on-brand but an inconsistent experience). Note: the sponsor flow is a separate codebase/experience from the main checkout → consistency/maintenance question.

### Coupon code — GOOD error pattern (screen: `step8-coupon-invalid.png`)
- Invalid code (`SAVE100`) → **inline red right under the field** "This coupon code is invalid". Right place, clear.
- 🔑 **Key implication:** the app **CAN do inline field-level errors** (coupon + form validation). So the upload top-toast (F6/F8) and the register generic error (C1) aren't a capability gap — they're an **inconsistency** → easy to fix, "confirmed" (apply the same pattern everywhere).

### 🔴 F14 — Bank-transfer details DISAGREE between checkout and the invoice PDF (user saved the PDF → confirmed)
Screens/file: `step7e-pay-banktransfer.png` vs `invoice_2285.pdf` (`step7j-invoice-email.png` = email render).
- **Checkout Bank Transfer:** "Bank of **America**, **222** Broadway, New York 10038"
- **Invoice PDF:** "Bank of **New York**, **22** Broadway, New York City 10038"
- Account (226001335778) / Routing (026009593) / **SWIFT `BOFAUS3N`** are all **identical** — and BOFAUS3N = **Bank of America**. So the invoice PDF's "Bank of New York, 22 Broadway" is **wrong/inconsistent.**
- **Impact:** two different bank names+addresses for the same $25 wire → sponsor/user confusion, risk of wiring to the wrong beneficiary, payment delay/failure = final-step (App complete) drop. International wires are already expensive/slow (see USD-only finding); conflicting info on top.
- **Fix (Confirmed):** one source of truth — checkout and invoice pull the same bank name/address; confirm the correct bank (Bank of America per the SWIFT).
- ✅ The invoice PDF uses the **correct term** ("Application Fee") → the email's "tuition fees" is the wrong copy (confirms the copy fix). Invoice design is clean/on-brand.

### Open question
- Is the $25 one-time or are fee waivers common? (the coupon field is a hint) — how real is the fee barrier?

---
## 📱 MOBILE PASS (full funnel, iPhone emulation) — screens: `m1..m6`
Fresh mobile registration (onyilmazllc+nxumobile, Nigeria/Lagos, MBA) → register→verify→education→personal→identity→checkout, all captured on mobile.
### Observations
- ✅ **Responsive layout generally good:** single column, fields stack cleanly, "Checkout Summary" moves to the top on checkout (sensible).
- 🟡 **M2 — Long forms = long scroll on mobile:** Personal info (14+ fields) is a very long scroll on mobile; no in-step progress/sections.
- 🟡 **M3 — Custom dropdowns (Institution Country, Nationality, Month, phone-country) are 245-250-item, search-less lists** → tedious to scroll on mobile (same pattern as the register phone-country; consistent with F/S findings).
- Extra: Education has an **"Institution Country"** required field (appears once Institution is chosen) — progressive disclosure deepens.
- ✅ **GOOD (user discovery):** the progress-bar step numbers are **clickable → you can go back to a completed step and edit**. (Note: direct-URL back redirects, but in-app step navigation works.) Gives the user control/confidence — pro-conversion.

## 🔑 F12 — Login / resume flow (screen: `step10-login.png`)
- "Returning to your application? Login" → **"Welcome Back!"** screen: **passwordless** (Email → Continue → OTP) + Continue with Google + "New to Nexford? Create an account". Consistent with the passwordless design.
- ✅ **Resume exists AND fully works (confirmed end-to-end, screen: `step10c-login-resumed.png`):** logging back in via email→OTP → **drops you straight at your last step (checkout)**, prior steps "complete", no data re-entry. A user can leave the heavy Identity step to find a document and return safely → **materially mitigates F4.** Login = same passwordless OTP mechanism as sign-up (consistent).
- Recommendation (Validate): is the resume path **visible/reminded** enough? Especially on the Identity step, surfacing a "save & come back" message would reduce document-not-ready drop.

## 🔀 F13 — Sign-in vs Sign-up: redundant but wrong-path handled GRACEFULLY (user finding + verified)
Edge behaviors tested live (screens: `test-login-unknown.png`, `test-register-existing-email.png`):
- **Login with an unknown email** → clear message: *"The email entered does not match any accounts in the system"* + a **"Create an account"** link. ✅
- **Register with an existing email** → validate 200, `send-verification-code` **409**; UI shows a clear message: *"We see that your email address is already linked to an existing application. Please **Login** or **Contact Support**."* ✅ (field-level, action-linked — **NOT a C1-family dead-end**).
- 🔑 This reinforces the main argument: the app **can do clear inline errors** (coupon + auth cross-routing) → C1 (register phone-country) and the upload toasts (F6/F8) are **inconsistencies, not capability gaps.**
### Remaining (minor) — redundancy / simplification (nice-to-have, not critical)
- Two switching paths (top Login|Register toggle + bottom "Returning?/New?") + Google on both tabs (OAuth is already create-or-signin).
- **Recommendation:** optional **identifier-first** (one "enter your email", the system routes) → reduces cognitive load and redundancy; but since the current cross-routing is good, **low priority**.

## 🌍 F11 — Identity content localizes BY COUNTRY but is INCONSISTENT (user discovery) — screens: `m5-identity.png` (Nigeria) vs `step6-identity-documents.png` (Ukraine)
- **Nigeria (Lagos) mobile:** "Accepted Documents: **Virtual NIMC ID, Passport, Driver's License, Birth Certificate (with photo), Voter's ID**" + "Rejected Documents: **NiN slip**" → country-specific, very clear.
- **Ukraine (Odessa) desktop:** generic "Upload an **English-language** government ID: passport, national ID, driver's license, or state ID" + US example images.
- **Implication:** the system **can localize the accepted-documents list to the applicant's country** (the capability EXISTS). But it's inconsistent/incomplete:
  1. **"See example" images don't localize** — even a Nigerian user is shown a US driver's license/passport (F7 ext).
  2. **The "English-language ID" requirement disappears** in the localized version → nuances F2: the barrier isn't shown in every country, it's country-dependent.
  3. Desktop (Ukraine) got the generic fallback → weak experience for unsupported countries (linked to C1).
- **"Take photo"** opens the native camera on mobile (webcam on desktop) → since the audience is mobile-first, this is the PRIMARY path for ID/selfie; the camera-capture flow (HEIC/size normalization) is critical (linked to Eng Brief #2).
- **Recommendation (Confirmed + lever):** the localization capability already exists → (a) localize the example images by country too, (b) cover more countries, (c) ensure desktop/mobile and text/image consistency. Low-med effort, high relevance for a 100+ country audience.

## 💡 OPPORTUNITIES
### O1 — AI photo assistant (user idea) — for the selfie: "take it at home, we make it compliant"
- **Problem:** selfie/photo requirements (plain/white background, passport-style crop, no hat/glasses, clear face, <10MB) aren't met by most users → rejection → drop at the highest-intent step.
- **Solution:** in-browser AI: replace background with white + crop to passport ratio + compliance check (face/glasses/hat) + auto-compress <10MB. One feature solves F6+F8+background/crop frictions at once.
- **Critical nuance:** apply to the SELFIE only; **NEVER to the Government ID** (AI-editing an ID breaks KYC/anti-fraud integrity → ID gets format/size normalization only, no content change).
- **Trend/credibility:** passport/visa (incl. US) photo tools ("take it at home, we make it compliant") are proliferating; client-side segmentation tech is ready.
- **Brand fit:** Nexford grants AI degrees → using AI in its own funnel is on-brand.
- **Prioritization:** MVP = file normalization (low effort); Strategic bet = AI photo assistant (high impact). Evaluated via `recommendation-canvas` + `prioritization-advisor`. (Full canvas in `RECOMMENDATION-CANVAS-O1.md`.)

# ✅ FUNNEL COVERED END-TO-END — DESKTOP + MOBILE. Deliverable (artifact), 2 engineering briefs, recommendation canvas, presentation script all produced.

---

## Additions from the 12 Jul separate test run (source: NXU_Funnel_Analysis draft — re-verify before shipping)

### E1 — "Not a native English speaker" dead-ends the form ⚠ CRITICAL
- **Observed (12 Jul, reproduced & confirmed by toggling):** answering "No" to "Are you a native English speaker?" should reveal an English-Proficiency field; it never renders. Submit returns "Your English Proficiency level is required" with no such field on the page. Toggling to "Yes" clears it.
- **Why it matters:** unrecoverable mid-funnel dead-end for the non-native-English majority of a 100+-country base ("No" segment plausibly 25–40%). Potentially the single largest leak.
- **Fix:** render the conditional field (or drop the rule); invariant: validation may only require rendered fields + regression test. **Size via % of "No" answers vs their step-completion.**

### R2 — Phone country code doesn't follow the chosen city (12 Jul)
Amsterdam city + default +1 → "Invalid phone number" with no hint the selector is the problem. Same class as C1's fix (field-level errors).

### R3 — Dynamic field injection shifts the submit button (12 Jul)
Choosing Degree/MBA injects new fields that push "Register Now" down with no cue — misclick risk. V-UX family.

### R4 — Google sign-in returns no last name, unflagged (12 Jul)
Required field left empty with no visual flag. F17/V-UX family.

### MKT — Acquisition promise vs form reality (Validate)
"Less than 10 minutes" promise vs mid-flow government ID + selfie + $25. Unmet promise makes the form absorb blame it didn't create. Either name the real steps upfront ("10 min + ID + $25") or remove the friction the promise assumes away. Measure: drop at the identity reveal by acquisition channel. (Mockup: Downloads/files/NXU_Marketing_Mismatch_Mockup.html)

### NEXA — Support chatbot answers the wrong funnel stage (out of core scope, flagged)
Asked generically "what happens after I make the payment?", the Nexa chatbot answered about tuition/enrollment (3 steps downstream) without disambiguating; asked precisely ("application fee"), it answered correctly (48h review → decision by email). Same root pattern as the form bugs: the system assumes user precision. Cheap fix: disambiguate on ambiguous input.
