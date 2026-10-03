# 08 — Disputes

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready

## Scope

- Raising, resolving and withdrawing Disputes.
- Eligible Resolvers, including the fallback Resolver.
- Disputes in archived groups.
- Disputes when the disputer becomes a Former member.

## Boundaries

- Editing and deleting Settlements in general: `07`.
- Notifications: `10` (recipients: NT-6).
- Audit: `12`.

## Sources

- Domain §5.7, §8.6, §9.
- Decisions: R2, R3 (P008), Q8 (P011, P012), A3 (P015), AD-Q4 (P027), S3,
  S12 (P043, P044).

## Dependencies

- None.

## Rules

- **DP-1** Only Settlements can be disputed.
- **DP-2** Only the Settlement's recipient may raise a Dispute, and only while
  an Active registered member.
- **DP-3** Placeholders, Former members and Intermediate members cannot raise
  a Dispute.
- **DP-4** A Settlement has at most one open Dispute at a time. There is no
  time limit. A new Dispute may be raised once none is open.
- **DP-5** A disputed Settlement stays in effect until the Dispute is
  resolved.
- **DP-6** States: *open* → *upheld* | *rejected* | *withdrawn*. Every end
  state is final.
- **DP-7** **Resolver:** the Settlement's Creator, while an Active member, or
  an Admin. Never the disputer.
- **DP-8** **Fallback Resolver (S3):** if no member qualifies under DP-7, any
  other Active registered member may resolve the Dispute. When upholding it,
  the fallback Resolver deletes the Settlement in the same command. They gain
  no other edit or delete authority.
- **DP-9** **Upholding is one atomic command.** It records the outcome and
  edits or deletes the Settlement together. A Routed settlement can only be
  deleted (ST-15). A fallback Resolver can only delete (DP-8).
- **DP-10** The disputer may withdraw an open Dispute while an Active
  registered member.
- **DP-11** In an archived group, Disputes can't be raised, and an open
  Dispute is **frozen**: it can't be resolved or withdrawn until the group is
  unarchived.
- **DP-12** If the disputer becomes a Former member, the Dispute stays open
  and resolvable under DP-7 and DP-8. It can no longer be withdrawn.

## Acceptance criteria

- **AC-DP-1** (DP-2, DP-3): Given Routed settlement A→C through B, then only C
  may raise a Dispute. B's attempt is rejected.
- **AC-DP-2** (DP-7): Given C disputes a Settlement that C created, then C
  cannot resolve it.
- **AC-DP-3** (DP-8): Given the Creator is a Former member and the only Admin
  is the disputer, when Active registered member D upholds the Dispute, then
  the Settlement is deleted in the same command. D's later attempt to delete
  any other Settlement is rejected.
- **AC-DP-4** (DP-9): Given an Admin upholds a Dispute with an edit, then the
  outcome and the new Version are both committed, or neither.
- **AC-DP-5** (DP-11): Given an open Dispute, when the group is archived, then
  resolving and withdrawing it are rejected until the group is unarchived.
- **AC-DP-6** (DP-12): Given the disputer is removed, then their Dispute stays
  open, and an eligible Resolver can resolve it.

## Out of scope

- Disputes on Expenses (excluded from the first release).

## Open items

- None.
