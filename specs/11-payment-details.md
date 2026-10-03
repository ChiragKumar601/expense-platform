# 11 — Payment details

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready

## Scope

- Storing, viewing and copying Payment details.
- Who can see them.
- Notification when they change.
- Deleting them.

## Boundaries

- Audit of changes: `12` (AU-3).
- The account-deletion workflow: `13`.
- Notification delivery: `10`.

## Sources

- Domain §4.3.1, §5.10, §5.12.
- Intent §6, §7.
- Decisions: R11 (P008), NQ3 (P033), PH3 (P030).

## Dependencies

- None.

## Rules

- **PD-1** Payment details belong to a User, not to a Member. One set of
  details applies in every group the User belongs to.
- **PD-2** A User may add, change and remove their own Payment details.
  Nobody else may.
- **PD-3** Payment details are visible to, and can be copied by, the Active
  registered members of each group where the owning User is an Active
  member. They are hidden from Former members, and hidden in a group once the
  owning User becomes a Former member there.
- **PD-4** Placeholders have no Payment details.
- **PD-5** When a User's Payment details change, the Active registered
  members of every group where the User is an Active member are notified.
  This is a required notification (NT-2).
- **PD-6** The audit trail records only **that** Payment details changed,
  never their values (AU-3).
- **PD-7** Payment details are deleted when the User's account-deletion gate
  takes effect (PR-2).
- **PD-8** Splitsy never processes payments and doesn't integrate with
  payment apps.
- **PD-9** A User may store up to **5** Payment-detail entries. Each is a
  label (up to 40 characters) and a free-text value (up to 200 characters),
  for example "UPI: name@bank". There is no type-specific validation.
- **PD-10** Adding, changing or removing Payment details requires the User to
  sign in again first (XC-25).

## Acceptance criteria

- **AC-PD-1** (PD-3): Given Ana is Active in groups G1 and G2, then members of
  both groups see her details. When Ana leaves G1, G1 members no longer see
  them.
- **AC-PD-2** (PD-5, NT-2): Given Ana changes her details, then members of G2
  get an in-app notification, even with every category switched off.
- **AC-PD-3** (PD-6): Given Ana changes her details, then the audit entry
  shows that a change happened, with no values.
- **AC-PD-4** (PD-7): Given Ana deletes her account, then her Payment details
  are gone immediately.

## Out of scope

- Security measures for stored details (see `docs/architecture.md` §12).

## Open items

- None. PD-OI-1 was resolved by PD-9 (P050).
