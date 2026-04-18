# Fitzy — broker invite flow (member permission)

## Intent

Fitzy asks the member (e.g. Kodi) for the **broker’s email** when the broker may already hold application-time money data (e.g. Open Banking via Frollo for Brokers).

The member must give **explicit permission** before Fitzy involves the broker or before the broker can upload into the **member’s** DIY area.

## Why brokers engage

1. **Strengthens** the broker–client relationship (ongoing service, not one-off lending).
2. **Shows** the broker how DIY works in practice — useful when they explain the concept to other prospects (“other Kodis”).

## Flow (product)

1. Member enters broker name (optional) and **broker email**.
2. Member ticks **authorisation**: Fitzy may contact the broker; broker may upload evidence to the member’s member area for this purpose.
3. System generates a **time-limited, auditable** broker upload link (production).
4. Member sends invite — ideally **from their own email** (Kodi → Jack) with template text, or system sends on their behalf with consent logged.
5. Broker uploads statements, payslips (no TFN), etc., to the **member-scoped** inbox.
6. Fitzy ingests and builds the quarterly budget; member does not re-key Open Banking if data already exists at the broker.

## Compliance

- Consent must be **recorded** (who, when, scope: budget evidence for Rule 3 / quarter).
- Broker access must be **scoped** to that member and that invite token — no cross-client visibility.
- TFN rules unchanged: reject uploads containing TFNs.

## Demo vs production

Current `index.html` demo: local state + `mailto:` + placeholder URL.  
Production: server-issued tokens, expiry, broker identity check, audit trail, encrypted storage.
