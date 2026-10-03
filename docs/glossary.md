# Splitsy Glossary

The canonical business language of Splitsy. Use these terms in specs,
tickets, code and conversation. Where a word is listed under _Avoid_, use the
canonical term instead. For rules and behaviour, see `docs/domain.md`.

## People and membership

**User**:
A person's account in Splitsy. One User can be a Member of many Groups.
_Avoid_: Account holder, customer

**Member**:
One person's participation in one Group. Debts, roles and history belong to
the Member, not the User.
_Avoid_: User (when meaning a participant in a group), participant (when
meaning group membership)

**Registered member**:
A Member linked to a User.

**Placeholder**:
A Member with no User, identified only by a display name. Other people act on
its behalf until a User claims it.
_Avoid_: Ghost member, guest, dummy

**Active member**:
A Member who currently belongs to the Group. This includes Placeholders.

**Active registered member**:
An Active member linked to a User. Only Active registered members can act in
a Group.
_Avoid_: Using "active member" when meaning "can act"

**Former member**:
A Member who no longer belongs to the Group, because they *left*, were
*removed*, their account was *deleted*, or (for a Placeholder) they were
*anonymized*. Their records and debts stay in the Group unchanged.
_Avoid_: Inactive member, ex-member, deleted member

**Admin**:
An Active registered member with administrative rights in a Group. The role
ends only through Admin step-down, leaving, or account deletion. Another
Admin cannot take it away.
_Avoid_: Owner, moderator

**Admin step-down**:
An Admin giving up the Admin role by their own action, while remaining an
Active registered member. The last Admin cannot step down.
_Avoid_: Demotion (no Admin demotes another)

**Group creator**:
The User who created the Group, and who starts as its first Admin.

**Creator**:
The Member who recorded a specific ledger record or Recurring series. For an
expense confirmed from a Draft, this is the Member who confirmed it.
_Avoid_: Author, owner (for records)

**Invitation**:
An admin-issued offer for a User to join a Group, either as a new Member
(*member invitation*) or by taking over a specific Placeholder (*claim
invitation*).
_Avoid_: Invite link, join request

**Claim**:
Accepting a claim invitation, which links a Placeholder to a User and gives
that User the Placeholder's whole history. An anonymized Placeholder can
never be claimed.
_Avoid_: Merge, link, adopt

**Rejoin**:
The return of a Former member who left or was removed. They come back as the
same Member, with continuous history. Members whose account was deleted, and
anonymized Placeholders, can never rejoin.

**Anonymization**:
Replacing a person's identity everywhere in a Group, history included, with
an anonymous label. Amounts and dates are kept. It happens on account
deletion, or on request for a Placeholder, and it is final.
_Avoid_: Deletion (when records are kept)

**Anonymous label**:
The stable identity shown in place of an anonymized person (for example
"Former member 3").

## Groups

**Group**:
The container for all ledger records and debts among a set of Members.
_Avoid_: Household (a use case, not a concept), circle, team

**Group currency**:
The single currency of a Group. It is chosen when the Group is created and
never changes. Every amount in the Group is in this currency.
_Avoid_: Base currency, default currency

**Archived group**:
A read-only Group. Nothing can be recorded, edited or disputed in it.
_Avoid_: Closed group, deleted group, frozen group

**Rotation order**:
The fixed order of a Group's Members, by join order, used to place leftover
units deterministically.

**Rotation position**:
The point in the Rotation order where the next leftover unit in a Group will
be placed. It advances as leftover units are placed, and an Expense Version
uses the position current when it is recorded.

## Ledger records

**Ledger record**:
A record that affects debts: an Expense, a Settlement or a Write-off.

**Expense**:
A cost paid by one or more Payers and shared equally among Participants.
_Avoid_: Bill, transaction, charge

**Payer**:
A Member who paid part of an Expense. Each Payer has a Paid amount.

**Paid amount**:
How much one Payer contributed to an Expense.

**Participant**:
A Member who shares the cost of an Expense.
_Avoid_: Splitter, debtor

**Share**:
A Participant's portion of an Expense: an equal split of the total, plus any
leftover unit they absorb.
_Avoid_: Split (as a noun for one portion)

**Leftover unit**:
One smallest currency unit that remains when an amount can't be divided
exactly. This happens when splitting Shares among Participants, and when
dividing an Expense debtor's debt among Expense creditors. Leftover units are
placed by Rotation order, from the Rotation position.
_Avoid_: Remainder, rounding error, penny

**Expense position**:
A Member's Paid amount on an Expense minus their Share of it.
_Avoid_: Net (for one expense), contribution

**Expense creditor**:
A Member whose Expense position is positive. They are owed money by that
Expense.

**Expense debtor**:
A Member whose Expense position is negative. They owe money for that Expense.

**Obligation**:
The amount one Expense debtor owes one Expense creditor because of one
Expense Version. Each debtor's debt is divided among the creditors in
proportion to their positive positions, exactly and deterministically.
_Avoid_: Split, sub-share

**Expense date**:
The date the cost happened, set by the person recording it. It may be in the
past, but not the future.

**Recorded time**:
When a ledger record was entered into Splitsy.

**Version**:
One state of a ledger record. Every edit creates a new Version, and the
latest one is the current Version. A Version locks its Shares, Obligations or
allocation.

**Deleted record**:
A ledger record that no longer affects debts but stays in history and can be
restored.
_Avoid_: Removed record, voided record

**Restore**:
Returning a Deleted record to effect.

**Settlement**:
A record that one Member paid another outside Splitsy. Splitsy never moves
money.
_Avoid_: Payment, transfer, repayment

**Direct settlement**:
A Settlement whose allocation affects only the Pairwise debt between its
payer and recipient (plus any Overpayment).

**Routed settlement**:
A Settlement whose allocation also reduces debts along one or more Chains
through Intermediate members. Any Settlement can become routed, whether or
not it came from a Suggested settlement. It cannot be edited.

**Chain**:
A sequence of Pairwise debts between Active members (for example A→B, B→C)
that one Routed settlement reduces by the same amount at every link.

**Intermediate member**:
A Member inside a Chain whose debts change because of a Routed settlement
they took no part in.

**Settlement allocation**:
How a Settlement's amount is divided across the debts it reduces: the direct
debt first, then Chains, then any Overpayment. It is fixed when the
Settlement is recorded.

**Overpayment**:
The part of a Settlement that exceeds what it can reduce. It becomes a debt
owed by the recipient to the payer.

**Write-off**:
A ledger record in which a Member forgives all or part of a debt owed to them
by a Former member.
_Avoid_: Forgiveness, cancellation, adjustment

## Debts and views

**Pairwise debt**:
The net amount one Member owes another, determined by ledger records alone:
Expense Obligations, Settlement allocations and Write-offs.
Opposite debts between the same two Members offset each other.
_Avoid_: Balance

**Net balance**:
A Member's total owed to them minus their total owing, across the Group.
_Avoid_: Balance (unqualified). "Balance" appears only in "Net balance" and
in the name of the "Balance clarity" differentiator (intent §2), which
refers to Pairwise debts and Net balances.

**Settled up**:
The state of a Member whose Pairwise debts are all zero.
_Avoid_: Even, square, zero balance

**Cleared**:
Said of a debt that has reached zero, whether through Settlements,
Write-offs or changes to Expenses.
_Avoid_: Resolved (reserved for Disputes)

**Pairwise view**:
A display of debts exactly as incurred, one Pairwise debt per pair of
Members.

**Simplified view**:
A display of Suggested settlements that would clear all debts between Active
members in fewer payments. It never changes debts.
_Avoid_: Minimum view, optimized view, debt simplification (as a debt change)

**Suggested settlement**:
A payment proposed by the Simplified view. It is not a record until someone
records it as a Settlement.

**Trace**:
The list of ledger records that produced a given debt.

## Disputes

**Dispute**:
A challenge, raised by a Settlement's recipient, that the Settlement is
incorrect.
_Avoid_: Complaint, objection, flag

**Disputer**:
The Member who raised a Dispute.

**Resolver**:
The Member who decides a Dispute.

**Upheld**:
The outcome where the Dispute is accepted and the Settlement is corrected or
deleted. A Routed settlement can only be deleted.

**Rejected**:
The outcome where the Dispute is declined and the Settlement stands.

**Withdrawn**:
The outcome where the Disputer cancels the Dispute.

**Resolved**:
Said of a Dispute that ended as Upheld or Rejected.

## Recurring expenses

**Recurring series**:
A definition that produces a Draft for each occurrence of a repeating
Expense.
_Avoid_: Subscription, template, schedule (as the concept)

**Draft**:
A proposed Expense produced by a Recurring series. It does not affect debts
until it is confirmed.
_Avoid_: Pending expense

**Confirm**:
Turning a Draft into an Expense.

**Discard**:
Rejecting a Draft without creating an Expense.

## History and data

**Audit trail**:
The append-only history of changes in a Group: who changed what, when, and
the values before and after.
_Avoid_: Activity feed, log (in user-facing language)

**Audit entry**:
One change recorded in the Audit trail.

**Payment details**:
A User's stored information for receiving payments outside Splitsy (for
example payment handles or bank details).
_Avoid_: Wallet, payment method

**Receipt**:
An image or document attached to an Expense as evidence.

**Comment**:
A Member's remark on an Expense.

**Group export**:
A copy of a Group's ledger, Audit trail and Comments, taken by an Active
registered member.

**Personal data export**:
A copy of everything Splitsy holds about one User, taken by that User.
