# Product Intent

**Product:** Splitsy — a commercial expense-sharing platform.

## 1. Problem

When people share costs, as roommates in a household do, money gets messy. People lose track of who paid what, who owes whom, and whether past debts were settled. Some people end up carrying more than their fair share, often without anyone noticing, and that causes friction between people.

## 2. Desired Outcome

Every member of a group can see clearly, and trust, where money stands between them, so that no one is unfairly burdened. Splitsy competes on **better user experience**, defined as:

- **Fast expense entry:** recording a typical expense takes minimal effort.
- **Balance clarity:** any member can understand who owes whom, and why. Every balance traces back to the expenses and settlements that produced it.
- **Trust and transparency:** every change is visible and explainable.

## 3. Target Users

- **Primary use case for the first release:** roommates and households, meaning long-lived groups that share recurring costs.
- **Group size:** groups of 50+ members must be supported well, even though households are typically smaller.
- **Geography:** international from launch. Each group uses a single currency, chosen when the group is created. Multiple currencies within a group come in a later release.
- **Placeholder members:** people without an account can take part as placeholder members (see §6).

## 4. Core User Journeys

1. **Create a group:** a user creates a group, becomes its admin, and sets the group currency.
2. **Build membership:** an admin invites people to the group or adds placeholder members by name. A person who signs up claims their placeholder and inherits its history.
3. **Record an expense:** any member records an expense in the group currency, with one or more payers, split equally among the chosen participants, optionally with a receipt attached.
4. **Recurring expenses:** a member defines a recurring expense, such as rent. Each occurrence appears as a draft that the series creator or an admin confirms.
5. **Understand balances:** a member views balances either pairwise (debts as incurred) or simplified (fewest transfers, possibly routed through other members), and can trace any balance back to its source records.
6. **Settle up:** a member records a payment made outside Splitsy, which can be partial. They can see the counterparty's stored payment details to make that payment.
7. **Dispute a settlement:** the recipient of a settlement they believe is wrong raises a dispute. The creator or an admin resolves it.
8. **Correct a record:** the creator of an expense or settlement, or an admin, edits or deletes it. Every change is recorded and deleted records can be restored.
9. **Discuss an expense:** members comment on an expense.
10. **Leave or remove:** a member settles up and leaves, or an admin removes a member who still has a balance. Removal never changes recorded expenses, settlements or debts.
11. **Export and erasure:** a user exports group history or their personal data, and can delete their account.

## 5. Product Scope

### Included (first release)

- Groups, with admin roles, admin-only invites and name-only placeholder members
- Expenses with multiple payers and **equal split only**
- One currency per group, chosen at creation. Every expense and settlement in the group uses it.
- Recurring expenses as drafts that need confirmation
- Pairwise and simplified balance views, toggled per user. Simplified suggestions can route payments through members.
- Record-only settlements, including partial settlements and overpayment (with a warning)
- Settlement disputes
- Stored payment details that members can view and copy
- Full audit trail with restore
- Receipt attachments and receipt OCR (automatic extraction from receipt images)
- Comments on expenses
- Notifications: changes to expenses, recorded settlements, settle-up reminders, membership changes
- Data export: group history, and personal data
- Account deletion with anonymization
- Responsive web application

### Explicitly Excluded

**Deferred to later releases:**

- Multiple currencies within a group (expenses or settlements in a currency other than the group currency), including currency conversion and exchange rates
- Non-equal splits (exact amounts, percentages, shares, itemized). These are planned as the first **premium** features.
- Any paid tier or billing
- All offline capability. The first release is online only. When offline support comes later, it is intended to cover adding, editing and deleting, with conflicts shown to the user rather than silently overwritten, and permissions checked when the change syncs.
- Native mobile applications

**Non-goals** (Splitsy must never become these):

- A payment wallet or a processor of payments. Splitsy never holds or moves money.
- A budgeting or personal-finance analytics app
- Accounting software (invoicing, tax filing, bookkeeping)
- A social network (feeds or following beyond group collaboration)

## 6. Core Product Behavior

### Domain terms

| Term | Meaning |
|---|---|
| **Group** | The container for all expenses, settlements and balances. Every expense belongs to exactly one group. "Household" is the target use case, not a separate concept. |
| **Member** | A participant in a group. Either a **registered member** (has an account) or a **placeholder member** (has no account yet, and is identified by name only). |
| **Former member** | A member who has left the group, been removed by an admin, or deleted their account. Their recorded expenses, settlements and balances remain in the group unchanged. |
| **Admin** | A member with administrative rights in a group. |
| **Creator** | The member who recorded a specific expense, settlement or recurring series. This is not the group creator unless the text says so. |
| **Group currency** | The single currency of a group, chosen when the group is created. Every expense, settlement and balance in the group is in this currency. |
| **Expense** | A cost paid by one or more payers and shared equally among participants. |
| **Settlement** | A record that one member paid another outside Splitsy. Splitsy never processes the payment itself. |
| **Balance** | What is owed between members, shown either pairwise or simplified. |
| **Routed settlement** | A settlement suggested by the simplified view between two members who do not owe each other directly, which settles a chain of pairwise debts through one or more intermediate members. |
| **Dispute** | A challenge by a settlement's recipient that the settlement is incorrect. |
| **First release** | The initial production launch scope defined in §5. |

### Groups and roles

- The group creator starts as an admin. Admins can promote and demote other members. A group always has at least one admin.
- Only admins can invite people or add placeholder members.
- Every member can see every expense and settlement in the group.

### Placeholder members

- A placeholder holds only a display name and its ledger entries. It holds no contact details, payment details or other personal information.
- Personal information can be added only after the person claims the placeholder and accepts it as their account.
- Placeholder members count fully toward expenses and balances.
- When a person signs up and claims a placeholder, the placeholder's whole history transfers to their account.
- A name linked to debts is still personal data under GDPR and DPDP, so placeholders remain in scope for those obligations.

### Expenses

- Any member can add an expense. An expense can have multiple payers.
- Every expense is in the group currency. There is no currency conversion in the first release.
- In the first release the split is equal among the selected participants.
- **Rounding:** when an amount can't be divided evenly, the leftover smallest currency units are assigned to participants in rotation, tracked within each group, so that no one is systematically disadvantaged. Once an expense is saved, its allocation is fixed unless the expense itself is edited.

### Recurring expenses

- Any member can define a recurring expense. Each occurrence is created as a draft, which the series creator or an admin must confirm.

### Balances

- Each user chooses for themselves between a **pairwise** view (debts as incurred) and a **simplified** view (the minimum set of transfers).
- The simplified view changes only the settle-up suggestions. It never changes the underlying debts. Only recorded expenses and settlements change debts.
- Simplified suggestions may be **routed**: if A owes B and B owes C, the simplified view can suggest that A pays C directly.
- Every routed payment must reconcile exactly with the underlying pairwise debts it settles. When every suggested payment has been recorded, every pairwise debt in the group is zero.

### Settlements

- Every settlement is in the group currency.
- Any member can record a settlement. It affects balances as soon as it is recorded.
- A settlement recorded from a routed suggestion reduces each pairwise debt in the chain it was routed through, by exactly the amounts that reconcile them.
- Partial settlements are allowed. A settlement larger than the amount owed is allowed after a warning.
- Members can store payment details (for example, payment handles or bank details), and other members can view and copy them. Splitsy does not integrate with payment apps.
- The recipient of a settlement can **dispute** it. A disputed settlement stays in effect until its creator or an admin resolves it.

### Edits, deletion and history

- Only the creator of an expense or settlement, or an admin, can edit or delete it.
- Every create, edit and delete is recorded in a **full audit trail**: who made the change, when, and the values before and after. Group members can see the audit trail. Deleted records can be restored.

### Membership changes

- A member whose balance is not zero must settle up before leaving.
- An admin can remove a member who still has a balance. Removal never changes or redistributes recorded expenses, settlements or debts. The removed member becomes a former member, and their balance stays on record, shown as owed to or from them.

### Accounts

- Deleting an account removes the user's personal data. Their expenses, settlements and balances stay in the group intact under an anonymized identity, so the group's ledger remains correct.
- Any open balance is shown as owed to or from a deleted account until it is resolved.

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
- Rounding must be deterministic once saved, and fair over time (rotating within each group).
- Balances must always be fully explained by the recorded expenses and settlements.
- Removing a member or deleting an account never changes or redistributes recorded expenses, settlements or debts.
- Every routed settlement reconciles exactly with the pairwise debts it settles.

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
  - members can trace any balance to its source records
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

- A full, member-visible audit trail of every create, edit and delete for expenses and settlements, with the values before and after. Deletes can be restored.

### Scalability

- Must support **100k+ users in the first year**, and groups of **50+ members**.

## 10. Assumptions

These were proposed during discussion and explicitly accepted:

1. Placeholder members count fully toward balances, and claiming a placeholder transfers its whole history to the new account.
2. The simplified balance view changes only settle-up suggestions, not the underlying debts.
3. The rotation of rounding leftovers is tracked within each group.
4. The first release has no paid tier or billing. Premium features, starting with non-equal splits, come in later releases.

## 11. Open Questions

1. **Recurring drafts:** do unconfirmed drafts affect balances? Do they expire?
2. **Monetization:** what is premium beyond non-equal splits? Are ads allowed?
3. **Group lifecycle:** can the group currency change once expenses exist? What do archiving and deleting a group mean?
4. **Recurring schedules:** which frequencies are supported? What happens when a series is edited or stopped?
5. **Notifications:** which channels (in-app, email, push, SMS)? What preferences can each user set?
6. **Receipt OCR:** which languages and currencies are supported? How accurate must it be? What happens when extraction fails?
7. **Launch markets:** which countries come first?
8. **Native mobile:** when, and in what order?
9. **Placeholder claims:** how does a person claim a placeholder, given it holds no contact details? How are mistaken or malicious claims prevented?
10. **Audit retention:** how long is history kept, and how does it fit with GDPR and DPDP erasure?
11. **Disputes:** what states can a dispute be in? What resolution actions exist? Are there time limits? Can expenses be disputed too?
12. **Success metric:** who counts as a user (registered only?), and what counts as returning?
13. **Former member balances:** what does "resolved" mean for a balance with a removed member or deleted account? Who can record settlements with them? Is a write-off allowed, and who approves it?
14. **Leaving while owed by a former member:** does a debt to or from a former member stop a remaining member from leaving?
15. **Removed members:** can a removed member still see their balance? Can they be re-added to the group?
16. **Routed settlements:** how is a partial payment against a routed suggestion allocated across the chain? Who can dispute a routed settlement? How are intermediate members told that their debts changed?

## 12. Risks

- **Scope:** the first release combines receipt OCR, comments, recurring drafts, an availability target of 99.95%+, scale of 100k+ users, and compliance with GDPR, DPDP and WCAG. That is a lot to deliver well at once.
- **Trust in records:** settlements take effect immediately and only the creator or an admin can edit records, so incorrect entries rely on disputes and admins to be corrected.
- **Admin power:** admins can edit any record and remove members. Misuse could damage trust, and the audit trail is the main safeguard.
- **Placeholder claims:** a mistaken or malicious claim transfers someone else's financial history.
- **Erasure vs auditability:** GDPR and DPDP erasure obligations may conflict with a full, restorable audit trail.
- **Unresolvable balances:** balances with removed members or deleted accounts may never be settled, leaving remaining members permanently owed or owing.
- **Routed settlements:** a routed settlement changes the debts of intermediate members who took no action, which may confuse them or feel like a loss of control.
- **Differentiation:** "better UX" is the only differentiator, and established competitors exist.
- **Monetization:** there is no revenue in the first release, and premium value so far rests only on non-equal splits.

## 13. Approval

Status: Draft

Approver: Chirag Kumar
Approval date: —
