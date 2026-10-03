# 03 — Expenses

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready, except EX-5, which is blocked by FR-3

## Scope

- Recording and validating an Expense.
- The Expense date.
- Shares (step 1) and the multi-payer split into Obligations (step 2,
  M2).
- Rounding.
- What a new Version keeps or recalculates.
- Refunds.
- Comments.

## Boundaries

- Rights, Versions, delete/restore, the Former-member restriction and previews:
  `00` (XC-6 to XC-14).
- Receipts and OCR: `04`.
- How Expenses produce debts and appear in the Trace: `06`.
- Recurring Drafts: `05`.
- Comment deletion on erasure: `13`.
- Notifications: `10`.

## Sources

- Domain §5.1, §5.2, §5.9, §7.3–7.8, §7.16–7.17, §13.
- Decisions:
  - Q4, Q5, Q6 (P007);
  - M2 (P013); A1 (P015);
  - Q-A, Q-B (P021); FR-1 (P022);
  - LQ1, LQ2 (P029);
  - S1, S6, S7 (P043); S25 (P046);
  - R3-6 (P009).

## Dependencies

- `[FR-3 PROVISIONAL]`: EX-5.

## Rules

### Recording and validation

- **EX-1** Any Active registered member may record an Expense, except in an
  archived group (GR-12).
- **EX-2** The total is greater than zero and at most the limit (XC-16).
- **EX-3** Payers:
  - each Payer has a Paid amount greater than zero;
  - a Member appears at most once as a Payer;
  - Paid amounts add up exactly to the total;
  - a Payer need not be a Participant.
- **EX-4** Participants:
  - at least one;
  - a Member appears at most once.
- **EX-5** `[FR-3 PROVISIONAL]` The Expense date is set by the person
  recording it. It may be in the past but not in the future, judged by the
  group time zone (XC-18). It is separate from the recorded time, and never
  affects debts.
- **EX-6** Only Active members (placeholders included) can be added as Payers
  or Participants. Former members already on an existing Expense may remain
  on it.
- **EX-7** **Minimum share (S6):** the total must be at least the number of
  Participants, in smallest units. Otherwise the Expense is rejected with an
  explanation that the amount is too small to split among that many
  Participants.
- **EX-8** A refund is recorded by editing the original Expense. There are no
  negative Expenses and no separate refund record.

### Step 1: Shares

- **EX-9** Each Participant's Share is the total divided equally, in smallest
  units. Leftover units go one at a time to Participants in rotation order
  (MI-19), starting at the group's rotation position and skipping Members who
  aren't Participants. The rotation position then moves past the last Member
  who received a unit (LQ1).

### Step 2: Positions and Obligations (M2)

- **EX-10** Each Member's Expense position is their Paid amount minus their
  Share. Members with a positive position are Expense creditors. Members with
  a negative position are Expense debtors. Members at zero are neither.
- **EX-11** Each Expense debtor owes each Expense creditor an Obligation. The
  debtor's total debt is divided among the creditors in proportion to each
  creditor's positive position.
- **EX-12** Exactness:
  - each debtor's Obligations add up exactly to the size of their negative
    position;
  - each creditor's Obligations received add up exactly to their positive
    position;
  - every Obligation is within one smallest unit of its exact proportional
    value (Q-B);
  - a Member with a zero or positive position has no Obligation from that
    Expense.
- **EX-13** **Placing leftover units:**
  - units go first to the Obligations with the largest fractional
    remainder;
  - among Obligations with exactly equal remainders, the order is the
    debtor's rotation distance from the rotation position, then the
    creditor's (LQ2);
  - the canonical result is the first placement, in that order, that still
    keeps EX-12 exact;
  - the rotation position advances only when this tie-break actually
    decides where a unit goes.
- **EX-14** With a single Payer, every other Participant owes the Payer their
  Share.

### Versions

- **EX-15** Shares and Obligations are locked into the Expense Version that
  produced them.
- **EX-16** **Non-financial edit (S1):** a new Version that changes none of
  the total, Payers, Paid amounts or Participants keeps the previous Version's
  Shares and Obligations. It uses no rotation positions.
- **EX-17** **Financial edit:** a new Version that changes any of those inputs
  recalculates Shares and Obligations, using the rotation state current at
  that moment (EX-9, EX-13).
- **EX-18** Deleting or restoring an Expense doesn't change the rotation
  position.

### Comments

- **EX-19** Active registered members may comment on Expenses.
- **EX-20** Authors may edit or delete their own Comments. Admins may delete
  any Comment.
- **EX-21** Comment edits are not audited.
- **EX-22** Comments trigger no notifications (NT-5).
- **EX-23** An Expense description is required: 1–200 characters.
- **EX-24** A Comment is 1–2,000 characters.

## Acceptance criteria

- **AC-EX-1** (EX-10 to EX-12): Given a total of 100.00 paid A 60.00 and B
  40.00, shared by A, B, C and D, when it's recorded:
  - positions are A +35.00, B +15.00, C −25.00, D −25.00;
  - C owes A 17.50 and B 7.50;
  - D owes A 17.50 and B 7.50;
  - A and B owe each other nothing.
- **AC-EX-2** (EX-9, EX-13): Given a total of 10.00 paid A 6.00 and B 4.00,
  shared by C, D and E with Shares 3.34, 3.33 and 3.33, when it's recorded:
  - C owes A 2.00 and B 1.34;
  - D and E each owe A 2.00 and B 1.33;
  - the rotation doesn't advance in step 2, because no tie-break decided a
    unit.
- **AC-EX-3** (EX-7): Given 50 Participants and a total of 0.03 in a
  two-decimal currency, then the Expense is rejected with the minimum-share
  explanation.
- **AC-EX-4** (EX-16): Given Expense E, when only its description is edited,
  then the new Version's Shares and Obligations equal the previous ones, and
  the rotation position is unchanged.
- **AC-EX-5** (EX-17): Given Expense E, when its total changes, then Shares
  and Obligations are recalculated from the current rotation position.
- **AC-EX-6** (EX-5): Given the group's local date is 2026-03-10, when an
  Expense dated 2026-03-11 is recorded, then it is rejected. *(Provisional:
  FR-3.)*
- **AC-EX-7** (EX-3): Given Paid amounts that add up to 99.99 for a total of
  100.00, then the Expense is rejected.

## Out of scope

- Non-equal splits and multiple currencies (deferred by the intent).

## Open items

- None. EX-OI-1 was resolved by EX-23 and EX-24 (P050).
