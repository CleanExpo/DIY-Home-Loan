# DIY Open Banking — roadmap and broker handoff

## North star

One day the DIY Home Loan platform may run its **own** Consumer Data Right–style connection (or equivalent accredited data flow) so members complete consent **entirely inside the DIY website** and Fitzy ingests accounts without sending people to a separate app.

Until that exists, the interim method stands: **Open Banking via an accredited app (e.g. Frollo)** opened from the Member area, plus uploads where needed.

## Why interim is acceptable

Many members will **already** have been exposed to Open Banking through their **broker** during a home loan application (e.g. Frollo for Brokers). That familiarity lowers friction when Fitzy asks them to connect or refresh for **ongoing quarterly Rule 3**.

## Broker handoff path (reduces duplicate work)

Where the broker (e.g. Jack) **already holds** transaction summaries, exports, or consent-based data from application time:

- The broker can **provide that package to Fitzy / DIY** (secure channel, once built) so the member (e.g. Kodi) does **not** have to re-do the same linking exercise from scratch.
- If the broker needs more or fresher data for the quarter, they **chase the member** for only what is missing.

**Member-initiated invite:** the member gives **explicit permission** and may email the broker an upload link to their member area — see `FITZY_BROKER_INVITE_FLOW.md`.

This helps **Kodi and Fitzy at once**: less member fatigue, faster Rule 3 completion, and the broker stays inside the DIY rhythm.

## Broker upside

Brokers who routinely supply or refresh Open Banking–backed data for DIY members become **more exposed to how the DIY Home Loan actually runs** in practice — behaviour, surplus, and quarterly discipline — not just the upfront deal.

## Product notes

- Document consent scope: what the broker may share with DIY/Fitzy and for how long.
- Audit trail: who supplied data, which quarter, which import method.
- TFN and sensitive identifiers: never in broker handoff packs; same redaction rules as member uploads.
