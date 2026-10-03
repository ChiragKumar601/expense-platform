# Product Intent

**Product:** Splitsy — a commercial expense-sharing platform.

## 1. Problem

When people share costs, as roommates in a household do, money gets messy. People lose track of who paid what, who owes whom, and whether past debts were settled. Some people end up carrying more than their fair share, often without anyone noticing, and that causes friction between people.

## 2. Desired Outcome

Every member of a group can see clearly, and trust, where money stands between them, so that no one is unfairly burdened. Splitsy competes on **better user experience**, defined as:

- **Fast expense entry:** recording a typical expense takes minimal effort.
- **Balance clarity:** any member can understand who owes whom, and why. Every debt traces back to the expenses, settlements and write-offs that produced it.
- **Trust and transparency:** every change is visible and explainable.

## 3. Target Users

- **Primary use case for the first release:** roommates and households, meaning long-lived groups that share recurring costs.
- **Group size:** groups of 50+ members must be supported well, even though households are typically smaller.
- **Geography:** international from launch. Each group uses a single currency, chosen when the group is created. Multiple currencies within a group come in a later release.
- **Placeholder members:** people without an account can take part as placeholder members (see §6).

## 4. Core User Journeys

1. **Create a group:** a user creates a group, becomes its admin, and sets the group currency.
2. **Build membership:** an admin invites people to the group or adds placeholder members by name. A person takes over a placeholder only by accepting a claim invitation from an admin, and inherits its whole history.
3. **Record an expense:** an active registered member records an expense in the group currency, with one or more payers, split equally among the chosen participants, optionally with a receipt attached. When there are several payers, each member who comes out owing on the expense owes the members who come out ahead, in proportion to how much each of them is owed.
4. **Recurring expenses:** an active registered member defines a recurring expense, such as rent. Each occurrence appears as a draft that the series creator or an admin confirms.
5. **Understand debts:** a member views debts either pairwise (as incurred) or simplified (a simplified set of transfers, possibly routed through other active members), and can trace any debt back to its source records.
6. **Settle up:** the payer, the recipient or an admin records a payment made outside Splitsy, which can be partial. When one party is a placeholder, the other party or an admin records it. A payment to a former member can also be recorded. The member can see the counterparty's stored payment details to make the payment.
7. **Dispute a settlement:** the recipient of a settlement they believe is wrong raises a dispute. The settlement's creator or an admin resolves it, but never the person who raised it. If no such member exists, any other active registered member resolves it.
8. **Correct a record:** the creator of an expense, settlement or write-off (while still an active member), or an admin, edits or deletes it. Every change is recorded and deleted records can be restored. A routed settlement is not edited: it is deleted and recorded again. Only an admin can edit, delete or restore a record in a way that affects a former member's debt.
9. **Discuss an expense:** active registered members comment on an expense.
10. **Leave or remove:** a member who is settled up leaves the group. An admin removes a non-admin member, even one with open debts. Removal never changes recorded expenses, settlements or debts.
11. **Export and erasure:** a user exports group history or their personal data, and can delete their account. At a person's request, an admin permanently anonymizes the placeholder that represents them.
12. **Write off a debt:** a member forgives all or part of a debt owed to them by a former member.
13. **Archive a group:** an admin makes a group read-only, and can reverse it.
14. **Step down as admin:** an admin gives up the admin role and stays in the group as a member.

## 5. Product Scope

### Included (first release)

- Groups, with admin roles (promotion and step-down), admin-only invites and name-only placeholder members
- Expenses with multiple payers and **equal split only**
- One currency per group, chosen at creation. Every expense and settlement in the group uses it.
- Recurring expenses as drafts that need confirmation
- Pairwise and simplified debt views, toggled per user. Simplified suggestions can route payments through active members.
- Record-only settlements, including partial settlements and overpayment (with a warning), allocated automatically across the debts they reduce
- Write-offs of debts owed by former members
- Settlement disputes
- Stored payment details that active registered members can view and copy
- Full audit trail with restore
- Receipt attachments and receipt OCR (automatic extraction from receipt images)
- Comments on expenses
- Notifications: changes to expenses, recorded settlements, settle-up reminders, membership changes, payment-details changes, routed settlements that change a member's debts, and edits, deletions or restorations that change a former member's debt
- Group archiving
- Data export: group history, and personal data
- Account deletion with anonymization, and anonymization of placeholders on request
- Responsive web application

### Explicitly Excluded

**Deferred to later releases:**

- Multiple currencies within a group (expenses or settlements in a currency other than the group currency), including currency conversion and exchange rates
- Non-equal splits (exact amounts, percentages, shares, itemized). These are planned as the first **premium** features.
- Any paid tier or billing
- All offline capability. The first release is online only. When offline support comes later, it is intended to cover adding, editing and deleting, with conflicts shown to the user rather than silently overwritten, and permissions checked when the change syncs.
- Native mobile applications

**Not in the first release:**

- Negative expenses or a separate refund concept. A refund is recorded by editing the original expense.
- Disputes on expenses. Only settlements can be disputed.
- Merging two members of the same group
- Deleting a group, except where erasure obligations require it

**Non-goals** (Splitsy must never become these):

- A payment wallet or a processor of payments. Splitsy never holds or moves money.
- A budgeting or personal-finance analytics app
- Accounting software (invoicing, tax filing, bookkeeping)
- A social network (feeds or following beyond group collaboration)

## 6. Core Product Behavior

### Domain terms

Canonical definitions are in `docs/glossary.md`. The main terms are:

| Term | Meaning |
|---|---|
| **Group** | The container for all expenses, settlements, write-offs and debts. Every expense belongs to exactly one group. "Household" is the target use case, not a separate concept. |
| **Member** | One person's participation in one group. Either a **registered member** (has an account) or a **placeholder member** (has no account yet, and is identified by name only). |
| **Active registered member** | A registered member who currently belongs to the group. Only these members can act. |
| **Former member** | A member who has left, been removed, had their account deleted, or (for a placeholder) been anonymized. Their records and debts remain in the group unchanged. |
| **Admin** | An active registered member with administrative rights in a group. |
| **Creator** | The member who recorded a specific expense, settlement, write-off or recurring series. This is not the group creator unless the text says so. |
| **Group currency** | The single currency of a group, chosen when the group is created. Every amount in the group is in this currency. |
| **Expense** | A cost paid by one or more payers and shared equally among participants. |
| **Settlement** | A record that one member paid another outside Splitsy. Splitsy never processes the payment itself. |
| **Routed settlement** | A settlement that also reduces debts between other active members along a chain (for example, A pays C, reducing A→B and B→C). |
| **Write-off** | A record in which a member forgives all or part of a debt owed to them by a former member. |
| **Pairwise debt** | The net amount one member owes another, determined by the recorded expenses, settlements and write-offs. |
| **Net balance** | A member's total owed to them minus their total owing in the group. |
| **Dispute** | A challenge by a settlement's recipient that the settlement is incorrect. |
| **Archived group** | A read-only group. |
| **First release** | The initial production launch scope defined in §5. |

### Groups and roles

- The group creator starts as an admin. Admins can promote other active registered members to admin.
- An admin cannot demote or remove another admin, including the group creator. An admin stops being an admin only by **stepping down**, by leaving the group, or by deleting their account.
- The last admin cannot step down or leave. If the last admin deletes their account, the admin role passes automatically to another active registered member.
- Every group that has an active registered member has at least one admin. A group with no registered members left is archived permanently.
- Only admins can invite people or add placeholder members.
- Every active registered member can see every expense and settlement in the group. A former member can see only their own debts and the records behind them.

### Placeholder members

- A placeholder holds only a display name and its ledger entries. It holds no contact details, payment details or other personal information.
- Personal information can be added only after the person claims the placeholder.
- A placeholder can be claimed only by accepting a claim invitation from an admin, and not by someone who is already a member of the group.
- Placeholder members count fully toward expenses and debts.
- When a person claims a placeholder, the placeholder's whole history transfers to their account.
- At the person's request, an admin can anonymize a placeholder. This is final: it can never be re-invited, claimed or made identifiable again.
- A name linked to debts is still personal data under GDPR and DPDP, so placeholders remain in scope for those obligations.

### Expenses

- Any active registered member can add an expense. An expense can have multiple payers.
- Every expense is in the group currency. There is no currency conversion in the first release.
- In the first release the split is equal among the selected participants.
- **Several payers:** for each expense, a member's position is what they paid minus their share. Members with a positive position are owed money by the expense; members with a negative position owe money for it. Each member who owes pays the members who are owed in proportion to how much each of them is owed. A member who comes out ahead on an expense never owes anything for it.
- **Rounding:** when an amount can't be divided evenly, the leftover smallest currency units are assigned deterministically, in rotation tracked within each group, so that no one is systematically disadvantaged. This applies both to participants' shares and to dividing what each member owes among the members who are owed. Once an expense version is recorded, its allocation is locked into that version and does not change when the group's rotation moves on.
- A refund is recorded by editing the original expense.

### Recurring expenses

- Any active registered member can define a recurring expense. Each occurrence is created as a draft, which the series creator or an admin must confirm.
- A draft does not affect debts until it is confirmed. Drafts do not expire.

### Debts

- Each user chooses for themselves between a **pairwise** view (debts as incurred) and a **simplified** view (a simplified set of transfers).
- The simplified view changes only the settle-up suggestions. It never changes the underlying debts. Only recorded expenses, settlements and write-offs change debts.
- Simplified suggestions may be **routed** through active members: if A owes B and B owes C, the simplified view can suggest that A pays C directly. Suggestions never route through former members. Debts with former members are shown separately.
- When every suggested payment has been recorded, every pairwise debt between active members is zero.

### Settlements

- Every settlement is in the group currency.
- A settlement is recorded by its payer, its recipient or an admin. When one party is a placeholder, it is recorded by the other party or an admin. An active member may record a settlement to a former member; it is a valid record even though the former member can no longer dispute it.
- A settlement affects debts as soon as it is recorded.
- Every settlement is allocated automatically, whether or not it came from a suggestion. It first reduces the direct debt between payer and recipient, then reduces debts routed through other active members, and anything left is an overpayment. An overpayment is allowed after a warning.
- A settlement that reduces debts through other members is a routed settlement. The members whose debts it changes are notified. A routed settlement cannot be edited, only deleted and recorded again. Restoring it re-applies its original allocation.
- Partial settlements are allowed.
- Members can store payment details (for example, payment handles or bank details). Active registered members of the user's groups can view and copy them while that user is an active member of that group. Splitsy does not integrate with payment apps.
- The recipient of a settlement can **dispute** it. A disputed settlement stays in effect until the dispute is resolved. The settlement's creator or an admin resolves it, never the person who raised it; if no such member exists, any other active registered member resolves it. There is no time limit, and a settlement has at most one open dispute at a time.

### Write-offs

- A member can write off all or part of a debt owed to them by a former member. When the member who is owed is a placeholder, only an admin can.
- A write-off cannot exceed the debt. Write-offs are recorded, audited, and can be edited, deleted and restored like other records.

### Edits, deletion and history

- Only the creator of an expense, settlement or write-off (while an active member), or an admin, can edit, delete or restore it.
- Only an admin can edit, delete or restore an existing record in a way that affects a former member's debt, including a change to who they owe when their total stays the same. The former member is notified.
- This restriction does not apply to recording a new settlement to a former member (see Settlements), or to the member who is owed writing off a former member's debt (see Write-offs).
- Every create, edit and delete is recorded in a **full audit trail**: who made the change, when, and the values before and after. Active registered members can see the audit trail; a former member sees only what concerns their own debts. Deleted records can be restored.
- The audit trail is append-only for everyone, admins included. Anonymization is the only change ever made to history.

### Membership changes

- A member can leave only when settled up, meaning all their pairwise debts are zero. A debt owed to a deleted account does not block leaving.
- An admin can remove a non-admin member, even one with open debts. Removal never changes or redistributes recorded expenses, settlements or debts. The removed member becomes a former member, and their debts stay on record.
- A member who left or was removed can be invited back, and returns as the same member with their history.

### Accounts

- Deleting an account removes the user's personal data. Their identity is replaced by an anonymous label everywhere, history included. Their expenses, settlements, write-offs and debts stay in the group intact, so the group's ledger remains correct.
- Their payment details and comments are deleted. Expense descriptions and receipts are kept as group records.
- Any open debt is shown as owed to or from a deleted account. A deleted account cannot rejoin a group.

### Archived groups

- An admin can archive a group and unarchive it. Nothing can be recorded, edited or disputed in an archived group, and recurring drafts pause.
- Members of an archived group can still view and export it, and can leave if settled up.

## 7. Constraints

### Product

- Equal split is the only split method in the first release.
- One currency per group in the first release.
- Splitsy is record-only and never moves money.
- There is no paid tier in the first release. All features in the first release are free.

### Security

- Must comply with **GDPR** and **India's Digital Personal Data Protection (DPDP) Act**. This includes rights of access, portability and erasure.
- Stored payment details are personal and financial data and must be protected accordingly.
- Placeholders hold no personal information beyond a display name until claimed.
- Permission rules (§6) apply to every action.

### Financial

- Splitsy never holds, moves or processes funds.
- Every expense and settlement in a group is in the group currency.
- Rounding must be deterministic, locked into each expense version once recorded, and fair over time (rotating within each group).
- What each member owes each other member from an expense must be exact: nothing is created or lost.
- Debts must always be fully explained by the recorded expenses, settlements and write-offs.
- Removing a member or deleting an account never changes or redistributes recorded expenses, settlements or debts.
- Every routed settlement reconciles exactly with the debts it reduces.

### Operational

- Must be able to launch internationally from day one, with each group able to use its own currency.
- Must provide personal data export, and erasure through anonymization.

### Technical

- The first release is a **responsive web application**. Native mobile apps come later.
- The first release is **online only**.
- The web application must meet **WCAG 2.2 AA**.

## 8. Success Criteria

- **Primary:** at least **40%** of users return to the app at least once within **30 days** of recording their first expense. (The exact definitions of "user" and "return" are open; see §11.)
- **UX differentiators**, which still need measurable targets:
  - fast expense entry
  - members can trace any debt to its source records
  - every change is visible in the audit trail

## 9. Non-Functional Expectations

### Reliability

- Availability target: **99.95% or higher**.
- Financial records must never be silently lost or overwritten.

### Performance

- Recording an expense must be fast enough to support the "fast expense entry" differentiator. No quantitative target has been set yet.

### Security

- Must comply with GDPR, India's DPDP Act and WCAG 2.2 AA (see §7).
- Every action is checked against the role-based permissions in §6.

### Auditability

- A full, member-visible, append-only audit trail of every change to expenses, settlements, write-offs and disputes, with the values before and after. It also covers membership changes, role changes, recurring series and drafts, group settings, receipts, and the fact that payment details changed. Deletes can be restored.
- Anonymization is the only change ever made to history.

### Scalability

- Must support **100k+ users in the first year**, and groups of **50+ members**.

## 10. Assumptions

These were proposed during discussion and explicitly accepted:

1. Placeholder members count fully toward debts, and claiming a placeholder transfers its whole history to the new account.
2. The simplified view changes only settle-up suggestions, not the underlying debts.
3. The rotation of rounding leftovers is tracked within each group.
4. The first release has no paid tier or billing. Premium features, starting with non-equal splits, come in later releases.

## 11. Open Questions

1. **Monetization:** what is premium beyond non-equal splits? Are ads allowed?
2. **Recurring schedules:** which frequencies are supported? What happens when a series is edited or stopped?
3. **Notifications:** which channels (in-app, email, push, SMS)? What preferences can each user set?
4. **Receipt OCR:** which languages and currencies are supported? How accurate must it be? What happens when extraction fails?
5. **Launch markets:** which countries come first?
6. **Native mobile:** when, and in what order?
7. **Audit retention:** how long is history kept, and how does it fit with GDPR and DPDP erasure?
8. **Success metric:** who counts as a user (registered only?), and what counts as returning?

## 12. Risks

- **Scope:** the first release combines receipt OCR, comments, recurring drafts, an availability target of 99.95%+, scale of 100k+ users, and compliance with GDPR, DPDP and WCAG. That is a lot to deliver well at once.
- **Trust in records:** settlements take effect immediately and only the creator or an admin can edit records, so incorrect entries rely on disputes and admins to be corrected. Settlements recorded to placeholders or former members cannot be disputed by the recipient.
- **Admin power:** admins can edit any record and remove non-admin members. An admin cannot be demoted or removed by others, so nobody can take the role from an admin who misuses it. The audit trail, and members' ability to leave once settled up, are the safeguards.
- **Placeholder claims:** a mistaken claim transfers someone else's financial history. Claims require an admin-issued claim invitation.
- **Erasure vs auditability:** GDPR and DPDP erasure obligations may conflict with a full, restorable audit trail. Expense descriptions are kept on erasure and may still identify the person.
- **Unresolvable debts:** debts with removed members or deleted accounts may never be settled, leaving remaining members permanently owed or owing unless they write the debt off.
- **Routed settlements:** every settlement can reduce the debts of other members who took no action, which may confuse them or feel like a loss of control.
- **Differentiation:** "better UX" is the only differentiator, and established competitors exist.
- **Monetization:** there is no revenue in the first release, and premium value so far rests only on non-equal splits.

## 13. Approval

Status: Draft

Approver: Chirag Kumar
Approval date: —
