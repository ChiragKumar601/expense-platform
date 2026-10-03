# 12 — Audit trail and history

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready

## Scope

- What the audit trail contains, and who can see it.
- Version history.
- Anonymization as the only historical identity rewrite.
- Tamper detection.
- Audit retention.

## Boundaries

- The anonymization workflow: `13`.
- Operator-access audit logs: `15`.
- Name display after a claim: `02` (MI-14).
- Notification history isn't audit: `10` (NT-15).

## Sources

- Domain §10.
- Decisions: R5 (P008), R3-6 (P009), Q9 (P011), LQ3 (P029), PH1, PH2, PQ2
  (P030, P031), S12 (P044), S18 (P045), S40 (P047), FR-3 (P035).

## Dependencies

- None. A change to the group time zone is audited per GR-4, which is itself
  `[FR-3 PROVISIONAL]`.

## Rules

### Content

- **AU-1** Each group has one audit trail. It is append-only for everyone,
  Admins and operators included.
- **AU-2** Each audit entry records:
  - who made the change (a Member, or the System);
  - when;
  - the record and Version affected;
  - the action;
  - the values before and after.
- **AU-3** These are audited:
  - every create, edit, delete and restore of an Expense, Settlement
    (including its allocation) or Write-off;
  - every Dispute action and outcome;
  - membership: joins, leaves, removals, claims, rejoins, anonymizations,
    promotions and step-downs;
  - admin succession and automatic archiving by the System;
  - Recurring series changes, and Draft confirmations and discards;
  - group settings, including archive and unarchive;
  - adding and removing Receipts;
  - issuing Invitations, Admin revocation of Invitations (MI-26), and
    automatic revocation of Invitations (MI-8, MI-9);
  - the **fact** that Payment details changed, never their values.
- **AU-4** Comment edits are not audited.

### History

- **AU-5** Earlier Versions of every ledger record stay viewable, with the
  Shares, Obligations or allocation locked into them. Deleted records stay
  in history, and can be restored (XC-12).
- **AU-6** Historical results are never recalculated. If a calculation
  defect is found, history is left unchanged, and corrections happen through
  ordinary edits and new Versions (LQ3).
- **AU-7** Anonymization is the **only** change ever made to how history
  identifies people. It replaces a person's identity with their Anonymous
  label everywhere, the audit trail included. Amounts and dates are
  unchanged.

### Visibility, integrity, retention

- **AU-8** Active registered members see the whole audit trail of their
  group. A Former member sees only entries about their own debts. Users
  whose account is deleted see nothing.
- **AU-9** Any change to, deletion of, or insertion into past audit entries
  is detectable. When it's detected, an incident is raised (OP-6).
- **AU-10** The audit trail is kept for as long as the group exists, subject
  to anonymization (AU-7). It is separate from:
  - notification history (NT-15);
  - operator-access logs (OP-8);
  - application logs (OP-9).

## Acceptance criteria

- **AC-AU-1** (AU-1): Given an audit entry, when anyone, an Admin included,
  tries to change or delete it through the product, then it's impossible.
- **AC-AU-2** (AU-5): Given Expense E edited twice, then all three Versions
  are viewable, each with its own Obligations.
- **AC-AU-3** (AU-7): Given Ana deletes her account, then audit entries
  previously showing "Ana" show her Anonymous label, with amounts and dates
  unchanged.
- **AC-AU-4** (AU-8): Given Former member F, then F sees audit entries for the
  records behind F's debts, and no others.
- **AC-AU-5** (AU-9): Given an audit entry altered directly in storage, then
  verification detects it, and an incident is raised.
- **AC-AU-6** (AU-3): Given a group is archived, then its pending Invitations'
  automatic revocations appear in the audit trail.

## Out of scope

- How tamper detection is implemented (see `docs/architecture.md` §9.2).

## Open items

- None. AU-OI-1 was resolved by MI-26 and AU-3 (P050).
