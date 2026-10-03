# 15 — Operations and reliability

Status: Approved (P052). Blocked rules stay blocked until their dependency is resolved.
Implementation readiness: ready, except OP-11, which is blocked by S41

## Scope

- Availability.
- Recovery objectives, backups and restore drills.
- Continuous verification.
- Operator access and its logs.
- Application logs.
- The incident and breach procedure.

## Boundaries

- Architecture mechanisms: `docs/architecture.md` §12.
- Audit trail retention: `12` (AU-10).
- Personal-data inventory: `13` (PR-14).

## Sources

- Intent §9.
- Decisions:
  - OQ1, OQ5 (P035);
  - OPS1–OPS7 (P034);
  - PQ3 (P031);
  - LQ3 (P029);
  - S39, S40, S41 (P047).

## Dependencies

- `[S41 OPEN]`: OP-11.

## Rules

### Availability and recovery

- **OP-1** **Availability target:** 99.95%. This target is separate from the
  recovery-time objective (OP-2).
- **OP-2** **Recovery-time objective (RTO):** service restored within **15
  minutes**.
- **OP-3** **Recovery-point objective (RPO):** **zero** loss of committed
  financial records. Point-in-time recovery covers **35 days**.
- **OP-4** **Restore drills** run quarterly. Each includes re-applying erasure
  (PR-7).
- **OP-5** After a restore, derived data (debt totals, suggestions) is rebuilt
  from the ledger records. It is never restored as authoritative.

### Verification

- **OP-6** **Continuous verification** checks that:
  - debt totals match the ledger records;
  - each Version's effects match its locked results;
  - Net balances in each group sum to zero;
  - the audit trail is intact (AU-9).

  A mismatch raises an incident. Nothing is ever repaired silently.
- **OP-7** **Degraded operation:** if suggestions or notifications are
  delayed, recorded financial data stays correct and authoritative.

### Access and logs

- **OP-8** **Operator access** to production data is limited, time-bound, and
  recorded in an operator-access log separate from groups' audit trails. The
  log is kept **1 year**.
- **OP-9** **Application logs** contain internal IDs only, never personal
  data, and are kept **30 days**.
- **OP-10** A new calculation algorithm version never changes the recorded
  results of earlier Versions (AU-6).

- **OP-12** **Operator access procedure:**
  - just-in-time access, requested with a reason and an incident or ticket
    reference;
  - approved by a second authorized person;
  - lasts at most **4 hours**;
  - **read-only** by default;
  - write access only to fix an incident, with a separate approval;
  - every access recorded in the operator-access log (OP-8).

### Incidents

- **OP-11** `[S41 OPEN]` **Incident and breach procedure:**
  1. detection, severity classification and containment;
  2. preserving evidence;
  3. notifying regulators within legally required deadlines;
  4. notifying affected users where legally required, without undue delay
     where appropriate;
  5. post-incident review and corrective actions.

  Regulatory deadlines, authorities, notification thresholds and
  user-notification requirements are **legal-review dependencies**, not
  product guarantees.

## Acceptance criteria

- **AC-OP-1** (OP-3): Given a committed Expense, when the primary database
  fails, then the Expense exists after recovery.
- **AC-OP-2** (OP-5): Given a restore, then debt totals are rebuilt, and match
  the ledger records.
- **AC-OP-3** (OP-6): Given a debt total altered so it no longer matches the
  ledger, then verification raises an incident, and the total isn't silently
  corrected.
- **AC-OP-4** (OP-8): Given an operator accesses production data, then the
  access is time-bound and appears in the operator-access log, not in any
  group's audit trail.
- **AC-OP-5** (OP-9): Given application logs, then they contain no names,
  email addresses or Payment details.

## Out of scope

- Choice of cloud and region (deferred to deployment planning).

## Open items

- S41 (OP-11) is tracked in the README register.
- OP-OI-1 was resolved by OP-12 (P050).
