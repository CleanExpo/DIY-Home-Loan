---
name: tfn-redaction-gate
description: The TFN hard rule — detection pipeline, reject response, audit logging. No retention, ever.
triggers: [upload, TFN, tax file number, privacy, bank statement, payslip]
---

# TFN Redaction Gate

> Australian Tax File Numbers are protected by the Privacy (Tax File Number) Rule 2015. The DIY Home Loan platform does not accept TFNs anywhere. Detection → reject. That is the whole policy.

## Legal anchor

- **Privacy Act 1988 (Cth)**
- **Privacy (Tax File Number) Rule 2015**
- **APP 11 (security of personal information)**

## The detection pipeline (ordered)

### 1. Pre-filter

- Reject if file size > 20 MB
- Reject if MIME not in `[application/pdf, image/jpeg, image/png]`

### 2. Regex on extracted text

Pattern: `/\b\d{3}\s?\d{3}\s?\d{3}\b/`

Catches: `123456789`, `123 456 789`, `123-456-789` (after normalising dashes to spaces).

False positives are acceptable — safety bias rejects ambiguous cases.

### 3. Vision check (images)

For JPG/PNG and for PDFs with poor text layer: send to Claude Haiku 4.5 vision with:

```
You are scanning a document for the presence of an Australian Tax File Number (TFN).
A TFN is 9 digits, typically formatted as 3 3 3. It is sometimes labelled "TFN"
or "Tax File Number". Respond with exactly one of:
TFN_PRESENT
NO_TFN
```

Temperature 0. Max tokens 5.

### 4. Decision

- Any positive signal → **REJECT**
- All negative → **ACCEPT**

## On reject

1. Delete the temp file (use `try / finally`).
2. Write to `consent_audit`:
   ```
   action:    'upload_rejected_tfn'
   household: <id>
   detail:    { filename, size, mime, detector: 'regex' | 'vision' | 'both' }
   at:        now()
   ```
3. Return to the user (copy owned by Emma + Helen):

> "This file appears to contain a Tax File Number. For your privacy, we don't accept TFNs anywhere on the DIY Home Loan platform. Please remove or black out any TFN, save a new copy, and upload again."

## On accept

1. Move the file to Supabase Storage under `evidence/<household_id>/<yyyy-qq>/`.
2. Insert `evidence` row with `tfn_scan_status = 'clean'`.
3. Write `consent_audit` with `action = 'upload_accepted'`.

## What we log (never)

- The extracted text.
- The Claude vision response beyond `TFN_PRESENT` / `NO_TFN`.
- The file contents in any log line.

## What we log (always)

- Filename, size, MIME, detector used, accept/reject result, timestamp, `household_id`.

## Test set (owned by Chen)

- 10 docs known to contain TFNs in various positions/fonts → expect **100% reject**
- 10 clean bank statements → expect **0% reject**
- 5 adversarial: 9-digit numbers that aren't TFNs (ABNs, superfund USIs) → acceptable to reject; flagged for review

## Member self-serve guidance (on the upload page)

Short plain-English tip shown before the uploader, with a one-line link to "How to redact a PDF" (Emma writes).
