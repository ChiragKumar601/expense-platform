# 14 — Analytics and success metric

Status: **Provisional.** The whole spec depends on `[S38 OPEN]`.
Implementation readiness: blocked by S38

> These definitions are approved as **proposed amendments to the intent**
> (intent §8 and §11.8). They are **not authoritative** until the intent is
> explicitly amended and approved. Specification drafting doesn't make them
> authoritative.

## Scope

- The definitions behind the primary success metric.
- What analytics data is collected, how long it's kept, opt-out, and
  erasure.

## Boundaries

- The OCR acceptance signal is defined in `04` (RO-13). This spec only
  covers it as analytics data.
- Privacy workflow: `13`.

## Sources

- Intent §8, §11.8.
- Decisions: OP6 (P034), S38 (P047).

## Dependencies

- `[S38 OPEN]`: every rule (AN-1 to AN-7).
- Legal review: whether prior consent is required, per market (AN-7).

## Rules

- **AN-1** `[S38 OPEN]` A **user**, for the success metric, is a registered
  User who has recorded at least one Expense.
- **AN-2** `[S38 OPEN]` **Returning** means an authenticated session on a
  later calendar day, within 30 days of the User's first recorded Expense.
  Calendar days are counted in **UTC**. The notification time zone isn't used
  (XC-19).
- **AN-3** `[S38 OPEN]` Analytics events identify people only by internal IDs.
- **AN-4** `[S38 OPEN]` Raw analytics events are kept **12 months**.
  Aggregated figures may be kept longer.
- **AN-5** `[S38 OPEN]` When a User deletes their account, their analytics
  events are deleted.
- **AN-6** `[S38 OPEN]` Analytics is first-party, and Users can opt out.
- **AN-7** `[S38 OPEN]` Whether consent must be asked for before collecting
  analytics is decided by legal review in each launch market, before
  analytics is enabled there.

## Acceptance criteria

All provisional.

- **AC-AN-1** (AN-1, AN-2): Given User U recorded a first Expense on 1 March
  and signed in again on 15 March, then U counts as returning. Signing in only
  on 1 March doesn't count.
- **AC-AN-2** (AN-5): Given U deletes their account, then U's analytics events
  are deleted.
- **AC-AN-3** (AN-6): Given U opts out, then no further analytics events are
  recorded for U.

## Out of scope

- Dashboards and reporting tools.

## Open items

- S38 is tracked in the README register.
- AN-OI-1 was resolved in AN-2 (UTC). The rule is still provisional under
  S38.
