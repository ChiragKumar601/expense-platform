# 00 — Cross-cutting rules

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready, except XC-18, which is blocked by FR-3

## Scope

Rules shared by every state-changing command, and the general lifecycle of
ledger records (Expenses, Settlements, Write-offs):

- idempotency;
- authoritative previews and re-confirmation;
- version conflicts;
- failure categories;
- creator rights, Versions, delete and restore;
- the Former-member restriction;
- money representation and limits;
- currencies;
- which time zone governs dates.

## Boundaries

- Feature-specific validation: `03`, `05`, `07`, `09`.
- Archive effects on commands: `01` (GR-11 to GR-13).
- Notification time zone: `10` (NT-14).
- Audit content: `12`.

## Sources

- Domain §5.1, §5.8, §6, §7, §9, §12.
- Decisions: A2/IMP-2 (P019), AD-Q2 (P027), P2, P4, P7, P8 (P026), CP1–CP3 /
  BQ2–BQ4 (P036, P037), AR3 (P041), S7 (P043), LC1/LQ4 (P029), NQ1 (P033),
  FR-3 (P035).

## Dependencies

- `[FR-3 PROVISIONAL]`: XC-18.

## Rules

### Commands

- **XC-1** Only Active registered members act. Placeholders are always acted
  for. Former members, and Users whose account is deleted, cannot act. System
  actions (producing Drafts, automatic revocations, admin succession,
  automatic archiving) are carried out by the System.
- **XC-2** Every state-changing command carries an idempotency key from the
  client. Repeating a command with the same key returns the original result
  and has no further effect.
- **XC-3** Commands that change the same group take effect as if executed one
  at a time. No committed change is lost or silently overwritten.
- **XC-4** Every command is checked against the current committed state at
  the moment it is recorded. That covers permissions, membership state,
  archive state and domain rules.
- **XC-5** A command that fails is rejected with one of these categories, and
  has no effect:
  - (a) **not permitted**;
  - (b) **rule violated**, with an explanation;
  - (c) **outdated version**, with the current version shown (XC-11);
  - (d) **needs re-confirmation**, with the new outcome (XC-8).

  Contention inside a group is not a failure that users see.

### Previews and confirmation

- **XC-6** Before a member confirms a command that records or changes an
  Expense, Settlement or Write-off, or restores a ledger record, the system
  gives an **authoritative preview** computed from the current committed
  state. The preview contains:
  - the computed figures;
  - every applicable warning;
  - whether the member is permitted;
  - whether an Admin is required.

  Producing a preview never changes state, and never uses up rotation
  positions.
- **XC-7** The client may show provisional figures while a member is typing,
  if they are clearly marked as provisional. The confirmation screen always
  shows the authoritative preview figures.
- **XC-8** When the member confirms, the outcome is calculated again against
  the current state. The command returns *needs re-confirmation* only if one
  of these differs from what the member confirmed:
  - the Overpayment amount;
  - whether the Settlement is Routed or Direct;
  - whether the restore warning about a Former member's debt applies;
  - whether an Admin is required.

  A difference only in the exact Chains doesn't require re-confirmation.

### Ledger-record lifecycle (Expenses, Settlements, Write-offs)

- **XC-9** The Creator, while an Active member, or an Admin may edit, delete
  or restore a ledger record. When the Creator becomes a Former member, only
  Admins keep these rights.
- **XC-10** Every edit creates a new Version. Debts reflect the current
  Version. Earlier Versions stay in history, together with their locked
  results (see `12`).
- **XC-11** An edit, delete or restore must refer to the Version it was based
  on. If a newer Version exists, the command is rejected as *outdated
  version*, and the current Version is shown.
- **XC-12** Deleting removes a record's effect on debts and keeps it in
  history. Restoring brings back exactly the effect of the restored Version,
  without recalculating it.
- **XC-13** Only an Admin may edit, delete or restore an existing record in a
  way that changes any Former member's debt, including who they owe when the
  total is unchanged. The affected Former member is notified (see `10`).
  This rule does **not** restrict:
  - recording a new Settlement to a Former member (ST-3);
  - a Write-off by the member who is owed (WO-2).
- **XC-14** If restoring a record would bring back debt involving a Former
  member, the preview shows a warning (XC-6) before the member confirms.

### Money, limits and currency

- **XC-15** Every amount is an integer number of the group currency's
  smallest unit. At the API, amounts are decimal strings of that integer, for
  example "1050" for 10.50 in a two-decimal currency. Fractional or
  floating-point amounts are rejected.
- **XC-16** An Expense total and a Settlement amount may not exceed **10^12
  smallest units** (S7). Paid amounts are bounded by their Expense total.
  Debts and intermediate calculations are not limited by this rule.
- **XC-17** A group's currency is chosen at creation from a versioned snapshot
  of the active ISO 4217 currencies. The currency and its precision are kept
  with the group, and never change, even if the currency's official
  precision changes later.

### Dates

- **XC-18** `[FR-3 PROVISIONAL]` Dates that matter to the business use the
  **group's time zone**. That means the "Expense date not in the future" check
  (EX-5) and recurring occurrence dates (RE-8). Who sets and changes the group
  time zone (GR-3, GR-4) is provisional.
- **XC-19** A User's notification time zone affects only when notifications
  are sent (NT-14). It never affects any date, debt or ledger behaviour.

### Platform constraints (intent §7)

- **XC-20** The product is a responsive web application meeting **WCAG 2.2
  AA**.
- **XC-21** The first release is online only. No command is recorded while
  the client is offline.

### Sign-in (XC-OI-1, resolved in P050)

- **XC-22** Users sign in through the identity provider with a **verified
  email address**.
- **XC-23** Multi-factor authentication is available to every User and is
  optional in the first release.
- **XC-24** Account recovery uses the verified email address, through the
  identity provider.
- **XC-25** Changing Payment details requires signing in again (PD-10).
  Account deletion also requires it (PR-1).

## Acceptance criteria

- **AC-XC-1** (XC-2): Given an Expense command with key K that succeeded,
  when the same command with K is sent again, then the original result is
  returned and no second Expense exists.
- **AC-XC-2** (XC-11): Given Expense E at Version 3, when a member submits an
  edit based on Version 2, then the edit is rejected as *outdated version*
  and Version 3 is shown.
- **AC-XC-3** (XC-8): Given a member confirmed a Settlement preview with
  Overpayment 0, when another Settlement committed meanwhile makes this
  Settlement's Overpayment 500, then the command returns *needs
  re-confirmation* with the new outcome, and nothing is recorded.
- **AC-XC-4** (XC-8): Given a confirmed preview, when only the Chains used
  differ at commit and the four warned outcomes are unchanged, then the
  Settlement is recorded without re-confirmation.
- **AC-XC-5** (XC-13): Given Expense E includes Former member F, when a
  non-admin Creator edits E so that F's Obligations change, then the edit is
  rejected as *not permitted*. When an Admin makes the same edit, it succeeds
  and F is notified.
- **AC-XC-6** (XC-15): Given an API request with amount "10.5", then it is
  rejected. With amount "1050", it is accepted as 1050 smallest units.
- **AC-XC-7** (XC-16): Given an Expense total of 10^12 + 1 smallest units,
  then it is rejected.

## Out of scope

- API endpoint design.
- Storage and locking mechanisms (see `docs/architecture.md` §6, §8).

## Open items

- None. XC-OI-1 was resolved by XC-22 to XC-25 (P050). FR-3 is tracked in the
  README register.
