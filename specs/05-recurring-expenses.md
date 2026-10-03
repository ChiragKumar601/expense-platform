# 05 — Recurring expenses

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: blocked by FR-3 (RE-8, RE-13, RE-20, RE-25); RE-24 is a domain-level decision pending incorporation into the frozen domain

## Scope

- Recurring series: schedules, start and end, states, validation, editing,
  permissions.
- Producing occurrences and Drafts, including after unarchiving.
- Draft visibility, confirmation and discarding.

## Boundaries

- Expense validation and calculation when confirming: `03`.
- Notification delivery: `10` (draft notices: NT-4).
- Version conflicts: `00`.
- Archive: `01`.

## Sources

- Domain §5.3, §8.7, §8.8, §9.
- Decisions: R4 (P008), Q6 (P011), SR1–SR5 (P032), NQ1, NQ2, NQ4 (P033),
  S15–S22 (P045), FR-3 (P035).

## Dependencies

- `[FR-3 PROVISIONAL]`: RE-8, RE-13, RE-20. Every occurrence date depends on
  the group time zone (XC-18). These rules can't be finalized until FR-3 is
  explicitly approved.

## Rules

### Series

- **RE-1** Any Active registered member may define a Recurring series, except
  in an archived group (GR-12).
- **RE-2** A series is validated with the Expense rules when it is created
  and whenever it is edited:
  - EX-2, EX-3, EX-4 and EX-7;
  - every Payer and Participant must be an Active member at that moment.
- **RE-3** Supported schedules:
  - weekly on a selected weekday;
  - every N weeks;
  - monthly on a selected day of the month;
  - every N months;
  - yearly.
- **RE-4** For monthly schedules on days 29–31, a month without that day uses
  its last day. A yearly schedule on 29 February uses 28 February in non-leap
  years.
- **RE-5** A series requires a **start date**. It may also have **either** an
  end date **or** a maximum number of occurrences. With neither, it continues
  until stopped. The end date and the maximum number of occurrences are
  mutually exclusive, so supplying both is rejected. So is an end date before the
  start date, a maximum number of occurrences below 1, an interval N below 1,
  or a day-of-month outside 1–31.
- **RE-6** A series is **Active** or **Stopped**. Stopping is final and
  produces no further Drafts. There is no paused state.
- **RE-7** The series creator, while an Active member, or an Admin may edit or
  stop a series. Nobody else may. A series edit affects only occurrences not
  yet produced. Series edits are audited.

### Occurrences and Drafts

- **RE-8** `[FR-3 PROVISIONAL]` Occurrence dates are calculated in the group
  time zone. A Draft is produced **on** its occurrence date, never before.
- **RE-9** Each Draft is identified by (series, occurrence date). At most one
  Draft exists for each.
- **RE-10** A Draft copies the series' amount, Payers and Participants when it
  is produced. Later series edits don't change it.
- **RE-11** A Draft doesn't affect debts, and doesn't expire.
- **RE-12** While the group is archived, no Drafts are produced. Archiving
  doesn't change a series' state.
- **RE-13** `[FR-3 PROVISIONAL]` When a group is unarchived, Drafts are
  produced for every occurrence that fell due while it was archived (RE-9
  prevents duplicates).
- **RE-14** If a Payer or Participant of a series becomes a Former member, the
  series stays Active and keeps producing Drafts. Former members are never
  removed silently. The series creator and the Admins are notified (see
  `10`).

### Confirming and discarding

- **RE-15** Pending Drafts are visible to all Active registered members.
- **RE-16** The series creator, while an Active member, or an Admin may
  confirm or discard a Draft. When the series creator becomes a Former member,
  only Admins can.
- **RE-17** A Draft is confirmed at most once.
- **RE-18** Confirming a Draft records an Expense through the normal Expense
  rules (`03`) at the moment of confirmation, including the rotation state.
- **RE-19** The member who confirms becomes the Expense's Creator.
- **RE-20** `[FR-3 PROVISIONAL]` The resulting Expense's date is the
  occurrence date, never the confirmation date. The occurrence date is never
  in the future (RE-8).
- **RE-21** A Draft that includes a Former member cannot be confirmed until
  it is edited into a valid state.
- **RE-22** Drafts have Versions. Edits follow XC-10 and XC-11.
- **RE-23** Stopping a series leaves its existing Drafts pending, under RE-16
  to RE-22.
- **RE-24** **Domain-level decision (P050), pending incorporation into the
  frozen domain (`docs/architecture.md` §13.1):** a Draft may be edited by
  the series creator, while an Active member, or by an Admin. These are the
  same members who may confirm or discard it.
- **RE-25** `[FR-3 PROVISIONAL]` A series may start in the past. Every
  occurrence already due is produced as a Draft straight away (RE-9 prevents
  duplicates).

## Acceptance criteria

- **AC-RE-1** (RE-4): Given a monthly series on day 31, then the February
  occurrence is on 28 February (29 in leap years), and April's is on 30
  April.
- **AC-RE-2** (RE-5): Given both an end date and a maximum number of
  occurrences, then the series is rejected.
- **AC-RE-3** (RE-8, RE-20): Given an occurrence date of 1 May, then no Draft
  exists before 1 May (group time zone), and confirming it later records an
  Expense dated 1 May. *(Provisional: FR-3.)*
- **AC-RE-4** (RE-12, RE-13, RE-9): Given a group archived through 3 monthly
  occurrences, when it is unarchived, then exactly 3 Drafts are produced, and
  unarchiving again produces no duplicates.
- **AC-RE-5** (RE-10): Given Draft D was produced, when the series amount is
  edited, then D's amount is unchanged.
- **AC-RE-6** (RE-21): Given Draft D includes Former member F, when someone
  confirms D, then it's rejected until D is edited so it no longer includes F
  as a Payer or Participant.
- **AC-RE-7** (RE-11): Given a pending Draft, then debts are unchanged until
  it is confirmed.

## Out of scope

- Reminders about debts (see `10`).

## Open items

- None. RE-OI-1, RE-OI-2 and RE-OI-3 were resolved by RE-24, RE-4 and RE-25
  (P050). RE-24 still has to be incorporated into the frozen domain.
