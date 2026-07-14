# Engineering Brief #1 — Eliminate the silent "unsupported country" dead-end at App start
> Priority: **Tier-1 / Critical.** Metric: **App start → App complete conversion.**
> Evidence: `screens/step2b-register-error.png` (generic toast), network `400 POST /api/public/learner-profile/learners/self/new/validate` → `PhoneCountryCode "380" — condition not met`.

## 1. What we're building & why
On `/v2/register` the phone-number field offers a **245-country** selector, but the backend `validate` endpoint **rejects some country codes** (observed: Ukraine `+380`). The applicant fills the entire form, clicks **Register Now**, and receives only a generic top-of-form toast **"An error occurred. Please try again."** — with no indication that the *phone country* is the problem and no way to recover. This is a **hard drop at the very first, widest, highest-intent point of the funnel**, and it directly contradicts Nexford's "100+ countries" positioning.

**Two coupled changes:**
- **C1 — Prevent the dead-end + make any error field-level and actionable.** Source-of-truth: the list of supported phone country codes. Don't let users pick a path that will 400.
- **C2 — Fire conversion/analytics pixels only on a successful (2xx) registration.** Today Google Ads / TikTok / Mixpanel / Ahrefs fire even on the failed (400) submit, corrupting the exact metric we're optimizing.

### User story (Mike Cohn)
- **As a** prospective applicant from any country Nexford serves,
- **I want to** know upfront whether my phone country is supported and get a clear, specific message if something's wrong,
- **so that** I can complete registration instead of hitting an unexplained error and giving up.

## 2. Acceptance Criteria (Gherkin)

**Scenario A — Supported country registers successfully**
- **Given** I am on `/v2/register`
- **and** I selected a supported phone country and filled all required fields
- **When** I click **Register Now**
- **Then** my registration is created (2xx) and I advance to email verification
- **and** conversion/analytics pixels fire exactly once, only now.

**Scenario B — Unsupported country is prevented at the source (preferred fix)**
- **Given** the supported-country list does **not** include my country
- **When** I open the phone-country selector
- **Then** unsupported countries are **not selectable** (hidden, or shown disabled with a short note e.g. "Not available yet")
- **and** I can never reach a state that will 400 on submit.

**Scenario C — Any validation error is field-level and actionable**
- **Given** the backend returns a validation error for a specific field (e.g. `PhoneCountryCode`)
- **When** the response is received
- **Then** the error is shown **inline on that field** with a specific, human message (e.g. "We can't accept phone numbers from {country} yet — try another contact number") 
- **and** the page **auto-scrolls/focuses** the first errored field
- **and** the generic "An error occurred. Please try again." is **not** the only feedback.

**Scenario D — Pixels never fire on failure (C2)**
- **Given** the submit returns a non-2xx (e.g. 400)
- **When** the failure is handled
- **Then** **no** conversion/lead pixels fire (Google Ads, TikTok, Mixpanel lead event, Ahrefs)
- **and** an internal error event may be logged for observability.

## 3. Edge cases & states
- **Empty:** required-field validation still fires client-side before submit (don't rely only on backend).
- **Loading:** Register button shows a disabled/spinner state during submit; no double-submit; pixels wait for the 2xx.
- **Error (network/5xx):** show a retryable inline banner "Something went wrong on our end — please try again" (distinct from field validation). No pixel fire.
- **Mobile:** inline error + auto-scroll must work on small screens (error must land in viewport, not above the fold); disabled options render legibly.
- **Google-signup path:** if "Continue with Google" skips phone at register, ensure the same supported-country rule is enforced wherever phone is later collected.
- **Race:** if supported-country list fails to load, fail open to full list but keep field-level error handling (don't block everyone).

## 4. Dependencies & assumptions
- **Source of truth for supported countries/phone codes** — need the canonical list (and whether the constraint is regulatory/sanctions, ops, or a bug). Owner: Admissions/Compliance + Backend.
- Assumes the `validate` endpoint returns a **machine-readable field key** (it does: `PropertyName: "PhoneCountryCode"`) → front-end can map field→message.
- Analytics/pixel wiring is currently in the submit handler (fires pre-response); needs to move to the success callback. Owner: Growth/MarTech.
- Copy for disabled-country note + field errors → Content/Localization.

## 5. Open questions (for eng/PM)
1. Is the country restriction **intentional** (sanctions/ops) or a **bug/incomplete allowlist**? If unintentional, the real fix may be backend (accept the code) rather than UI-hiding.
2. Should unsupported countries be **hidden** or **shown-disabled with reason**? (Disabled+reason is more transparent for a "100+ countries" brand.)
3. Is phone **required** at register at all, or can it be deferred/optional to remove this failure mode entirely?
4. Which exact pixels must be gated, and do any legitimately need to fire on "form start" vs "lead created"?
