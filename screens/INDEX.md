# Screens — index (flow order)
> ApplyNXU funnel walkthrough evidence. Organized by phase; desktop and mobile kept separate. `step*` = desktop, `m*` = mobile.

## 01-desktop-flow/ — App start → App complete (in order)
| File | Step |
|---|---|
| step1.png | Register / App start (empty) |
| step2-after-register.png | After Register (Ukraine phone → error state) |
| step2c-after-register-ng.png | Register success (Nigeria phone) → email verify |
| step3-after-verify.png | Step 1 Education (empty) |
| step3b-education-filled.png | Step 1 Education (filled) |
| step4-personal-info.png | Step 2 Personal information |
| step6-identity-documents.png | Step 3 Identity documents |
| step6f-identity-filled.png | Step 3 filled (dummy ID + selfie) |
| step7-checkout.png | Step 4 Checkout = App complete ($25) |
| step10-login.png / step10b / step10c | Returning-user Login → resume to last step |

## 02-mobile-flow/ — full funnel, iPhone emulation
| File | Step |
|---|---|
| step1-mobile.png | Register (mobile) |
| m1-register-empty / m1-register-filled | Register empty / filled |
| m2-verify | Email verification |
| m3-education / m3b-education-filled | Step 1 Education |
| m4-personal / m4b-personal-filled | Step 2 Personal info |
| step2-personal-info-MOBILE.png | Personal info (early capture) |
| m5-identity / m5b-identity-filled | Step 3 Identity |
| m6-checkout | Step 4 Checkout (mobile) |

## 03-edge-and-errors/ — deliberately-triggered failures & edge cases
| File | Scenario |
|---|---|
| step2b-register-error.png | C1 — generic "An error occurred" (Ukraine phone rejected) |
| step5b-error-state.png | Personal info required-field error (postal) |
| step6b-pdf-rejected.png | F6 — PDF rejected (weak toast) |
| step6e-oversize-rejected.png | F8 — 11MB file rejected |
| step6c/6d-example-* .png | F7 — "See example" ID + selfie modals (US-centric) |
| m5c/m5d-example-* .png | Same example modals (mobile) |
| step8-coupon-invalid.png / m8 | Invalid coupon → clean inline error (good) |
| step9-verify-phone.png / m9 | "Verify now" → WhatsApp OTP modal |
| test-login-unknown.png | Login w/ unknown email → clear message |
| test-register-existing-email.png | Register w/ existing email → 409 + "Login" message |
| dd-city.png / dd-program.png | Register dropdown options |

## 04-payments-and-invoice/ — checkout variants + sponsor + invoice
| File | Scenario |
|---|---|
| step7b-payment-options.png | Payment method dropdown |
| step7c/7f-pay-* .png | Card / PayPal / Sponsor / Bank Transfer (desktop) |
| m6b–m6e-pay-* .png | Same variants (mobile) |
| step7g-sponsor-filled / step7h-sponsor-sent | Sponsor invite → "Awaiting Payment" |
| step7i-sponsor-payment-portal / m7 | paynxu.nexford.edu sponsor portal |
| step7j-invoice-email.png | Sponsor invoice email (rendered) — see also `../invoice_2285.pdf` (F14) |

## 05-drop-recovery/ — off-platform re-engagement (real phone test)
| File | What it shows |
|---|---|
| recovery-1-missed-call-usa.png | Next-day missed call from +1 (660) 696-3810 |
| recovery-2-whatsapp-intro.png | WhatsApp from "Nexford University" (Business acct) — "Grace, your Application Specialist" |
| recovery-3-whatsapp-outreach.png | Outreach msg: offer a call + YouTube intro |
| recovery-4-whatsapp-questions.png | Info-gathering (education, how heard, goals, background) + quick-reply buttons |
| recovery-5-whatsapp-conversation.png | Conversation incl. applicant reply |

## 06-qa-and-working/ — deliverable QA + interim capture artifacts
`qa-*` = deliverable.html render QA (light/dark/mockups). `live-*`, `peek-*`, `*-current`, `phone-open` = interim automation captures (kept for provenance).
