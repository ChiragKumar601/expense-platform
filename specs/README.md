# Splitsy Product Specifications

This folder holds the product specifications for the first release. This
README is organizational only. **It contains no behavioural rules.**

## Authority

| Document | Role |
|---|---|
| `intent/intent.md` | Product intent (frozen) |
| `docs/glossary.md` | Canonical terminology (frozen). Specs use these terms only. |
| `docs/domain.md` | Domain model (frozen) |
| `docs/architecture.md` | Approved architecture baseline. §13 lists decisions approved after the domain was frozen. |
| `specs/*.md` | Testable product behaviour, derived from the documents above and the decisions in `prompts/development-log.md` |
| `prompts/development-log.md` | Historical record. Not a source of requirements. |

If a spec conflicts with a frozen document, the frozen document wins, and the
conflict is raised for human review. Specs never resolve conflicts silently.

## Specification index

| File | Rule prefix | Owns |
|---|---|---|
| `00-cross-cutting.md` | XC | Command semantics, idempotency, previews and re-confirmation, version conflicts, ledger-record lifecycle (rights, versions, delete/restore, Former-member restriction), money format, limits, currencies, time zones |
| `01-groups-and-roles.md` | GR | Group creation and settings, archiving, admin roles, step-down, last-admin rule, admin succession |
| `02-membership-and-invitations.md` | MI | Invitations and their lifecycle, placeholders and their names, claims, joining, leaving, removal, rejoining, rotation order and longest-standing, what Former members can see |
| `03-expenses.md` | EX | Expense recording and validation, rounding and multi-payer split, edits, comments |
| `04-receipts-and-ocr.md` | RO | Receipts (limits, access, retention behaviour) and OCR |
| `05-recurring-expenses.md` | RE | Recurring series, Drafts, occurrences |
| `06-debts-and-suggestions.md` | DS | Pairwise debts, views, Trace, Suggested settlements |
| `07-settlements.md` | ST | Settlement recording, allocation, editing, duplicate warning |
| `08-disputes.md` | DP | Disputes |
| `09-write-offs.md` | WO | Write-offs |
| `10-notifications-and-reminders.md` | NT | Channels, recipients, preferences, digests, reminders, notification time zone, notification history |
| `11-payment-details.md` | PD | Payment details |
| `12-audit-and-history.md` | AU | Audit trail, history semantics, tamper detection, audit retention |
| `13-privacy-and-data-rights.md` | PR | Account deletion, anonymization workflow, placeholder erasure, exports, personal-data inventory |
| `14-analytics-and-success-metric.md` | AN | Success-metric definitions and analytics data **(PROVISIONAL)** |
| `15-operations-and-reliability.md` | OP | Availability, recovery, verification, operator access, logs, incident procedure |

## Conventions

1. **One owner per fact.** Each behavioural fact is stated in exactly one
   spec. Other specs refer to it by rule ID (for example "see EX-12"), and
   never restate it.
2. **Retention ownership.** A feature spec owns the retention *behaviour* of
   its feature. `13` owns the personal-data inventory and erasure mapping, and
   refers to the owning rule.
3. **No new requirements.** Anything not decided goes under the spec's *Open
   items*, never into a rule.
4. **Rules** are numbered (`PREFIX-n`) and testable.
5. **Acceptance criteria** use *Given / When / Then* and cite the rules they
   test.
6. **Sources:** every spec lists its sources: domain sections, decision IDs,
   and log prompts (`P0NN`).
7. **Status per file:**
   - *Draft* (pending cross-spec review);
   - *Reviewed, awaiting implementation readiness* (passed the review;
     approved subject to its listed open items);
   - *Approved*;
   - *Provisional* (depends on an unapproved decision).

   Each spec also has an *Implementation readiness* line, naming the open
   items that block implementing it.

## Dependency register

These items are **not authoritative**. A spec that depends on one marks every
affected rule with the tag shown, and that rule must not be treated as final
until the dependency is resolved.

| Tag | Item | Status | Affects |
|---|---|---|---|
| `[FR-3 PROVISIONAL]` | Group time zone: set by the group creator; admins may change it; audited; affects only future occurrences | **PROVISIONAL.** Needs explicit domain approval (P035). | 00, 01, 03, 05 |
| `[S34 OPEN]` | Placeholder erasure when no admin acts | **OPEN.** Needs legal/privacy review. An operator action would need domain reopening (P047). | 02, 13 |
| `[S38 OPEN]` | Success-metric definitions and analytics retention | **OPEN.** Needs an explicit intent amendment (P047). | 14 |
| `[Q-M OPEN]` | OCR languages (depend on launch markets) | **OPEN** (P047) | 04 |
| `[S41 OPEN]` | Legal obligations for breaches and incidents | **OPEN.** Needs legal review (P047). | 15 |
| `[PR-15 LEGAL + INTENT AMENDMENT]` | Minimum age 18, no parental-consent flow | **Provisional.** Recommendation approved (P050); needs legal review and an intent amendment. | 13 |
| `[LEGAL REVIEW]` | Contents of the personal data export (how other members' data appears) | Approved; needs legal review (P050) | 13 |

Approved decisions not yet reflected in the frozen documents (AT-1, FR-1,
FR-2, S1, S3, S5, S6, S12, S13, S14, S17, S20, RE-24) are listed in
`docs/architecture.md` §13.1. Specs cite them by ID.

## Spec template

```text
# <Title>
Status: Draft | Reviewed | Approved | Provisional
## Scope            what this spec owns
## Boundaries       what it leaves to other specs (with rule references)
## Sources          domain sections, decision IDs, log prompts
## Dependencies     dependency tags, or "None"
## Rules            PREFIX-n, numbered and testable
## Acceptance criteria   Given / When / Then, citing rules
## Out of scope
## Open items       unresolved questions; never answered by invention
```
