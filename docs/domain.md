# Splitsy Domain Model

This document describes what Splitsy means as a business domain: who acts,
what exists, which rules always hold, and how things change over time. It
does not describe how any of it is implemented. Terms are defined in
`docs/glossary.md`. Product intent is in `intent/intent.md`.

**Status:** Draft. Synchronized with all decisions recorded through Prompt
016 of `prompts/development-log.md`, with the IMP-2 clarification and
wording fixes of Prompt 019.

## 1. Actors

| Actor | Who | Can act? |
|---|---|---|
| **Active registered member** | A User who belongs to a Group | Yes. This is the normal acting role. |
| **Admin** | An Active registered member with administrative rights | Yes, with extra rights (§9) |
| **Placeholder** | A person with no User, represented in a Group by name | No. Others act on its behalf (§9). |
| **Former member** | A Member who left, was removed, had their account deleted, or (for a Placeholder) was anonymized | No. They can only view their own debts, unless their account was deleted or they were anonymized (§9). |
| **Invitee** | A User who has received an Invitation | Can only accept or decline it |
| **Data subject** | Any person whose data Splitsy holds, including a Placeholder's person | Can exercise erasure and access rights, through the paths in §5.14 |
| **System** | Splitsy itself | Produces Drafts, passes on the admin role, archives Groups, and sends notifications (§11) |

## 2. Entities

**Records** are facts that Splitsy stores. **Derived concepts** are fully
determined by the records and are never separate facts.

### Records

| Entity | Essence |
|---|---|
| **User** | A person's account. Owns Payment details. Can hold one Member in each of many Groups. |
| **Group** | Has a fixed Group currency, a Rotation order, a Rotation position, an archived flag and a set of Members. |
| **Member** | One person in one Group. Holds a role (Admin or not), a state (§8.1), and a link to a User unless it is a Placeholder. |
| **Invitation** | An admin-issued offer, either a *member invitation* or a *claim invitation* for a named Placeholder. |
| **Expense** | Total amount, Payers with Paid amounts, Participants with Shares, Expense date, Recorded time, description and Creator. Each Version also locks its Expense positions and Obligations (§5.2). |
| **Settlement** | Payer, recipient, amount, Creator, Settlement allocation. Direct or routed. Has Versions. |
| **Write-off** | Creditor, Former-member debtor, amount, Creator. Has Versions. |
| **Dispute** | Belongs to one Settlement. Has a Disputer, a state and an outcome. |
| **Recurring series** | Defines a repeating Expense (amount, Payers, Participants) and its Creator. |
| **Draft** | One occurrence produced by a Recurring series. |
| **Comment** | Attached to one Expense and written by one Member. |
| **Receipt** | Attached to one Expense. |
| **Payment details** | Belong to one User. |
| **Audit entry** | One recorded change within a Group. |

### Derived concepts

| Concept | Determined by |
|---|---|
| **Pairwise debt** | The Obligations, Settlement allocations and Write-offs of the current Versions of all non-deleted ledger records in the Group |
| **Net balance** | The sum of a Member's Pairwise debts |
| **Suggested settlement** | The Pairwise debts between Active members |
| **Trace** | The ledger records that contribute to a Pairwise debt |

## 3. Relationships

- A **User** has zero or more **Members**, at most one per **Group**.
- A **Group** has one or more **Members**, one **Group currency**, one
  **Rotation order** and one **Rotation position**.
- A **Member** has at most one **User**. A Member with no User is a
  Placeholder.
- An **Invitation** belongs to one Group. A claim invitation targets exactly
  one Placeholder.
- An **Expense** belongs to one Group. It has one or more **Payers** and one
  or more **Participants**, all Members of that Group. Each Version of it
  produces zero or more **Obligations**, each from one Expense debtor to one
  Expense creditor.
- A **Settlement** belongs to one Group and has one payer and one recipient.
  Its **Settlement allocation** refers to one or more Pairwise debts.
- A **Write-off** belongs to one Group, between a creditor Member and a
  Former-member debtor.
- A **Dispute** belongs to one Settlement. A Settlement has at most one open
  Dispute at a time.
- A **Recurring series** belongs to one Group and produces zero or more
  **Drafts**. A confirmed Draft becomes exactly one Expense.
- A **Comment** and a **Receipt** each belong to one Expense.
- **Payment details** belong to one User. They are visible in Groups where
  that User is an Active member.
- An **Audit entry** belongs to one Group and refers to the record or
  Member it describes.

## 4. Groups, roles and membership: business rules

### 4.1 Groups

1. Any User can create a Group. The Group creator becomes its first Admin.
2. The Group currency is chosen at creation and never changes.
3. Every Active registered member can see every Expense and Settlement in
   the Group. Former members see only what §9 allows.

### 4.2 Roles

1. Admins can promote other Active registered members to Admin.
2. An Admin cannot demote or remove another Admin, including the Group
   creator.
3. An Admin stops being an Admin only by **Admin step-down**, by leaving the
   Group, or by deleting their account.
4. **Admin step-down:** an Admin gives up the Admin role by their own action,
   and remains an Active registered member.
5. **Last-Admin rule:** the last Admin cannot step down or leave until
   another Admin exists. The last Admin cannot be removed either, because no
   Admin can remove another Admin.
6. If the last Admin's account is deleted, the admin role passes
   automatically to the longest-standing Active registered member.
7. If no registered Members remain, the Group is archived automatically and
   permanently. Its Placeholders can no longer be claimed.

### 4.3 Placeholders

1. A Placeholder holds only a display name and its ledger records. It holds
   no contact details, Payment details or other personal information.
2. Only Admins can add Placeholders.
3. Placeholders count fully as Payers and Participants.
4. Personal information can be added only after a User claims the
   Placeholder.
5. At the person's request, an Admin can anonymize a Placeholder. It becomes
   a Former member in the final *anonymized* state, under an Anonymous label.
   Its records are kept. It can never be re-invited, claimed or made
   identifiable again.

### 4.4 Invitations and claims

1. Only Admins issue Invitations.
2. A Placeholder can be claimed only by accepting a claim invitation. Nobody
   can claim one on their own.
3. A User who already has a Member in a Group cannot accept a claim
   invitation in that Group. Merging Members is not supported.
4. On a claim, the Placeholder becomes that User's Member, and its whole
   history goes with it.
5. A member invitation accepted by a User who left or was removed
   reactivates their existing Member (a Rejoin). Any debt still open reappears
   on their active membership. Members whose account was deleted, and
   anonymized Placeholders, can never rejoin.

### 4.5 Leaving and removal

1. A Member can leave only when Settled up. The exception is a debt owed to a
   deleted account, which does not block leaving. The last-Admin rule also
   applies.
2. An Admin can remove a **non-admin** Member who is not Settled up. Removal
   never changes or redistributes any ledger record or debt.
3. A removed Member becomes a Former member, and their debts stay on record.
4. Creator rights apply only while the Creator is an Active member. After
   that, only Admins can edit, delete or restore their records.

## 5. Ledger: business rules

### 5.1 Expenses

1. Any Active registered member can record an Expense.
2. The total is greater than zero and in the Group currency.
3. Each Payer has a Paid amount greater than zero. A Member appears at most
   once as a Payer. The Paid amounts add up exactly to the total.
4. There is at least one Participant. A Member appears at most once as a
   Participant.
5. A Payer need not be a Participant.
6. Only Active members can be added as Payers or Participants. Former members
   already on a record may remain there.
7. The Expense date may be in the past but not the future. It affects
   display order and recurring occurrences only, not debts.
8. A refund is recorded by editing the original Expense. Negative Expenses
   do not exist.

### 5.2 Splitting and multi-payer attribution

An Expense Version turns into debts in two steps.

**Step 1: Shares**

1. Each Participant's Share is the total divided equally, in the Group
   currency's smallest unit.
2. Leftover units go one at a time to Participants in Rotation order,
   starting at the Group's Rotation position, and skipping Members who
   aren't Participants. The Rotation position then advances.
3. Shares change only if an edit changes the total or the Participants. Such
   an edit re-allocates leftover units from the current Rotation position.

**Step 2: Positions and Obligations (proportional to net credit)**

4. Each Member's **Expense position** is their Paid amount minus their Share.
   A Member who isn't a Payer has a Paid amount of zero. A Member who isn't a
   Participant has a Share of zero.
5. Members with a positive position are the **Expense creditors**. Members
   with a negative position are the **Expense debtors**. Members at zero are
   neither.
6. Each Expense debtor owes each Expense creditor an **Obligation**. Their
   debt is divided among the creditors in proportion to each creditor's
   positive position.
7. Obligations are exact:
   - each debtor's Obligations add up exactly to the size of their negative
     position;
   - each creditor's Obligations received add up exactly to their positive
     position.
8. Where the proportional division leaves fractions of the smallest unit,
   the leftover units are placed deterministically. Ties are decided by
   Rotation order, starting at the Group's Rotation position at the moment
   the Expense Version is recorded.
9. The resulting Obligations are **locked into that Expense Version**. Later
   advances of the Rotation position never change them.
10. With a single Payer, step 2 reduces to "every other Participant owes the
    Payer their Share".

**Both steps**

11. Deleting or restoring an Expense does not affect the Rotation position. A
    restored Expense keeps the Shares and Obligations of its Version.
12. The requirement is the deterministic rotation rule, applied in both
    steps. "Fair over time" is its purpose, not a separately measured
    guarantee.
13. These rules, the exactness of Obligations and the rotation-based
    tie-break, are domain rules. Only the specific procedure for placing
    leftover units within them belongs to architecture.

### 5.3 Recurring series and Drafts

1. Any Active registered member can define a Recurring series.
2. The System produces a Draft for each occurrence. A Draft does not affect
   debts, and it does not expire.
3. The series Creator (while Active) or an Admin confirms or discards each
   Draft. The Member who confirms becomes the Creator of the resulting
   Expense.
4. A Draft that includes a Former member cannot be confirmed until that
   Member is removed from it.
5. If the series Creator becomes a Former member, only Admins manage the
   series.
6. Drafts pause while the Group is archived.

### 5.4 Pairwise debts and views

1. Pairwise debts come only from the current Versions of non-deleted ledger
   records: Expense Obligations, Settlement allocations and Write-offs.
   Opposite debts between two Members offset each other.
2. Each User chooses the Pairwise view or the Simplified view for themselves.
3. The Simplified view only proposes Suggested settlements. It never changes
   debts.
4. Suggested settlements route only through Active members, Placeholders
   included. Debts that involve Former members appear only as Pairwise
   debts.

### 5.5 Settlements

1. A Settlement is recorded by its payer, its recipient, or an Admin. When
   one party is a Placeholder, it is recorded by the other party or an
   Admin.
2. An Active member may record a Settlement to a Former member. It is a valid
   financial record even though the Former member cannot dispute it.
3. The amount is greater than zero and in the Group currency.
4. A Settlement affects debts as soon as it is recorded.
5. **Every Settlement is allocated automatically, whether or not it came from
   a Suggested settlement:**
   - first, it reduces the payer's direct debt to the recipient;
   - then, it reduces Chains through eligible Active members, in a
     deterministic order, with each Chain reducing every one of its links by
     the same amount;
   - any remaining amount is an Overpayment, which becomes a debt owed by the
     recipient to the payer. It is allowed only after a warning, including
     when the payer owes the recipient nothing.
6. The Settlement allocation is computed when the Settlement is recorded and
   is fixed from then on. Later changes to other records change debts
   separately and never alter it.
7. A Settlement whose allocation reduces any Chain is a **Routed
   settlement**. Otherwise it is a **Direct settlement**. This depends only
   on the allocation, not on where the Settlement came from.
8. Intermediate members whose debts a Routed settlement changes are
   notified, and the change appears in their Trace.
9. Partial Settlements are allowed.
10. A Direct settlement can be edited like any ledger record, and its
    allocation is re-computed when it is edited.
11. A Routed settlement cannot be edited. It can only be deleted and
    recorded again. Restoring a deleted Routed settlement re-applies its
    original allocation.

### 5.6 Write-offs

1. A Write-off forgives all or part of a debt owed by a Former member.
2. Only the Member who is owed can write off the debt. When that Member is a
   Placeholder, only an Admin can.
3. A Write-off amount is greater than zero and cannot exceed the debt it
   writes off.
4. Write-offs follow the edit, delete and restore rules of ledger records
   (§5.8).
5. A debt that an Active member owes to a Former member can only be cleared
   by a Settlement (§5.5.2).

### 5.7 Disputes

1. Only Settlements can be disputed.
2. Only the Settlement's recipient can raise a Dispute, and only if they are
   an Active registered member. Placeholders, Former members and
   Intermediate members cannot raise Disputes.
3. A Settlement has at most one open Dispute at a time. There is no time
   limit.
4. A disputed Settlement stays in effect until the Dispute is resolved.
5. Who resolves:
   - The Resolver is the Settlement's Creator (while Active) or an Admin.
   - The Resolver is never the Disputer.
   - If no such Member exists, any other Active registered member can
     resolve.
6. Outcomes:
   - **Upheld:** the Settlement is edited, or deleted if it is routed.
   - **Rejected:** the Settlement stands.
   - **Withdrawn:** the Disputer cancels the Dispute.
7. No Dispute can be raised in an Archived group.

### 5.8 Editing, deleting and restoring ledger records

1. The Creator (while Active) or an Admin can edit, delete or restore a
   ledger record.
2. Every edit creates a new Version. Debts reflect the current Version, and
   earlier Versions stay in history with the Shares, Obligations and
   allocations locked into them.
3. Every field of an Expense or Direct settlement can be edited, within the
   rules of §5.1 and §5.5.
4. Only an Admin can edit, delete or restore an existing ledger record in a
   way that affects a Former member's debt. This includes a change to who
   they owe, even when their total stays the same. The Former member is
   notified. The rule does not restrict recording a new Settlement to a
   Former member (§5.5.2), or a Write-off by the Member who is owed
   (§5.6.2).
5. Deleting removes a record's effect on debts but keeps it in history.
   Restoring brings the effect back.
6. A restore that brings back debt involving a Former member shows a warning
   first.
7. A change based on an outdated Version is rejected. Nothing is silently
   overwritten.

### 5.9 Comments and Receipts

1. Active registered members can comment on Expenses.
2. Authors can edit or delete their own Comments. Admins can delete any
   Comment.
3. Receipts follow the Expense's edit rights.

### 5.10 Payment details

1. Payment details belong to a User.
2. They are visible to Active registered members of Groups where that User
   is an Active member. They are hidden from Former members, and hidden once
   the User becomes a Former member of that Group.
3. Changes to Payment details notify the members of those Groups.

### 5.11 Archived groups

1. An Admin can archive a Group and unarchive it.
2. In an Archived group, nothing can be recorded, edited or disputed, and
   Drafts pause.
3. Members can still view and export the Group, and can leave if Settled up.
4. A Group with no registered Members is archived automatically and cannot
   be unarchived (§4.2).
5. Groups cannot be deleted in the first release, except where erasure
   obligations require it.

### 5.12 Account deletion

1. A User can delete their account at any time. Deletion does not depend on
   any debt being cleared.
2. In every Group, that person's identity is replaced, history included, by
   an Anonymous label.
3. What is kept and what is deleted:
   - **Kept:** amounts, dates, ledger records, debts, expense descriptions
     and Receipts.
   - **Deleted:** Payment details and Comments.
4. A Member whose account was deleted cannot rejoin.

### 5.13 Exports

1. Any Active registered member can take a Group export. It contains the
   ledger, the Audit trail and Comments, with identities as currently shown.
   It excludes Payment details.
2. A User can take a Personal data export.

### 5.14 Erasure and access paths

- **Registered member:** account deletion (§5.12) and Personal data export.
- **Placeholder's person:** an Admin anonymizes the Placeholder (§4.3) at the
  person's request. This is final.

## 6. Business invariants

1. A User has at most one Member per Group.
2. Every Group with at least one Active registered member has at least one
   Admin.
3. An Admin loses the Admin role only by their own step-down, by leaving, or
   by account deletion. No Admin demotes or removes another Admin.
4. Admins, Creators exercising rights, Disputers and Resolvers are always
   Active registered members at the moment they act.
5. A Placeholder holds no personal information beyond a display name.
6. A Placeholder is claimed at most once. An Invitation is accepted at most
   once.
7. Anonymization is final. An anonymized Member is never re-invited,
   claimed, rejoined or re-identified.
8. Only Active members are added as Payers or Participants of an Expense or
   Draft. A Settlement or Write-off may involve a Former member.
9. A Settlement has at most one open Dispute.
10. A Draft is confirmed at most once, and becomes exactly one Expense.
11. The Audit trail is append-only. Anonymization is the only permitted
    rewrite of history.
12. Nothing happens in an Archived group except viewing, exporting and
    leaving.
13. Permission and state rules are checked against the state at the moment
    a change is recorded.

## 7. Financial invariants

1. **Single currency:** every amount in a Group is in the Group currency.
   Splitsy never converts currencies.
2. **Explained debts:** every Pairwise debt is fully determined by the
   current Versions of non-deleted Expenses (through their Obligations),
   Settlements and Write-offs. It can always be Traced to them.
3. **Positive amounts:** Expense totals, Paid amounts, Settlement amounts and
   Write-off amounts are all greater than zero.
4. **Paid amounts sum to the total:** the Paid amounts of an Expense add
   up exactly to its total.
5. **Shares sum to the total:** the Shares of an Expense add up exactly
   to its total, in the smallest currency unit. No unit is created or lost.
6. **Positions sum to zero:** the Expense positions of an Expense add up
   to zero.
7. **Exact Obligations:** each Expense debtor's Obligations add up exactly to
   their negative position, and each Expense creditor's Obligations received
   add up exactly to their positive position.
8. **Creditors owe nothing:** a Member with a positive or zero Expense
   position has no Obligation from that Expense.
9. **Allocation sums to the amount:** a Settlement's allocation plus any
   Overpayment add up exactly to its amount.
10. **Chain consistency:** a Chain reduces every one of its links by the same
    amount.
11. **Bounded write-off:** a Write-off never exceeds the debt it writes off.
12. **Drafts are inert:** Drafts never affect debts.
13. **Views never move money:** the Simplified view never changes a debt.
    Only ledger records do.
14. **Simplified view guarantee:** if every Suggested settlement is
    recorded, every Pairwise debt between Active members is zero.
15. **No redistribution:** removing a Member or deleting an account never
    changes or redistributes any ledger record or debt.
16. **Locked Versions:** an Expense Version's Shares and Obligations, and a
    Settlement's allocation, never change once recorded. A change creates a
    new Version. A Routed settlement is never edited.
17. **Deterministic rounding:** in both steps, leftover units follow the
    Rotation order from the Group's Rotation position when the Version is
    recorded. The same inputs and Rotation position always give the same
    Shares and Obligations.
18. **Record-only:** Splitsy never holds, moves or processes money.

## 8. Lifecycles and state transitions

### 8.1 Member

| From | Event | To | Conditions |
|---|---|---|---|
| (none) | added by Admin | Active Placeholder | Admin acts |
| (none) | member invitation accepted | Active registered | — |
| Active Placeholder | claim invitation accepted | Active registered | The User has no Member in this Group |
| Active | left | Former: left | Settled up, except debts owed to deleted accounts. Not the last Admin. |
| Active, non-admin | removed | Former: removed | Admin acts |
| Active Placeholder | anonymized | Former: anonymized | Admin acts at the person's request. Final. |
| Active registered | account deleted | Former: account deleted | Final |
| Former: left or removed | member invitation accepted | Active registered | Same Member, history continuous |

**Role (alongside the state):**

| From | Event | To | Conditions |
|---|---|---|---|
| Member | promoted | Admin | Promoted by an Admin. The Member is Active registered. |
| Admin | stepped down | Member | The Admin's own action. Not the last Admin. |
| Admin | left or account deleted | (Former member) | Leaving requires that they are not the last Admin. On account deletion of the last Admin, the role passes on (§4.2.6). |

No transition exists for one Admin demoting or removing another.

### 8.2 Invitation

`pending → accepted | declined | revoked | expired`. All end states are
final.

### 8.3 Group

`active ⇄ archived`. Archiving and unarchiving are done by an Admin. A Group
with no registered Members goes to archived automatically, and from there no
transition is possible.

### 8.4 Ledger record (Expense, Direct settlement, Write-off)

- `current (Version n) → edited → current (Version n+1)`. Each Version keeps
  its locked Shares, Obligations or allocation.
- `current ⇄ deleted` (delete, restore)

### 8.5 Routed settlement

`current ⇄ deleted`. There is no edit transition. Restore re-applies the
original allocation.

### 8.6 Dispute

`open → upheld | rejected | withdrawn`. All end states are final. A new
Dispute can be opened on the same Settlement once none is open.

### 8.7 Draft

`pending → confirmed (becomes an Expense) | discarded`. Drafts pause while
the Group is archived. A Draft with a Former member cannot be confirmed until
it is edited.

### 8.8 Recurring series

Defined by a Member and managed by its Creator or an Admin. Its further
states (for example stopped), and how edits affect existing Drafts, are left
to specification (§14).

### 8.9 User

`active → deleted` (final; anonymization follows in every Group).

## 9. Authorization concepts

Only **Active registered members** act. A Placeholder is always acted *for*.
A right held as "Creator" lapses when the Creator becomes a Former member.

| Action | Who |
|---|---|
| Create a Group | Any User |
| Invite; add a Placeholder; issue a claim invitation | Admin |
| Promote a Member to Admin | Admin |
| Demote another Admin | Nobody |
| Step down as Admin | The Admin themselves, unless they are the last Admin |
| Remove a Member | Admin, for non-admin Members only |
| Leave | The Member, if Settled up (except debts owed to deleted accounts), and not the last Admin |
| Anonymize a Placeholder | Admin, at the person's request |
| Record an Expense | Active registered member |
| Edit, delete or restore an Expense | Creator or Admin |
| Edit, delete or restore a record in a way that affects a Former member's debt, including who they owe | Admin only, and that person is notified. Recording a new Settlement to a Former member, or a Write-off by the Member who is owed, is not restricted by this rule. |
| Record a Settlement | Payer, recipient or Admin. If one party is a Placeholder: the other party or an Admin. A Settlement to a Former member: the Active payer or an Admin. |
| Edit a Direct settlement; delete or restore any Settlement | Creator or Admin |
| Edit a Routed settlement | Nobody |
| Record a Write-off | The Member who is owed. If that Member is a Placeholder: Admin. |
| Edit, delete or restore a Write-off | Creator or Admin |
| Raise a Dispute | The Settlement's recipient, if an Active registered member |
| Resolve a Dispute | The Settlement's Creator or an Admin, never the Disputer. If none exists: any other Active registered member. |
| Withdraw a Dispute | The Disputer |
| Define a Recurring series | Active registered member |
| Confirm or discard a Draft; manage a series | Series Creator or Admin |
| Comment | Active registered member |
| Edit or delete a Comment | Its author. Admins can also delete. |
| Add or remove a Receipt | Same as editing the Expense |
| Archive or unarchive | Admin |
| View the Group, its ledger and Audit trail | Active registered members |
| View as a Former member | Only their own debts and the records behind them. Nothing after account deletion or anonymization. |
| View Payment details | Active registered members of Groups where the owning User is Active |
| Choose the Pairwise or Simplified view | Each User, for themselves |
| Group export | Active registered member |
| Personal data export; delete account | The User |

## 10. Audit and history concepts

1. **Audit trail:** each Group has one. It is append-only for everyone,
   Admins included.
2. **Audit entry:** records who made the change, when, what changed, and the
   values before and after.
3. **What is audited:**
   - Expenses, Settlements (including allocations), Write-offs and Disputes:
     every create, edit, delete, restore and outcome.
   - Membership: joins, leaves, removals, claims, rejoins, anonymizations
     and role changes (promotions and step-downs).
   - Recurring series changes, and Draft confirmations and discards.
   - Group settings, including archive and unarchive.
   - Adding or removing a Receipt.
   - The *fact* that Payment details changed. The values are not recorded.
4. **What is not audited:** Comment edits.
5. **Versions:** every edit of a ledger record keeps the previous Version,
   including the Shares, Obligations or allocation locked into it.
6. **Deleted records:** they stay in history and can be restored.
7. **Trace:** every Pairwise debt can be traced to the ledger records that
   produced it, down to each Expense's Obligations.
8. **Anonymization:** the only permitted rewrite of history. It replaces
   identity, including in Audit entries. Amounts and dates are kept. It is
   final.
9. **Visibility:** the Audit trail is visible to Active registered members.
   Former members see only what concerns their own debts.

## 11. Important domain events

These are business facts that other parts of the product (Audit trail,
notifications, Trace) react to.

| Area | Events |
|---|---|
| Group | GroupCreated, GroupArchived, GroupUnarchived, GroupAutoArchived |
| Membership | PlaceholderAdded, InvitationIssued, InvitationAccepted, InvitationDeclined, InvitationRevoked, InvitationExpired, PlaceholderClaimed, MemberRejoined, MemberLeft, MemberRemoved, PlaceholderAnonymized, AdminPromoted, AdminSteppedDown, AdminRolePassedOn |
| Accounts | AccountDeleted, MemberAnonymized |
| Expenses | ExpenseRecorded, ExpenseEdited, ExpenseDeleted, ExpenseRestored, FormerMemberDebtChanged, ReceiptAttached, ReceiptRemoved |
| Settlements | SettlementRecorded, RoutedSettlementRecorded (affects Intermediate members), OverpaymentRecorded, SettlementEdited, SettlementDeleted, SettlementRestored |
| Write-offs | WriteOffRecorded, WriteOffEdited, WriteOffDeleted, WriteOffRestored |
| Disputes | DisputeRaised, DisputeUpheld, DisputeRejected, DisputeWithdrawn |
| Recurring | RecurringSeriesDefined, RecurringSeriesChanged, DraftProduced, DraftConfirmed, DraftDiscarded |
| Other | PaymentDetailsChanged, CommentAdded, CommentEdited, CommentDeleted, GroupExported, PersonalDataExported |

Notifications named by the intent or by decisions include:
- changes to Expenses
- recorded Settlements
- reminders to settle up
- membership changes
- Payment details changes
- Routed settlements, for Intermediate members
- edits, deletions and restorations that change a Former member's debt

Notification channels and preferences are open (intent §11).

## 12. Concurrency-sensitive business rules

These rules must hold even when changes happen at the same moment. How they
are enforced is an architecture concern.

1. A change based on an outdated Version is rejected. Nothing is silently
   overwritten.
2. Settlement allocations are computed one at a time per Group. Two
   Settlements never both allocate against the same debt as it stood before
   either was recorded.
3. Rotation positions are consumed one at a time per Group, by both rounding
   steps. No two Expense Versions receive the same leftover positions.
4. A Draft is confirmed at most once.
5. A Placeholder is claimed at most once, and an Invitation is accepted at
   most once.
6. The last-Admin rule holds even when several Admins step down or leave at
   the same moment.
7. The leaving rule, "Active members only", archive state and permissions
   are checked against the state when the change is recorded.
8. A Write-off never exceeds the debt as it stands when the Write-off is
   recorded, even if a Settlement against the same debt is recorded at the
   same moment.
9. A Settlement has at most one open Dispute even when two Disputes are
   raised at the same moment.
10. An anonymized Placeholder can never be claimed, even when a claim and an
    anonymization happen at the same moment.

## 13. Financial edge cases (decided)

| Case | Handling |
|---|---|
| Several Payers (A pays 60 and B pays 40 on 100, shared by A, B, C and D at 25 each) | Positions: A +35, B +15, C −25, D −25. C owes A 17.50 and B 7.50; D owes A 17.50 and B 7.50. A and B owe each other nothing for this Expense. |
| A Payer who doesn't share the cost | Allowed. Their Share is zero, so their position equals their Paid amount. |
| A Payer whose Paid amount is less than their Share | They are an Expense debtor for the difference only. |
| A refund | Edit the original Expense. There are no negative Expenses. |
| A Settlement larger than the direct debt between payer and recipient | The excess reduces Chains through Active members, which makes it a Routed settlement. Only what remains after that is Overpayment, allowed after a warning. |
| A Settlement when the payer owes the recipient nothing | Allocated the same way: Chains first, then Overpayment after a warning. |
| A partial payment | Allocated in the same order: direct debt, then Chains, then Overpayment. |
| Expenses change after a Routed settlement | The allocation stays fixed. Debts change separately. |
| Restoring a Routed settlement after debts changed | The original allocation is re-applied. A warning is shown if it brings back debt with a Former member. |
| A debt owed by a Former member | Cleared by a Settlement or a Write-off from the Member who is owed. |
| A debt owed to a Former member | Cleared by a Settlement recorded by the Active payer or an Admin. The Former member cannot dispute it. |
| A debt owed to a deleted account | Stays on record and does not block the debtor from leaving. |
| A Member who is net zero but has open Pairwise debts | Not Settled up, so they cannot leave. |
| Currencies with 0, 2 or 3 decimal places | Shares, Obligations and leftover units use that currency's smallest unit. |

## 14. Open items

No domain-level decision is outstanding. The remaining questions are
resolved at a later phase.

### 14.1 For specification

1. Which name history shows for records created before a Placeholder was
   claimed.
2. Who can revoke an Invitation, and what happens to pending Invitations,
   open Disputes and Drafts when their subject is removed or the Group is
   archived.
3. The Rotation order position of a Member who rejoins or claims, and of
   Former members.
4. Whether a new Expense Version that leaves the total, Payers, Paid amounts
   and Participants unchanged keeps the previous Version's Shares and
   Obligations. A Version that changes them is always re-allocated (§5.2).
   This affects only where leftover units fall, but under §5.8.4 any change
   it causes to a Former member's Obligations requires an Admin.
5. Recurring series: states (for example stopped), schedules, and whether
   series edits change existing Drafts.
6. Whether Intermediate members are notified when Expenses change after a
   Routed settlement.
7. Whether a Share of zero is allowed (a small amount among many
   Participants).
8. How a member sees their Obligations in the Trace.
9. Supported currencies and their smallest units. Amount limits.
10. Invitation expiry periods, minimum age under DPDP, the Anonymous label
    format, how a Placeholder erasure request is verified, export formats,
    notification channels, audit retention and launch markets.

### 14.2 For architecture

1. The procedure for placing leftover units in step 2, within the rules of
   §5.2.
2. The procedure for ordering Chains and computing Suggested settlements,
   within the rules of §5.4 and §5.5.
3. How outdated Versions are detected (§12.1).
4. How allocations and Rotation positions are applied one at a time per
   Group (§12.2–3).
5. Whether debts are stored or computed each time (the domain requires only
   that they are fully determined by records).
6. How anonymization and the Audit trail are stored.
7. How notifications are delivered.
