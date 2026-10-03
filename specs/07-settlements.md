# 07 — Settlements

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready

## Scope

- Who records a Settlement, and validation.
- Allocation, Overpayment, and Routed versus Direct.
- Partial payment.
- The possible-duplicate warning.
- Editing, deleting and restoring Settlements.

## Boundaries

- Disputes: `08`.
- Write-offs: `09`.
- General lifecycle and previews: `00`.
- Suggestion staleness: `06`.
- Notifications to Intermediate members: `10`.

## Sources

- Domain §5.5, §7.9–7.10, §13.
- Decisions:
  - Q8, Q9 (P007); R10 (P008); Q4, Q7 (P011);
  - A3, A6 (P015);
  - Q-C (P021);
  - AT-1 (P042);
  - S2, S4, S5 (P043).

## Dependencies

- None.

## Rules

### Recording

- **ST-1** A Settlement is recorded by its payer, its recipient, or an Admin.
  When one party is a placeholder, it is recorded by the other party or an
  Admin.
- **ST-2** The payer and the recipient must be different members (S5). The
  amount is greater than zero and within the limit (XC-16).
- **ST-3** A Settlement to a Former member is recorded by its payer (who must
  be an Active member) or by an Admin. It is a valid record even though the
  Former member cannot dispute it (DP-3).
- **ST-4** A Settlement affects debts as soon as it is recorded.
- **ST-5** Partial Settlements are allowed.
- **ST-6** **Possible-duplicate warning (S4):** if a Settlement with the same
  payer, recipient and amount was recorded in the previous **7 days**, the
  preview shows a warning. It never blocks recording, and it is not a
  guarantee of catching duplicates.

### Allocation

- **ST-7** Every Settlement is allocated automatically, whether or not it came
  from a suggestion:
  1. the payer's direct debt to the recipient is reduced first;
  2. then Chains through Active members are reduced, each Chain reducing every
     one of its links by the same amount;
  3. anything left is an Overpayment.
- **ST-8** **Q-C:** the maximum possible amount is routed, preferring shorter
  Chains, with no limit on Chain length.
- **ST-9** **AT-1:** allocation is deterministic. Only when allocations remain
  equally valid under ST-7 and ST-8, a stable ordering of immutable member
  identifiers decides between them. The rotation position is never used for
  allocation. The same committed state, algorithm version and inputs always
  give the same allocation.
- **ST-10** An **Overpayment** becomes a debt owed by the recipient to the
  payer. It is allowed only after the preview shows a warning (XC-6). That
  includes a payer who owes the recipient nothing.
- **ST-11** The allocation is fixed when the Settlement is recorded. Later
  changes to other records never change it.
- **ST-12** A Settlement whose allocation reduces any Chain is **Routed**.
  Otherwise it is **Direct**. Intermediate members of a Routed settlement are
  notified (see `10`), and see the change in their Trace (DS-4).

### Editing, deleting, restoring

- **ST-13** A **Direct** settlement may be edited (XC-9 to XC-11). Its
  allocation is recalculated for the new Version.
- **ST-14** An edit may not turn a Direct settlement into a Routed one (S2).
  If the edited amount would need routing, because it exceeds the direct debt
  that applies, the edit is rejected. The member deletes the settlement and
  records it again.
- **ST-15** A **Routed** settlement cannot be edited. It can be deleted, and
  restored.
- **ST-16** Restoring any Settlement re-applies the allocation of the
  restored Version exactly (XC-12). The warning in XC-14 applies.

## Acceptance criteria

- **AC-ST-1** (ST-2): Given payer = recipient, then it is rejected.
- **AC-ST-2** (ST-7, ST-8, ST-12): Given A owes B 10 and B owes C 10, and A
  owes C nothing, when A pays C 10, then A→B and B→C both become 0, the
  Settlement is Routed, and B is notified.
- **AC-ST-3** (ST-8, ST-10): Given A owes C 30 directly, and Chains from A to
  C can carry 50, when A pays C 100, then 30 is direct, 50 is routed, and 20
  is Overpayment (C owes A 20), after a warning.
- **AC-ST-4** (ST-9): Given two equally short Chains with equal capacity, then
  repeating the same allocation from the same state always selects the same
  Chain.
- **AC-ST-5** (ST-14): Given Direct settlement S of 30, where A owes C 30
  directly, when S is edited to 50 and 20 would need routing, then the edit is
  rejected.
- **AC-ST-6** (ST-15): Given a Routed settlement, then any edit is rejected.
  Delete and restore succeed, and restore re-applies the original allocation.
- **AC-ST-7** (ST-6): Given A paid B 50 three days ago, when A pays B 50
  again, then a duplicate warning is shown, and recording succeeds if the
  member confirms.
- **AC-ST-8** (ST-1): Given placeholder P owes Ana, when Dev, who is neither
  P, Ana nor an Admin, records "P paid Ana", then it's rejected. When Ana or
  an Admin records it, it succeeds.

## Out of scope

- Moving money (Splitsy only records it).

## Open items

- None.
