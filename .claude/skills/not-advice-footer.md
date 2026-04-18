---
name: not-advice-footer
description: Canonical "general information, not personal advice" footer. Required on money-touching AI output.
triggers: [disclaimer, footer, not advice, general information]
---

# Not-Financial-Advice Footer

> One canonical string. Don't improvise. Helen signs off on any variant.

## The canonical footer

```
This is general information to support your DIY Home Loan journey.
It is not personal financial, tax, or legal advice. For advice on
your circumstances, speak with a licensed broker, accountant, or
solicitor.
```

## When it appears

| Surface | Footer? |
|---|---|
| Fitzy chat, response touches money / loans / tax / budget | **Yes** |
| Fitzy chat, pure greeting / navigation help | No |
| Rule explanations (pages + chat) | **Yes** |
| Quiz result summaries | **Yes** |
| Broker handoff summary shown to member | **Yes** |
| Broker handoff summary shown to broker | No (broker is a professional) |
| Transactional emails (verify, invite, revoke) | No |
| Marketing pages (`/`, `/mandy-and-dave`) | Standard site-wide footer text separately |

## Implementation

- Stored as a single string constant: `lib/compliance/footers.ts` → `export const NOT_ADVICE_FOOTER`
- Appended by a server-side wrapper after every qualifying Fitzy response
- Disclaimer Sentinel (`agents/sub-agents/disclaimer-sentinel.md`) verifies presence before the response ships

## Layout (Emma)

- Separated from the body by a visible rule (`<hr>` or a faint border)
- Smaller font (13 px), muted colour (`var(--muted)`)
- Italic optional, never all-caps, never bold
- Never collapsible — always visible

## Approved variants

Only one variant is currently approved:

- **Canonical** (above)

Future variants (voice, SMS, push notifications) require Helen's written sign-off and get added here, not improvised in code.

## Hard rules

- Never auto-translate. If multilingual is ever added, Helen approves each translation.
- Never shorten to fit a UI. Make the UI fit the footer.
- Never replace "licensed broker, accountant, or solicitor" with a shorter list.
