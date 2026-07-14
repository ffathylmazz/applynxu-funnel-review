# Measurement / Event-Tracking Plan — ApplyNXU funnel
> **Why this exists:** you can't optimize (or scale) App start → App complete on a number you're measuring wrong. Today conversion pixels fire on *failed* sign-ups (C2), so "App starts" are partly fictional. This plan is the **prerequisite** for every recommendation and every number in the impact model. Written for the engineer who has to implement it.

## 1. Conventions
- **Event names:** `snake_case`, `object_action` (e.g. `registration_submitted`, `upload_rejected`). Stable, lowercase, no spaces.
- **One analytics abstraction:** send every event through a single `track(event, props)` wrapper so taxonomy + gating live in one place (not scattered across pixels).
- **Success-gating (fixes C2):** lead/conversion pixels (Google Ads, TikTok, Mixpanel funnel steps) fire **only inside the 2xx success callback** — never on a 400/5xx. See §5.
- **IDs:** assign an `anonymous_id` on first load; on account creation call `identify(user_id)` and stitch. Every funnel event carries `application_id`.
- **PII:** never send raw email/phone as a property — hash (SHA-256) if needed for matching. Respect consent (no non-essential tracking pre-consent).

**Common properties on every event**
| prop | type | example |
|---|---|---|
| `application_id` | string | `app_2285` |
| `anonymous_id` / `user_id` | string | — |
| `device` / `is_mobile` | string / bool | `mobile` / `true` |
| `effective_connection_type` | string | `3g` |
| `country_code` | string | `NG` |
| `program` | string | `MBA` |
| `source` / `utm_*` | string | `google_ads` |

## 2. Funnel events by step (the core spec)
Each event: **when it fires**, **key properties**, and **which finding it sizes** (so the data maps straight to the impact model).

### Step 0 — Register (`/v2/register`)  → this is **App start**
| event | fires when | key props | sizes |
|---|---|---|---|
| `register_viewed` | page load | `source`, `utm_*`, `device` | funnel entry / Perf |
| `register_field_error` | a field error is shown | `field`, `error_type` (`unsupported_country`…), `country_code` | **C1** (drop by country) |
| `register_submit_attempted` | Register clicked | `method` (`email`\|`google`), `country_code` | — |
| `register_submit_failed` | response **non-2xx** | `status_code`, `error_field`, `error_message` | **C1 / C2** (NO pixel here) |
| `app_started` | account created (**2xx**) | `application_id`, `program`, `method`, `country_code` | **App start — pixels fire HERE** |
| `google_auth_started` / `_completed` | OAuth path | `scopes_ok`, `token_in_url` (bool) | F17 |

### Step 0.5 — Email verification
| event | fires when | key props | sizes |
|---|---|---|---|
| `verify_viewed` | page load | — | gate drop |
| `verify_failed` | wrong/expired code | `reason` | gate friction |
| `verify_completed` | code accepted | `method` | — |

### Step 1 — Previous education
| event | fires when | key props | sizes |
|---|---|---|---|
| `step_viewed` | load | `step="education"`, `step_index=1` | — |
| `step_completed` | Save & Continue (2xx) | `step="education"` | — |

### Step 2 — Personal information
| event | fires when | key props | sizes |
|---|---|---|---|
| `step_viewed` | load | `step="personal"` | form fatigue |
| `field_blur` | field loses focus (optional) | `field`, `filled` (bool) | field-level drop / form-trim |
| `form_validation_error` | submit blocked | `first_error_field`, **`error_in_viewport`** (bool) | **V-UX** (off-screen error) |
| `step_completed` | Save & Continue (2xx) | `step="personal"`, `fields_filled` | form-trim |

### Step 3 — Identity documents (heaviest wall)
| event | fires when | key props | sizes |
|---|---|---|---|
| `step_viewed` | load | `step="identity"` | — |
| `upload_attempted` | file chosen | `slot` (`front`\|`back`\|`selfie`), `file_ext`, `file_size_mb` | **F-files** |
| `upload_rejected` | file refused | `slot`, `reason` (`format`\|`size`\|`type`), `file_ext`, `file_size_mb` | **F-files** (rejection reasons, first-try accept) |
| `upload_accepted` | file accepted | `slot`, `normalized` (bool), `original_type` | F-files (normalizer effect) |
| `example_modal_opened` | "See example" | `country_code` | **F7** |
| `identity_submitted` | Continue | `front` `back` `selfie` (bools) | **F9** (front/back completeness) |
| `kyc_result` | async verification returns | `result` (`pass`\|`fail`), `reason`, **`after_payment`** (bool) | **F9** (post-pay rejection timing), **F1** |
| `step_completed` | identity passes gate | `step="identity"` | — |

### Step 4 — Application fee checkout ($25)  → this is **App complete**
| event | fires when | key props | sizes |
|---|---|---|---|
| `checkout_viewed` | load | `amount`, `currency`, `methods_available[]` | — |
| `payment_method_selected` | method chosen | `method` (`card`\|`paypal`\|`wire`\|`ngn`\|`momo`) | local-rails |
| `payment_attempted` | Pay clicked | `method`, `currency`, `country_code` | — |
| `payment_failed` | charge declined/error | `method`, `currency`, `country_code`, `decline_reason` | **local rails** (USD-card failure by country) |
| `app_completed` | **$25 paid (2xx)** | `application_id`, `amount`, `currency`, `method`, `country_code` | **App complete — purchase pixel fires HERE** |

## 3. Cross-cutting
- **Performance (Perf):** `page_performance` on each step — `lcp`, `dcl`, `load_ms`, `effective_connection_type`, `third_party_count`. Enables real-user LCP by device/geo.
- **Identify:** on `app_started`, set user props `country_code`, `program`, `source`, `device`.

## 4. Off-funnel recovery events (the WhatsApp / call / email loop — see §Off-funnel in the review)
Abandoned applicants are re-engaged by a named Application Specialist across **email (always), phone call, and WhatsApp**. This is a real conversion lever — but today it's **unmeasured**. Instrument it so recovery ROI and channel mix are visible.
| event | fires when | key props |
|---|---|---|
| `recovery_email_sent` | specialist/automation emails a dropped applicant | `application_id`, `specialist`, `sequence_step`, `days_since_drop` |
| `recovery_email_opened` / `_clicked` | open / link click | `link_type` (`testimonials`\|`alumni`\|`book_time`\|`whatsapp`\|`resume`) |
| `recovery_call_made` | outbound call logged | `answered` (bool), `days_since_drop` |
| `recovery_whatsapp_sent` / `_replied` | WhatsApp outreach / reply | `template`, `days_since_drop` |
| `application_resumed` | applicant returns and re-enters the flow | **`resume_source`** (`email`\|`whatsapp`\|`call`\|`direct`), `resume_step` |
| `app_completed` (+ recovery attribution) | paid after a recovery touch | `recovered` (bool), `recovery_channel` |

→ Lets you compute **recovery-attributed completions**, cost per recovered app, and human-vs-automated lift.

## 5. Integrity rules (fixes C2 — do these first)
1. **Gate all pixels on 2xx.** Move Google Ads / TikTok / Mixpanel conversion calls into the success callback of `app_started` and `app_completed`. A failed submit fires **nothing**.
2. **Dedupe** by `application_id` (fire each conversion once).
3. **Stitch** anonymous → identified at account creation.
4. **Validate the tag** with a QA pass: force a 400 and confirm zero pixels fire.

## 6. The funnel to build in the analytics tool
`app_started` → `verify_completed` → `step_completed(education)` → `step_completed(personal)` → `step_completed(identity)` → `app_completed`
- **Primary conversion:** `app_started → app_completed`.
- **Segment by:** `country_code`, `device`, `payment method`, `source`.
- **Dashboards:** (a) step-to-step drop; (b) drop by country (C1); (c) Step-3 `upload_rejected` reasons (F-files); (d) `payment_failed` by country/method (local rails); (e) recovery funnel (§4); (f) real-user LCP (Perf); (g) time-to-complete (session duration app_started→app_completed).
