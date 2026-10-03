# 02 — Membership and invitations

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready; MI-24 depends on S34

## Scope

- Invitations and their lifecycle, including automatic revocation.
- Placeholders and placeholder names.
- Claims, and how names appear after a claim.
- Joining, leaving, removal and rejoining.
- Rotation order and "longest-standing".
- What Former members can see.

## Boundaries

- Placeholder anonymization workflow: `13` (PR-8 to PR-11).
- Archive behaviour: `01`.
- Admin roles: `01`.
- How rotation is used in rounding: `03` (EX-9, EX-13).
- Audit entries for membership changes: `12`.
- Personal-data classification of invitation emails: `13`.

## Sources

- Domain §4.3, §4.4, §4.5, §6.1, §6.5–6.7, §8.1, §8.2, §9.
- Decisions: Q1, Q2, Q3 (P007), Q7 (P011), R3-3, R3-4 (P009), A4, A5 (P015),
  S8–S14, S14b (P044, P045), PQ1 (P031).

## Dependencies

- `[S34 OPEN]`: by reference only (MI-24).

## Rules

### Invitations

- **MI-1** Only Admins issue Invitations. An Invitation is either a **member
  invitation** or a **claim invitation** for one named placeholder.
- **MI-2** Every Invitation is addressed to one email address and delivered
  as a single-use link. There are no shareable group links.
- **MI-3** The recipient must sign in, or create an account, before
  accepting.
- **MI-4** Invitation states: *pending* → *accepted* | *declined* | *revoked*
  | *expired*. Every end state is final.
- **MI-5** A pending Invitation expires **14 days** after it is issued.
- **MI-6** Only a pending Invitation can be accepted. Accepting an expired,
  revoked, declined or already-accepted Invitation is rejected.
- **MI-7** Any Admin may revoke a pending Invitation. Non-admins cannot.
- **MI-8** When a placeholder is anonymized, its pending claim invitation is
  revoked automatically.
- **MI-9** When a group is archived, all its pending Invitations are revoked
  automatically.
- **MI-25** An Invitation's email address is deleted **30 days** after the
  Invitation reaches an end state (MI-4). This was recommended as a detail,
  subject to a light legal check (P050).
- **MI-26** Issuing an Invitation, and an Admin revoking one, are audited
  (AU-3). Declines and expiries are not.
- **MI-10** Accepting a member invitation:
  - by a User with no Member in the group, creates an Active registered
    member;
  - by a User whose Member **left or was removed**, reactivates that same
    Member (a Rejoin), with continuous history, and any open debts reappear;
  - by a User who is already an Active member, is rejected.

  Members whose account was deleted, and anonymized placeholders, can never
  rejoin.

### Placeholders and claims

- **MI-11** Only Admins add placeholders. A placeholder holds only a display
  name. Contact details, payment details and other personal information
  cannot be added before a claim.
- **MI-12** A placeholder's display name must be unique, ignoring case, among
  the display names of all Active members of the group. Registered members'
  names do not have to be unique.
- **MI-13** Accepting a claim invitation links the placeholder to the
  accepting User, and the placeholder's whole history becomes that User's
  history. A User who already has a Member in the group, in any state, cannot
  accept a claim invitation for that group. A claim is never blocked because
  the User's account name matches another member's name.
- **MI-14** After a claim, every record, history entry included, shows the
  claiming User's current name. The claim itself is visible in the audit
  trail (see `12`). Records never show both the old placeholder name and the
  new name.

### Leaving and removal

- **MI-15** A Member may leave only when Settled up. The one exception: a debt
  owed **to** a deleted account doesn't block leaving. The last-admin rule
  applies (GR-8).
- **MI-16** An Admin may remove a **non-admin** Member, even one with open
  debts. Removal never changes or redistributes any ledger record or debt. The
  Member becomes a Former member (removed).
- **MI-17** A Former member can see only their own debts and the records
  behind them. That includes receipts on those records (RO-6). Former members
  cannot act.
- **MI-18** Placeholders count fully as Payers and Participants. Others act
  for them, under the rules in `07` and `09`.

### Rotation order and longest-standing

- **MI-19** The rotation order lists a group's Members by their **original
  join order**. A Member who rejoins keeps their original position. A claimed
  placeholder keeps the placeholder's position. A temporary change in
  membership state never resets a position.
- **MI-20** "Longest-standing" means the earliest **original join date**,
  which is consistent with MI-19.

### Display

- **MI-21** When two Active members' names match, ignoring case, the
  presentation adds a short number based on join order, for example "Priya
  Shah (2)". Partial email addresses are never shown. There is no separate
  display name per group.

### Placeholder erasure

- **MI-22** Placeholder anonymization is specified in `13` (PR-8 to PR-11).
- **MI-23** Anonymization is final (A4).
- **MI-24** `[S34 OPEN]` Erasure when no Admin acts is unresolved.

## Acceptance criteria

- **AC-MI-1** (MI-5, MI-6): Given an Invitation issued 15 days ago, when the
  recipient tries to accept it, then it is rejected as expired.
- **AC-MI-2** (MI-7): Given a pending Invitation, when a non-admin tries to
  revoke it, then it's rejected. When any Admin revokes it, it becomes
  *revoked*.
- **AC-MI-3** (MI-9): Given two pending Invitations, when the group is
  archived, then both become *revoked*, and that change is audited.
- **AC-MI-4** (MI-12): Given an Active member named "Priya Shah", when an
  Admin adds a placeholder named "priya shah", then it is rejected.
- **AC-MI-5** (MI-13, MI-14): Given placeholder "Ravi" with history, when
  User "Ravi Kumar" accepts the claim invitation, then every historical record
  shows "Ravi Kumar", and the audit trail shows the claim.
- **AC-MI-6** (MI-13): Given User U is an Active member of group G, when U
  tries to accept a claim invitation in G, then it is rejected.
- **AC-MI-7** (MI-15): Given member M owes 0 to everyone except 500 owed to a
  deleted account, when M leaves, then it succeeds.
- **AC-MI-8** (MI-10, MI-19): Given member M left, when M accepts a new member
  invitation, then M is the same Member, with their previous history,
  previous open debts and original rotation position.

## Out of scope

- How invitation emails are delivered (see `10`, `docs/architecture.md`).

## Open items

- None. MI-OI-1, MI-OI-2 and MI-OI-3 were resolved by MI-25, MI-21 and MI-26
  (P050).
