# 09 — Write-offs

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready

## Scope

- Recording Write-offs, and their limits.
- Editing, deleting and restoring them, with the limit check.

## Boundaries

- General lifecycle: `00`.
- Debts: `06`.
- Audit: `12`.

## Sources

- Domain §5.6, §7.11.
- Decisions: R7 (P008), R3-1 (P009), Q11 (P011), Q-F (P021), IMP-2 (P019).

## Dependencies

- None.

## Rules

- **WO-1** A Write-off forgives all or part of a debt owed **by a Former
  member**.
- **WO-2** Only the member who is owed may record the Write-off, while an
  Active registered member. When the member who is owed is a placeholder,
  only an Admin may.
- **WO-3** The amount is greater than zero, and no greater than the current
  debt it writes off.
- **WO-4** Write-offs follow the ledger-record lifecycle (XC-9 to XC-14).
- **WO-5** An edit or restore is **rejected** if the resulting Write-off
  would exceed the current debt. It is never capped, and never creates a
  reverse debt (Q-F). The member may record a new Write-off for the current
  amount instead.
- **WO-6** A debt that an Active member **owes to** a Former member cannot be
  written off. It is cleared only by a Settlement (ST-3).
- **WO-7** Recording a Write-off notifies the Former member whose debt is
  written off (NT-6, NT-7). This is an optional notification, subject to
  their preferences, and not a required one.

## Acceptance criteria

- **AC-WO-1** (WO-1, WO-2): Given Former member F owes Ben 50, when Ben writes
  off 20, then F owes Ben 30. When Ana, who is not owed, tries to, it's
  rejected.
- **AC-WO-2** (WO-3): Given F owes Ben 30, when Ben writes off 40, then it is
  rejected.
- **AC-WO-3** (WO-5): Given Ben wrote off 50, deleted it, and then F's debt
  fell to 10 through a Settlement, when Ben restores the 50 Write-off, then it
  is rejected.
- **AC-WO-4** (WO-2): Given placeholder P is owed by Former member F, then only
  an Admin may write it off.
- **AC-WO-5** (WO-6): Given Ana owes Former member F 40, then a Write-off of
  that debt is rejected.

## Out of scope

- None.

## Open items

- None. WO-OI-1 was resolved by WO-7 (P050).
