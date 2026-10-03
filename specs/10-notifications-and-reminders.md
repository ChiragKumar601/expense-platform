# 10 — Notifications and reminders

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready

## Scope

- Channels.
- Required notifications.
- Recipients.
- Preferences and digests.
- Settle-up reminders.
- The notification time zone.
- Notification history and how long it is kept.

## Boundaries

- Which events happen is defined by the feature specs. This spec defines who
  is told, and how.
- Personal-data classification of notification history: `13`.

## Sources

- Domain §11.
- Decisions:
  - R3 (P008); NP1–NP8 (P032); NQ3 (P033);
  - S21 (P045);
  - S23–S31 (P046);
  - IMP-2 (P019).

## Dependencies

- None. The group time zone (FR-3) plays no part in notification timing.

## Rules

### Channels and required notifications

- **NT-1** First-release channels are **in-app** and **email**. There is no
  push and no SMS.
- **NT-2** **Required notifications** are always delivered in-app,
  whatever the member's preferences. They are:
  - a change to a Former member's debt by an edit, deletion or restoration
    (XC-13), sent to that Former member;
  - a Routed settlement that changes an Intermediate member's debts (ST-12),
    sent to each Intermediate member;
  - a change to someone's Payment details (PD-5).

### Recipients

- **NT-3** Recipients are worked out from the group's state at the version of
  the event. Placeholders and Users whose account is deleted never receive
  notifications. Notification content shows each person's current identity
  when it is displayed.
- **NT-4** When a Draft is produced (RE-8), and when a series includes a
  Former member (RE-14), the series creator and the Admins are notified.
- **NT-5** Comments trigger no notifications.
- **NT-6** Recipients by event:

| Event | Recipients |
|---|---|
| Expense recorded, edited, deleted or restored | Every member whose debts the old or new Version affects, **except the member who made the change** |
| Settlement recorded or changed | The payer, the recipient, and any Intermediate members its allocation affects, **except the member who made the change** |
| Membership change: join, leave, removal, claim, role change, step-down | All Active registered members |
| Payment-details change | As in NT-2 and PD-5 |
| Dispute raised or resolved | The disputer, the Settlement's Creator, and the Admins |
| Write-off recorded | The Former member whose debt is written off. Optional, in the settlements category (P050). |

- **NT-7** Former members who left or were removed receive **only**:
  - the required notifications about their own debts (NT-2); and
  - the Write-off notification (NT-6) when a Write-off affects their debt.
    It is **optional**, follows the normal channel and preference rules
    (NT-9 to NT-11), and is **not** a required notification under NT-2.

  Email follows their preferences (NT-9).

### Preferences and delivery

- **NT-8** Delivery: in-app notifications appear immediately and are never
  batched.
- **NT-9** **Preferences:** a User sets preferences per **category**
  (expenses, settlements, membership, drafts, disputes, reminders) and per
  **channel** (in-app, email). Preferences affect delivery only.
- **NT-10** Required notifications can't be switched off in-app. Email for
  required notifications is on by default, and the User may switch it off.
- **NT-11** **Email:** a **daily digest** by default. A User may choose
  immediate email instead. Required notifications go into the digest unless
  the User chose immediate email.
- **NT-12** No member receives the same notification twice on the same
  channel.

### Reminders

- **NT-13** **Settle-up reminders** are automatic only. Every week, each
  member with at least one Pairwise debt outstanding for **more than 7 days**
  gets a reminder. It lists what they owe each creditor, and offers a way to
  settle. It never includes Suggested settlements. A User may switch reminders
  off. There are no manual nudges.

### Time zone and history

- **NT-14** **Notification time zone (S29):** each User has a time zone
  preference, used **only** to schedule their email digests and reminders. It
  never affects Expense dates, occurrence dates, debts, allocation or any
  ledger behaviour (XC-19). Its starting value is taken from the browser at
  sign-up, or UTC if it can't be detected. The User may change it.
- **NT-17** The daily email digest is sent at **08:00**, and the weekly
  reminder on **Monday at 09:00**, both in the User's notification time
  zone.
- **NT-15** **Notification history:** in-app notification history is kept
  **90 days**, then deleted. It is not an audit record. The audit trail (see
  `12`) is the permanent record.
- **NT-16** Pending notifications to a User are cancelled when the User
  deletes their account (PR-2).

## Acceptance criteria

- **AC-NT-1** (NT-2, NT-10): Given a member switched off every in-app
  category, when a Routed settlement changes their debt, then they still get
  an in-app notification.
- **AC-NT-2** (NT-6): Given Ana records an Expense shared by Ben and Chen,
  then Ben and Chen are notified, Ana is not, and other members are not.
- **AC-NT-3** (NT-7): Given Former member F, when an Admin edits an Expense
  changing F's debt, then F is notified. When a membership change happens in
  that group, F is not notified.
- **AC-NT-4** (NT-11): Given default preferences, then email notifications
  arrive as one daily digest. Given immediate email is chosen, each
  notification is emailed when it happens.
- **AC-NT-5** (NT-13): Given Ben has owed Ana 40 for 8 days, then Ben's weekly
  reminder lists 40 owed to Ana and no suggestions.
- **AC-NT-6** (NT-14): Given a User sets their time zone to UTC+5:30, then
  their digest is scheduled in that zone, and no Expense date or occurrence
  date changes.
- **AC-NT-7** (NT-15): Given an in-app notification is 91 days old, then it no
  longer exists in the history.
- **AC-NT-8** (NT-5): Given a Comment is added, then no notification is
  created.

## Out of scope

- Push notifications and SMS (deferred).
- Email template design.

## Open items

- None. NT-OI-1, NT-OI-2 and NT-OI-3 were resolved by NT-17, NT-14 and NT-6
  (P050).
