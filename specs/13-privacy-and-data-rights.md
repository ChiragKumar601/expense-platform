# 13 — Privacy and data rights

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: blocked by S34 (PR-11), legal review (PR-12 contents), and legal review plus intent amendment (PR-15)

## Scope

- Account deletion and the anonymization workflow: the gate, per-group
  processing and deadlines.
- Placeholder erasure.
- Personal data exports and group exports.
- The personal-data inventory, with retention and erasure mapped for each
  item.

## Boundaries

- Historical-record semantics (what anonymization does to history,
  visibility, audit retention): `12`. This spec refers to them and doesn't
  restate them.
- Each feature's retention behaviour stays in that feature's spec, and is
  referenced from the inventory (PR-14).

## Sources

- Domain §4.3.5, §5.12–5.14.
- Decisions:
  - R6 (P008); R3-3, R3-8 (P009); A4 (P015);
  - AD-Q3 (P027);
  - PH1, PH3, PQ3, PQ4 (P030, P031);
  - S9, S12 (P044);
  - S32, S33, S34, S42 (P047).

## Dependencies

- `[S34 OPEN]`: PR-11.
- Legal review: the details of lawful basis and consent (see `14` for
  analytics consent).

## Rules

### Account deletion

- **PR-1** A User may delete their account at any time. Before they confirm,
  the system shows their open debts and offers a personal data export (PR-12).
  The User must sign in again and explicitly confirm. There is **no grace
  period**, and deletion can't be undone. Deletion never depends on debts
  being cleared.
- **PR-2** **The gate takes effect immediately on confirmation:**
  - signing in is no longer possible;
  - the User can no longer act in any group, and can't be added to any
    record (XC-1, XC-4);
  - Payment details are deleted (PD-7);
  - pending notifications to the User are cancelled (NT-16).
- **PR-3** **Then, in each group the User belongs to:**
  - the Member gets the Anonymous label and becomes a Former member (account
    deleted);
  - their Comments are deleted;
  - admin succession (GR-9) and automatic archiving (GR-10) apply where
    needed.

  The effect on history is defined in AU-7.
- **PR-4** Each group's processing (PR-3) normally completes within **24
  hours** and must complete within **72 hours**. Breaching 72 hours raises an
  operational alert. This is an internal operational target, not a legal
  deadline. The gate (PR-2) never waits for it.
- **PR-5** **Kept:** amounts, dates, ledger records, debts, Expense
  descriptions and Receipts. **Deleted:** Payment details and Comments. Open
  debts are shown as owed to or from the Anonymous label.
- **PR-6** The User's data held by outside providers (sign-in, analytics and
  provider copies) is deleted as part of the deletion.
- **PR-7** Erased personal data never reappears after a backup is restored
  (see OP-4).

### Placeholder erasure

- **PR-8** Only an Admin may anonymize a placeholder, at the request of the
  person it represents.
- **PR-9** The Admin confirms in the product that the request was received.
  That confirmation is audited, without storing any personal data of the
  requester.
- **PR-10** Anonymizing a placeholder makes it a Former member in the final
  *anonymized* state, under an Anonymous label. Its records are kept. It can
  never be re-invited, claimed or made identifiable again. Its pending claim
  invitation is revoked (MI-8).
- **PR-11** `[S34 OPEN]` What happens when the person's request isn't acted on
  by any Admin is unresolved. It needs legal and privacy review. Allowing an
  operator action would change the domain's authorization model, and needs
  explicit domain reopening.

### Exports

- **PR-12** **Personal data export:** a User may request a copy of the
  personal data Splitsy holds about them, in machine-readable **JSON** and
  readable **CSV**. It is generated within **24 hours** (normally sooner). Its
  download link expires after **24 hours**. The file isn't kept after that.
  `[LEGAL REVIEW]` It contains:
  - the User's profile, preferences, opt-out status and Payment details;
  - for each group, their membership, the records they take part in (as
    an Expense Payer or Participant, a Settlement payer or recipient, or a
    Creator), and their own
    Comments;
  - their notification history (the last 90 days).

  Other members appear by name only, where needed for context. Other
  members' Payment details and Comments are never included.
- **PR-13** **Group export:** an Active registered member may export a group.
  It contains the ledger, the audit trail and Comments, with identities as
  currently shown, and **excludes Payment details**. Same formats, generation
  and link rules as PR-12. It is available in archived groups (GR-12).

### Personal-data inventory

- **PR-14** The personal-data inventory, with its erasure mapping:

| Data | Retention (owning rule) | On account deletion |
|---|---|---|
| User identity (name, email, sign-in link) | While the account exists | Deleted at the gate; external sign-in data deleted (PR-6) |
| Member identity | While the group exists | Replaced by the Anonymous label (PR-3, AU-7) |
| Payment details | While the User keeps them (PD-2) | Deleted at the gate (PD-7) |
| Invitation email addresses | 30 days after the Invitation ends (MI-25) | Deleted (MI-25) |
| Comments | While the Expense exists | Deleted (PR-3) |
| Expense descriptions | While the group exists | Kept (accepted risk, R3-8) |
| Receipt files | RO-4, RO-7 | Kept (accepted risk, R6) |
| In-app notification history | 90 days (NT-15) | Pending notifications cancelled (NT-16); history expires (NT-15) |
| Audit trail | As long as the group exists (AU-10) | Identity anonymized (AU-7) |
| Analytics events | `14` (AN-4) `[S38 OPEN]` | Deleted (AN-5) |
| Application logs (IDs only) | 30 days (OP-9) | Expire |
| Operator-access logs | 1 year (OP-8) | Expire |
| Backups | Point-in-time recovery for 35 days (OP-3) | Erasure re-applied on restore (PR-7) |
| OCR provider data | No retention, or the unavoidable minimum (RO-14) | — |
| Export files | Until the 24-hour link expires (PR-12, PR-13) | — |

### Minimum age

- **PR-15** `[PR-15 LEGAL + INTENT AMENDMENT]` Users must be **18 or older**,
  confirmed by the User at sign-up. There is no parental-consent flow in the
  first release. **Provisional:** this needs legal review and an intent
  amendment (it narrows intent §3), and isn't authoritative until both are
  done.

## Acceptance criteria

- **AC-PR-1** (PR-1): Given Ana owes Ben 40, when Ana requests deletion, then
  she sees the debt and is offered an export. Deletion requires signing in
  again and confirming.
- **AC-PR-2** (PR-2): Given Ana confirms deletion, then immediately Ana can't
  sign in, can't be added as a Participant in any group, and her Payment
  details are gone.
- **AC-PR-3** (PR-3, PR-5): Given Ana belonged to G1 and G2, then within 72
  hours both groups show her Anonymous label, her Comments are gone, and
  Ben's debt with her is unchanged in amount.
- **AC-PR-4** (PR-4): Given a group's processing hasn't finished after 72
  hours, then an operational alert is raised.
- **AC-PR-5** (PR-10): Given placeholder P with a pending claim invitation,
  when an Admin anonymizes P, then the invitation is revoked, and any later
  claim or re-invite is rejected.
- **AC-PR-6** (PR-13): Given a group export, then it has no Payment details,
  and its link stops working after 24 hours.
- **AC-PR-7** (PR-7): Given Ana's account was deleted, when a backup taken
  before the deletion is restored, then Ana's personal data stays erased.

## Out of scope

- Legal text (privacy notice, consent wording).

## Open items

- PR-OI-1 was resolved by PR-12, pending legal review. PR-OI-2 was resolved
  by MI-25. PR-OI-3 was resolved by PR-15, which is provisional: it needs
  legal review and an intent amendment.
- S34 (PR-11) is tracked in the README register.
