We are maintaining a persistent, repository-based development history for
this project.

The ONLY persistent source for the historical record of our AI-assisted
development process is:

prompts/development-log.md

Do NOT rely on the Claude conversation history as the persistent record.

The conversation is temporary context. The development-log.md file is the
persistent history that must survive across sessions, context limits, and
future development work.

From this point forward, maintain prompts/development-log.md throughout the
entire project.

## Core rule

Before performing any significant development task:

1. Read prompts/development-log.md.
2. Use it to understand the historical development context.
3. Continue the existing chronological record.
4. Do not assume that information exists merely because it appeared in an
   earlier conversation.
5. If an important historical decision is not present in the log, treat it
   as unknown rather than inventing it.

After every significant development interaction:

1. Determine whether it meets the logging criteria below.
2. If it does, append a new entry to prompts/development-log.md.
3. Preserve all previous entries.
4. Use the next sequential prompt number.
5. Record the important prompt/instruction.
6. Record decisions and reasoning that materially affected the project.
7. Record artifacts created, modified, reviewed, or deleted.
8. Record important corrections or rejected approaches.
9. Record the outcome.

Do this automatically.

I should NOT have to remind you to update the development log.

## What must be recorded

Create a log entry when an interaction:

- establishes or changes a product requirement
- establishes or changes a domain rule
- resolves an important ambiguity
- makes an architectural decision
- changes an important technical direction
- creates or changes a specification
- creates or changes a ticket
- creates or changes an implementation plan
- implements a significant feature
- discovers or fixes an important bug
- performs significant verification
- performs security review
- performs code review
- changes the development workflow
- changes CLAUDE.md
- changes a skill
- changes a hook
- changes an agent
- changes an important project convention

Do NOT record every trivial conversational message.

The goal is to maintain a useful development history, not a raw transcript.

## Historical reconstruction

When the current conversation contains important historical information
that is not yet present in prompts/development-log.md, add it if and only
if it can be reliably reconstructed from the available context.

Never fabricate:

- prompts
- decisions
- requirements
- reasoning
- dates
- outcomes

If something cannot be reliably reconstructed, leave it out.

## Entry format

Every entry must use:

## Prompt NNN — <short descriptive title>

### Phase

<SDLC phase>

### Purpose

<why this interaction occurred>

### Prompt

<important user/AI instruction, preserved as closely as practical>

### Decisions

<important decisions resulting from this interaction>

### Artifacts

<files created, modified, reviewed, or deleted>

### Outcome

<result of the interaction>

### Corrections

<important corrections, rejected approaches, or changed assumptions>

## Important distinction

prompts/development-log.md is the historical record.

It is NOT the authoritative source of current requirements.

The authoritative documents remain:

- intent/intent.md
- docs/glossary.md
- docs/domain.md
- docs/architecture.md
- specs/
- tickets/
- plans/

If a historical entry conflicts with a current authoritative document,
the current authoritative document determines the current product behavior,
while the historical log preserves what happened previously.

## No secrets

Never record:

- passwords
- API keys
- access tokens
- private keys
- credentials
- secrets
- sensitive personal information

Redact such information if it appears in a prompt or output.

## Continuity requirement

At the beginning of every major SDLC phase, read the development log.

At the beginning of every significant task, read the relevant recent
development-log entries.

Before completing a significant task, verify that the development log
contains the important interaction.

At the end of a major phase, ensure the phase's significant development
history has been recorded.

The development log must remain understandable to an engineer who joins
the project later and has never seen the original Claude conversation.

## Current task

First:

1. Read prompts/development-log.md.
2. Review the conversation context currently available to you.
3. Identify significant historical interactions that can be reliably
   reconstructed.
4. Append any missing entries.
5. Do not fabricate anything.
6. Do not begin domain discovery yet.
7. Show me exactly what you added.

---

# Log

## Prompt 001 — Skeptical product-owner review of product intent

### Phase

Product intent / requirements review

### Purpose

Stress-test `intent/intent.md` before any further work. At the start of the
session, `intent/intent.md` already existed as an untracked Draft. How it was
produced is not recorded here. `CLAUDE.md` and `README.md` were empty at that
point, so there was no source discussion to check the intent against.

### Prompt

```text
Review intent/intent.md as a skeptical product owner.

Do NOT modify it.

Check for:

- invented requirements
- ambiguous requirements
- contradictory requirements
- missing critical product decisions
- accidental implementation decisions
- scope creep
- unclear terminology
- requirements that cannot be tested or observed
- hidden assumptions
- security-sensitive omissions
- financial correctness risks

For every finding explain:
- the exact issue
- why it matters
- what decision is needed

Do not propose implementation yet.
```

### Decisions

None. This was a review only. Five findings were flagged as blockers to
decide before design work:

1. Force-removal redistribution contradicted "balances fully explained by
   expenses and settlements" and "no one unfairly burdened".
2. Balances with deleted accounts and never-claimed placeholders could never
   reach zero, which blocked others from leaving.
3. Settlement currency was undecided.
4. Placeholders put personal data into the system without the person's
   consent (GDPR/DPDP).
5. A settlement made from a simplified suggestion had no defined mapping onto
   pairwise debts.

### Artifacts

- `intent/intent.md`: reviewed, not modified.

### Outcome

The review was delivered in chat with 40 numbered findings, grouped as:
contradictions, financial correctness risks, security/compliance omissions,
missing product decisions, ambiguous terminology, untestable requirements,
invented or unsupported requirements, scope creep, accidental implementation
decisions, and hidden assumptions. Other notable findings that remained open:
recurring drafts' effect on balances, placeholder claim verification,
protection against payment-detail swaps, minors under DPDP, and audit trail vs
erasure. Entry reconstructed in Prompt 004.

### Corrections

None.

## Prompt 002 — Decide the five blocker findings

### Phase

Product intent / requirements refinement

### Purpose

Resolve the five blocker findings from Prompt 001.

### Prompt

```text
1. Force-removal: Removing a member should never change or redistribute
   already-recorded expenses or debts.
2. Deleted accounts: Keep their financial records intact and show the balance
   as owed to/from a deleted account until it is resolved.
3. Settlement currency: Use the currency selected when the group is created,
   with one currency per group for the first release.
4. Placeholders: Allow placeholders only for names without personal data, and
   require the person to claim/accept the account before adding personal
   information.
5. Simplified settlements: Every suggested settlement must map back to the
   underlying pairwise debts so the final payments fully reconcile the ledger.
```

### Decisions

The five decisions above, as stated by the user. The assistant's analysis of
their consequences included:

- Decisions 1 and 2 together imply one concept for a member who has left, been
  removed, or deleted their account and still has an open balance. It was
  proposed as "former member".
- "Resolved" for such balances is undefined. Write-offs don't exist in the
  intent yet.
- Account deletion cannot be conditioned on a zero balance, because GDPR/DPDP
  erasure can't depend on paying a debt.

### Artifacts

- `intent/intent.md`: not modified in this step.

### Outcome

The assistant asked for two confirmations before editing the intent:

1. Do expenses also become single-currency per group?
2. Should simplified settlements be (a) routed through intermediate members,
   or (b) direct only, between members who already owe each other?

Entry reconstructed in Prompt 004.

### Corrections

- Decision 4's phrase "without personal data" cannot be met literally, because
  under GDPR/DPDP a name linked to debts is still personal data. The assistant
  proposed this wording instead: a placeholder holds only a display name and
  its ledger entries, with no contact or payment details until claimed.

## Prompt 003 — Confirm single currency and routed settlements; apply to intent

### Phase

Product intent / requirements refinement

### Purpose

Answer the two confirmations from Prompt 002, and apply all decisions to the
intent document.

### Prompt

```text
Yes — expenses should also be single-currency per group. Every expense in a
group uses the group's currency; multi-currency support can come later.
Choose (a) Routed. Simplified settlements should be allowed to route through
members, but every routed payment must reconcile exactly with the underlying
pairwise debts.
```

### Decisions

- One currency per group in the first release. Every expense, settlement and
  balance uses it. Multi-currency within a group is deferred.
- Simplified settlements may be routed through members (A owes B, B owes C →
  A pays C). Each routed settlement reduces every pairwise debt in its chain
  by exactly the reconciling amounts. When every suggested payment has been
  recorded, every pairwise debt is zero.

### Artifacts

`intent/intent.md` was modified:

- "Base currency" renamed to "group currency" throughout.
- Exchange rates, rate locking and conversion removed from the first release.
  "Multiple currencies within a group" added to deferred scope.
- Force-removal no longer redistributes. Removed members keep their balance
  on record.
- Deleted accounts show open balances as owed to or from a deleted account
  until resolved.
- Placeholders hold a display name and ledger entries only. Personal
  information can be added only after the claim.
- Glossary terms added: "Former member" and "Routed settlement".
- Open questions on settlement currency, exchange rates and redistribution
  closed. New open questions added on former-member balance resolution and
  write-offs, leaving while a former member owes, removed-member visibility
  and re-adding, and routed-settlement partial allocation, disputes and
  notification of intermediate members. The list was renumbered 1–16.
- Risks updated: the exchange-rate and redistribution risks were removed.
  "Unresolvable balances" and "routed settlements" risks were added.
- Status left as Draft.

### Outcome

The user's reply confirmed both points, and the assistant applied the
decisions as it had offered in Prompt 002. Undecided consequences were
recorded as open questions rather than answered. The user then committed the
revised intent as `1bcd132` "docs: define product intent" (2026-10-03,
together with the then-empty `CLAUDE.md` and `README.md`). Entry reconstructed
in Prompt 004.

### Corrections

- Rejected: option (b), direct-only simplified settlements.
- Rejected: per-expense multi-currency for the first release (deferred).
- Rejected: redistributing a force-removed member's balance among the
  remaining members.

## Prompt 004 — Establish the development log and reconstruct history

### Phase

Development workflow

### Purpose

Make `prompts/development-log.md` the persistent historical record of
AI-assisted development, and bring it up to date before domain discovery.

### Prompt

```text
Before doing anything else: 1. Read CLAUDE.md. 2. Read intent/intent.md.
3. Read prompts/development-log.md. 4. Update prompts/development-log.md with
any significant missing interactions from the work completed so far, if they
can be reliably reconstructed. 5. Do not fabricate any historical information.
6. Do not begin domain discovery until the development log is up to date. The
development log is the persistent historical record for this project. Do not
rely on the conversation history as the project's persistent record. Once the
log is up to date, tell me that we are ready to move ahead Do not create any
new domain files yet. Do not write application code.
```

### Decisions

- `prompts/development-log.md` is the persistent historical record. It is not
  a source of requirements. The authoritative artifacts are listed in
  `CLAUDE.md`.
- Entries use the format and logging criteria defined at the top of this file.

### Artifacts

- `prompts/development-log.md`: created empty earlier in the session. Logging
  rules were added to it and to `CLAUDE.md` in commit `825080b` "Chat history
  config" (2026-10-03). This step appended Prompts 001–004.
- `CLAUDE.md`, `intent/intent.md`: read, not modified.

### Outcome

The log covers all significant interactions of the session that can be
reliably reconstructed. Trivial commands (viewing files, `git log`) were not
logged. Domain discovery has not begun. No domain files or application code
were created.

### Corrections

None.

## Prompt 005 — Begin Step 10: Domain Discovery

### Phase

Domain discovery

### Purpose

Establish a precise domain model before any architecture, specification or
implementation work.

### Prompt

```text
Begin Step 10 — Domain Discovery.

Read:
- CLAUDE.md
- intent/intent.md
- prompts/development-log.md

Use the AI Hero workflow where appropriate, especially:
/mattpocock-skills:grill-with-docs /mattpocock-skills:domain-modeling

The purpose of this phase is to establish a precise domain model before
architecture, specification, or implementation.

Do NOT: write application code, choose a technology stack, design database
tables, design API endpoints, design React components, design
infrastructure, create implementation tickets, create implementation plans,
invent product requirements.

The approved intent is the current product-intent source of truth.

Identify and discuss: actors, core domain entities, relationships, canonical
terminology, entity lifecycles, state transitions, business invariants,
financial invariants, authorization concepts, audit/history concepts,
important domain events, concurrency-sensitive business rules, financial
edge cases, ambiguous or unresolved product decisions.

Pay particular attention to: users, groups, group membership, invitations,
expenses, payers, participants, splitting, balances, settlements,
currencies, expense editing, expense deletion, member removal, historical
records, authorization, auditability.

Do not assume this product behaves like Splitwise or any other existing
expense-sharing application. If an important decision is missing from the
intent, ask me. Do not silently resolve important product ambiguities.

First use /mattpocock-skills:grill-with-docs to establish shared terminology
and identify uncertainties. Then use /mattpocock-skills:domain-modeling to
establish the domain model.

Do not create docs/glossary.md or docs/domain.md yet. We will create those
only after the important domain decisions have been discussed and settled.
Throughout this phase, maintain prompts/development-log.md according to the
persistent development-history rules.
```

### Decisions

- Discovery runs as grilling rounds. Each round asks the questions whose
  prerequisites are already settled, with a recommended answer for each.
- The domain-modeling skill's habit of writing a `CONTEXT.md` glossary inline
  is suspended. The user asked for no glossary or domain files until
  decisions are settled. Resolved terms are recorded in this log until then.

### Artifacts

- `CLAUDE.md`, `intent/intent.md`, `prompts/development-log.md`: read.
- `docs/decisions/` exists and is empty. Nothing was created there.

### Outcome

Round 1 of the grilling was posed: identity and membership, invitations and
claims, expense structure (payers, participants, amounts), the meaning of
balance, routed settlement structure, settlement recording rights, and
edit/delete/restore semantics. Answers pending.

### Corrections

- An earlier attempt at this step was stopped by the user ("stop") right after
  the skill was loaded. No work had been done and no files had been changed.

## Prompt 006 — Domain consistency review requested before decisions existed

### Phase

Domain discovery

### Purpose

The user asked for a domain consistency review before creating the domain
artifacts.

### Prompt

```text
We have finished the initial domain discussion.

Before creating the domain artifacts, perform a domain consistency review.

Read:
- intent/intent.md
- prompts/development-log.md
- all domain decisions established during this phase

Do NOT modify files yet.

Identify: contradictions with the product intent, missing domain decisions,
ambiguous terminology, conflicting definitions, invalid or incomplete
business invariants, financial edge cases, authorization gaps, lifecycle
gaps, historical-data problems, concurrency-sensitive business rules,
accidental scope expansion, decisions that actually belong to architecture
rather than domain.

For every finding provide: the exact issue, why it matters, the decision
required. Do not resolve these issues yourself. Ask me only about issues
that materially affect the domain model. Do not create docs/glossary.md or
docs/domain.md yet.
```

### Decisions

- The review was deferred. No domain decisions had been recorded: the Round 1
  questions from Prompt 005 were unanswered. Reviewing the assistant's own
  unconfirmed recommendations as if they were decisions would have
  fabricated product decisions.

### Artifacts

None. No files were modified during that request, as instructed.

### Outcome

The user chose to answer the Round 1 questions first (Prompt 007). The
consistency review will run after the remaining grilling rounds.

### Corrections

None.

## Prompt 007 — Domain discovery Round 1 answered

### Phase

Domain discovery

### Purpose

Settle identity, membership, invitations, expense structure, the meaning of
balance, routed settlement structure, settlement recording rights, and
edit/delete/restore semantics.

### Prompt

```text
Q1-Q8 agree, Q9-(c), Q10-agree
```

### Decisions

1. **User vs Member:** a User is a person's account across Splitsy. A Member
   is one person's participation in one group. A User can belong to many
   groups, with one Member per group. Balances, roles and history belong to
   the Member. There are no cross-group balances. A placeholder is a Member
   with no User.
2. **Membership lifecycle:** Member states are Active and Former, with
   reason *left*, *removed* or *account deleted*. A placeholder is an Active,
   unclaimed Member. A member who left or was removed and rejoins is the same
   Member, reactivated with continuous history. Any open balance reappears.
   A Member whose account was deleted cannot rejoin.
3. **Invitations:** an Invitation is a domain concept with the lifecycle
   pending → accepted / declined / revoked / expired. It is either for a new
   member or to claim a specific placeholder. A placeholder can be claimed
   only by accepting an admin-issued claim invitation, never by the person on
   their own. Expiry periods are undecided.
4. **Payers:** each payer has a paid amount greater than zero. Paid amounts
   add up exactly to the expense total. A payer need not be a participant.
5. **Participants:** at least one participant per expense. Placeholders can
   be payers and participants. Only Active members can be added to new or
   edited expenses. A member appears at most once as a participant.
6. **Amounts:** expense amounts are greater than zero. A refund is handled by
   editing the original expense. Negative expenses and a separate refund
   concept are excluded from the first release.
7. **Balance terms:** a *pairwise debt* is the net amount one member owes
   another, derived only from expenses and settlements, with debts between
   the same two members offsetting each other. A *net balance* is a member's
   total owed minus total owing in the group. A *suggested settlement* is what
   the simplified view proposes. To leave, all of a member's pairwise debts
   must be zero. A net balance of zero is not enough.
8. **Routed settlement:** a single Settlement (payer, recipient, amount) with
   a settlement allocation across the pairwise debts in its chain. The
   allocation is fixed when the settlement is recorded and is not changed by
   later expense edits.
9. **Recording settlements:** only the payer or the recipient, or an admin,
   can record a settlement. Any member can record one on behalf of a
   placeholder. This deliberately narrows the intent's "any member can record
   a settlement".
10. **Edit, delete, restore:** an edit creates a new version, and every field
    is editable. Balances reflect the current version and earlier versions
    stay in history. A delete is reversible: the record stops affecting
    balances but stays in history. Restore requires the same rights as
    delete (the creator or an admin). Settlements follow the same rules. A
    settlement's allocation is recalculated only when the settlement itself
    is edited.

### Artifacts

- `prompts/development-log.md`: this entry and Prompt 006.
- No domain files were created, as instructed.

### Outcome

Round 1 settled. Round 2 was posed: rounding rotation, disputes, recurring
drafts, audit scope and erasure, former-member balance resolution, admin
continuity, concurrency, routed partial payments, payment details, edits
involving former members, former creators' rights, group currency
immutability, group lifecycle, and expense date.

### Corrections

- Decision 9 narrows `intent/intent.md` §6 ("Any member can record a
  settlement"). The intent has not been updated yet. This is to be raised in
  the consistency review.

## Prompt 008 — Domain discovery Round 2 answered

### Phase

Domain discovery

### Purpose

Settle the questions that became answerable after Round 1: rounding,
disputes, recurring drafts, audit and erasure, former-member balances, admin
continuity, concurrency, partial routed payments, payment details, records
involving former members, group lifecycle and expense date.

### Prompt

```text
I agree with your recommendations. Are there any questions from your side?
```

### Decisions

1. **Rounding rotation:** the group keeps a fixed rotation order of members,
   by join order. Each leftover smallest unit goes to the next participant in
   that order, skipping non-participants, and the rotation advances. An edit
   that changes the amount or participants re-allocates using the current
   rotation position. Delete and restore don't touch the rotation. Fairness
   bound: no member is charged more than one leftover unit above any other
   member who took part in the same number of uneven splits.
2. **Disputes:** settlements only in the first release, with at most one open
   dispute per settlement. States: open → resolved (upheld: the settlement is
   edited or deleted; rejected: it stands) or withdrawn by the disputer. No
   time limit. The resolver is the settlement's creator or an admin, and
   cannot be the disputer.
3. **Routed settlements and disputes:** only the recipient can dispute.
   Intermediate members cannot. They are notified, and the change appears in
   their trace and in the audit trail.
4. **Recurring drafts:** a draft does not affect balances until confirmed.
   Drafts don't expire. The series creator or an admin confirms or discards
   each draft. A draft that includes a former member can't be confirmed until
   edited. If the series creator becomes a former member, only admins manage
   the series.
5. **Audit scope:** changes to expenses, settlements (including allocations)
   and disputes. Membership: joins, leaves, removals, claims and role
   changes. Recurring series changes, plus draft confirmations and discards.
   Group settings. The fact that payment details changed, without the
   values. The audit trail is append-only for everyone, admins included.
6. **Audit trail vs erasure:** on account deletion, the person's identity is
   replaced everywhere, history included, by a stable anonymous label.
   Amounts and dates are kept. Their payment details and comments are
   deleted. Receipts they uploaded stay as group records.
7. **Former-member balances:** a new ledger record, the **Write-off**, is
   introduced. Only the member who is owed can write off a debt owed to them
   by a former member, and the write-off is audited. A debt an active member
   owes to a former member closes only by a recorded settlement. A debt owed
   to a deleted account does not block the debtor from leaving.
8. **Admin continuity:** the last admin cannot leave, be removed, or demote
   themselves until another admin exists. If the last admin deletes their
   account, admin passes to the longest-standing active registered member. A
   group with no registered members left becomes read-only.
9. **Concurrency:** an edit or delete based on an outdated version is rejected
   and the person is shown the current version. Nothing is silently
   overwritten. Permission checks, the leaving rule and "Active members only"
   checks are applied at the moment a change is recorded.
10. **Partial routed payments:** allocation is deterministic. The payer's
    direct debt to the recipient is reduced first, then routed chains in a
    fixed order. Each chain reduces every link by the same amount. An
    overpayment becomes a direct debt owed by the recipient to the payer,
    after a warning.
11. **Payment details:** belong to the User. They are visible to Active
    members of groups where that User is an Active member, and hidden once
    the User is a former member. Changes notify the members of those groups.
12. **Records involving former members:** they can be edited and restored.
    Former members may stay on a record but cannot be added. Restoring a
    record that brings back debt with a former member shows a warning.
13. **Creator rights:** apply only while the creator is an Active member.
    After that, only admins can edit their records.
14. **Group currency and lifecycle:** the group currency is fixed at
    creation. An admin can archive a group, which makes it read-only, and
    the archive is reversible. Group deletion is not in the first release,
    except where required by erasure obligations.
15. **Expense date:** set by the user, may be past but not future, and
    separate from the recorded time. It affects display order and recurring
    occurrences only, not balances.

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

Round 2 settled. Round 3 was posed to cover the remaining frontier:
write-off details, archived-group behaviour, placeholder erasure, claiming by
an existing member, routing through former members, comment and receipt
rights, group export, and erasure of expense descriptions.

### Corrections

- Write-off (decision 7) is a new ledger concept that is not in
  `intent/intent.md`. Group archive (decision 14) partly resolves intent open
  question 3. Both are to be checked in the consistency review.

## Prompt 009 — Domain discovery Round 3 answered

### Phase

Domain discovery

### Purpose

Settle the last frontier questions that opened up after Round 2.

### Prompt

```text
I agree with your recommendations. Now, [the domain consistency review
request recorded in Prompt 006, repeated verbatim]
```

### Decisions

1. **Write-off details:** partial write-offs are allowed. Write-offs follow
   the edit/delete/restore rules of other records. When the member who is
   owed is a placeholder, only an admin can write off.
2. **Archived group:** nothing can be recorded, edited or disputed, and
   recurring drafts pause. Members can still view, export, and leave if their
   pairwise debts are zero. Only an admin can unarchive. A group with no
   registered members left is archived automatically. This is not a separate
   state.
3. **Placeholder erasure:** when the person asks, an admin can anonymize a
   placeholder. It gets the same treatment as account deletion, and the
   placeholder becomes a former member.
4. **Claim by an existing member:** a claim invitation cannot be accepted by a
   User who already has a membership in that group. Merging members is not
   supported in the first release.
5. **Routing:** simplified suggestions route only through Active members,
   placeholders included. Debts with former members appear only as pairwise
   debts.
6. **Comments and receipts:** authors edit or delete their own comments, and
   admins can delete any comment. Receipts follow the expense's edit rights.
   Adding or removing a receipt is audited. Comment edits are not.
7. **Group export:** any Active member can export. The export includes the
   ledger, audit history and comments, with identities as currently shown.
   Payment details are excluded. (Narrowed to Active registered members in
   Prompt 011.)
8. **Erasure and descriptions:** expense descriptions are kept on account
   deletion. Only identity fields are replaced.

Deferred to specification: invitation expiry periods, and minimum age under
DPDP.

### Artifacts

- `prompts/development-log.md`: this entry. It was logged after the review in
  Prompt 010, because that request said not to modify files.

### Outcome

The grilling frontier is empty.

### Corrections

None.

## Prompt 010 — Domain consistency review

### Phase

Domain discovery

### Purpose

Check all domain decisions (Prompts 002–003, 007–009) against the intent and
each other before creating the domain artifacts.

### Prompt

The request text is recorded verbatim in Prompt 006.

### Decisions

None. This was a review. Findings were grouped into 12 categories.
Material findings were put to the user as Q1–Q11:

- **Q1:** what former members can see.
- **Q2:** whether edits can change a former member's share.
- **Q3:** the simplified-view guarantee is false when former members hold
  debts, and "minimum transfers" doesn't match direct-first, active-only
  routing.
- **Q4:** how routed settlements are edited and restored.
- **Q5:** the rounding fairness bound (R1) is unproven.
- **Q6:** the creator of an expense confirmed from a draft.
- **Q7:** placeholders are "Active" but can't act; one placeholder-settlement
  path allows undisputable false records.
- **Q8:** groups with no admin, and disputes with no eligible resolver.
- **Q9:** the append-only audit trail vs anonymization.
- **Q10:** whether to update the intent before the domain docs.
- **Q11:** missing amount invariants.

The non-material findings were recorded for the glossary, domain doc or
specs:

- Terminology: "resolved", "balance", "settle up", the reason a member
  became former.
- Lifecycle edge cases: invitations, disputes and drafts during removal or
  archive.
- How names appear in history after a placeholder claim.
- Notification for intermediate members when expenses change after a routed
  settlement.
- Zero shares in big groups.
- Concurrency invariants K1–K6: allocation, rotation, draft confirmation,
  claim, last admin, and commit-time checks.
- Items that belong to architecture: the version-check mechanism, the stored
  rotation position, the routing algorithm, whether debts are stored or
  derived, and spec-level details.

### Artifacts

None modified during the review, as instructed.

### Outcome

The review was delivered in chat, and the user answered it (Prompt 011).

### Corrections

- The rounding fairness bound proposed by the assistant in Round 2 (R1) was
  flagged as an unproven claim.
- "Derived, not stored" (the assistant's Round 1 sketch) was flagged as an
  architecture decision, not a domain one.

## Prompt 011 — Consistency review decisions

### Phase

Domain discovery

### Purpose

Resolve the material findings of the consistency review.

### Prompt

```text
Q1 b · Q2 b · Q3 a + "simplified" instead of "minimum" · Q4 c + restore
original allocation · Q5 a · Q6 b · Q7 active registered member +
placeholder can only be acted for by other party/admin · Q8 a/b · Q9
anonymization-only exception · Q10 yes · Q11 all three invariants.
```

### Decisions

1. **Former-member visibility:** a former member can see only their own
   debts and the records behind them.
2. **Former members' shares:** only an admin can make an edit that changes a
   former member's share, and that person is notified.
3. **Simplified view guarantee:** when every suggested payment is recorded,
   every pairwise debt between Active members is zero. Debts involving
   former members are shown separately. "Minimum set of transfers" becomes
   "simplified set of transfers".
4. **Routed settlements:** they cannot be edited, only deleted and
   re-recorded. A restore re-applies the original allocation.
5. **Rounding:** the requirement is the deterministic rotation rule. "Fair
   over time" is its intent, not a measurable bound. The bound proposed in
   Round 2 (R1) is withdrawn.
6. **Confirmed drafts:** the member who confirms a draft is the creator of
   the resulting expense.
7. **Acting members:** authorization rules use "Active registered member".
   A settlement involving a placeholder can be recorded only by the other
   party or an admin. This refines Round 1 Q9(c).
8. **Groups without admins and stuck disputes:** answered "a/b". The
   assistant reads this as: (a) a group whose last registered member has gone
   stays permanently archived, with unclaimable placeholders; and (b) a
   dispute with no eligible resolver can be resolved by any other Active
   registered member. **This reading is pending the user's confirmation.**
9. **Audit trail:** anonymization is the only permitted exception to the
   append-only audit trail.
10. **Intent first:** `intent/intent.md` is to be updated before the domain
    docs are written.
11. **Amount invariants:** every settlement amount is > 0. A write-off cannot
    exceed the debt it writes off. A payer appears at most once on an
    expense.

### Artifacts

- `prompts/development-log.md`: Prompts 009–011.

### Outcome

Decisions recorded. Waiting for confirmation of the Q8 reading and for the
go-ahead on the intent update.

### Corrections

- Round 2 R1's fairness bound was withdrawn (decision 5).
- Round 1 Q9(c) ("any member on behalf of a placeholder") was replaced
  (decision 7).
- Round 2 R2 ("resolver is the creator or an admin") was extended (decision
  8, pending confirmation).
- Round 3 R3-7 was narrowed (export by Active registered members only).

## Prompt 012 — Q8 confirmed; domain artifacts created

### Phase

Domain discovery → domain artifacts

### Purpose

Confirm the pending Q8 reading and write the canonical glossary and domain
model.

### Prompt

```text
I confirm the Q8 and now I want you to,

Create the domain artifacts:
1. docs/glossary.md
2. docs/domain.md

Use:
- intent/intent.md
- the domain decisions established during this phase
- prompts/development-log.md as historical context only

docs/glossary.md must define the canonical business terminology.

docs/domain.md must define: actors, entities, relationships, business rules,
invariants, financial invariants, lifecycles, state transitions,
authorization concepts, audit/history concepts, important domain events.

Do NOT include: database schemas, API endpoints, React components, framework
choices, infrastructure implementation, deployment details.

These documents describe what the product means, not how it will be
implemented.

After creating them, update prompts/development-log.md with the significant
interaction and resulting decisions.

Do not begin architecture yet.
```

### Decisions

- **Q8 confirmed:**
  - A group whose last registered member has gone stays permanently
    archived, and its placeholders cannot be claimed.
  - A dispute with no eligible resolver can be resolved by any other Active
    registered member.
  - "A group always has at least one admin" becomes "every group with at
    least one Active registered member has at least one admin".
- **Terminology choices made while writing the glossary** (non-material
  findings T2–T4 from Prompt 010):
  - "Cleared" is used for a debt reaching zero. "Resolved" is reserved for
    disputes.
  - "Settled up" is the state where all of a member's pairwise debts are
    zero.
  - "Balance" is not a canonical term. Use "pairwise debt" or "net balance".
  - "Group currency" replaces "base currency".
- **Upheld disputes on routed settlements:** since routed settlements can't
  be edited, an upheld dispute on one means the settlement is deleted.
- **Discovered while writing, not decided:** how pairwise debts are
  attributed when an expense has several payers (how much each participant
  owes each payer). It is recorded as unresolved in `docs/domain.md` §14.2.
- Architecture-level items flagged in Prompt 010 (X1–X6) were kept out of
  the domain model. Concurrency rules are stated as rules, not mechanisms.

### Artifacts

- Created `docs/glossary.md`: canonical terms with _Avoid_ aliases, grouped
  into people and membership, groups, ledger records, debts and views,
  disputes, recurring expenses, and history and data.
- Created `docs/domain.md`:
  - actors, entities (stored records vs derived concepts) and relationships
  - business rules by area, business invariants and 15 financial invariants
  - lifecycles and state transitions, authorization table, and audit
    concepts
  - domain events, concurrency rules, and decided financial edge cases
  - open items: differences from the intent, unresolved domain questions,
    and items deferred to specification
- `intent/intent.md`: **not** updated. The Q10 decision (update the intent
  first) is still pending. The differences are listed in `docs/domain.md`
  §14.1.

### Outcome

The domain artifacts exist with Status Draft. Architecture has not begun.

### Corrections

- While editing, the draft invariant "only Active members are added to an
  Expense, Draft or Settlement" was corrected. Settlements and write-offs may
  involve Former members, so the invariant now covers only Payers and
  Participants of Expenses and Drafts.
- A draft ASCII member-state diagram was removed in favour of the transition
  table, because it was ambiguous.

## Prompt 013 — Multi-payer attribution decided (delegated to the assistant)

### Phase

Domain discovery

### Purpose

Resolve the unresolved question in `docs/domain.md` §14.2 item 1: when an
expense has several payers, how much does each participant owe each payer?

### Prompt

```text
Let's resolve the multi-payer domain question before changing any
authoritative documents. [...] When one expense has multiple payers, how is
each participant's obligation to the individual payers determined?
Example: A pays 60, B pays 40, total 100, participants A, B, C, D. [...]
Do NOT choose a rule yourself. Explain the viable domain models [...]. Ask me
to make the product decision. Once I make the decision, update
prompts/development-log.md with the decision, but do not modify intent.md or
docs/domain.md until I explicitly tell you to.
```

The user then answered: "It is open, you can do whatever you think here is
best". The decision was explicitly delegated to the assistant.

### Decisions

Four viable models were presented: M1 (proportional to paid amounts), M2
(proportional to net credit), M3 (ordered matching) and M4 (primary payer).
Restricting expenses to one payer, or showing only net balances, were
excluded because they contradict intent §5 and the pairwise view.

**Chosen: M2, proportional to net credit.** The assistant chose it under the
user's delegation.

1. For each Expense, each Member's **expense position** is their total Paid
   amount minus their Share. Members with a positive position are the
   expense's **creditors**. Members with a negative position are its
   **debtors**.
2. Each debtor owes each creditor in proportion to that creditor's share of
   the total credit. In the example, A is +35 and B is +15 (70% / 30%),
   giving: C owes A 17.50, C owes B 7.50, D owes A 17.50, D owes B 7.50.
   There is no debt between A and B.
3. A member who comes out ahead on an expense never owes anything for it.
   With a single payer, this reduces to "every other Participant owes the
   payer their Share".
4. **Second-step rounding:** the attribution must be exact both ways. Each
   debtor's obligations add up exactly to their negative position, and each
   creditor's receipts add up exactly to their positive position. Leftover
   smallest units are placed deterministically, with ties decided by
   Rotation order. The exact procedure belongs to architecture or
   specification. The domain fixes only the invariant and the tie-break
   principle.

**Rationale:**
- Like M1, it treats identical participants identically (unlike M3) and
  doesn't depend on picking a collector (unlike M4).
- Unlike M1, it creates no payer-to-payer debts, and it gives the fewest
  pairwise debts of the models that treat participants equally.
- Edits change debts gradually.
- Its explanation fits the "Balance clarity" differentiator: "this expense
  left A owed 35 and B owed 15, so your 25 is split 70/30".

**Rejected:**
- M1: it creates payer-to-payer debts (B owes A 5 in the example).
- M3: identical participants owe different people, depending only on order.
- M4: the result depends on choosing a collector, and a net creditor ends
  up owing money.

### Artifacts

- `prompts/development-log.md`: this entry only.
- `intent/intent.md` and `docs/domain.md`: not modified, as instructed.
  `docs/domain.md` §14.2 item 1 still shows the question as open until the
  user says to update it.

### Outcome

Multi-payer attribution is decided. Applying it to `docs/domain.md` (and
`docs/glossary.md`: expense position, creditor/debtor of an expense) is
waiting for the user's explicit instruction.

### Corrections

- The user's earlier instruction "Do NOT choose a rule yourself" was
  superseded by the explicit delegation.

## Prompt 014 — Domain consistency check after the M2 decision

### Phase

Domain discovery

### Purpose

Find every remaining unresolved decision that could materially affect the
ledger and the domain, sorted by when it must be resolved. Also verify the
M2 log entry against the current domain artifacts.

### Prompt

```text
Now that the multi-payer rule has been decided, perform another domain
consistency check. [...] The multi-payer rule has now been explicitly
decided: M2 — Proportional to net credit. For each expense:
1. Each member's expense position is: amount paid - amount owed as their
   share.
2. Positive positions are creditors of the expense.
3. Negative positions are debtors of the expense.
4. Each debtor's obligation is distributed among the creditors
   proportionally to each creditor's positive position.
5. The resulting pairwise obligations must be exact: every debtor's
   obligations sum exactly to that debtor's obligation; every creditor's
   receipts sum exactly to that creditor's credit.
6. Any fractional-cent allocation must be deterministic. Ties are resolved
   using the group's rotation order.
7. The exact implementation algorithm for fractional-cent allocation belongs
   to the architecture/design phase, not the product intent.
Do NOT modify any documents yet. [...] Separate findings into:
A. MUST RESOLVE BEFORE DOMAIN APPROVAL
B. CAN BE RESOLVED DURING SPECIFICATION
C. CAN BE DEFERRED TO ARCHITECTURE/IMPLEMENTATION [...]
Ask me only about Category A decisions. Also verify that the M2 decision
recorded in prompts/development-log.md is consistent with the current
domain artifacts, but do not synchronize those artifacts yet.
```

### Decisions

None. This was a review. Findings:

- **M2 consistency:** the log matches the user's restatement, except that the
  log says the fractional-cent procedure belongs to "architecture or
  specification", while the user says architecture/design. The user's
  wording governs.
- **Stale artifacts** (no contradictions):
  - `docs/domain.md` §14.2.1 still lists the question as open.
  - §5.2 lacks the second rounding step and the order: shares, then
    positions, then split.
  - §7 lacks the M2 invariants. §12.3 is silent on whether the second step
    uses up rotation positions.
  - `docs/glossary.md` lacks expense position and creditor/debtor of an
    expense, and its "Leftover unit" is defined for participants only.
  - `intent/intent.md` §6 Rounding is incomplete under M2.
- **Category A** (asked): A1 what the second rounding step depends on; A2
  changes to who a former member owes; A3 settlements to former members that
  they can't dispute; A4 the final state of an anonymized placeholder; A5
  admins removing admins; A6 automatic routing of every settlement vs only
  suggested ones. A6 noted that `docs/domain.md` §5.5.5 had generalized R10
  to all settlements without an explicit decision.
- **Category B** (specification):
  - rotation position on rejoin or claim
  - zero shares
  - names in history after a claim
  - invitation revocation, and pending items on removal or archive
  - recurring series states and edits
  - notifications to intermediate members, and channels
  - how M2 appears in the trace
  - the currency list and minor units
  - amount limits
  - audit retention
  - anonymous label format and verifying erasure requests
  - invitation expiry and minimum age
- **Category C** (architecture):
  - the fractional-cent method
  - routing and simplified-transfer methods
  - detecting outdated versions
  - applying allocations and rotation one at a time per group
  - stored vs computed debts
  - anonymization and audit storage
  - notification delivery

### Artifacts

None modified during the check, as instructed. Logged afterwards.

### Outcome

Six Category A questions were put to the user (Prompt 015).

### Corrections

- The assistant's generalization of R10 to all settlements in
  `docs/domain.md` §5.5.5 was flagged as possibly silently resolving a
  decision. A6 settled it.

## Prompt 015 — Category A decisions after the M2 consistency check

### Phase

Domain discovery

### Purpose

Resolve the Category A findings from Prompt 014.

### Prompt

```text
A1 — (ii) Group rotation position. The second rounding step should use the
group's rotation position at the time the expense version is recorded. The
resulting allocation is then locked into that expense version and does not
change if the group's rotation position advances later.

A2 — Yes. If an edit changes who a former member owes, that counts as
changing their debt even if the former member's total debt remains
unchanged. Therefore the edit is subject to the same admin-only restriction
as any other change to a former member's debt.

A3 — (i) Accepted as-is. An active member may record a settlement to a
former member. The settlement is treated as a valid financial event even
though the former member can no longer dispute it.

A4 — (ii) Final anonymized state. An anonymized placeholder is permanently
anonymized and cannot later be re-invited, claimed, or converted back into
an identifiable member.

A5 — (ii) No. An admin cannot remove another admin, including the group
creator. An admin may only remove eligible non-admin members. Admins must
relinquish their role through the explicitly defined admin-step-down
mechanism.

A6 — (i) All settlements route automatically. Every recorded settlement
first reduces the direct debt between the payer and recipient, then applies
the defined routing rules through eligible active members, and only the
remaining excess is treated as overpayment. This behavior does not depend on
whether the settlement originated from a suggested settlement.
```

### Decisions

1. **A1:** the second rounding step uses the group's rotation position at
   the moment the expense version is recorded. The resulting split is
   locked into that version, and later rotation advances never change it.
2. **A2:** an edit that changes who a former member owes counts as changing
   their debt, even if their total is unchanged. It is admin-only, and the
   former member is notified.
3. **A3:** an active member may record a settlement to a former member. It is
   a valid financial event even though the former member cannot dispute it.
4. **A4:** an anonymized placeholder is in a final anonymized state. It can
   never be re-invited, claimed, or made identifiable again.
5. **A5:** an admin cannot remove another admin, including the group
   creator. Admins can only remove eligible non-admin members. Admins give up
   the role through an explicit admin step-down mechanism.
6. **A6:** every settlement allocates automatically: direct debt first, then
   routing through eligible Active members, then overpayment for any
   remaining excess. This does not depend on whether the settlement came from
   a suggestion.

### Artifacts

- `prompts/development-log.md`: Prompts 014–015.
- `intent/intent.md`, `docs/domain.md`, `docs/glossary.md`: not modified.

### Outcome

Category A decisions recorded. A follow-up question arising from A5 was put
to the user (see below). Artifact synchronization is waiting for the user's
instruction.

### Corrections

- A5 refines the intent's "Admins can promote and demote other members". It
  is not yet settled whether an admin can still demote another admin (see the
  follow-up question).
- A consequence of A6, noted and not separately decided: any settlement
  larger than the direct debt between payer and recipient can touch chains.
  That makes it a Routed settlement, which cannot be edited (Prompt 011
  decision 4), and it changes intermediate members' debts.

## Prompt 016 — Admin demotion decided (delegated to the assistant)

### Phase

Domain discovery

### Purpose

Resolve the follow-up from A5 (Prompt 015): can an admin demote another
admin?

### Prompt

```text
Whatever you think will be the best, keep it
```

The decision was explicitly delegated to the assistant. The options were:
(a) admins can demote other admins; (b) an admin loses the role only by
stepping down, leaving or deleting their account; (c) demotion only under a
user-defined condition.

### Decisions

**Chosen: (b).** The assistant chose it under the user's delegation.

1. An admin cannot demote another admin. An admin loses the role only by
   **stepping down** themselves, by **leaving** the group, or by
   **deleting their account**. The last-admin rule applies throughout.
2. **Admin step-down** is the explicit mechanism A5 referred to. An admin
   gives up the role and remains an Active registered member. It is not
   allowed for the last admin.
3. Admins can still **promote** members to admin. The intent's "Admins can
   promote and demote other members" becomes "promote other members; step
   down themselves".

**Rationale:**
- It is the only option under which A5 ("an admin cannot remove another
  admin") can't be bypassed by demoting first and then removing.
- It matches the user's own wording in A5 ("relinquish their role through
  the explicitly defined admin-step-down mechanism").
- It needs no new concept, which option (c) would.

**Accepted consequence:** nobody can take the admin role from an admin who
misuses it. The append-only audit trail, and members' ability to leave once
Settled up, are the safeguards. This is to be added to the intent's risks
(§12, admin power) when the intent is updated.

**Rejected:**
- (a): it makes A5 ineffective.
- (c): it adds a new concept, and the group creator could be the one
  misusing the role.

### Artifacts

- `prompts/development-log.md`: this entry.
- `intent/intent.md`, `docs/domain.md`, `docs/glossary.md`: not modified.

### Outcome

All Category A decisions are resolved. The domain is ready for artifact
synchronization and approval, waiting for the user's instruction.

### Corrections

- This supersedes the intent's "Admins can promote and demote other
  members", and `docs/domain.md` §4.2.1 and §9 ("Promote or demote an
  Admin: Admin").

## Prompt 017 — Synchronize intent, glossary and domain model through Prompt 016

### Phase

Domain discovery → domain artifacts (synchronization)

### Purpose

Bring the three authoritative current-state documents in line with every
decision recorded through Prompt 016, then check them against each other.

### Prompt

```text
Now synchronize the authoritative domain artifacts with all decisions that
have been explicitly approved and recorded through Prompt 016. [...] Before
modifying anything, produce a synchronization plan [...] After showing the
synchronization plan, apply the changes to: intent/intent.md,
docs/domain.md, docs/glossary.md. Then perform a consistency check across all
three documents. [...] Do NOT introduce any new product or domain decisions.
[...] Do NOT commit anything. [...] Finally, update
prompts/development-log.md with this synchronization work and the resulting
consistency-check outcome. Do not begin architecture.
```

(The full list of decisions to incorporate and checks to verify was in the
prompt. It is summarized under Outcome below.)

### Decisions

No new product or domain decisions. The decisions promoted were:

- Prompts 002–003, 007–009 and 011.
- M2 (Prompt 013).
- A1–A6 (Prompt 015).
- Admin step-down (Prompt 016).

Kept in `docs/domain.md` only, not promoted into the intent:

- Payer and participant uniqueness and sum rules; amount bounds.
- The exact M2 invariants and the step-2 tie-break.
- Rotation-position mechanics.
- Allocation order details, and allocations fixed when a settlement is
  recorded.
- The rule that the member confirming a draft is its creator.
- Admin succession by longest-standing member.
- The state machines.
- Audit entry contents and domain events.
- The concurrency rules.
- Terminology choices ("cleared", "settled up").

### Artifacts

- `intent/intent.md` (synchronization plan items I1–I27):
  - Journeys 2–3 and 5–11 updated; new journeys 12 (write-off), 13 (archive)
    and 14 (admin step-down).
  - §5 scope: write-offs, archiving, placeholder anonymization, step-down
    and notification types added. New "Not in the first release" list:
    negative expenses and refunds, expense disputes, merging members, group
    deletion.
  - §6: the domain terms table aligned with the glossary, which it points to
    as canonical.
  - §6 rewritten:
    - roles (promote only; no demoting or removing admins; step-down;
      last-admin rule; admin invariant applies only while a registered
      member is active)
    - placeholders (claim invitation only; anonymization final)
    - expenses (M2 in product terms; two-step rounding locked per version)
    - drafts (inert, no expiry)
    - debts (simplified wording; active-only routing and guarantee)
    - settlements (who records; A3; automatic allocation under A6; routed
      settlements not editable; disputes)
    - new write-offs section
    - edits and history (A2; append-only, with anonymization the only
      exception)
    - membership, accounts, and new archived-groups section
  - §7 financial: write-offs added to "explained debts"; exact,
    deterministic, per-version split.
  - §9 auditability: scope widened (R5).
  - §11: answered questions removed (old 1, 3, 9, 11, 13–16), the rest
    renumbered 1–8.
  - §12 risks updated: admin power under Prompt 016, A3 trust, A6 routing,
    descriptions kept on erasure.
  - Status left as Draft.
- `docs/domain.md` (D1–D19):
  - Header status updated; old §14.1 (differences from the intent) removed.
  - Rotation position added to the Group; versions lock positions and
    Obligations.
  - §4.2 rewritten: promote; no demote or remove; step-down; last-admin rule.
  - Anonymization final (§4.3, §4.4).
  - Removal of non-admins only.
  - §5.2 rewritten into step 1 (shares) and step 2 (positions and M2
    Obligations, exactness, rotation tie-break at version recording, locking
    per version). It states explicitly that only the procedure for placing
    leftover units belongs to architecture.
  - §5.4: debts built from Obligations.
  - §5.5 restated under A6 (routed = defined by the allocation) and A3.
  - §5.8.4 includes who a former member owes (A2).
  - New invariants in §6 and §7 (positions balance, exact Obligations,
    creditors owe nothing, locked versions, deterministic rounding in both
    steps).
  - Role transitions added to §8.1.
  - §9 authorization table updated.
  - Audit (§10), events (AdminSteppedDown, FormerMemberDebtChanged) and
    concurrency (both rounding steps use rotation positions) updated.
  - §13 edge cases extended.
  - §14 restructured into specification and architecture lists.
- `docs/glossary.md` (G1–G9):
  - Former member (anonymized reason), Admin, Claim, Rejoin, Anonymization
    (final), Rotation order, Leftover unit (both steps), Version (locks),
    Direct and Routed settlement (defined by allocation), Settlement
    allocation, Pairwise debt: updated.
  - New terms: Admin step-down, Rotation position, Expense position, Expense
    creditor, Expense debtor, Obligation.

### Outcome

Consistency check across the three documents passed on every requested
point:

- No Category A decision is unresolved, and no stale open question remains
  for one.
- M2 is stated consistently: intent J3, intent §6 and §7, domain §5.2, §7 and
  §13, and the glossary.
- The second rounding step is a domain rule. Only the placement procedure is
  left to architecture (domain §5.2.13 and §14.2.1).
- Per-version locking is explicit in the intent (§6, §7) and the domain
  (§5.2.9, §5.8.2, §7.16, §8.4).
- Admin demotion can't bypass the removal ban: there is no demotion of
  another admin anywhere (intent §6; domain §4.2, §6.3, §8.1, §9). Admin
  step-down is defined (glossary; domain §4.2.4). The last-admin rule is
  intact (domain §4.2.5, §4.5.1, §8.1, §9, §12.6).
- Former-member behaviour is consistent: visibility, A2, A3, write-offs,
  rejoin. Anonymization is treated as final everywhere.
- Settlement routing matches A6 in all three documents. Audit and history
  behaviour is consistent.
- No new requirement was invented.

A search for stale wording ("demote", "minimum", "base currency", "any member
can record", "fewest", "always has at least one admin", unqualified
"balance") found only intended uses.

### Corrections

- The intent's rounding wording was revised during the sync, so that it no
  longer implied every new version re-allocates step-1 shares. Step-1
  re-allocation on edit stays as R1 decided: only when the total or
  participants change.
- One question surfaced during the sync and was recorded as open for
  specification, not decided: whether a new expense version that leaves the
  total, payers, paid amounts and participants unchanged keeps the previous
  version's Obligations (`docs/domain.md` §14.1.4). It affects only where
  leftover units fall, but it interacts with the A2 admin-only rule.
- Nothing was committed.

## Prompt 018 — Final Domain Approval Review

### Phase

Domain discovery → approval review

### Purpose

Verify intent, glossary and domain model for approval before architecture,
and classify every remaining open question.

### Prompt

```text
Perform the final Domain Approval Review. Read: intent/intent.md,
docs/glossary.md, docs/domain.md, prompts/development-log.md. Do NOT modify
any files. [...] Verify: product intent, terminology, expense allocation,
ledger, settlements (A6 points 1–6), membership, administration (including
that demotion cannot bypass the admin-removal restriction), authorization,
history and audit, concurrency. Review every remaining open question and
classify it as BLOCKER / SPECIFICATION / ARCHITECTURE / IMPLEMENTATION. Do not
resolve any open question yourself. Pay particular attention to: "If a new
expense version leaves the total, payers, paid amounts and participants
unchanged, does it keep the previous version's split?" [...] Produce:
BLOCKER count, IMPORTANT count, NON-BLOCKING count, [lists], and a final
statement of whether the domain is ready for architecture. Do not modify
anything. Do not commit anything.
```

### Decisions

None. This was a review.

- **Result:** 0 BLOCKER, 5 IMPORTANT, 9 NON-BLOCKING. The domain was judged
  ready for architecture.
- **IMPORTANT:**
  - **IMP-1:** whether a version that changes no amounts keeps its split.
    Classified as specification, not a blocker: every option keeps the
    invariants and the model, and the question interacts with A2 and with
    rotation usage.
  - **IMP-2:** the A2 wording ("any change") conflicted with A3 and R7.
  - **IMP-3:** whether an edit can turn a Direct settlement into a Routed
    one.
  - **IMP-4:** who carries out an upheld Dispute when the fallback Resolver
    lacks edit rights.
  - **IMP-5:** the suggestion method must be designed together with the A6
    allocation rule to meet invariant §7.14. Carried into architecture as a
    constraint.
- **NON-BLOCKING:**
  - **N1:** intent J3's "participant's cost" was imprecise under M2.
  - **N2:** unqualified "member" where only active registered members can
    act.
  - **N3:** "balance" survived as a label and in invariant names.
  - **N4:** the "Cleared" definition was too narrow.
  - **N5:** "Upheld" in the glossary didn't mention the routed case.
  - **N6:** three cases of simultaneous changes were not listed.
  - **N7:** payer ≠ recipient not stated.
  - **N8:** "longest-standing" is ambiguous after a rejoin.
  - **N9:** an A3 payer can't see a former member's payment details.
- Open questions were classified: none BLOCKER; specification items
  (including IMP-1, 3 and 4; launch markets and audit retention flagged as
  early architecture inputs); architecture items (§14.2.1–6 plus IMP-5);
  implementation item (notification delivery).

### Artifacts

None modified during the review, as instructed. Logged afterwards in
Prompt 019's session.

### Outcome

The user confirmed the IMP-2 reading and asked for the IMP-2 and N1–N6 fixes
(Prompt 019).

### Corrections

- The user's message ended with a stray "4". It was treated as a typo.

## Prompt 019 — IMP-2 clarification and N1–N6 wording fixes

### Phase

Domain discovery → approval fixes

### Purpose

Apply the confirmed clarification of A2 and the non-blocking wording fixes
from the approval review.

### Prompt

```text
Confirm IMP-2 with the following interpretation:

The admin-only restriction for former-member debt applies to editing,
deleting, and restoring existing records that affect a former member's
debt.

It does not restrict:
- recording a new settlement to a former member, as permitted by A3;
- writing off a former member's debt by the member who is owed, as permitted
  by R7.

This is a clarification of the existing A2 decision, not a new product
decision.

Apply this clarification and the N1–N6 documentation wording fixes to:
- intent/intent.md
- docs/domain.md
- docs/glossary.md

Update prompts/development-log.md with the clarification and wording fixes.

Do not change any other decisions. Do not resolve IMP-1, IMP-3, IMP-4, or any
other remaining specification question. Do not begin architecture yet. Do
not commit anything.

Afterward, verify that the three authoritative documents remain consistent
and show me the changes.
```

### Decisions

- **IMP-2 (clarifies A2; not a new decision):** the admin-only restriction
  applies to editing, deleting and restoring existing records that affect a
  former member's debt, including who they owe. It does not restrict
  recording a new settlement to a former member (A3), or a write-off by the
  member who is owed (R7).

### Artifacts

- `intent/intent.md`:
  - J3 reworded for M2 (N1).
  - J4, J9, §5 payment details, §6 promotion, §6 recurring, §6 payment
    details: "member" → "active registered member" (N2).
  - J8 and §6 Edits: admin-only rule scoped to edit, delete and restore,
    with an explicit A3/R7 exception bullet (IMP-2).
  - §5 notifications: "edits, deletions or restorations that change a former
    member's debt" (IMP-2).
  - §6 audit visibility: active registered members; former members see only
    their own debts (N2, per Q1).
- `docs/domain.md`:
  - Status line mentions Prompt 019.
  - §5.8.4 scoped with the A3/R7 exception, and the matching §9 row (IMP-2).
  - §11 notification wording (IMP-2).
  - §7 invariants 4, 5, 6 and 9 renamed to avoid "balance" (N3).
  - §12 items 8–10 added: write-off limit with a settlement at the same
    moment, one open dispute, claim vs anonymization (N6).
- `docs/glossary.md`:
  - Net balance: note on where "balance" may appear (N3).
  - Cleared: zero through any records (N4).
  - Upheld: routed settlements can only be deleted (N5).

### Outcome

The consistency check passed:

- A search for "make a change that affects", "Group members can see", "Any
  member can define", the old invariant names and "participant's cost" found
  nothing.
- The A2 wording now agrees with A3 (intent §6 Settlements; domain §5.5.2)
  and R7 (intent §6 Write-offs; domain §5.6.2) in all three documents.
- The new concurrency items follow from existing invariants (write-off
  bound, one open dispute, anonymization final). They add no new rule.
- IMP-1, IMP-3, IMP-4, IMP-5 and N7–N9 remain open as classified in
  Prompt 018.
- Nothing was committed. Architecture has not begun.

### Corrections

None.
