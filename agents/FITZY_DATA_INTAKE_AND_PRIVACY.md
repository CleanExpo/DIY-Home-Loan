# Fitzy data intake and privacy gate

## Intake order (Rule 3 workflow)

1. Payslips or income summaries first (net income baseline)
2. Bank and credit card statements (last 90 days)
3. Cash in/out notes and family loans
4. Future irregular spends (holidays, medical, car, insurance, gifts)
5. NOA or tax return only when needed, with TFN removed

## TFN hard rule

- If a file includes a TFN, intake is rejected.
- Member is instructed to redact/remove TFN and upload again.
- Fitzy records a rejection reason and timestamp.

## Legal anchors (Australia)

- Privacy Act 1988 (Cth)
- Australian Privacy Principles (APP 3, APP 6, APP 11)
- Privacy (Tax File Number) Rule 2015

This is a compliance design note, not legal advice.

## Product behavior

- Member stays inside the DIY site.
- Evidence queue appears in Fitzy Member area.
- Quarterly API path can be offered for convenience (consent-based bank feed), but manual upload remains supported.
