# 01 — Groups and roles

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready, except GR-3 and GR-4, which are blocked by FR-3

## Scope

- Creating a group.
- Group settings (currency, time zone).
- Archiving and unarchiving.
- Admin roles: promotion, step-down, the last-admin rule.
- Admin succession.
- Automatic archiving.

## Boundaries

- Invitations and their automatic revocation on archive: `02` (MI-9).
- Disputes while archived: `08` (DP-11).
- Drafts while archived, and catch-up: `05` (RE-12, RE-13).
- The meaning of "longest-standing": `02` (MI-20).
- Leaving and removal of members: `02`.

## Sources

- Domain §4.1, §4.2, §5.11, §6.2, §6.3, §8.1, §8.3, §9.
- Decisions: A5 (P015), admin step-down (P016), R8, R14 (P008), R3-2 (P009),
  Q8 (P011, P012), S12 (P044), FR-3 (P035).

## Dependencies

- `[FR-3 PROVISIONAL]`: GR-3, GR-4.

## Rules

### Group creation and settings

- **GR-1** Any User can create a group. The creating User becomes its first
  Member and its first Admin.
- **GR-2** The group currency is chosen at creation (XC-17) and never changes.
- **GR-3** `[FR-3 PROVISIONAL]` The group creator sets the group's time zone
  at creation.
- **GR-14** A group name is required: 1–100 characters after trimming
  leading and trailing spaces. It doesn't have to be unique.
- **GR-4** `[FR-3 PROVISIONAL]` An Admin may change the group's time zone. The
  change is audited as a group-setting change, and affects only recurring
  occurrences that haven't happened yet.

### Roles

- **GR-5** An Admin may promote an Active registered member to Admin.
- **GR-6** No Admin may demote or remove another Admin, including the group
  creator. An Admin stops being an Admin only by **Admin step-down**, by
  leaving the group, or by deleting their account.
- **GR-7** **Admin step-down:** an Admin may give up the Admin role by their
  own action, and remains an Active registered member. The last Admin cannot
  step down.
- **GR-8** **Last-admin rule:** while the group has at least one Active
  registered member, it has at least one Admin. The last Admin cannot leave
  (MI-15).
- **GR-9** **Succession** (domain §4.2.6): if the last Admin's account is
  deleted, the System passes the Admin role to the longest-standing Active
  registered member (as defined in MI-20).
- **GR-10** **Automatic archiving** (domain §4.2.7): if no registered Members
  remain in a group, the System archives it permanently. It cannot be
  unarchived, and its placeholders can no longer be claimed.

### Archiving

- **GR-11** An Admin may archive a group, and may unarchive a group that was
  not archived automatically (GR-10).
- **GR-12** In an archived group, nothing can be recorded, edited, restored or
  disputed. Members can still view the group, export it (see `13`), and leave
  if Settled up (MI-15). Effects that other specs own:
  - pending invitations are revoked (MI-9);
  - open Disputes freeze (DP-11);
  - Draft production pauses (RE-12).
- **GR-13** A group cannot be deleted in the first release, except where an
  erasure obligation requires it (see `13`).

## Acceptance criteria

- **AC-GR-1** (GR-6): Given Admins A and B, when A tries to demote or remove
  B, then the command is rejected as *not permitted*.
- **AC-GR-2** (GR-7, GR-8): Given A is the only Admin, when A tries to step
  down or leave, then it's rejected. Given B is also an Admin, A's step-down
  succeeds and A stays an Active registered member.
- **AC-GR-3** (GR-9): Given A is the only Admin, and C and D are Active
  registered members with C longest-standing, when A deletes their account,
  then the System makes C an Admin.
- **AC-GR-4** (GR-10): Given a group's only registered Member deletes their
  account, then the group is archived permanently, and unarchiving is
  rejected.
- **AC-GR-5** (GR-12): Given an archived group, when a member records an
  Expense, then it is rejected. When a Settled-up non-admin member leaves, it
  succeeds.

## Out of scope

- The anonymization workflow that triggers GR-9 and GR-10 (see `13`).

## Open items

- None. GR-OI-1 was resolved by GR-14 (P050). FR-3 (GR-3, GR-4) is tracked
  in the README register.
