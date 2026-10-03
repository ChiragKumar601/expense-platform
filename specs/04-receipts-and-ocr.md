# 04 — Receipts and OCR

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: OCR languages (RO-15) blocked by Q-M; other rules not blocked

## Scope

- Attaching Receipts to Expenses: file types and limits, malware scanning.
- Viewing access and link lifetime.
- Keeping and deleting receipt files (retention behaviour).
- Receipt OCR.

## Boundaries

- Rights to edit an Expense: `00` (XC-9).
- Audit of adding and removing receipts: `12`.
- Personal-data classification and erasure mapping: `13`.
- OCR analytics metrics as data: `14`.

## Sources

- Domain §5.9.3, §9.
- Decisions: R3-6 (P009), Q1 (P011), OP2, OP3 (P034), OQ2, OQ4 (P035), S35,
  S36, S37 (P047).

## Dependencies

- `[Q-M OPEN]`: RO-15.

## Rules

### Receipts

- **RO-1** Anyone with the right to edit an Expense (XC-9) may add or remove
  its Receipts. Adding and removing are audited (see `12`).
- **RO-2** Accepted types: JPEG, PNG, HEIC, WebP, PDF. Maximum **10 MB** per
  file. Maximum **5 Receipts** per Expense.
- **RO-3** Every upload is scanned for malware. A file that fails is rejected
  with a message, and is not stored.
- **RO-4** An uploaded file that isn't attached to an Expense within **24
  hours** is deleted.
- **RO-5** Receipts are viewed only through links that expire after **5
  minutes**. Access is checked each time a link is issued.
- **RO-6** Who may view a Receipt follows record visibility. Active registered
  members may view every Receipt in the group. A Former member may view a
  Receipt only if it belongs to a record behind their own debts (MI-17).
  There is no separate Receipt permission model.
- **RO-7** A Receipt file is kept while its Expense exists, including after
  it is removed from the Expense, if a historical Version or audit entry
  refers to it. Account deletion doesn't remove Receipts (see `13`).

### OCR

- **RO-8** OCR is advisory only. It runs asynchronously on an uploaded
  receipt, and may pre-fill the total, the date, and the merchant (as the
  description).
- **RO-9** OCR never records or changes financial data by itself. A member
  must review and confirm every pre-filled value before the Expense is
  recorded.
- **RO-10** If OCR detects a currency other than the group currency, it shows
  a warning and **doesn't fill in the amount**. It never converts currencies.
- **RO-11** If OCR hasn't finished within **30 seconds**, or fails, the member
  continues by entering details manually. OCR never blocks recording an
  Expense.
- **RO-12** There is no guaranteed OCR accuracy.
- **RO-13** For each pre-filled field, the system records whether the member
  accepted it without editing (see `14` for analytics handling).
- **RO-14** The OCR provider keeps no data, or only the minimum transient data
  that is technically unavoidable.
- **RO-15** `[Q-M OPEN]` Supported OCR languages are undecided until launch
  markets are decided.

## Acceptance criteria

- **AC-RO-1** (RO-2): Given an Expense with 5 Receipts, when a sixth is
  added, then it is rejected. Given an 11 MB PNG, then it is rejected.
- **AC-RO-2** (RO-4): Given an upload not attached within 24 hours, then it
  is deleted.
- **AC-RO-3** (RO-6): Given Former member F, and Expense E behind F's debts
  with Receipt R1, and Expense X not involving F with Receipt R2, then F can
  view R1 but not R2.
- **AC-RO-4** (RO-10): Given a group in INR and a receipt in USD, when OCR
  completes, then a currency warning is shown and the amount stays empty.
- **AC-RO-5** (RO-11): Given OCR takes 31 seconds, then manual entry
  continues, and the Expense can be recorded without OCR data.
- **AC-RO-6** (RO-7): Given Receipt R removed from Expense E in Version 3, when
  Version 2 is viewed, then R is still available.

## Out of scope

- The OCR provider choice (not chosen; see `docs/architecture.md`).

## Open items

- Q-M (RO-15) is tracked in the README register.
