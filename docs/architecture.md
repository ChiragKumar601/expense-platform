# Splitsy Architecture

This document describes how Splitsy is built to satisfy the approved product
intent (`intent/intent.md`), domain model (`docs/domain.md`) and glossary
(`docs/glossary.md`). It contains no endpoint designs, database schemas or
component designs. Those belong to specifications and implementation plans.

**Status:** **Approved architecture baseline** (P042), incorporating the
AT-1 decision. Written after architecture discovery (development-log Prompts
020–042). The intent, domain model and glossary are frozen and were not
changed. Where this document refers to a domain rule, the domain document is
authoritative. Decisions approved after the domain was frozen are listed in
§13.

## How to read this document

Each decision carries a status tag:

| Tag | Meaning |
|---|---|
| **[APPROVED]** | Explicitly approved architecture decision. Log references in brackets, e.g. (P039). |
| **[PROVISIONAL]** | Relies on something not yet authoritative. Must not be treated as final. |
| **[OPEN-ARCH]** | Architecture decision still to make. |
| **[OPEN-SPEC]** | Product or specification question. Architecture must not invent an answer. |
| **[RESEARCH]** | Investigation workstream. Its findings are **not** product or domain rules. |

"P0NN" means Prompt 0NN in `prompts/development-log.md`.

---

## 1. Architectural drivers

These come from the frozen intent and domain. They are not choices.

1. **Financial exactness:** single-currency groups; exact, deterministic,
   locked results; every debt explained by ledger records (domain §5.2, §7).
2. **Historical reproducibility:** immutable Versions; an append-only audit
   trail; anonymization is the only rewrite of history (domain §10).
3. **Changes processed at the moment they're recorded:** the invariants that
   must hold when changes happen concurrently (domain §12).
4. **Settlement semantics:** automatic routing for every Settlement (A6);
   maximal routing with shorter chains preferred (Q-C, P021); the one-set,
   any-order Suggested-settlement guarantee (domain §7.14, FR-2, P022).
5. **Authorization:** only Active registered members act. Some rules can be
   checked only after calculating (A2 / IMP-2).
6. **Privacy:** GDPR and DPDP; anonymization that is final; erasure.
7. **Non-functional:** 99.95% availability; no committed financial record
   lost; 100k+ users in year one; groups of 50+ members; WCAG 2.2 AA;
   online only; responsive web.

---

## 2. System context

[APPROVED] (P034, P035, P039)

```text
            ┌──────────────────────── Splitsy ────────────────────────┐
 Browser ──▶│  Web client (React)  ──HTTP/JSON──▶  Application core     │
            │                                     (Express, one app)    │
            │                                     │  transactional DB   │
            │  Background workers (same codebase) ┘  (outbox, jobs)     │
            └───────┬──────────────┬─────────────┬──────────────┬───────┘
                    ▼              ▼             ▼              ▼
             Identity provider  Object storage  OCR provider  Notification
             (sign-in)          (receipts)      (advisory)    delivery
```

- Splitsy never moves money. There are no payment integrations (intent §5).
- Every outside provider sits behind an internal interface (P034).
- No specific cloud or vendor has been chosen (SQ5, SQ7; P039).

---

## 3. Technology stack

[APPROVED] (P039, P041)

| Concern | Decision |
|---|---|
| Language | TypeScript everywhere: web client, backend, workers, shared domain types, calculation core |
| Backend | Node.js + Express |
| Web client | React + TypeScript; responsive single-page app; WCAG 2.2 AA tooling |
| Production database | **Managed PostgreSQL.** SQLite cannot meet production durability and availability requirements (P040). |
| Local development and most tests | SQLite through `better-sqlite3`. No local PostgreSQL required (AR1). |
| Persistence tooling | Hand-written SQL behind thin typed persistence interfaces. No ORM or query builder unless a later concrete need justifies one (AR2). |
| Outbox and jobs | Database-backed, behind an internal interface. No message broker unless later justified (SQ6). |
| API | HTTP/JSON, built around commands, with idempotency keys and server dry runs. Endpoint design deferred. |
| Runtime pinning | The Node.js version is pinned across development, CI and production, and the time-zone data / runtime version is recorded (AR5). |
| Cloud and region | Cloud-neutral until launch markets and residency are decided (SQ5). |
| Secrets | Never in the repository. |

---

## 4. Deployable shape and modules

[APPROVED] (P036, P037)

### 4.1 One application, plus workers

- **One application** with enforced internal module boundaries, plus
  **background worker processes from the same codebase** (BQ1).
- Everything that takes part in the group transaction (§6) lives in the
  **transactional core**, with one transactional store:
  - groups and membership;
  - Expenses, Settlements, Write-offs, Disputes and Drafts;
  - the debt journal and debt caches;
  - the audit trail;
  - stored command IDs;
  - the outbox.

  Splitting these apart would break atomicity.
- **Workers** only read committed state, and only write caches that are not
  the source of truth, or effects outside the system. Workers run:
  - outbox processing and notifications;
  - suggestion generation;
  - the scheduler (Drafts, reminders);
  - exports;
  - the erasure pipeline;
  - reconciliation and verification.

  Any worker may later become its own service without changing the core.

### 4.2 Logical modules (names illustrative)

| Module | Responsibility |
|---|---|
| Calculation core | Pure, versioned arithmetic and algorithms (§5). No I/O. |
| Groups and membership | Groups, Members, roles, invitations, claims, leaving and removal, archiving |
| Ledger | Expenses, Settlements, Write-offs, Disputes, Drafts and Versions; the debt journal |
| Policy | Central authorization and domain-rule evaluation (§6.6) |
| Audit | Append-only, hash-chained audit entries (§9.2) |
| Suggestions | Order-safe suggestion generation and caching (§7) |
| Recurring | Series, occurrence production (§10.1) |
| Notifications | Planning recipients and delivery (§10.2) |
| Identity and privacy | Identity records, anonymization, erasure pipeline, exports (§9) |
| Persistence | Repositories and unit of work; SQLite and PostgreSQL adapters (§8) |
| Integrations | Adapters for the identity provider, storage, OCR and notification delivery (§11) |

Shared domain types and the calculation core are packaged so they can be
used by the server, the workers and the client (SQ4).

---

## 5. Calculation core

### 5.1 Contract

[APPROVED] (P026 P10, P029)

- **Pure and deterministic.** No clock, storage, network, randomness or
  floating point. The current time and all state are passed in (SR3).
- **Snapshot in, effects out.** The core returns effects **per member pair**
  (not only totals). These are used for the journal, the audit before/after
  values and the A2 check (§6.6).
- **Versioned.** Every saved result is stamped with the calculation
  algorithm version (LC6).
- **Reusable** by the server, the workers and the client (for provisional
  figures only; §6.7).

Functions:

| Function | Inputs | Outputs |
|---|---|---|
| Shares | total, Participants, rotation order, rotation position | Shares, new rotation position |
| Obligations (M2) | Paid amounts, Shares, rotation order, rotation position | debtor→creditor Obligations, new rotation position |
| Pairwise debts | non-deleted, current journal effects | netted debt graph |
| Allocation | current Active-member debt graph (plus the direct pair), payer, recipient, amount | direct reduction, Chains with amounts, Overpayment |
| Suggestions | current Active-member debt graph | an order-safe suggestion set (§7) |

### 5.2 Money and arithmetic

[APPROVED] (P029 LC1, P041 AR3)

- Money is an integer number of the group currency's smallest unit, held as
  **`BigInt`** inside the system, under a dedicated money type. **JavaScript
  `number` is never used for money**; lint rules enforce this in money code.
- Intermediate M2 products use exact `BigInt` arithmetic.
- **At the API boundary, money is a decimal string of smallest units.**
- Database integer handling: `better-sqlite3` in safe-integers mode, and
  PostgreSQL BIGINT values mapped explicitly to `BigInt` (P040).
- A group's currency precision is **captured when the group is created** and
  kept, even if the currency's official smallest unit later changes (LQ4).

### 5.3 Step 1: Shares

[APPROVED] (domain §5.2; P029 LQ1)

- An equal split in the smallest unit. Leftover units go one at a time to
  Participants in rotation order, from the group's rotation position,
  skipping non-participants.
- **The rotation position then moves past the last member who received a
  leftover unit** (LQ1).
- Shares are re-allocated only when an edit changes the total or the
  Participants (domain §5.2.3).

### 5.4 Step 2: Obligations (M2, proportional to net credit)

[APPROVED] (domain §5.2; P021 Q-A/Q-B; P022 FR-1; P029 LQ2, LC2)

- Positions, Expense creditors and Expense debtors, and exact debtor and
  creditor totals, as in the domain (§5.2.4–7).
- **Q-B:** every debtor→creditor Obligation is within one smallest unit of
  its exact proportional value.
- **Placing leftover units (Q-A / FR-1):** the largest fractional remainder
  first. Rotation order breaks **only exact equal-remainder ties**.
- **Tie order (LQ2):** debtor rotation distance from the rotation position,
  then creditor rotation distance. **The rotation advances only when a
  tie-break actually decides where a unit goes.**
- **Canonical outcome (LC2):** the lexicographically first *feasible*
  placement in the order (remainder descending, then the LQ2 tie order),
  where "feasible" means every debtor and creditor total can still be met
  exactly. Any algorithm producing exactly this placement is acceptable.
- The results are locked into the Expense Version (domain §5.2.9).

### 5.5 Settlement allocation

[APPROVED] (domain §5.5; A6; P021 Q-C)

- Every Settlement is allocated automatically, whatever its origin:
  1. the direct debt first;
  2. then Chains through Active members, each Chain reducing all its links
     equally;
  3. any remainder is an Overpayment, after a warning.
- **Q-C:** the maximum possible amount is routed, shorter Chains are
  preferred, and there is no maximum chain length. Formally this is a
  **minimum-cost maximum-flow** problem with a cost of one per link. The
  resulting flow is broken down into Chains (P021). Shorter-first means the
  direct debt is used first automatically.
- The allocation is fixed when recorded. Routed settlements can't be edited.
  A restore re-applies the original allocation (domain §5.5).

**AT-1: tie-break between equally valid allocations.** [APPROVED]
(P042; a domain-level decision not yet reflected in the frozen
`docs/domain.md`, see §13)

1. Allocation is **deterministic**.
2. The primary rules stay primary: route as much as possible; prefer
   shorter Chains where the existing rules require it.
3. **Only** when several allocations remain equally valid under those rules
   does a **stable, deterministic ordering based on immutable member
   identifiers** decide between them.
4. The member-ID ordering never overrides "as much as possible" or
   shortest-chain, and **never affects the rounding rules**.
5. **The rotation pointer is never used for allocation.** Rotation is used
   only for the approved rounding tie-breaks (§5.3–5.4).
6. The allocation is **reproducible** for the same committed ledger state,
   algorithm version and command inputs.

Architecture realization:

- The member-ID ordering is implemented as an **exact total order** over
  candidate allocations, derived only from immutable member identifiers.
  For example, compare allocations lexicographically by their sequences of
  member IDs along Chains.
- A probabilistic tie-breaker (such as a hash-based cost perturbation, which
  is unique only with high probability) is **not** sufficient by itself. It
  may be used only if a final exact member-ID comparison guarantees the
  result.
- The exact construction is an implementation detail inside the calculation
  core, versioned with the algorithm version.

---

## 6. Consistency, concurrency and transactions

### 6.1 Unit of consistency

[APPROVED] (P026 P1, P027)

The **group ledger** is the unit of financial consistency. Rotation state,
the debt graph, membership and roles, the ledger version, uniqueness of open
Disputes, draft confirmation and the write-off limit are all consistent
within a group. Work spanning groups (account deletion, payment details) is
handled at User level (§6.5).

### 6.2 Two kinds of version

[APPROVED] (P026 P2)

| | Record version | Group ledger version |
|---|---|---|
| Source | Domain §5.8.7 | Architecture |
| Purpose | Detect edits based on an outdated record | Serializing changes, suggestion tokens, confirming previews |
| On conflict | **Reject and show the current version. Never retry silently.** | May be retried internally by recalculating |

### 6.3 Group locking

[APPROVED] (P027 AD-Q1)

- Every financial or domain-changing command takes **its group's lock**.
- The lock is held only for authoritative processing and commit. Slow derived
  work (suggestions, notifications, exports, OCR) stays outside.
- Locking mechanism by adapter (§8): PostgreSQL row lock on the group;
  SQLite's database-wide write lock (correct, but stricter).

### 6.4 Command pipeline: one command, one transaction, one group

[APPROVED] (P026 P4)

1. Load the group state and the actor's User status (§6.5). Resolve User →
   Member.
2. **Check before calculating** (§6.6).
3. **Calculate** (§5).
4. **Check after calculating** (§6.6): the A2 Former-member effect, the
   Write-off limit (Q-F), triggers for warnings.
5. Save the Version and its locked results, the journal entries, the debt
   cache updates, the rotation state, the ledger version, the audit entries,
   the command ID and the outbox events.
6. Commit.

- **Side effects go through the outbox** after commit, never inside the
  transaction (P5).
- **An upheld Dispute and the resulting settlement edit or deletion are one
  atomic command** (AD-Q4). Who may carry it out is [OPEN-SPEC] (IMP-4).

### 6.5 Operations spanning groups

[APPROVED] (P026 P6, P027 AD-Q3)

- **Account deletion:** a User-level *deleted* gate takes effect
  immediately, in its own transaction. Every group transaction reads it, so
  the person immediately stops being an acting member, and nobody can add
  them to records.
- Then, **per group, as a retry-safe step:** anonymize, mark as Former
  member, delete their Comments, pass on the admin role, auto-archive where
  needed (domain §4.2, §5.12). This runs within a bounded window.
  [OPEN-SPEC: the window length.]
- **Payment-details changes:** a User-level transaction plus outbox
  notifications. Visibility is evaluated when reading (§6.8).

### 6.6 Authorization and domain rules inside the transaction

[APPROVED] (P026 P8, P019 IMP-2)

- **One central, pure policy function:** (actor, command, state, calculation
  result) → allow, deny, or ask for confirmation. It is always evaluated
  inside the group-locked scope.
- **Check before calculating:** acting member status, role, creator rights
  while Active, archive state, the placeholder-party rule, Routed settlements
  not editable, record version.
- **Check after calculating:** **A2 / IMP-2.** Editing, deleting or
  restoring an existing record in a way that changes any Former member's
  debt, or who they owe, needs an Admin, and that person is notified.
  Recording a new Settlement to a Former member (A3) and a Write-off by the
  member who is owed (R7) are not restricted. Also checked after
  calculating: the Write-off limit (restoring or editing one is blocked if it
  would exceed the current debt: Q-F), and warning triggers.
- **Failure types:**

| Failure | Response |
|---|---|
| Not permitted | Deny |
| Domain rule violated | Reject, with an explanation |
| Record version outdated | Reject, and show the current version |
| Group contention | Retry internally |
| Warned outcome changed since the preview | Ask the user to confirm again (§6.7) |

### 6.7 Previews and confirmation

[APPROVED] (P036 CP1–CP3, P037 BQ2–BQ4)

- **The authoritative preview is a server dry run.** It runs the normal
  pipeline on committed state, without the lock and without committing. It
  never uses up rotation positions. It returns the outcome, the warnings, the
  authorization result, the ledger version and an **outcome fingerprint**.
- **The client** may show clearly marked provisional figures while a member
  types. **The confirmation screen always shows the dry-run figures.**
- **Commit** carries the command ID, the record version being edited and the
  confirmed fingerprint. The core recalculates under the lock. **The member
  confirms again only if one of these warned outcomes changed:**
  - the Overpayment amount;
  - routed vs direct;
  - whether the restore warning about a Former member's debt applies;
  - whether an Admin is required.

  A change in the exact Chains does not trigger re-confirmation.

### 6.8 Read consistency

[APPROVED] (P026 P12)

- Ledger reads come from committed state, and users always see their own
  writes.
- Visibility rules are evaluated **when reading**, against current
  membership:
  - Active registered members see the whole group;
  - Former members see only their own debts and the records behind them;
  - payment details follow the cross-group rule.

  Nothing is copied ahead of time.
- Derived views (suggestions, caches) show the ledger version they were
  computed from.

### 6.9 Idempotency

[APPROVED] (P026 P7, P039)

- **Every command carries an idempotency key generated by the client.** It
  is stored in the same transaction. A duplicate returns the original result,
  and internal retries reuse the key.
- **Domain uniqueness:** one Draft per (series, occurrence date); claims and
  invitations accepted at most once; at most one open Dispute.
- **Outbox and scheduler consumers are idempotent** (keyed by event, or by
  event plus recipient plus channel).
- Two members recording the same real-world payment is **not** idempotency
  [OPEN-SPEC: Q-L].

---

## 7. Suggested settlements

[APPROVED] (P022, P023, P024)

- **The guarantee (domain §7.14, FR-2):** if every suggestion in one
  generated set is recorded, in any order, every Pairwise debt between Active
  members becomes zero. This is a hard requirement. "Fewer payments" is a
  product **goal**, not a promise of the minimum (P024).
- **A suggestion is only a proposed settlement.** Recording it uses the
  normal allocation (§5.5). Its planned routing never overrides allocation.
- **Order-safe construction** (a sufficient condition found in the
  prototype):
  1. each suggestion's planned allocation equals the allocation function's
     result on the full graph at generation time;
  2. the planned allocations add up exactly to the graph;
  3. allocation has a fixed, unique preference.

  Recording a payment only ever reduces debts, so each plan is then
  reproduced exactly in any order. Debts left over may become direct-debt
  suggestions. That's a construction and UX strategy, **not** a domain
  invariant.
  The fixed, unique preference in condition 3 comes from **AT-1** (§5.5):
  "as much as possible", then shortest Chains, then the exact member-ID total
  order. A fixed total order keeps the chosen allocation optimal and unique
  when other suggestions reduce debts, because those reductions only remove
  alternatives.
- **Generation runs outside the recording path** in a worker, from a
  committed snapshot, and is cached by (group, ledger version).
- **Suggestions carry a token for the set they belong to.** A suggestion
  stays valid only while every ledger change since the set was generated is
  a full recording of a different suggestion from the same set. Any other
  change, including a **partial payment**, makes the set stale (Q-E, P024).
  Recording a stale suggestion warns and shows the current suggestions; it is
  not blocked.
  Validation always happens in the core, under the group lock.
- Cycles are handled only through ordinary Settlements. There is no
  cycle-specific operation (P021, P022).

---

## 8. Persistence

[APPROVED] (P039, P040, P041 AR1–AR2)

- **Thin typed repository and unit-of-work interfaces** in the application
  layer, all async. The calculation core and domain types **never depend on
  persistence**.
- **Adapters per dialect**, each with its own hand-written SQL and
  migrations. Database-specific SQL stays inside the adapters.
- **One shared contract test suite** runs against both adapters.

| Concern | PostgreSQL (production) | SQLite / `better-sqlite3` (development and tests) |
|---|---|---|
| Async unit of work | Native transactions | `db.transaction()` can't contain `await`. Use an **in-process mutex plus `BEGIN IMMEDIATE`** so concurrent requests never share an open transaction. |
| Group lock | Row lock on the group (`SELECT … FOR UPDATE`) | Database-wide write lock |
| Claiming jobs | `FOR UPDATE SKIP LOCKED` | Claim inside an immediate transaction |
| Append-only tables | Triggers, plus revoked UPDATE/DELETE privileges | Triggers |
| Isolation | READ COMMITTED by default, so explicit locks are required | Effectively serializable |
| Integers | BIGINT, mapped to `BigInt` | Safe-integers mode, `BigInt` |
| Timestamps | UTC epoch integers | UTC epoch integers |
| JSON payloads (for example audit values) | `jsonb` | text |

**Test tiers** [APPROVED] (AR1):

- SQLite for local development and most unit and integration tests.
- A **CI-only PostgreSQL suite** for behaviour that matters in production:
  per-group locking, transaction isolation, concurrent commands, job claiming
  and multi-group operations.
- PostgreSQL is **not** a local development prerequisite.

---

## 9. Ledger representation, history, privacy and audit

### 9.1 Ledger and history

[APPROVED] (P029 LC3–LC7)

- **Immutable Versions** for Expenses, Settlements, Write-offs and Drafts.
  Each stores its locked results and the calculation algorithm version.
- **Append-only debt journal:** entries of debtor, creditor, amount, source
  Version and kind (Obligation, direct allocation, chain link, Overpayment,
  Write-off).
  - **Delete** posts reversing entries.
  - **Restore** re-posts the original entries, so a Routed settlement's
    original allocation is restored exactly.
  - A **Trace** is the journal entries for one member pair.
- **Pairwise debt totals are a cache**, updated in the same transaction and
  always rebuildable from the journal. They are never the source of truth
  (domain §2).
- **Rotation state** is stored as the rotation order plus a pointer to the
  next member.
- **Algorithm versions:** history is never recomputed. If a calculation bug
  is found, historical results stay unchanged, the old algorithm and results
  are kept for verification, and fixing happens forward through ordinary
  edits and new Versions. No system-generated correcting Versions without
  explicitly reopening the domain (LQ3).

### 9.2 Identity separation, audit and erasure

[APPROVED] (P030, P031)

- **Identity kept separate from records (PH1):**
  - Records, journal entries, audit entries, events and caches hold only
    opaque Member and User IDs.
  - Names, contact details and Payment details live in separate identity
    records, looked up when reading or sending.
  - **Anonymization replaces the identity record and sets the stable
    Anonymous label. No ledger, journal or audit row is rewritten.**
  - By default, a claimed placeholder's history shows the claiming User's
    current identity (PQ1). [OPEN-SPEC: the exact display behaviour.]
- **Audit trail (PH2, PQ2):**
  - written in the same transaction; append-only; IDs only;
  - each entry records actor, time, entity and Version, action,
    before/after values, command ID and algorithm version;
  - a **per-group hash chain** makes tampering detectable even with
    database-level access; identities stay outside the hashed data;
  - visibility for each viewer comes from §6.8;
  - retention is configurable [OPEN-SPEC: the period; intent §11.7].
- **Erasure pipeline (PH3):**
  - A required **inventory of where personal data lives**.
  - Account deletion in stages: the User-level gate (sign-in disabled,
    Payment details deleted immediately) → per-group anonymization →
    deletion at outside providers → an **erasure tombstone** re-applied
    after any backup restore → a completion record holding no personal
    data.
  - Backup retention is limited (PQ3). Per-User encryption keys may come
    later for especially sensitive fields; they are not the primary
    mechanism.
  - Logs and events carry IDs only, with short retention.
- **Exports (PQ4):** generated on demand, delivered through short-lived
  secure download access, and not kept beyond delivery. Personal data
  exports are built from the same data inventory.
- **Accepted risks** (decided earlier, not reopened): kept expense
  descriptions and receipt images may still identify a person.

---

## 10. Recurring expenses and notifications

### 10.1 Recurring Drafts

[APPROVED] (P032, P033)

- Occurrences are a deterministic sequence of dates. **A Draft is
  identified by (series, occurrence date)**, which is unique, so producing
  it is idempotent.
- A periodic sweep with a cursor per series; producing a Draft is a System
  command through the standard pipeline (§6.4).
- **Drafts are produced on the occurrence date, never ahead of it** (NQ4).
- **Unarchiving produces Drafts for occurrences that fell due while the group
  was archived.** The unique identity prevents duplicates (NQ2).
- A Draft copies the series' payers and participants when it is produced,
  and has Versions. **Confirming it is a normal Expense-recording command**
  (the rotation is used at commit; it is confirmed at most once).
- **Time zone:** occurrence dates and the "Expense date not in the future"
  check use a **time zone configured per group** (NQ1).
  [PROVISIONAL: FR-3. Who sets the group time zone, whether admins can
  change it, auditing, and the effect on future occurrences only, are a
  **proposed** domain decision. It is **not approved**. See §12.]
- [OPEN-SPEC] Schedules and frequencies, series states, how series edits
  affect existing Drafts.

### 10.2 Notifications

[APPROVED] (P032, P033)

- **The pipeline runs:** outbox events → notification planner → delivery per
  channel → notification history and in-app inbox.
- **Recipients** are decided from committed state at the event's ledger
  version, using the domain and visibility rules. Placeholders and deleted
  Users never receive notifications. Content is shown with current
  identities when sent or read.
- **Delivery** is at-least-once, de-duplicated by (event, recipient,
  channel), with retries, backoff and a queue for failed deliveries. It never
  blocks the ledger.
- **Notifications the domain requires** are always delivered at least
  in-app, whatever the preferences: Former-member debt changes,
  routed-settlement changes for Intermediate members, and payment-details
  changes (NQ3). Optional channels follow preferences.
- **Settle-up reminders** come from a scheduled evaluator over the debt
  caches, idempotent per (member, reminder period).
- [OPEN-SPEC] Channels, preferences, the digest policy, reminder triggers
  and frequency.

---

## 11. Outside providers

[APPROVED] (P034, P035)

- **Every provider:**
  - sits behind an internal interface;
  - integrates through the outbox when asynchronous;
  - is covered by a data processing agreement;
  - meets data residency;
  - is listed in the erasure inventory.

  **No vendor has been chosen** (SQ7).
- **Sign-in:** a managed identity provider, never built in-house (OQ3). It
  maps the provider's account ID to an opaque User ID and keeps minimal
  attributes. Deletion at the provider is part of erasure.
- **Receipts:** private object storage, short-lived signed links checked
  against the visibility rules, malware scanning. Uploads not yet attached to
  an Expense expire. **Files are kept while the Expense exists, including
  receipts referenced by historical Versions or audit history**. Account
  deletion doesn't remove receipts (OQ2).
- **OCR:** advisory only, asynchronous, pre-fills the form, always confirmed
  by a member. It never writes to the ledger and never blocks recording
  (OQ4).
- **Reference data:** a versioned ISO 4217 snapshot, and time-zone data tied
  to the pinned runtime (AR5).
- **Analytics:** internal IDs only; consent where required.
  [OPEN-SPEC: metric definitions.]

---

## 12. Operations

[APPROVED] (P034, P035)

- **Availability 99.95%:**
  - redundancy;
  - deployments and schema changes without downtime;
  - an error-budget policy.

  In degraded operation, **financial recording stays correct**; suggestions
  and notifications may lag.
- **Durability:**
  - **zero loss of committed financial records**;
  - recovery within minutes;
  - point-in-time recovery and a synchronous standby (managed PostgreSQL);
  - restore drills that include re-applying erasure tombstones.

  [OPEN-SPEC: detailed recovery objectives.]
- **Continuous verification:**
  - the debt caches equal the journal sums;
  - each Version's journal entries equal its locked results;
  - net balances in each group add up to zero;
  - the audit hash chains verify.

  A mismatch raises an incident, and the caches are rebuilt from the
  journal. **Nothing is ever repaired silently.**
- **Monitoring:** logs, metrics and traces carry IDs only, never personal
  data; short retention.
- **Releasing calculation algorithm versions:** deliberate. Old versions are
  kept for verification. Migrations never rewrite locked results.
- **Security:**
  - encryption in transit and at rest;
  - extra protection for Payment details;
  - **operator access to production data is limited, time-bound and audited
    separately** from the group's domain audit trail (OQ5);
  - a breach-notification process (GDPR, DPDP).
- **Capacity:** tested against a **provisional engineering ceiling of 100
  members per group**. This is not a product limit (AD-Q5). Suggestion
  generation has a background compute budget.

---

## 13. Decisions relative to the frozen domain documents

### 13.1 Approved, not yet reflected in the frozen documents

These are approved and govern design. They will be incorporated when the
domain documents are next explicitly reopened.

| ID | Decision | Source |
|---|---|---|
| **AT-1** | Allocation tie-break: deterministic; the member-ID total order only between otherwise equally valid allocations; never the rotation pointer; reproducible (§5.5) | P042 |
| **FR-1** | "In rotation" wording for rounding is read precisely as Q-A: largest remainder first; rotation only for exact ties; advances only on tie-breaks (wording clarification, not a rule change) | P022 |
| **FR-2** | §7.14 is read as a one-set, any-order guarantee (wording clarification, not a rule change) | P022 |
| **S3** | Fallback Resolver may uphold an eligible Dispute; the Settlement is deleted atomically in that resolution command; no general edit/delete authority is granted | P043 |
| **S5** | A Settlement's payer and recipient must be different members | P043 |
| **S6** | An Expense total must give every Participant at least one smallest unit | P043 |
| **S1** | Clarifies how A1 applies: a Version with no financial change keeps the previous allocation and uses no rotation positions | P043 |
| **S12** | System-driven transitions: archiving revokes pending invitations; anonymizing a placeholder revokes its claim invitation; Disputes freeze while the group is archived; a Former-member disputer can't withdraw | P044 |
| **S13** | A rejoining member keeps their rotation position; a claimed placeholder keeps the placeholder's position; "longest-standing" = original join date | P044 |
| **S14 / S14b** | Placeholder display names are unique among active members, ignoring case; registered members' names needn't be unique; a claim is never blocked by a name | P044, P045 |
| **S17** | A Recurring series is Active or Stopped (final); no paused state; archiving suspends processing without changing the series' state | P045 |
| **S20** | A series stays Active when a payer or participant becomes a Former member; affected Drafts can't be confirmed until edited; Former members are never removed silently | P045 |
| **RE-24** | A Draft may be edited by the series creator (while Active) or an Admin, the same members who may confirm or discard it | P050 |

### 13.2 Provisional or open, needs separate explicit approval (domain or intent)

| ID | Item | Status |
|---|---|---|
| **FR-3** | Group time zone: set by the group creator at creation; admins may change it later; the change is audited as a group-setting change; it affects only occurrences that haven't happened yet. | **PROVISIONAL.** Proposed domain decision (P035), **not approved**. The frozen domain has no group time zone. §10.1 relies on it only provisionally. |
| **S34** | Placeholder erasure when no admin acts | **OPEN** (P047). Needs legal/privacy review. An operator action would change the domain's authorization model and would need domain reopening. |
| **S38** | Success-metric definitions (user, returning, analytics retention) | **Needs an intent amendment** (P047). Not authoritative until the intent is amended with approval. |
| **PR-15** | Minimum age 18, no parental-consent flow | **Provisional** (P050). Needs legal review and an intent amendment (it narrows intent §3). |

---

## 14. Open items

### 14.1 Open architecture items

None. AT-1 was decided in P042 (§5.5).

### 14.2 Open specification questions (not answered here)

- **IMP-1:** does a new Version that leaves the total, Payers, Paid amounts
  and Participants unchanged keep the previous Version's Shares and
  Obligations?
- **IMP-3:** can an edit turn a Direct settlement into a Routed one?
- **IMP-4:** who carries out an upheld Dispute when the fallback Resolver
  lacks edit rights?
- **Q-L:** detecting duplicate real-world settlements.
- **Q-M:** recovery objectives, data residency and launch markets, audit
  retention.
- **Membership:** names shown after a placeholder claim; who can revoke
  invitations, and what happens to pending items when the group is archived
  or a member removed; rotation position on rejoin or claim.
- **Recurring and notifications:** schedules and frequencies, series states,
  how series edits affect Drafts, reminder triggers and frequency, the
  digest policy, channels and preferences.
- **Amounts and currencies:** zero Shares; supported currencies; amount
  limits.
- **Privacy and operations:** the anonymization window; receipt size and
  type limits; OCR languages and accuracy; analytics definitions; the
  breach-notification procedure.
- **Smaller items:** N7 (payer ≠ recipient), N8 ("longest-standing" after a
  rejoin), N9 (paying a Former member whose payment details are hidden).
- **Intent §11:** monetization, native mobile, success-metric definitions.

### 14.3 Research workstreams (findings are not product or domain rules)

| ID | Workstream | State |
|---|---|---|
| **RS-1 (AR6)** | **A stronger order-safe suggestion generator** that needs fewer transfers while keeping Q-C, FR-2 and AT-1 | The prototype (P023) proved the order-safe construction sufficient but found little simplification on dense graphs (greedy: about 14% fewer than the number of debts at n=50; best coverage: about 45–55%, but slow). Continue: an efficient coverage generator, local search, exact benchmarks for small groups. **It must respect Q-C, FR-2 and AT-1, and must not introduce product behaviour just to reduce the number of settlements.** Not a blocker. |
| **RS-2** | Prototype finding: a fixed preference over stable IDs makes allocation optima unique and keeps them stable as debts shrink | **Superseded by AT-1** (P042), which approves a member-ID tie-break **only** between equally valid allocations. The prototype's hash perturbation is not an exact realization; the implementation must use an exact member-ID total order (§5.5). |

The throwaway prototype lives only in the session scratchpad and is not part
of the architecture or the repository.

---

## 15. Decision index

| Area | Decisions | Log |
|---|---|---|
| Financial rules for algorithms | Q-A, Q-B, Q-C, Q-D (no cycle offset), Q-E, Q-F; FR-1, FR-2 | P021, P022 |
| Suggestion strategy | Option (1); suggestions are proposals only | P024 |
| Consistency and transactions | P1–P12; AD-Q1 to AD-Q5 | P026, P027 |
| Ledger core | LC1–LC7; LQ1–LQ4 | P028, P029 |
| Privacy and history | PH1–PH3; PQ1–PQ4 | P030, P031 |
| Scheduling and notifications | SR1–SR5, NP1–NP8; NQ1–NQ4; FR-3 (provisional) | P032, P033, P035 |
| Providers and operations | OP1–OP6, OPS1–OPS7; OQ1–OQ5 | P034, P035 |
| Shape and previews | DB1–DB4, CP1–CP3; BQ1–BQ4 | P036, P037 |
| Stack | SQ1–SQ7; AR1–AR6 | P039, P040, P041 |
| Allocation tie-break and approval | AT-1; architecture approved as baseline | P042 |
