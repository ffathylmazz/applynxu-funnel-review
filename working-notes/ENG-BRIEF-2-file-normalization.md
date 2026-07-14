# Engineering Brief #2 — Client-side file normalization + explicit Front/Back for Identity uploads
> Priority: **Tier-1 / High-leverage.** Metric: **App start → App complete.** Bundles **F6 (PDF rejected), F8 (10MB limit), F9 (front+back not enforced)**.
> Evidence: `screens/step6b-pdf-rejected.png`, `step6e-oversize-rejected.png`, `step7-checkout.png` (single ID accepted → advanced).

## 1. What we're building & why
The Identity step (`/application/identity`) is the funnel's heaviest friction wall and sits **before** the $25 payment (App complete). Three upload problems cause drop-off at the highest-intent moment — and one causes **post-payment rejection**:
- **PDF rejected** (accept=".png,.jpg,.jpeg") with a weak top-right toast → users who store their ID as a PDF hit a dead end. (Transcript step accepts PDF → inconsistent.)
- **>10MB rejected** with no auto-compression → modern phone photos routinely exceed 10MB.
- **Front+back not enforced** → users can submit a one-sided ID and pay; async verification then fails → refund/support/churn.

**Constraint to respect:** ID+selfie feed an automated **KYC pipeline** (face-match/OCR) that needs **raster images** — so the fix is NOT "accept anything," it's **normalize on the client to a compliant image before upload**, keeping the pipeline intact.

### User story
- **As a** applicant on my phone with my ID as a PDF or a large/HEIC photo,
- **I want to** upload whatever I have and have the system make it compliant automatically,
- **so that** I'm not blocked or silently rejected at the last step before paying.

## 2. Acceptance Criteria (Gherkin)
**Scenario A — PDF auto-converted**
- **Given** I select a PDF for Government ID
- **When** the file is added
- **Then** the client converts page 1 (and page 2 if present → front/back) to image(s), shows a preview, and no "unsupported" error appears.

**Scenario B — Oversized image auto-compressed**
- **Given** I select an image > 10MB (e.g. a 12MB phone photo)
- **When** the file is added
- **Then** the client downscales/re-encodes it to ≤10MB (preserving legibility) and accepts it — no size error.

**Scenario C — HEIC (iPhone default) accepted**
- **Given** I select a `.heic`/`.heif` photo (iOS default capture format)
- **When** the file is added
- **Then** it is converted to JPEG client-side and accepted.

**Scenario D — Front & back enforced**
- **Given** I chose an ID type that has two sides (national ID / driver's license)
- **When** I try to continue with only one image
- **Then** continue is blocked with an inline message; two labeled slots ("Front", "Back") are shown. *(Passport = single slot.)*

**Scenario E — Clear, field-level errors (fallback)**
- **Given** a file genuinely can't be processed (corrupt/unsupported)
- **When** it's added
- **Then** the error is shown **inline at the upload field** with a specific reason + how to fix — not only a transient top toast.

## 3. Edge cases & states
- **Multi-page PDF:** page1→front, page2→back; >2 pages → ask which/warn.
- **HEIC/HEIF:** convert to JPEG (iOS Safari default).
- **Very large/hi-res:** cap longest edge (~2000px) before re-encode; keep text readable for OCR.
- **Corrupt / zero-byte / password-protected PDF:** graceful inline error.
- **Loading:** conversion/compression spinner + preview; disable continue until processed.
- **Mobile camera capture ("Take photo"):** feeds the same normalizer.
- **Low bandwidth:** compress **before** upload to cut data usage (emerging-market benefit).
- **Selfie:** same size/HEIC normalization (front/back N/A).

## 4. Dependencies & assumptions
- Client libs: PDF→canvas (e.g. pdf.js), canvas resize/re-encode, HEIC decode. Assess bundle size (see perf finding) → **lazy-load only on this step**.
- **KYC pipeline expects images** — confirmed by "we'll combine front & back into one file" + selfie face-match. Normalizer must output pipeline-compatible JPEG/PNG.
- Server already combines front+back → dual-slot output maps to existing contract.
- Document-type list (which IDs are 2-sided vs passport=1) → Admissions/Compliance.

## 5. Open questions
1. Auto-detect document type or ask the user (to decide 1 vs 2 slots)?
2. Client-side vs server-side conversion for heavy PDFs (bundle/perf vs upload size)? Recommend client for data savings, server fallback.
3. Max dimension/quality that still passes OCR/face-match? (align with KYC vendor specs)
4. Apply the same normalizer to the **transcript** upload for consistency?
