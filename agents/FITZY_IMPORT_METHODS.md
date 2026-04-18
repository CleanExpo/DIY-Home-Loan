# Fitzy import methods (quarterly budgeting)

Fitzy asks: **"Which one do you want to use?"**

Core message:

- You get what you pay for.
- We are happy to work with your chosen system.
- Most automated path is usually best for quarterly consistency.

## Supported paths

1. Open Banking / API feed (preferred where available)  
   - **Frollo for You** (consumer app): [Frollo app](https://frollo.com.au/frollo-app/) — CDR linking, categorisation, exports.  
   - **Frollo for Brokers**: often used during **home loan application** with a broker (e.g. Jack Perkins). For **ongoing quarterly DIY Rule 3**, members are usually directed to the **consumer** path so the feed serves the budget rhythm, not only the application file.
2. Direct bank export (CSV/OFX)
3. PDF statement conversion tools
4. Manual entry + quarterly evidence upload

## In-site DIY flow

- Member starts on the DIY Member page: Fitzy explains the path and opens Frollo (or another ADR) in a **new tab** so the journey is anchored on DIY.
- **Roadmap:** embed CDR consent fully in-site when integration and compliance are ready.
- **Budget grid:** Fitzy populates from feeds + evidence; members do not type monthly categories by hand.

## Broker handoff

If the broker already holds application-time Open Banking data or exports, they can **push the quarter’s package to Fitzy** so the member avoids repeating the same linking. The broker only chases the member for gaps. See `DIY_OPEN_BANKING_ROADMAP_AND_BROKER_HANDOFF.md`.

## Simplification tip for members

If possible, keep daily banking simple:

- one primary transaction account (income + core outgo)
- one credit card account
- two core loans: GISI and LOC

This makes quarterly categorisation and rule reviews faster and more accurate.

## Program note

- GISI and LOC may use direct lender API access where the loan is inside the White Label Home Loan program.
- For lenders outside the program, Fitzy uses alternate import paths.

