# 06 — Debts, views and Suggested settlements

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready

## Scope

- Pairwise debts, Net balance, Settled up, Cleared.
- The Pairwise and Simplified views.
- The Trace.
- Suggested settlement sets: the guarantee, staleness and recording.

## Boundaries

- How a recorded Settlement is allocated: `07` (ST-7 to ST-11).
- Obligations: `03`.
- What Former members can see: `02` (MI-17).
- Notifications: `10`.

## Sources

- Domain §2, §5.4, §7.2, §7.13–7.14.
- Decisions:
  - Q7 (P007); Q3 (P011);
  - R3-5 (P009);
  - Q-D, Q-E (P021); FR-2 (P022);
  - Option (1) (P024);
  - P11 (P026);
  - AT-1 (P042).

## Dependencies

- None. RS-1 (research into how few transfers suggestions can use) doesn't
  change any rule here.

## Rules

### Debts

- **DS-1** A Pairwise debt between two members is determined only by the
  current Versions of non-deleted ledger records:
  - Expense Obligations;
  - Settlement allocations;
  - Write-offs.

  Opposite amounts between the same two members offset each other.
- **DS-2** A member's Net balance is the total owed to them minus the total
  they owe, across the group.
- **DS-3** A member is **Settled up** when every one of their Pairwise debts
  is zero.
- **DS-4** The **Trace** of a Pairwise debt lists every ledger record that
  contributes to it, and for each Expense, the Obligation it contributes.

### Views

- **DS-5** Each User chooses, for themselves, the Pairwise view or the
  Simplified view.
- **DS-6** No view ever changes a debt.
- **DS-7** Debts involving Former members are shown as Pairwise debts only.
  They never appear inside Suggested settlements.

### Suggested settlements

- **DS-8** The Simplified view shows a **suggestion set**: Suggested
  settlements among Active members (placeholders included), generated for one
  committed ledger state.
- **DS-9** **Guarantee (domain §7.14):** if every Suggested settlement in one
  set is recorded, in **any order**, then every Pairwise debt between Active
  members becomes zero.
- **DS-10** A Suggested settlement is only a proposal. Recording it creates an
  ordinary Settlement, allocated by the normal rules (ST-7 to ST-11). A
  suggestion's own routing never overrides allocation.
- **DS-11** Using fewer payments is a goal, not a guarantee of the minimum
  number of transfers. A set may include suggestions that are just direct
  debts.
- **DS-12** Debt cycles are cleared only with ordinary Settlements. A set may
  ask a member whose Net balance is zero to pay or receive money.
- **DS-13** A suggestion set stays **valid** only while every ledger change
  since it was generated is the recording, **in full**, of a different
  suggestion from the same set. Any other change makes the set **stale**:
  - a new or edited record;
  - a partial payment;
  - a repeat recording of the same suggestion.
- **DS-14** Recording a Settlement from a stale suggestion is **not**
  blocked. The member sees a warning and the current suggestions, then may
  proceed (normal allocation applies).
- **DS-15** Suggestions are computed separately from recording, so a
  suggestion set may not yet exist for the latest state. The Simplified view
  never presents a stale set as current.
- **DS-16** While no valid set exists for the current state, the Simplified
  view shows the debts with a "Suggestions updating…" indicator, and **no**
  suggestions. A stale set is not shown, even if labelled.

## Acceptance criteria

- **AC-DS-1** (DS-9): Given a set of 4 suggestions, when they are recorded in
  each of the 24 possible orders (starting each time from the same state),
  then all Pairwise debts between Active members are zero afterwards, and no
  Overpayment occurs.
- **AC-DS-2** (DS-12): Given Ana owes Ben 10, Ben owes Chen 10 and Chen owes
  Ana 10, then the suggestion set is not empty, and recording all of it clears
  all three debts.
- **AC-DS-3** (DS-13, DS-14): Given a set S, when an Expense is recorded, then
  S is stale. Recording a suggestion from S shows a warning and the current
  suggestions, and is still allowed.
- **AC-DS-4** (DS-13): Given a set S, when one suggestion is recorded only in
  part, then the rest of S is stale.
- **AC-DS-5** (DS-7): Given Former member F is owed 300 by Ana, then the
  Simplified view shows that debt separately, and no suggestion routes
  through F.
- **AC-DS-6** (DS-4): Given a Pairwise debt, then its Trace lists each
  contributing record and Obligation, and their sum equals the debt.

## Out of scope

- The algorithm that generates suggestions (see `docs/architecture.md` §7,
  RS-1).

## Open items

- None. DS-OI-1 was resolved by DS-16 (P050).
