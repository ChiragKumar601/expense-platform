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

## Prompt 020 — Begin Architecture Discovery: architectural requirements analysis

### Phase

Architecture discovery

### Purpose

Derive the architectural requirements implied by the approved intent and
domain model before any architecture decision, stack choice or
`docs/architecture.md`.

### Prompt

```text
We have completed and approved the product intent and domain model. Before
starting this task: 1. Read prompts/development-log.md. 2. Read
intent/intent.md. 3. Read docs/glossary.md. 4. Read docs/domain.md. 5. Read
CLAUDE.md. We are now entering the Architecture Discovery phase. Do NOT write
application code. Do NOT create implementation tickets. Do NOT create an
implementation plan. Do NOT silently choose a technology stack. Do NOT create
docs/architecture.md yet. Your job is to discover the architectural
requirements implied by the approved product/domain model. Analyze, at
minimum: [27 areas: system boundaries; client/server responsibilities;
financial calculation boundaries; expense calculation and M2; money
representation and precision; two-stage deterministic rounding; expense
versioning; ledger/debt representation; calculation vs materialization;
settlement routing; settlement suggestions; IMP-5; transaction boundaries;
concurrency and race conditions; idempotency; authorization boundaries;
former members; anonymized members; disputes; write-offs; recurring
expenses; notifications; audit/history; failure and recovery; external
dependencies; scalability; data consistency]. For each area identify:
architectural requirement, domain rule that creates it, design
alternatives, tradeoffs, unresolved questions, risks if designed
incorrectly. Distinguish: requirements already determined by the domain /
architectural decisions still to be made / implementation details to defer.
[...] Do not make a final architecture decision yet. At the end: summarize
the architectural problem; list the architectural decisions; identify
independent vs coupled decisions; identify remaining domain/specification
questions that must be resolved before architecture can be finalized;
recommend the next architecture-discovery step. Update
prompts/development-log.md with this significant interaction. Do not commit
anything.
```

### Decisions

None. No architecture decision, stack or design was chosen.

Key findings:

1. **Ledger calculation core:** the domain requires a pure, deterministic
   calculation core, kept apart from I/O, that is authoritative on the
   server.
2. **Money:** integer minor units, with wide intermediate arithmetic for M2.
   Each group's currency exponent must be fixed so history can be
   reproduced.
3. **Historical reproducibility:** each version's results must be stored,
   or the calculation algorithm itself must be versioned.
4. **Group as consistency boundary:** the group is the natural unit of
   serialization. The rotation position, the debt graph, settlement
   allocation and the admin invariants all require it.
5. **Authorization depends on calculation:** whether an edit "affects a
   former member's debt" (A2) is only known after the new version is
   computed. So the authorization check must run inside the same
   transaction, after the calculation.
6. **IMP-5:** a suggestion method based on net balances alone is
   incompatible with chain-only allocation. Suggestions must be realizable
   as chain flows in the current debt graph, and must be tagged with the
   ledger state they were computed from.
7. **Debt cycles:** cycles among active members (A→B→C→A) have zero net
   balances but non-zero pairwise debts. Under the current domain they can
   only be cleared by real routed payments (at least 2 for a 3-cycle).
   Product question raised.
8. **Anonymization:** an identity layer kept separate from the records would
   satisfy "append-only, except anonymization" without rewriting ledger or
   audit records. Backups, logs and receipts that contain personal data
   remain erasure concerns.

New domain or specification questions surfaced, to resolve before
architecture is finalized (not decided):

- **Q-A:** step-2 rotation semantics: rotation over debtors, creditors or
  pairs, and how far the position advances.
- **Q-B:** how close each Obligation must be to the exact proportional value
  (for example, within one minor unit).
- **Q-C:** must allocation route as much as possible through Chains before
  any Overpayment, and is there a maximum Chain length?
- **Q-D:** must debt cycles be cleared by real payments, or is a
  non-monetary cancellation acceptable?
- **Q-E:** the exact scope of the simplified-view guarantee (valid =
  computed against the current state; recomputed after each recording; any
  recording order).
- **Q-F:** restoring or editing a Write-off after the debt has shrunk below
  its amount (conflicts with the Write-off bound invariant).
- **Q-G:** IMP-1 (re-allocation on a version that changes no amounts).
- **Q-H:** IMP-3 and IMP-4.
- **Q-I:** maximum group size and amount limits.
- **Q-J:** which time zone governs "expense date not in the future" and
  recurring occurrences.
- **Q-K:** how calculation defects are corrected after Versions are locked.
- **Q-L:** detecting duplicate settlements (non-blocking).
- **Q-M:** RPO/RTO, audit retention and launch markets (data residency).

Architecture decisions identified (AD1–AD23), grouped into four tightly
coupled clusters:

1. **Ledger core:** money representation, rounding procedure, ledger
   representation, materialization, versioning, algorithm versioning.
2. **Settlement:** allocation and suggestions (IMP-5), coupled with the debt
   graph and serialization.
3. **Consistency:** consistency boundary, transactions, outbox,
   idempotency, in-transaction authorization.
4. **Privacy and history:** history model, identity separation, audit store,
   erasure pipeline.

Relatively independent: the scheduler, notifications, external providers,
the deployment unit, and the data store/hosting choice (deliberately last).

### Artifacts

- `prompts/development-log.md`: this entry.
- Read only: `CLAUDE.md`, `intent/intent.md`, `docs/glossary.md`,
  `docs/domain.md`.
- `docs/architecture.md` was not created. No code, tickets or plans.

### Outcome

The analysis was delivered in chat.

Recommended next step: resolve Q-A to Q-F with the user first. Then define
the contract and invariants of the calculation core. Then validate the
allocation and suggestion algorithms (IMP-5, cycles, maximality) with a
throwaway prototype and randomized simulation, before the consistency
boundary and stack discussions.

Housekeeping note: the user describes the intent and domain as approved, but
both documents still say "Status: Draft". This was flagged, not changed.

### Corrections

None. The user committed the domain artifacts as `01a93e0` "Domain Discovery
& Domain Decisions" before this step.

## Prompt 021 — Architecture discovery Round A decisions (Q-A to Q-F)

### Phase

Architecture discovery

### Purpose

Settle the financial-algorithm questions surfaced in Prompt 020 that the
architecture must not decide alone. Then continue architecture discovery
with the calculation-core contract and the IMP-5 analysis.

### Prompt

```text
1. Q-A: Agree — closest-to-proportion first; rotation only breaks exact
   fractional ties, with rotation advancing for each tie-break.
2. Q-B: Agree — every debtor→creditor allocation must be within one smallest
   currency unit of its exact proportional value while preserving exact row
   and column totals.
3. Q-C: Agree — route the maximum possible amount through available
   active-member chains, preferring shorter chains, with no artificial
   maximum chain length.
4. Q-D: Do not introduce cycle offset. Handle cycles within the existing
   settlement/debt model. Do not add a new cycle-specific domain operation or
   behavior.
5. Q-E: Agree — suggestions are state-bound; stale suggestions warn and
   expose current suggestions rather than being blocked; the convergence
   guarantee applies to repeatedly recording valid suggestions, including
   partial payments, in any order. Cycles must be handled within the
   existing approved settlement/debt model.
6. Q-F: Agree — restore/edit is blocked when it would violate the current
   write-off constraint. It does not silently cap or create a reverse debt.

These decisions must not modify the approved product intent or domain
decisions. Treat the following as frozen and authoritative:
intent/intent.md, docs/domain.md, docs/glossary.md. If any of these decisions
appear to conflict with the frozen domain, do not resolve the conflict by
changing those documents. Flag the conflict for explicit human review
instead. Record these Q-A through Q-F decisions in
prompts/development-log.md, then continue with architecture discovery. Do
not begin implementation, create tickets, or create implementation plans
yet.
```

### Decisions

1. **Q-A:** in step 2, leftover units go first to the debtor→creditor
   amounts closest to proportion (largest fractional part cut off). Rotation
   order, from the group's Rotation position, breaks only exact ties between
   equal fractional parts. The Rotation position advances one step for each
   tie-break.
2. **Q-B:** every debtor→creditor amount is within one smallest unit of its
   exact proportional value, and the totals for every debtor and every
   creditor stay exact.
3. **Q-C:** allocation routes the maximum possible amount through chains of
   Active members, preferring shorter chains, with no maximum chain length.
   Overpayment is only what genuinely cannot be routed.
4. **Q-D:** no cycle offset. Debt cycles are cleared within the existing
   model: real Direct and Routed settlements, at least 2 for a 3-member
   cycle. No cycle-specific operation exists.
5. **Q-E:** suggestions are tied to the ledger state they were computed
   from. Recording a stale suggestion warns and shows the current
   suggestions; it is not blocked. The convergence guarantee: repeatedly
   recording valid suggestions, including partial payments, in any order,
   clears every pairwise debt between Active members in finitely many steps,
   cycles included, within the existing model.
6. **Q-F:** a restore or edit of a Write-off is blocked when it would exceed
   the current debt. It is never silently capped, and it never creates a
   reverse debt.

The frozen documents (`intent/intent.md`, `docs/domain.md`,
`docs/glossary.md`) were not modified, as instructed.

### Conflicts flagged for human review (not resolved)

- **FR-1:** `docs/domain.md` §7.17 says that in both steps leftover units
  "follow the Rotation order". `docs/glossary.md` "Leftover unit" says
  leftover units "are placed by Rotation order", and "Rotation position"
  says it "advances as leftover units are placed". `intent/intent.md` §6
  Rounding says leftover units are assigned "in rotation" for both steps.
  Under Q-A, step-2 units are placed by largest fractional part, rotation
  only breaks exact ties, and the position advances only on tie-breaks.
  `docs/domain.md` §5.2.8 ("placed deterministically. Ties are decided by
  Rotation order") is consistent with Q-A.
- **FR-2:** `docs/domain.md` §7.14 says that "if every Suggested settlement is
  recorded, every Pairwise debt between Active members is zero". That reads as
  a one-shot guarantee: recording one complete set of suggestions, in any
  order, clears everything. Q-E guarantees convergence when suggestions are
  repeatedly recomputed and recorded. Under maximal, shorter-first routing,
  recording a whole snapshot of suggestions in an arbitrary order may not
  clear every debt in one pass, because one suggestion's allocation can use
  links another suggestion relied on. Which guarantee is authoritative needs
  a human decision.
- **Consequence noted (not a conflict):** under Q-D, members whose net
  balance is zero but who sit in a debt cycle will be asked to make and
  receive real payments. That bears on the "fewer transfers" and "Balance
  clarity" aims (intent §2), and is a UX risk.

### Artifacts

- `prompts/development-log.md`: this entry.
- No other files changed.

### Outcome

Decisions recorded. Architecture discovery continued in chat:

- **The calculation-core contract:** the core needs five functions:
  - shares
  - obligations
  - pairwise debts
  - settlement allocation
  - suggestions

  It also needs testable laws:
  - L1 shares
  - L2 positions
  - L3 obligations
  - L4 single payer
  - L5 allocation, including maximality
  - L6 suggestions and convergence
  - L7 conservation
  - L8 reproducibility
- **Allocation:** under Q-C, allocation is a minimum-cost maximum-flow
  problem. Each link costs 1, which gives "shorter chains first", and the
  direct debt comes first automatically. The resulting flow is broken down
  into chains. Ties are broken deterministically.
- **Convergence:** if every valid suggestion's amount is no more than what
  can be routed from payer to recipient, and allocation is maximal, then
  every recorded valid suggestion strictly reduces total active debt, so
  debts converge with recomputation, cycles included.
- **Proposed next step:** a throwaway simulation prototype, which needs the
  user's go-ahead because it is code.

### Corrections

None.

## Prompt 022 — FR-1 and FR-2 resolved; simulation prototype authorized

### Phase

Architecture discovery

### Purpose

Resolve the two conflicts flagged in Prompt 021 without changing the frozen
documents, and authorize a throwaway simulation prototype.

### Prompt

```text
For FR-1: Keep Q-A as the governing calculation decision. The intended
second-rounding behavior is: allocate leftover smallest units to the largest
fractional remainders first; use Rotation order only to break exact
equal-remainder ties; advance the rotation position only when Rotation is
actually used as that tie-break. Do not change intent/intent.md,
docs/domain.md, or docs/glossary.md. Treat the existing wording such as "in
rotation" as an imprecise description of the already-approved deterministic
rounding behavior, and use the more precise Q-A rule for architecture and
calculation design.

For FR-2: The existing approved domain wording in docs/domain.md §7.14
governs. Specifically, the intended guarantee is: If every Suggested
settlement in one generated suggestion set is recorded, then every Pairwise
debt between Active members reaches zero, regardless of the order in which
those suggestions are recorded. Therefore, do NOT weaken this to merely
"repeatedly recording suggestions eventually reaches zero." [...] treat this
as an architecture requirement that the suggestion-generation and allocation
design must satisfy. This means the architecture must ensure that: one
generated suggestion set is internally consistent; recording its suggestions
in any order remains valid; routing caused by an earlier suggestion does not
invalidate the remaining suggestions in that same set; the complete set
clears all Active-member Pairwise debts; stale suggestions from an earlier
ledger state remain subject to the existing Q-E stale-state behavior. If the
current allocation rule (Q-C) makes this impossible with a particular
suggestion-generation strategy, do not change Q-C or the domain. Flag the
architectural conflict and investigate alternative suggestion-generation/
allocation designs.

For the cycle consequence: Do not introduce cycle offset or any new
cycle-specific domain operation. [...]

Regarding the throwaway simulation: yes, build the scratchpad prototype
before committing to the final calculation/suggestion architecture. It
should specifically test: 1. Q-A rounding invariants. 2. Q-B proportionality
tolerance. 3. Q-C maximal routing with shorter-chain preference. 4. The FR-2
one-complete-suggestion-set guarantee. 5. Arbitrary ordering of the
suggestions within a generated set. 6. Cyclic debt graphs. 7. Partial
settlements where permitted by the approved rules. 8. Stale suggestion
detection. 9. Groups of at least 50 members. Keep the prototype outside the
application architecture and do not modify the frozen product/domain
documents. Do not begin application implementation.
```

### Decisions

- **FR-1 resolved:** Q-A governs. The phrase "in rotation" in the intent,
  `docs/domain.md` §7.17 and the glossary is read as an imprecise
  description of the approved behaviour: largest fractional remainder
  first; rotation only for exact ties; the position advances only when a
  rotation tie-break is used. The documents are unchanged.
- **FR-2 resolved:** `docs/domain.md` §7.14 governs as a **one-set,
  any-order** guarantee. It is an architecture requirement:
  - each generated suggestion set is internally consistent;
  - its suggestions can be recorded in any order;
  - routing caused by one suggestion never invalidates the rest of the set;
  - the complete set clears all debts between Active members;
  - stale suggestions from an earlier state follow Q-E.

  If Q-C makes this impossible for some generation strategy, the
  architecture must find another design. Q-C and the domain are not
  changed.
- **Cycles:** handled only by the existing settlement and debt model. No
  cycle-specific operation.
- **Prototype authorized:** a throwaway, in the scratchpad only, outside the
  repository and outside the application architecture. It covers the nine
  test areas listed.

### Artifacts

- `prompts/development-log.md`: this entry. The prototype outcome is
  recorded in the next entry.

### Outcome

Prototype work started.

### Corrections

- The Q-E convergence guarantee recorded in Prompt 021 still applies to
  stale or partial recording. It is not the §7.14 guarantee, which is the
  stronger one-set, any-order guarantee.

## Prompt 023 — Simulation prototype results: calculation core, routing, FR-2

### Phase

Architecture discovery (throwaway prototype)

### Purpose

Before committing to the calculation and suggestion architecture, test the
approved rounding, allocation and suggestion rules by randomized simulation.
Authorized in Prompt 022.

### Prompt

Covered by Prompt 022: "build the scratchpad prototype [...] It should
specifically test: Q-A, Q-B, Q-C, FR-2 one-set guarantee, arbitrary ordering,
cyclic graphs, partial settlements, stale detection, groups of at least 50
members."

### Decisions

None. The prototype informs decisions but doesn't make them. A conflict is
flagged for human review (below).

### Artifacts

- The throwaway prototype lives only in the session scratchpad (`proto/core.py`, `proto/sim.py`, `proto/exp.py`,
  `proto/exp2.py`), outside the repository. Python standard library only.
  No repository file other than this log changed. Frozen documents
  untouched.

### Outcome

**Checks passed (0 failures):**

- **Rounding:** L1–L4 rounding invariants (3,000 random expenses, 2–60
  members, currency exponents 0, 2 and 3). Q-B: 37,711 debtor→creditor
  amounts all within one unit, with exact row and column totals. Q-A
  rotation advances only on tie-break units. Determinism.
- **Allocation (Q-C):** routed = min(amount, max-flow); overpayment only
  when no route remains; the direct debt first; the minimum total of link
  reductions, matching an independent unit-cost solver; conservation of
  net balances.
- **FR-2:** with the order-safe construction, 3,348 order trials gave no
  overpayment and cleared every debt. This covered every permutation of
  sets with up to 6 suggestions, plus random orders. Graphs included
  cycles (3-cycle, uneven 4-cycle, shared-node cycles, cycle plus tail,
  random dense cyclic graphs) and 50- and 60-member groups.
- **Partial payments:** splitting one suggestion of a set into two
  recordings broke nothing (440 trials, informational).
- **Convergence (Q-E):** recomputing and recording random valid
  suggestions, full or partial, always converged with no overpayment.
- **Stale detection:** a set-scoped validity token works. Full recordings
  of the same set stay valid. A foreign change, a duplicate or a partial
  recording marks the rest of the set stale.
- **Worked Q-A example:** 10.00 paid A 6.00 / B 4.00, shared by C, D and E
  (3.34 / 3.33 / 3.33). C owes A 2.00 and B 1.34; D and E each owe A 2.00
  and B 1.33. The amounts closest to proportion get the extra cents; no
  rotation tie-break was needed.

**Architecture findings:**

1. **The order-safe construction (sufficient for FR-2):** each
   suggestion's planned allocation equals the allocation function applied
   to the full graph at generation time, the planned allocations add up
   exactly to the graph, and allocation has a unique, fixed preference
   (minimum-cost flow with a generic perturbation). Then any recording order
   reproduces each plan exactly, because debts only shrink and a unique
   optimum survives capacity reduction. Remaining edges become direct
   suggestions, which always fit.
2. **Arbitrary decompositions fail.** A hand-built set that adds up to the
   graph failed in 6 of 24 orders. The standard net-balance method failed
   **100%** of order trials on every graph tested (overpayments, or debts
   left over; it suggests nothing at all for a pure cycle).
3. **CONFLICT flagged for human review.** Under Q-C (every settlement
   routed maximally, shorter chains first) combined with FR-2 (one set,
   any order), order-safe suggestion sets simplify little on realistic
   household graphs, where almost every pair has a direct debt:
   - greedy order-safe generator: 1,039 suggestions for 1,213 debts at n=50
     (only about 14% fewer), against a net-balance minimum of about 49;
   - best-coverage generator over all pairs: about 45–55% of the number of
     debts (for example 66 vs 120 debts at n=16, against a minimum of 15),
     but its runtime in naive form is impractical (41 s at n=16);
   - naive generators sometimes produced **more** suggestions than debts
     (4-cycle: 7 vs 4).

   This bears on the glossary's "fewer payments" and the intent's
   "simplified set of transfers". The frozen documents and Q-C were not
   changed.
4. **Architecture requirements derived:**
   - suggestions must never exceed the number of pairwise debts (fall back
     to the direct set);
   - suggestion generation is compute-heavy, so it is cached per ledger
     version and kept off the recording path;
   - the allocation tie-break must be a fixed total preference so the
     order-safe argument holds;
   - the set-scoped validity token is the basis for detecting stale
     suggestions.
5. **Partial payments:** splitting a suggestion did not break FR-2 in
   testing, although the stale rule as implemented treats a partial
   recording as staling the set. Whether a partial recording should stale
   the set is a specification question; Q-E only requires a warning.

### Corrections

- The first full simulation run was stopped, because recomputing
  suggestions after every step at 50+ members made it too slow. The final
  run tested convergence on small and cyclic graphs, and used fewer random
  orders at 50–60 members.

## Prompt 024 — Suggestion conflict resolved: Option (1), keep all decisions

### Phase

Architecture discovery

### Purpose

Resolve the conflict flagged in Prompt 023 (Q-C + FR-2 vs "fewer payments").

### Prompt

```text
Choose Option (1). Keep all existing decisions unchanged. Do not reopen or
modify the approved intent, domain model, or glossary. The architecture
should preserve all three existing constraints: 1. Q-C [...] 2. FR-2 /
docs/domain.md §7.14 [...] 3. The product's "fewer payments" / "simplified
set of transfers" language remains a product goal, but it should not be
interpreted as a guarantee of a globally minimal number of transfers.
Therefore: Continue investigating a stronger order-safe suggestion
generator. Prefer fewer transfers where practical, but do not violate Q-C or
FR-2 merely to reduce the number of suggestions. If an optimization cannot
safely preserve the guarantees, prefer correctness and the existing domain
rules. The suggestion generator may run outside the payment-recording path
and be cached per ledger state. Suggestions must carry the
ledger-state/version they were generated from. Recording must validate the
suggestion against the current authoritative state. Do not make the
suggestion's planned routing override the normal settlement allocation
rules. A suggestion is a proposed settlement, not a new settlement execution
mode. Do not introduce any new cycle-offset or cycle-specific domain
operation. Do not add a new domain requirement that suggestions must never
outnumber pairwise debts. If a fallback such as direct-debt suggestions is
useful, treat it as an architecture/UX strategy rather than a new domain
invariant. Regarding partial payments: Keep the existing Q-E
interpretation: a partial payment can make the original suggestion set
stale. The system should warn/recompute rather than treat the old suggestion
set as authoritative. [...] Do NOT reinterpret the existing domain rules to
make the algorithm easier. If another genuine contradiction is discovered,
stop and bring it to me rather than changing the approved documents. Log
this decision in prompts/development-log.md. Then continue with the next
coupled architecture decisions: concurrency, group-level processing,
transaction boundaries, idempotency, and authorization checks inside the
transaction. Do not begin application implementation yet.
```

### Decisions

1. **Option (1).** All decisions stay as they are: Q-C, FR-2 (§7.14,
   one set, any order) and A6.
2. "Fewer payments" / "simplified set of transfers" is a **product goal,
   not a guarantee of a minimal number of transfers**.
3. **A suggestion is only a proposed settlement.** Its planned routing never
   overrides normal allocation. The order-safe construction works by
   *predicting* the normal allocation exactly, not by changing it.
4. A stronger order-safe generator is to be researched. Correctness and the
   domain rules always win over the number of transfers.
5. **Architecture strategy:**
   - generation runs outside the recording path and is cached per ledger
     state;
   - each suggestion carries the ledger state or version it came from;
   - recording validates against the current authoritative state.
6. Falling back to direct-debt suggestions is an architecture/UX strategy,
   **not** a domain invariant. Prompt 023's derived requirement that
   "suggestions must never exceed the number of pairwise debts" is
   withdrawn as a requirement.
7. **Partial payments:** the Q-E reading stays. A partial payment can make
   the set stale; the system warns and recomputes.
8. No cycle-specific operation. The frozen documents are not changed or
   reinterpreted. Any further genuine contradiction stops work and goes to
   the user.

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

The AD11–AD14 discovery continued (Prompt 025).

### Corrections

- Prompt 023 finding 4's "must never exceed the number of pairwise debts"
  is downgraded to an optional architecture/UX fallback.

## Prompt 025 — Architecture discovery: concurrency, group processing, transactions, idempotency, in-transaction authorization (AD11–AD14)

### Phase

Architecture discovery

### Purpose

Analyze the coupled decisions AD11–AD14 under all approved rules, without
final decisions or a stack choice.

### Prompt

The continuation instruction in Prompt 024: "continue with the next coupled
architecture decisions: concurrency, group-level processing, transaction
boundaries, idempotency, and authorization checks inside the transaction. Do
not begin application implementation yet."

### Decisions

None final. The analysis proposed:

- **Consistency unit:** the Group ledger. It covers the rotation position,
  the debt graph, membership and roles, the ledger version, uniqueness of
  open disputes, draft confirmation and the write-off limit. Work spanning
  groups (account deletion, payment details) is user-level and eventually
  consistent.
- **Two kinds of version:** (a) the record version, under the domain rule
  that an edit based on an outdated version is rejected and shown to the
  user; (b) the group ledger version, used for serializing work, for
  suggestion tokens and for confirming previews. Contention at group level
  may be retried transparently. A stale record version must never be
  retried silently.
- **Concurrency options:**
  - a pessimistic lock per group;
  - an optimistic group version with retry;
  - a single writer per group;
  - serializable isolation.

  Leaning towards a lock per group or an optimistic group version, decided
  together with the stack.
- **Transaction:** one command = one transaction in one group:
  1. load state and resolve actor → Member;
  2. check permissions before calculation;
  3. run the pure calculation core;
  4. check permissions and domain rules after calculation (A2's
     former-member debt diff, the Q-F write-off limit, restore warnings);
  5. persist the version, locked results, debt effects, rotation, ledger
     version, audit entries and outbox events;
  6. commit.

  Outside the transaction: notifications, suggestion generation, OCR,
  exports. Recurring drafts get one transaction per occurrence. Account
  deletion is an idempotent per-group process.
- **Idempotency:** every mutating command carries a client-generated command
  ID, stored in the same transaction. Retries reuse it. Domain uniqueness
  covers drafts per occurrence, claims, invitations and open disputes.
  Outbox and scheduler consumers are idempotent. Duplicate real-world
  settlements (Q-L) are a different matter: a specification question.
- **Authorization:** a central pure policy function of (actor, command,
  state, calculation result), evaluated inside the serialized scope, with
  checks both before and after calculation. Read access goes through
  filtered views: full for Active registered members; for Former members,
  only their own debts and the records behind them; payment details by the
  cross-group rule.

**New architecture requirement surfaced (not a domain change):** several
domain rules show the user something *before* recording:

- the Overpayment warning (§5.5.5);
- the restore warning for Former members (§5.8.6);
- the A2 admin-only outcome;
- whether a settlement will be routed.

Because of concurrency, the state can change between that preview and the
commit. So a commit must carry the ledger version and the outcome the user
confirmed, and must ask for re-confirmation if the outcome would differ.

**Open questions put to the user:**

- **AD-Q1:** lock per group vs optimistic version (or defer to the stack
  decision).
- **AD-Q2:** confirming a preview against the ledger version: re-confirm
  only when the warned outcome changes, or on any change?
- **AD-Q3:** account deletion: sign-in disabled immediately, with
  anonymization completed per group, asynchronously, within a bounded time?
- **AD-Q4:** dispute resolution and the resulting settlement edit or delete
  in one command (depends on IMP-4)?

The research plan for a stronger order-safe generator was recorded as its
own workstream (incremental coverage greedy, local search that merges
suggestions under an order-safety check, exact optimization for small
groups), to be prototyped later.

### Artifacts

- `prompts/development-log.md`: this entry. No other files.

### Outcome

The analysis was delivered in chat. AD-Q1 to AD-Q4 await the user.

### Corrections

None.

## Prompt 026 — Architecture discovery: consistency and transaction cluster, proposed decisions P1–P12

### Phase

Architecture discovery

### Purpose

Go deeper on the coupled cluster (concurrency, the group as the unit of
consistency, transactions, idempotency, in-transaction authorization, and
how the calculation core interacts with them), as proposed decisions with
the invariants they protect.

### Prompt

```text
Continue with the next architecture decision cluster. Keep the approved
intent, domain model, and glossary frozen. For this round, focus on:
concurrency and simultaneous group changes; the group as the unit of
financial consistency; transaction boundaries; idempotency and duplicate
requests; authorization checks inside the transaction; interaction between
these concerns and the calculation core. For each proposed decision: 1.
State the invariant or failure mode it protects. 2. Explain the available
architectural alternatives. 3. Separate domain requirements from
architecture choices. 4. Do not choose a specific technology or
infrastructure prematurely. 5. Identify any assumptions or unresolved
questions. Do not modify: intent/intent.md, docs/domain.md,
docs/glossary.md. If anything genuinely conflicts with the frozen domain,
stop and bring it to me rather than changing the documents. Do not begin
implementation, create tickets, or create implementation plans yet.
Continue recording significant decisions and outcomes in
prompts/development-log.md.
```

### Decisions

Proposed, not final (pending user review):

- **P1:** the Group ledger is the unit of financial consistency.
- **P2:** two kinds of version: record version (domain §5.8.7) and group
  ledger version (architecture).
- **P3:** the mechanism for processing a group's changes one at a time
  (open: AD-Q1).
- **P4:** one command = one transaction in one group, as a fixed pipeline:
  load → check before calculation → calculate → check after calculation →
  persist → commit.
- **P5:** side effects go through an outbox after commit. The ledger never
  waits on outside services.
- **P6:** operations spanning groups run as a gate at User level plus
  idempotent per-group steps. Account deletion first sets a User-level
  deleted status in its own transaction. Every group transaction reads that
  status, so the member stops being an acting member (and can't be added to
  records) immediately, with no window. Per-group anonymization and Former
  status follow.
- **P7:** idempotency through client command IDs, domain uniqueness rules,
  and idempotent consumers.
- **P8:** a central, pure authorization policy evaluated inside the
  serialized scope. Checks before and after calculation, with a defined set
  of failure types.
- **P9:** previews are confirmed against the outcome the user saw (open:
  AD-Q2).
- **P10:** the calculation core's contract with transactions:
  - pure;
  - takes a snapshot, returns effects;
  - outputs per-member-pair effect deltas, used by A2 and the audit trail;
  - rotation position in and out;
  - stamped with the algorithm version;
  - no clock or storage access;
  - a retry is simply a recomputation.
- **P11:** suggestions are generated from a committed snapshot, cached by
  (group, ledger version), and validated with the set-scoped token when
  recorded.
- **P12:** reads come from committed state, with read-your-writes.
  Visibility rules (payment details, Former members) are evaluated against
  current membership when reading, not copied ahead of time.

No genuine conflict with the frozen documents was found. The User-level
gate for account deletion (P6) was checked against §5.12 and §12.7, and is
consistent with them.

### Artifacts

- `prompts/development-log.md`: this entry. No other files.

### Outcome

Delivered in chat. Open questions: AD-Q1 to AD-Q4 (carried over from Prompt
025, still unanswered) and AD-Q5 (an upper bound on group size, Q-I, needed
for lock duration and for loading the full debt graph per transaction).

### Corrections

None.

## Prompt 027 — Consistency cluster decisions (AD-Q1 to AD-Q5)

### Phase

Architecture discovery

### Purpose

Settle the open questions of the consistency and transaction cluster
(Prompts 025–026).

### Prompt

```text
For AD-Q1, choose group locking for each financial or domain-changing
command; hold the lock only for authoritative processing and commit, and
keep slow derived work outside it. Do not choose a locking technology yet.
For AD-Q2, ask the user to confirm again only when the specific warned
outcome changes, not merely because the group version changes. For AD-Q3,
the user-level deletion gate takes effect immediately; per-group
anonymization and former-member processing may complete asynchronously
within a bounded, retry-safe window to be specified later. For AD-Q4, an
upheld dispute and its resulting settlement edit or deletion must be one
atomic command; authorization remains open under IMP-4. For AD-Q5, keep the
maximum group size open. Use 100 members only as a provisional engineering
and test ceiling, not as a product constraint. Record these decisions in
prompts/development-log.md. Keep intent/intent.md, docs/domain.md, and
docs/glossary.md frozen. Do not choose concrete technologies yet. Then
continue to the ledger-core cluster.
```

### Decisions

1. **AD-Q1:** every financial or domain-changing command takes a lock on its
   group (P3 option a). The lock is held only for authoritative processing
   and commit. Slow derived work (suggestion generation, notifications,
   exports, OCR) stays outside. No locking technology chosen.
2. **AD-Q2:** ask the user to confirm again only when the specific outcome
   they were warned about changes (P9 option a).
3. **AD-Q3:** the User-level deletion gate takes effect immediately.
   Per-group anonymization and Former-member processing may complete
   asynchronously within a bounded, retry-safe window, to be specified.
4. **AD-Q4:** an upheld Dispute and the resulting settlement edit or
   deletion form one atomic command. Who may carry it out stays open under
   IMP-4.
5. **AD-Q5:** the maximum group size stays open. 100 members is a
   provisional engineering and test ceiling only, not a product constraint.

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

P1–P12 (Prompt 026) stand as proposed, with P3 and P9 now decided as
above. The ledger-core cluster followed (Prompt 028).

### Corrections

None.

## Prompt 028 — Architecture discovery: ledger-core cluster (AD3–AD7, AD22)

### Phase

Architecture discovery

### Purpose

Analyze money representation, the step-2 rounding procedure, ledger and debt
representation, storing vs computing debts, the history model, and
calculation-algorithm versioning and correction, under all approved rules.

### Prompt

The continuation instruction in Prompt 027: "Then continue to the
ledger-core cluster."

### Decisions

Proposed, not final:

- **LC1 Money:**
  - integer smallest units, with arbitrary-precision (or provably
    wide-enough) intermediate arithmetic for M2 products;
  - each group's currency exponent captured at creation and kept with the
    group;
  - no floating point anywhere.
- **LC2 Step 2:** the canonical outcome is defined as **the lexicographically
  first feasible placement** of leftover units, in the order (largest
  remainder, then rotation order). Any algorithm producing exactly that
  placement is acceptable. The prototype's greedy-with-feasibility method is
  one.
- **LC3 Ledger representation:** an append-only journal of debt entries
  (debtor, creditor, amount, source record version, kind). Delete posts
  reversing entries; restore re-posts the original entries; so a restored
  Routed settlement re-applies its original allocation automatically.
- **LC4 Stored debts:** a per-pair debt total, updated in the same
  transaction under the group lock, treated strictly as a derived cache
  (domain §2: derived concepts are never separate facts). Reconciliation
  against the journal; checkpoints optional.
- **LC5 History model:** immutable record Versions, an append-only journal
  and the audit trail, all written in one transaction ("event-sourcing
  lite"). Full event sourcing remains the alternative.
- **LC6 Algorithm versioning:** each Version stores its locked results and
  the calculation algorithm version. Results are never recomputed for
  history.
- **LC7 Rotation state:** stored as the rotation order plus a pointer to the
  next member (not a bare index), so changes to the order don't shift the
  meaning of the position.

**Questions surfaced (not decided):**

- **LQ1 (spec):** in step 1, how far the Rotation position moves: past the
  last member who received a unit (as the prototype does), by the number of
  units, or by the number of members visited. Domain §5.2.2 says only "then
  advances".
- **LQ2 (spec):** exact tie-break semantics in step 2: the order used inside
  a tie group (debtor then creditor rotation distance?), and how many
  advances count when feasibility rather than rotation excluded a cell
  inside a tie group.
- **LQ3 (potential domain decision):** correcting history after a
  calculation bug (Q-K). A correction made by the System would be a new kind
  of actor editing records, which the domain doesn't provide for. Leaving
  history unchanged and fixing only new Versions needs no domain change.
  Flagged for the user; not resolved.
- **LQ4 (spec, low risk):** if a currency's official smallest unit ever
  changes, the group's captured exponent stays as it is. Confirm.

No genuine conflict with the frozen documents was found. Storing debts was
checked against domain §2 ("derived concepts are never separate facts") and
is consistent, provided the stored totals are a reconcilable cache, never
the source of truth.

### Artifacts

- `prompts/development-log.md`: this entry and Prompt 027.

### Outcome

Delivered in chat. LQ1–LQ4 await the user.

### Corrections

None.

## Prompt 029 — Ledger-core decisions (LQ1–LQ4) and LC1–LC7 adopted as direction

### Phase

Architecture discovery

### Purpose

Settle the ledger-core questions from Prompt 028.

### Prompt

```text
For the ledger-core questions, use these decisions: LQ1: move the rotation
position past the last member who received a cent. LQ2: within a tie group,
order by debtor rotation distance first, then creditor rotation distance;
advance rotation only when a tie-break actually determines a cent's
placement. LQ3: if a calculation bug is discovered, leave historical results
unchanged; keep the old algorithm/results for verification and audit, and
fix forward through ordinary record edits and new versions. Do not introduce
system-generated correcting versions without explicitly reopening the
domain. LQ4: capture the currency precision when the group is created and
keep using that precision even if the currency's official smallest unit
later changes. Treat LC1–LC7 as the current architectural direction:
integer smallest-unit money, exact/wide intermediate arithmetic,
deterministic rounding, an append-only debt journal, pairwise debt totals as
rebuildable caches rather than source of truth, immutable version/history
records with algorithm-version stamps, and persisted rotation state. Record
these decisions in prompts/development-log.md. Keep intent/intent.md,
docs/domain.md, and docs/glossary.md frozen. Do not choose concrete
technologies yet. Continue to the privacy/history cluster covering AD8,
AD16, and AD21.
```

### Decisions

1. **LQ1:** in step 1, the rotation position moves past the last member who
   received a leftover cent.
2. **LQ2:** within a step-2 tie group, order by the debtor's rotation
   distance, then the creditor's. The rotation advances only when a
   tie-break actually determines where a cent goes.
3. **LQ3:** after a calculation bug, historical results are left unchanged.
   The old algorithm and results are kept for verification and audit.
   Fixing happens forward, through ordinary edits and new Versions. No
   system-generated correcting Versions without explicitly reopening the
   domain.
4. **LQ4:** a group's currency precision is captured at creation and kept,
   even if the currency's official smallest unit later changes.
5. **LC1–LC7 adopted as the architectural direction:**
   - integer smallest-unit money with exact or wide intermediate arithmetic;
   - deterministic rounding (canonical step-2 placement defined in LC2);
   - an append-only debt journal;
   - pairwise totals as rebuildable caches, not the source of truth;
   - immutable Versions and history stamped with the algorithm version;
   - stored rotation state (a pointer to the next member).

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

The ledger-core cluster is settled as a direction. No technology chosen.

### Corrections

- The throwaway prototype (Prompt 023) advanced the rotation by every cent
  placed in a contested tie group. LQ2 is narrower: it advances only when the
  tie-break actually determined a cent's placement. The prototype isn't the
  specification. Any future prototype or the real core must follow LQ2.

## Prompt 030 — Architecture discovery: privacy and history cluster (AD8, AD16, AD21)

### Phase

Architecture discovery

### Purpose

Analyze keeping identity separate from records, how the audit trail is
stored, and the erasure pipeline, under the frozen domain and all decisions
so far.

### Prompt

The continuation instruction in Prompt 029: "Continue to the privacy/history
cluster covering AD8, AD16, and AD21."

### Decisions

Proposed, not final:

- **PH1 (AD8), identity kept separate from records:**
  - Ledger records, journal entries, audit entries, outbox events and caches
    refer only to opaque Member and User identifiers. Names, contact
    details and payment details live in separate identity records, and are
    looked up when reading or sending.
  - Anonymization deletes or replaces the identity record and sets the
    Member's stable Anonymous label. **No ledger, journal or audit row is
    rewritten**, which is stronger than the domain's "anonymization is the
    only permitted rewrite".
  - Values carrying identity that can't be held as references (for example a
    placeholder's display name inside an audit before/after value) are kept
    out of audit payloads, or isolated in fields that can be erased.
- **PH2 (AD16), audit store:**
  - Written in the same transaction as the change (P4), append-only, holding
    references only.
  - Each entry records: actor Member, time, entity and Version, action,
    before/after values, command ID and algorithm version.
  - Optional tamper evidence: a per-group hash chain over reference-only
    entries, which anonymization doesn't break.
  - Visibility per viewer comes from the read-time rules (P12).
  - Retention is designed to be configurable; the period stays open (intent
    §11.7).
- **PH3 (AD21), erasure pipeline:**
  - A required **inventory of where personal data lives**: identity records,
    payment details, comments, invitation contact details, notification
    payloads and history, outbox events, generated export files, receipt
    files (kept by decision), copies held by the OCR provider, the sign-in
    provider, the email provider, logs, analytics and backups.
  - Account deletion runs in stages:
    1. T0: the User-level gate. Sign-in disabled, payment details deleted
       immediately.
    2. Per group, retry-safe: anonymize the Member, delete their Comments,
       pass on the admin role, auto-archive if needed.
    3. External deletion: sign-in provider, analytics, provider copies.
    4. An **erasure tombstone** replayed after any backup restore.
    5. A completion record holding no personal data.
  - Placeholder anonymization is one group transaction.
  - Logs and events carry identifiers, not personal data, and have short
    retention.
  - Personal data exports are built from the same data inventory: generated
    asynchronously, short-lived, delivered securely.

**Consistency with the frozen domain:** checked against domain §5.12, §10,
§4.3 and §5.13. No genuine conflict.

Risks restated, already accepted by the user: kept expense descriptions
(R3-8) and kept receipt images (R6) may still identify the person.

**Questions put to the user:**

- **PQ1:** with records holding references, history shows a claimed
  placeholder under the claiming User's current identity (this bears on the
  open specification item about names after a claim). Confirm as the
  architectural default?
- **PQ2:** require tamper evidence (a per-group hash chain) for the audit
  trail, or keep append-only enforced only by privileges?
- **PQ3:** backups: erasure tombstones replayed after restore plus limited
  backup retention, or per-User encryption keys destroyed on deletion?
- **PQ4:** generated exports: produced on demand with short-lived download
  links and not kept on the server?

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

Delivered in chat. PQ1–PQ4 await the user.

### Corrections

None.

## Prompt 031 — Privacy and history decisions (PQ1–PQ4)

### Phase

Architecture discovery

### Purpose

Settle the privacy and history questions from Prompt 030.

### Prompt

```text
For the privacy/history cluster, use these decisions: PQ1: yes, keep the
claiming user's current identity as the default for placeholder history,
while leaving the exact history-display behavior as the remaining
specification item. PQ2: use a per-group hash chain for the audit trail so
tampering is detectable even if someone has database-level access; keep
identities out of the hashed audit data by storing IDs only. PQ3: use
erasure tombstones that are re-applied when backups are restored, together
with a limited backup-retention period; per-user encryption keys may be used
later for especially sensitive fields but are not the primary erasure
mechanism. PQ4: yes, generate exports on demand, provide short-lived secure
download access, and do not retain completed export files on the server
beyond what is necessary for delivery. Record these as the next prompt in
prompts/development-log.md, keep intent/intent.md, docs/domain.md, and
docs/glossary.md frozen, do not choose technologies yet, and continue to the
recurring-draft scheduling and notification pipeline cluster.
```

### Decisions

1. **PQ1:** with records holding references, placeholder history shows the
   claiming User's current identity by default. The exact display behaviour
   remains a specification item.
2. **PQ2:** a per-group hash chain over audit entries makes tampering
   detectable even with database-level access. Hashed audit data holds IDs
   only, never identities.
3. **PQ3:** erasure tombstones are re-applied after any backup restore, with
   limited backup retention. Per-User encryption keys may be added later for
   especially sensitive fields; they are not the primary mechanism.
4. **PQ4:** exports are generated on demand and delivered through
   short-lived secure download access. Completed export files are not kept
   beyond delivery.
5. PH1–PH3 (Prompt 030) are adopted as the direction, with these choices.

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

The privacy and history cluster is settled as a direction. No technology
chosen.

### Corrections

None.

## Prompt 032 — Architecture discovery: recurring-draft scheduling and notification pipeline (AD17, AD18)

### Phase

Architecture discovery

### Purpose

Analyze how drafts and reminders are scheduled, and how notifications flow
from domain events to delivery, under the frozen domain and all decisions
so far.

### Prompt

The continuation instruction in Prompt 031: "continue to the
recurring-draft scheduling and notification pipeline cluster."

### Decisions

Proposed, not final:

- **SR1:** occurrences are a deterministic date sequence per series. A
  Draft's identity is (series, occurrence date), which is unique and makes
  production idempotent. Produced by a periodic sweep with a cursor per
  series. Producing lazily when someone reads is a possible complement.
- **SR2:** producing a Draft is a System command through the standard
  pipeline (P4), under the group lock. The archive pause and series state
  are checked consistently.
- **SR3:** the calculation core and System commands receive "now" in the
  command, never from a system clock. Occurrence dates are computed in an
  explicitly stored time zone.
- **SR4:** a Draft is produced on its occurrence date, not ahead of it, so
  confirming can never create a future Expense date (domain §5.1.7).
- **SR5:** a Draft copies the series' payers and participants when it is
  produced, and has Versions, so the record-version rule applies when it's
  edited. Confirming it is a normal Expense-recording command: the rotation
  is used at commit, and it is confirmed at most once.
- **NP1:** the pipeline runs: outbox events → notification planner →
  delivery per channel → notification history and in-app inbox.
- **NP2:** recipients are worked out from the committed state at the
  event's ledger version, using the domain rules and the visibility rules
  (P12). Placeholders and deleted Users are never recipients. Content is
  shown with current identities when sent or read (PH1), so anonymized
  people appear under their labels.
- **NP3:** payloads and history hold IDs only. Content is minimized.
- **NP4:** at-least-once delivery from the outbox, de-duplicated by (event,
  recipient, channel). Retries with backoff and a queue for failed
  deliveries. Delivery never blocks the ledger.
- **NP5:** fan-out to 100 members (the test ceiling), with an optional
  digest window. The policy is for specification.
- **NP6:** a provider abstraction per channel. In-app as the baseline
  channel. Channels are open (intent §11.3).
- **NP7:** settle-up reminders come from a scheduled evaluator over the
  cached debts. Idempotent per (member, reminder period). Trigger and
  cadence are for specification.
- **NP8:** erasure: pending notifications to a deleted User are cancelled;
  notification history and provider logs are in the data inventory (PH3).

No genuine conflict with the frozen documents was found. Checked: domain
§5.3 (drafts don't affect debts and don't expire; they pause when the group
is archived), §5.1.7 (no future Expense date), §5.8.4 and §5.5.8 (the
notifications the domain requires), and §9 (Former-member visibility).

**Questions put to the user:**

- **NQ1:** which time zone governs occurrences and the "no future Expense
  date" check (Q-J)?
- **NQ2:** when a group is unarchived, are Drafts produced for occurrences
  missed while it was archived?
- **NQ3:** are notifications the domain requires (the Former-member debt
  notice, Intermediate-member routed notice, payment-details change)
  always delivered at least in-app, regardless of preferences?
- **NQ4:** confirm that Drafts are produced on the occurrence date, not
  ahead of it.

### Artifacts

- `prompts/development-log.md`: this entry and Prompt 031.

### Outcome

Delivered in chat. NQ1–NQ4 await the user.

### Corrections

None.

## Prompt 033 — Scheduling and notification decisions (NQ1–NQ4)

### Phase

Architecture discovery

### Purpose

Settle the scheduling and notification questions from Prompt 032.

### Prompt

```text
Use these decisions for the scheduling/notification cluster: NQ1: Use a
time zone configured per group for recurring occurrence dates and the
"expense date cannot be in the future" check. This keeps occurrence dates
consistent for all members. NQ2: Yes. When a group is unarchived, produce
drafts for occurrences that became due while it was archived. Use the
unique (series, occurrence date) identity so retries and repeated
unarchiving cannot create duplicates. NQ3: Yes. The domain-required
notifications must be delivered at least in-app regardless of ordinary
notification preferences: former-member debt changes, routed-settlement
changes affecting intermediate members, and payment-details changes.
Optional channels remain preference-controlled. NQ4: Yes. Produce drafts on
the occurrence date, not ahead of it, because confirming an early draft
could create an expense dated in the future. Also keep the currently open
items as spec questions, not architecture decisions: schedule/frequency
definitions, series states, effects of editing a series on existing drafts,
reminder triggers/frequency, and notification digest policy. Record these as
the next prompt in prompts/development-log.md. Keep intent/intent.md,
docs/domain.md, and docs/glossary.md frozen. Do not choose technologies yet.
Then continue with AD19/AD23: outside providers and operations.
```

### Decisions

1. **NQ1:** a time zone configured per group governs occurrence dates and the
   "Expense date not in the future" check.
2. **NQ2:** unarchiving produces Drafts for occurrences that fell due while
   the group was archived. The unique (series, occurrence date) identity
   makes retries and repeated unarchiving duplicate-free.
3. **NQ3:** the notifications the domain requires (Former-member debt
   changes, routed-settlement changes for Intermediate members,
   payment-details changes) are always delivered at least in-app,
   regardless of preferences. Optional channels follow preferences.
4. **NQ4:** Drafts are produced on the occurrence date, never ahead of it.
5. **Kept as specification questions:** schedule and frequency definitions,
   series states, how editing a series affects existing Drafts, reminder
   triggers and frequency, and the notification digest policy.
6. SR1–SR5 and NP1–NP8 (Prompt 032) are adopted as the direction.

### Gap in the frozen domain flagged for human review (not resolved, documents unchanged)

- **FR-3: the group time zone is not in the frozen domain model.**
  `docs/domain.md` §2 describes the Group as having a Group currency,
  Rotation order, Rotation position, archived flag and Members. It has no
  time zone. NQ1 makes a group time zone a business-relevant setting. It
  decides occurrence dates and whether an Expense date counts as "future".
  Unresolved:
  - who sets it (the group creator at creation? Admins?);
  - whether it can change after creation, and what that does to future
    occurrences and to the future-date check;
  - confirmation that changing it is audited as a group setting (§10.3
    already audits "Group settings").

  This is an addition, not a contradiction, and needs explicit review
  before `docs/architecture.md` relies on it.
- **Consequence noted:** a member far from the group's time zone may find
  their own local "today" counted as a future date and rejected (for
  example, ahead of the group's zone). A UX/specification point.

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

The scheduling and notification cluster is settled as a direction, with
FR-3 pending review.

### Corrections

None.

## Prompt 034 — Architecture discovery: outside providers and operations (AD19, AD23)

### Phase

Architecture discovery

### Purpose

Analyze outside dependencies and operational requirements under the frozen
domain and all decisions so far.

### Prompt

The continuation instruction in Prompt 033: "Then continue with AD19/AD23:
outside providers and operations."

### Decisions

Proposed, not final:

- **Outside providers (AD19):** every provider sits behind an internal
  interface, integrates through the outbox where it is asynchronous, and is
  covered by a data processing agreement, the residency requirement (Q-M)
  and the erasure inventory (PH3).
  - **OP1 sign-in:** maps the provider's account ID to an opaque User ID;
    stores minimal attributes; deletion at the provider is part of erasure.
    Sign-in availability limits overall availability.
  - **OP2 receipt storage:** private object storage; access through
    short-lived signed links checked against the visibility rules (P12);
    malware scanning; uploads not yet attached to an Expense expire.
  - **OP3 receipt OCR:** asynchronous and advisory only. It pre-fills the
    expense form; a member always confirms; failure falls back to manual
    entry. It never blocks or writes to the ledger.
  - **OP4 notification delivery:** through the channel adapters (NP6).
  - **OP5 reference data:** a versioned ISO 4217 snapshot (precision captured
    per group, LQ4) and a versioned IANA time-zone database (NQ1).
  - **OP6 analytics:** for the success metric; IDs only; consent where
    required; metric definitions still open.
- **Operations (AD23):**
  - **OPS1 availability (99.95%, about 22 minutes a month):** redundancy,
    deployments and schema changes without downtime, and an error-budget
    policy. In degraded mode the ledger stays authoritative while
    suggestions and notifications may lag.
  - **OPS2 durability:** committed financial records are never lost
    (intent §9), which suggests a data-loss target of zero for committed
    records; point-in-time recovery; restore drills that include re-applying
    erasure tombstones (PQ3); limited backup retention.
  - **OPS3 continuous verification:**
    - cached totals equal the journal sums (LC4);
    - each Version's journal entries equal its locked results;
    - members' net balances in each group add up to zero;
    - the audit hash chains verify (PQ2).

    A mismatch raises an incident and the caches are rebuilt from the
    journal. Nothing is ever silently fixed.
  - **OPS4 monitoring:** logs, metrics and traces without personal data
    (IDs only), with short retention.
  - **OPS5 releasing a new calculation algorithm version:** old versions are
    kept for verification; data migrations never rewrite locked results
    (LQ3).
  - **OPS6 security operations:** encryption in transit and at rest; extra
    protection for payment details; production access by operators is
    limited and audited separately from the group Audit trail; a breach
    notification process (GDPR/DPDP).
  - **OPS7 capacity:** a test ceiling of 100 members; a budget for
    background suggestion generation; scheduler sweeps.

No genuine conflict with the frozen documents found.

**Questions put to the user:**

- **OQ1:** recovery targets: a data-loss target of zero for committed
  financial records, and how quickly service must be restored (Q-M).
- **OQ2:** a Receipt removed from an Expense: is the file kept as part of
  history, or deleted?
- **OQ3:** sign-in: buy a managed identity provider (behind an internal
  interface) or build it in-house? This is not a choice of specific
  technology.
- **OQ4:** confirm OCR is advisory only (pre-fills; a member confirms;
  never authoritative).
- **OQ5:** operator access to production data: limited, time-bound and
  audited separately.

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

Delivered in chat. OQ1–OQ5 and FR-3 await the user.

### Corrections

None.

## Prompt 035 — FR-3 proposed domain decision; providers and operations decisions (OQ1–OQ5)

### Phase

Architecture discovery

### Purpose

Record the user's proposed resolution of FR-3 (group time zone) and settle
the questions from Prompt 034.

### Prompt

```text
For FR-3, use this as the proposed domain decision for my review: the group
creator sets the group's time zone when creating the group; admins may
change it later; the change is audited as a group-setting change; and
changing the time zone affects only future/unoccurred recurring
occurrences. Do not modify docs/domain.md or intent/intent.md yet; record
this as a proposed domain decision and flag that explicit domain approval is
required before it becomes authoritative. For OQ1, target zero data loss
for committed financial records and recovery within minutes, subject to the
detailed recovery objectives being specified later. For OQ2, keep receipt
files while the expense exists and preserve receipts referenced by
historical versions/audit history; account deletion does not remove
receipts, consistent with the earlier decision. For OQ3, use a managed
sign-in/identity provider behind an internal interface rather than building
authentication infrastructure ourselves. Do not choose a specific provider
yet. For OQ4, yes: OCR remains advisory only, asynchronously pre-fills data,
always requires member confirmation, and never writes directly to the
ledger or blocks expense recording. For OQ5, yes: production-data operator
access should be limited, time-bound, and separately audited from the
group's domain audit trail. For OP1–OP6 and OPS1–OPS7, the proposed
architecture is consistent [...]. Record these decisions in
prompts/development-log.md. Keep intent/intent.md, docs/domain.md, and
docs/glossary.md frozen unless I explicitly approve reopening the domain for
FR-3. Do not choose concrete technologies yet. Then continue with AD1 and
AD2: deployable system boundaries and client preview architecture.
```

### Decisions

- **FR-3: PROPOSED domain decision, not authoritative.** The group creator
  sets the group's time zone when creating the group. Admins may change it
  later. The change is audited as a group-setting change. A change affects
  only recurring occurrences that haven't happened yet. **Explicit domain
  approval is required** before this becomes authoritative. Until then
  `docs/domain.md` and `intent/intent.md` stay unchanged, and architecture
  may rely on it only provisionally.
- **OQ1:** zero data loss for committed financial records, and recovery
  within minutes. Detailed recovery objectives to be specified.
- **OQ2:** receipt files are kept while the Expense exists, including
  receipts referenced by historical Versions or audit history. Account
  deletion does not remove receipts (consistent with R6).
- **OQ3:** a managed sign-in / identity provider behind an internal
  interface. No specific provider chosen.
- **OQ4:** OCR is advisory only. It pre-fills asynchronously, always needs a
  member's confirmation, never writes to the ledger and never blocks
  recording an Expense.
- **OQ5:** operator access to production data is limited, time-bound and
  audited separately from the group's domain Audit trail.
- OP1–OP6 and OPS1–OPS7 (Prompt 034) are accepted as the direction.

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

The providers and operations cluster is settled as a direction. FR-3 awaits
explicit domain approval.

### Corrections

None.

## Prompt 036 — Architecture discovery: deployable boundaries and client preview (AD1, AD2)

### Phase

Architecture discovery

### Purpose

Analyze how the system is split into deployable parts, and how the client
shows previews, under the frozen domain and all decisions so far.

### Prompt

The continuation instruction in Prompt 035: "Then continue with AD1 and
AD2: deployable system boundaries and client preview architecture."

### Decisions

Proposed, not final:

- **DB1:** everything that takes part in the group transaction (P4) stays
  in **one deployable core with a single transactional store**: groups and
  membership, the ledger (Expenses, Settlements, Write-offs, Disputes,
  Drafts), the journal and debt caches, the Audit trail, command IDs and the
  outbox. Splitting these apart would break atomicity (§12, P4) or need
  multi-step compensations.
- **DB2:** the shape is a modular monolith with enforced internal module
  boundaries, plus background workers from the same codebase:
  - processing the outbox and notifications;
  - generating suggestions;
  - the scheduler (drafts, reminders);
  - exports;
  - the erasure pipeline;
  - reconciliation and verification.

  Workers outside the transaction only read committed state and write
  non-authoritative caches or outside effects, so they could be split off
  later.
- **DB3:** the calculation core is a pure, versioned library (P10) shared by
  the core, the suggestion worker and (optionally) the client.
- **DB4:** the suggestion worker reads a committed snapshot at a ledger
  version and writes a cache keyed by (group, version). Validation when a
  suggestion is recorded always happens in the core, under the group lock
  (P11).
- **CP1:** the authoritative preview is a **server dry run**. It runs the
  normal command pipeline (checks before and after calculation, the
  calculation core) on a committed snapshot without the lock and without
  committing. It returns the outcome, the warnings, the authorization result,
  the ledger version and an **outcome fingerprint**. It never uses up
  rotation positions.
- **CP2:** a hybrid client. The client may show instant figures that don't
  depend on server state, but the exact cents (which depend on the rotation
  position), allocations and warnings always come from the dry run before a
  member confirms.
- **CP3:** committing sends the command ID, the record version being edited
  and the confirmed outcome fingerprint. The core recalculates under the
  lock. If a warned element differs, it returns "needs re-confirmation" with
  the new outcome (AD-Q2). Otherwise it commits.

No genuine conflict with the frozen documents was found.

**Questions put to the user:**

- **BQ1:** the deployable shape: a modular monolith plus workers (lean), or
  separate services?
- **BQ2:** the preview approach: a hybrid with the server dry run as the
  authoritative preview (lean)?
- **BQ3:** what goes into the warned-outcome fingerprint. Lean: the
  Overpayment amount, routed vs direct (which decides whether it can be
  edited), the restore warning about a Former member, and whether an admin
  is required. Not the exact Chains.
- **BQ4:** may the client show unconfirmed instant figures (for example
  equal shares before rotation cents are settled), or must every figure a
  member sees come from the server?

### Artifacts

- `prompts/development-log.md`: this entry and Prompt 035.

### Outcome

Delivered in chat. BQ1–BQ4 and FR-3 await the user.

### Corrections

None.

## Prompt 037 — System shape and preview decisions (BQ1–BQ4)

### Phase

Architecture discovery

### Purpose

Settle the questions from Prompt 036.

### Prompt

```text
For the current architecture questions, use these decisions: BQ1: choose one
application with enforced internal modules plus background workers using the
same codebase. Keep the transactional core together; workers handle
asynchronous work and may be separated into services later if scale
requires it. BQ2: yes, use a mixed preview model with the server dry run as
the authoritative preview. BQ3: re-confirm only when one of these warned
outcomes changes: the overpayment amount, whether the settlement is routed
or direct, whether the former-member debt restoration warning applies, or
whether an admin is required. Do not require re-confirmation merely because
exact settlement chains changed. BQ4: yes, the client may show clearly
marked provisional/rough figures while typing, but the confirmation screen
must always show the authoritative server dry-run figures. Record these
decisions in prompts/development-log.md. Keep intent/intent.md,
docs/domain.md, and docs/glossary.md frozen. Do not modify the frozen
documents. Before the final architecture is approved, explicitly check the
status of FR-3 and keep it provisional unless I explicitly approve reopening
the domain. Then proceed to the final architecture-discovery step: AD20
stack selection, but do not write docs/architecture.md yet. After the stack
discussion, perform the full cross-decision consistency review before
proposing the final architecture document.
```

### Decisions

1. **BQ1:** one application with enforced internal modules, plus background
   workers from the same codebase. The transactional core stays together.
   Workers may be split into services later if scale requires it.
2. **BQ2:** mixed previews, with the server dry run as the authoritative
   preview.
3. **BQ3:** re-confirmation is triggered only by a change in: the
   Overpayment amount; routed vs direct; whether the restore warning about
   a Former member's debt applies; whether an admin is required. A change
   in the exact Chains alone does not trigger it.
4. **BQ4:** the client may show provisional figures while typing, clearly
   marked as such. The confirmation screen always shows the authoritative
   dry-run figures.
5. DB1–DB4 and CP1–CP3 (Prompt 036) are adopted as the direction.
6. **Process:** check FR-3's status explicitly before the final architecture
   is approved. It stays provisional unless the user explicitly approves
   reopening the domain. Order of steps: stack discussion (AD20), then a full
   consistency review across decisions, then a proposal for
   `docs/architecture.md`.

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

The system-shape and preview cluster is settled as a direction. Stack
discussion follows (Prompt 038).

### Corrections

None.

## Prompt 038 — Architecture discovery: stack selection discussion (AD20)

### Phase

Architecture discovery

### Purpose

Turn the settled architectural direction into stack criteria and options,
for the user to decide. Nothing is chosen silently.

### Prompt

The continuation instruction in Prompt 037: "proceed to the final
architecture-discovery step: AD20 stack selection, but do not write
docs/architecture.md yet."

### Decisions

None. Options, criteria and recommendations were presented for decision:

- **Store:** a relational database with strong ACID guarantees, row or
  advisory locks for the group lock (AD-Q1), point-in-time recovery,
  synchronous standby for zero data loss (OQ1), privilege controls for
  append-only tables, and managed offerings in EU and India regions.
  Recommendation: managed PostgreSQL. Alternatives: MySQL, or distributed
  SQL if multi-region residency comes to dominate.
- **Queue and scheduling:** start with an outbox and job queue backed by the
  database (no separate broker). Add a broker later behind an interface.
- **Backend language:** needs typed domain modelling, native arbitrary-
  precision integers, and an ecosystem for web, workers and testing.
  Recommendation: TypeScript end to end, because a single language allows
  shared types and an optional shared core for provisional client figures.
  It requires strict rules that money is never held as a floating-point
  Number, only BigInt. Alternatives: Kotlin/JVM, Go, C#/.NET.
  **The team's skills decide this.**
- **Client:** a responsive web single-page app. React is assumed, since
  earlier phase prompts referred to "React components"; to confirm. WCAG 2.2
  AA tooling.
- **API style:** command-oriented HTTP/JSON with idempotency keys and dry-run
  operations. Endpoint design is deferred.
- **Providers:** managed sign-in, email delivery, OCR and object storage are
  shortlisted by criteria: data processing agreement, EU/India residency, a
  deletion API, MFA. The final vendor choice is deferred to implementation
  planning, after residency and DPA review.
- **Hosting:** a major cloud with EU and India regions, and a managed
  database with a synchronous standby. The region depends on launch
  markets (Q-M).

**Questions put to the user:**

- **SQ1:** the team's language skills and preference;
- **SQ2:** the database;
- **SQ3:** the backend language;
- **SQ4:** confirm React for the client;
- **SQ5:** cloud and region: choose now, or stay provider-neutral until
  launch markets are known;
- **SQ6:** an outbox backed by the database first;
- **SQ7:** specific vendors now or at implementation planning.

**FR-3 status:** still PROVISIONAL, waiting for explicit domain approval.

### Artifacts

- `prompts/development-log.md`: this entry and Prompt 037.

### Outcome

Delivered in chat. Waiting for SQ1–SQ7. The consistency review comes after
the stack decisions.

### Corrections

None.

## Prompt 039 — Stack decisions (SQ1–SQ7)

### Phase

Architecture discovery

### Purpose

Decide the stack (AD20) under the requirements already settled.

### Prompt

```text
For the stack, make the following decisions: SQ1 — Languages/platforms: Use
TypeScript across the project. I am comfortable with React, TypeScript,
Node.js and Express, so optimize for a single-language TypeScript stack
across the web client, backend, workers, shared domain types, and
calculation core. SQ2 — Database: I prefer SQLite for the initial
development and test environment because I want zero database-server
installation and a lightweight setup. Use SQLite through the Node package
better-sqlite3, with the persistence layer kept replaceable. However, do not
weaken the already-decided production requirements to accommodate SQLite.
In particular, 99.95% availability, zero loss of committed financial
records, standby/PITR recovery, and concurrent transactional group
processing remain mandatory. Therefore: Use better-sqlite3 + SQLite for
local development and automated tests. Evaluate the production database
against those requirements. If SQLite cannot satisfy them cleanly, use
managed PostgreSQL in production. Do not introduce PostgreSQL locally just
for development if SQLite can provide a realistic test environment. Keep the
repository/persistence design structured so moving from SQLite to PostgreSQL
does not contaminate the domain/calculation core. Use Drizzle only if it
genuinely helps maintain this portability and does not obscure the
SQL/transaction semantics we need. Do not add an ORM merely for abstraction's
sake. SQ3 — Backend: Use Node.js + Express + TypeScript. SQ4 — Client: Use
React + TypeScript for the web client. The calculation core and shared
domain types should be reusable between server, workers, and client where
appropriate. SQ5 — Cloud: Remain cloud-neutral for now. [...] SQ6 —
Outbox/jobs: Use the database-backed outbox and job mechanism initially. Do
not introduce Kafka, RabbitMQ, Redis, or another message broker unless a
later load/operational requirement actually justifies it. Keep the job
mechanism behind an internal interface so it can evolve later. SQ7 —
External providers: Do not choose specific vendors yet. [...] Additional
constraints: HTTP/JSON API with command-oriented operations. Idempotency
keys for commands. Server-authoritative dry-run previews. No endpoint design
decisions are required yet. No secrets or provider credentials in the
repository. Keep the architecture as simple as possible while satisfying the
already-approved requirements. Record these decisions in
prompts/development-log.md. Then run the full architecture consistency
review [...] Do not create or modify docs/architecture.md yet.
```

### Decisions

1. **SQ1 / SQ3 / SQ4:** a single-language TypeScript stack: Node.js +
   Express for the backend and workers; React + TypeScript for the web
   client. Shared domain types and the calculation core are reusable across
   server, workers and client where appropriate.
2. **SQ2:** SQLite through `better-sqlite3` for local development and
   automated tests, with a replaceable persistence layer. Production
   requirements are **not** weakened. The production database is evaluated
   against them, with managed PostgreSQL as the fallback if SQLite can't
   meet them cleanly. PostgreSQL is not added locally for development if
   SQLite gives a realistic test environment. Persistence must not leak into
   the domain or calculation core. Drizzle only if it genuinely helps
   portability without hiding SQL and transaction semantics.
3. **SQ5:** cloud-neutral. Cloud and regions are chosen during deployment
   planning, after launch markets and residency are decided.
4. **SQ6:** a database-backed outbox and jobs, behind an internal
   interface. No message broker unless a later requirement justifies one.
5. **SQ7:** no vendors chosen. Candidates are shortlisted later against the
   defined criteria.
6. **Constraints:**
   - HTTP/JSON API built around commands, with idempotency keys and
     server-authoritative dry runs;
   - no endpoint design yet;
   - no secrets or credentials in the repository;
   - the simplest architecture that meets the approved requirements.

### Artifacts

- `prompts/development-log.md`: this entry.

### Outcome

Stack decided as a direction. The consistency review followed (Prompt 040).

### Corrections

None.

## Prompt 040 — Full architecture consistency review (after the stack decisions)

### Phase

Architecture discovery → consistency review

### Purpose

Check every architecture decision (Prompts 020–039) for consistency with
each other, with the stack decisions, and with the frozen documents, before
proposing `docs/architecture.md`.

### Prompt

The review instruction in Prompt 039, which asked to verify specifically:
SQLite production suitability; TypeScript/Node support for the
deterministic core, BigInt, workers and client previews; a clean SQLite and
PostgreSQL persistence boundary; conflicts between earlier decisions and the
stack; FR-3 status; remaining decisions and contradictions. Do not create
`docs/architecture.md`.

### Decisions

None. Review findings:

1. **SQLite is suitable for local development and tests only.** It cannot
   cleanly meet production requirements:
   - an embedded single-file database with no built-in synchronous standby,
     so zero loss of committed records plus failover in minutes isn't
     achievable (the available replication add-ons are asynchronous, or
     replace `better-sqlite3`);
   - the app and workers would have to share one host, and the app can't
     scale horizontally;
   - a single writer for the whole database.

   **Per SQ2's own condition, production uses managed PostgreSQL.**
2. **TypeScript/Node supports the core, with required disciplines:**
   - money as BigInt only (a dedicated type; lint bans on `number` in money
     code);
   - the API encodes money as decimal strings (BigInt isn't valid JSON);
   - `better-sqlite3` must run in safe-integers mode, and PostgreSQL BIGINT
     values must be mapped explicitly to BigInt;
   - determinism: no clock and no floating point in the core; stable sorting
     and ordering rules;
   - suggestion generation in worker processes or threads;
   - the core is browser-compatible for provisional figures (BQ4).

   Time-zone data comes from the runtime's ICU data, so the Node and ICU
   version must be pinned or the time-zone data bundled (OP5).
3. **A shared persistence boundary is feasible, with conditions:**
   - an async unit-of-work and repository interface in the application
     layer, so the core never touches the database;
   - adapters per dialect, with migrations per dialect;
   - one shared contract test suite.

   **Differences the boundary must absorb:**
   - `better-sqlite3` is synchronous, and its `db.transaction()` can't
     contain awaits. An async unit of work therefore needs an in-process
     mutex plus `BEGIN IMMEDIATE`. Otherwise concurrent requests would mix
     their statements inside one open transaction.
   - The group lock: `SELECT … FOR UPDATE` on the group row in PostgreSQL;
     the global write lock in SQLite (correct, but stricter).
   - Claiming jobs: `FOR UPDATE SKIP LOCKED` in PostgreSQL vs an
     immediate-transaction claim in SQLite.
   - Append-only enforcement: triggers in both, plus revoked privileges in
     PostgreSQL.
   - Timestamps stored as UTC epoch integers, and JSON stored as text vs
     jsonb.
   - Default isolation: READ COMMITTED in PostgreSQL, so the explicit locks
     are required.
4. **Conflicts between stack and earlier decisions:**
   - No conflict for the production path with PostgreSQL: AD-Q1, P4, P5,
     OQ1/OPS2, PQ2, LC1–LC7, CP1 and BQ1 are all supportable.
   - **One tension:** SQLite's global write lock hides the concurrency
     behaviour that matters in production: per-group locks, lock ordering
     when work spans groups (account deletion), PostgreSQL isolation
     anomalies and job claiming. So SQLite isn't a *realistic* environment
     for concurrency tests. Raised as AR1 for the user.
   - The Drizzle question is open (AR2): its dialect-specific schemas don't
     remove the cost of two dialects, and its transaction behaviour with
     `better-sqlite3` and async code must be verified before adopting it.
5. **FR-3 (group time zone) is still PROVISIONAL.** No domain approval has
   been given. The frozen documents are unchanged.
6. **Remaining architecture-level items:**
   - **AR1:** test tiers. A PostgreSQL concurrency and integration suite
     in CI only (not installed locally)?
   - **AR2:** persistence tooling: no ORM (hand-written SQL per adapter)
     vs Drizzle vs a thin typed query builder.
   - **AR3:** money sent over JSON as decimal strings of smallest units.
   - **AR4:** confirm the allocation tie-break as a fixed total preference
     (a minimum-cost max-flow with a deterministic perturbation over stable
     member IDs). It was derived in Prompts 021 and 023 but never formally
     adopted.
   - **AR5:** pin the Node/ICU version or bundle the time-zone data.
   - **AR6:** the stronger order-safe suggestion generator is still a
     research workstream (not a blocker).

   Still-open specification items carried forward:
   - IMP-1, IMP-3, IMP-4;
   - Q-L duplicate settlements; Q-M recovery and residency details;
   - B-list items (names after a claim, revoking invitations, rotation
     position on rejoin/claim, series states, digest and reminder policy,
     zero shares, currency list, amount limits);
   - the time window for anonymization;
   - receipt limits; OCR languages; analytics definitions.

   **No contradiction between architecture decisions was found.**

### Artifacts

- `prompts/development-log.md`: this entry. `docs/architecture.md` not
  created.

### Outcome

Delivered in chat. AR1–AR5 await the user (AR6 is a workstream). The
architecture document waits for the user's approval.

### Corrections

- SQ2's production fallback is triggered by finding 1: managed PostgreSQL
  in production, SQLite for local development and tests only.

## Prompt 041 — AR1–AR6 decisions; docs/architecture.md created

### Phase

Architecture discovery → architecture document

### Purpose

Record the user's decisions on AR1–AR6 and write `docs/architecture.md` from
all approved architecture decisions.

### Prompt

```text
Approve the architecture decisions as follows: AR1 — Test tiers: APPROVED
[SQLite/better-sqlite3 for local development and most tests; PostgreSQL
integration/concurrency suite in CI; PostgreSQL not a local prerequisite].
AR2 — Persistence tooling: APPROVED [hand-written SQL behind thin typed
persistence interfaces; no Drizzle/ORM/query builder unless a later concrete
need justifies it; DB-specific SQL inside adapters; core DB-independent].
AR3 — Money JSON representation: APPROVED [decimal strings of smallest unit
at the API; BigInt/exact integers internally; never JavaScript number].
AR4 — Allocation tie-break: NOT APPROVED AS WRITTEN. Do not formally adopt
stable member IDs as a new allocation tie-break yet. Our previously
established allocation rules explicitly use the relevant rotation state to
break exact ties, and those rules are part of the frozen domain decisions.
[...] retain the prototype finding as an architecture/research finding;
preserve the already-approved rotation-based deterministic tie-breaking
rules; investigate whether the stronger order-safe suggestion generator can
satisfy the required guarantees while respecting those existing rules; if a
genuinely new tie-break rule is required, surface it as a domain-level
decision for my explicit approval before adopting it. AR5 — Runtime/time-zone
reproducibility: APPROVED [pin Node.js across dev, CI, production; record
time-zone data/runtime version]. AR6 — Stronger order-safe suggestion
generator: continue as a research workstream; not an architecture blocker;
must not delay the architecture document; do not change frozen domain
rules. Additional instruction: Keep intent/intent.md, docs/domain.md, and
docs/glossary.md frozen. Keep FR-3 explicitly marked as provisional. [...]
Do not silently convert any research finding into a product/domain rule.
Record these decisions in prompts/development-log.md. Now write
docs/architecture.md using all approved architecture decisions and clearly
distinguish approved decisions, provisional items, open specification
questions, and research workstreams. Do not resolve the remaining
specification questions by inventing requirements.
```

### Decisions

1. **AR1 approved:** SQLite / `better-sqlite3` for local development and
   most tests, plus a CI-only PostgreSQL suite for locking, isolation,
   concurrent commands, job claiming and multi-group operations.
   PostgreSQL is not a local prerequisite.
2. **AR2 approved:** hand-written SQL behind thin typed persistence
   interfaces. No ORM or query builder unless a later concrete need
   justifies one.
3. **AR3 approved:** money crosses the API as decimal strings of the
   smallest unit. Internally it is `BigInt`. Never JavaScript `number`.
4. **AR4 not approved:** stable member IDs are not adopted as an allocation
   tie-break. The prototype finding is kept as research only. Any new
   tie-break rule must come to the user as a domain-level decision.
5. **AR5 approved:** the Node.js version is pinned across development, CI and
   production, and the time-zone data / runtime version is recorded.
6. **AR6:** the stronger generator stays a research workstream. Not a
   blocker. No changes to domain rules.
7. FR-3 stays PROVISIONAL. Research findings are not turned into rules.

### Discrepancy flagged for human review (not resolved)

- **The AR4 instruction says "previously established allocation rules
  explicitly use the relevant rotation state to break exact ties".** In the
  frozen domain and the log, the rotation-based tie-break rules (Q-A, LQ2,
  FR-1) apply to **rounding** (shares and M2 Obligations). For allocation,
  `docs/domain.md` §5.5.5 requires only that Chains are reduced "in a
  deterministic order". Q-C adds "maximum amount, shorter chains first"; no
  rotation-based allocation tie-break was ever decided.
- So allocation has **no approved tie-break rule** beyond "deterministic".
  `docs/architecture.md` records this as **AT-1 (OPEN-ARCH, likely needs a
  domain decision)**, and states that the order-safety argument for
  suggestions (§7) depends on it. No rule was invented.

### Artifacts

- **Created `docs/architecture.md`:**
  - status legend: APPROVED / PROVISIONAL / OPEN-ARCH / OPEN-SPEC /
    RESEARCH;
  - drivers; system context; stack;
  - deployable shape and modules;
  - calculation core (contract, money, step 1, step 2, allocation);
  - consistency, concurrency and transactions (group lock, pipeline,
    operations spanning groups, authorization, previews, reads,
    idempotency);
  - suggested settlements;
  - persistence (adapters, dialect differences, test tiers);
  - ledger, history, privacy and audit;
  - recurring Drafts and notifications;
  - outside providers; operations;
  - provisional items (FR-3); open items (AT-1, the open specification
    list, research workstreams RS-1 and RS-2);
  - decision index linking back to log prompts.

  No endpoints, schemas or component designs.
- `intent/intent.md`, `docs/domain.md`, `docs/glossary.md`: unchanged.

### Outcome

The architecture document exists with Status Draft, waiting for review.
AT-1 needs the user's decision.

### Corrections

- The assistant's Prompt 040 AR4 wording ("adopt the tie-break as a fixed
  preference with stable member IDs") is withdrawn per the user. It is kept
  only as research finding RS-2.

## Prompt 042 — AT-1 allocation tie-break decided; architecture approved as baseline; specification phase begins

### Phase

Architecture → specification

### Purpose

Decide AT-1, approve the architecture baseline, and move to the
specification phase.

### Prompt

```text
For AT-1, make the following domain decision: AT-1 — Settlement allocation
tie-breaking. Settlement allocation must be deterministic. The existing
allocation rules remain the primary rules: 1. route as much as possible; 2.
prefer shorter chains where the existing rules require that; 3. when
multiple allocations remain equally valid under those rules, use a stable
deterministic ordering based on immutable member identifiers to break the
tie. The member-ID ordering is only a deterministic tie-break between
otherwise equally valid allocation choices. It must not replace the existing
"route as much as possible" or shortest-chain rules, and it must not affect
the rounding rules. Do not use the rotation pointer for
settlement-allocation tie-breaking. Rotation remains responsible for the
previously approved rounding tie-breaks only. The resulting allocation must
be reproducible for the same committed ledger state, algorithm version, and
command inputs. Record this as an approved decision in the development log
and update the architecture document accordingly. For the stronger
order-safe suggestion generator, keep the existing research workstream. The
generator must respect this allocation rule and the already-approved Q-C /
FR-2 behavior. Do not introduce additional product behavior merely to
optimize the number of settlements. Architecture approval: I approve
docs/architecture.md as the architecture baseline, subject to incorporating
the AT-1 decision above. Keep the following unchanged: intent/intent.md,
docs/domain.md, docs/glossary.md. FR-3: Do not reopen or modify the domain
for FR-3 yet. Keep the group-time-zone decision explicitly provisional. It
should remain listed as a provisional item requiring a separate explicit
domain approval. After incorporating AT-1, update the development log and
architecture document. Then treat the architecture as approved and move to
the specification phase. Do not resolve any remaining OPEN-SPEC questions by
inventing requirements. Carry them into specification discovery and
resolve them explicitly before implementation.
```

### Decisions

1. **AT-1 (approved, domain-level):**
   - Settlement allocation is deterministic.
   - The primary rules stay primary: route as much as possible; prefer
     shorter Chains.
   - Only between otherwise equally valid allocations, a stable deterministic
     ordering based on immutable member identifiers breaks the tie.
   - It never overrides the primary rules and never affects rounding.
   - **The rotation pointer is never used for allocation.**
   - Reproducible for the same committed ledger state, algorithm version
     and command inputs.
2. **The architecture is approved as the baseline**, with AT-1
   incorporated.
3. FR-3 stays **PROVISIONAL**, needing separate explicit domain approval.
4. RS-1 continues, and must respect Q-C, FR-2 and AT-1, without adding
   product behaviour to reduce the number of settlements.
5. **Specification phase begins.** Every OPEN-SPEC question is carried into
   specification discovery, and resolved explicitly before implementation.
   None is invented.

### Artifacts

- **`docs/architecture.md` updated:**
  - status: approved baseline;
  - §5.5: AT-1 as an approved decision, with an exact member-ID total order
    (a probabilistic hash perturbation alone isn't sufficient);
  - §7: the order-safety condition 3 now rests on AT-1;
  - §13 restructured: 13.1 lists approved decisions not yet in the frozen
    documents (AT-1; the FR-1 and FR-2 wording clarifications); 13.2 lists
    FR-3 as provisional;
  - §14.1: no open architecture items;
  - RS-1 bound to AT-1; RS-2 marked superseded by AT-1;
  - decision index updated.
- `intent/intent.md`, `docs/domain.md`, `docs/glossary.md`: unchanged.
  **Note:** AT-1 is a domain-level decision that the frozen `docs/domain.md`
  §5.5.5 doesn't yet state. It is recorded in architecture §13.1 for
  incorporation when the domain is next explicitly reopened.

### Outcome

The architecture baseline is approved. The specification phase starts with
a plan and a first round of ledger-critical questions (Prompt 042 reply).

### Corrections

- Prompt 041's AT-1 "open" status is closed by this decision.

## Prompt 043 — Specification Round 1 decisions (ledger: S1–S7)

### Phase

Specification discovery

### Purpose

Settle the ledger-critical specification questions.

### Prompt

```text
Proceed with the proposed specification phase and use the following
decisions for Round 1. S1 — IMP-1: non-financial expense edit: Choose (a).
If an expense version changes no financial inputs — including total amount,
payers, paid amounts, participants, or shares — preserve the previous
calculated financial allocation. Do not consume rotation positions or
recalculate rounding merely because a new version was created for a
non-financial change such as description/comments. If any financial input
changes, perform the normal calculation for the new version using the
applicable current rotation state. S2 — IMP-3: edit becoming routed: Choose
(a). An edit must not transform a direct settlement into a routed
settlement. If the edited amount would require routing because it exceeds
the applicable direct debt, reject the edit and require the member to delete
the existing settlement and record the intended settlement again. The
resulting new settlement must therefore go through the normal recording path
and confirmation rules. S3 — IMP-4: fallback dispute resolver: Choose (a).
If the dispute is eligible for the fallback resolver, an active registered
member other than the settlement creator and an admin may uphold the
dispute, and the settlement is deleted atomically as part of that
dispute-resolution command. The fallback resolver does not gain general
edit/delete authority over settlements. Their authority exists only for
resolving that specific eligible dispute. Record this as an explicit domain
decision. Do not broaden the resolver's general permissions. S4 — Q-L:
duplicate settlement warning: Use a warning rather than a hard block. For
the initial specification, use a 7-day window for detecting an existing
settlement with the same payer, recipient, and amount. The warning must not
prevent recording [...] Do not treat this as an exact duplicate-detection
guarantee [...]. S5 — N7: same-member settlement: Choose no. [...] Reject a
settlement where they are the same member. S6 — zero shares: Choose reject.
An expense must have an amount large enough that every participant receives
at least one smallest currency unit of share. [...] Return a clear
validation error [...]. This is a product/domain validation rule, not an
arithmetic limitation. [...] S7 — currencies: Use the active ISO 4217
currency set captured as a versioned snapshot when the group currency is
selected. [...] set the maximum amount for a single expense or settlement
to: 10^12 smallest currency units. The limit applies to a stored financial
amount, not to intermediate calculation values. [...] Do not use the amount
limit as justification for using JavaScript number [...]. Record these
decisions in the development log and carry them into the relevant
specifications. Continue with the next specification question round after
recording them. Do not modify the frozen intent, domain, or glossary
documents unless a decision is explicitly identified as requiring domain
reopening and I separately approve that reopening. Keep FR-3 provisional.
```

### Decisions

1. **S1 (IMP-1):** a new Expense Version that changes no financial input
   (total, payers, paid amounts, participants, shares) keeps the previous
   Version's calculated allocation. No rotation positions are used and no
   rounding is redone. If any financial input changes, the normal
   calculation runs with the current rotation state.
2. **S2 (IMP-3):** an edit may not turn a Direct settlement into a Routed
   one. If the edited amount exceeds the applicable direct debt, so that
   routing would be needed, the edit is rejected. The member deletes the
   settlement and records it again through the normal recording path and
   confirmation rules.
3. **S3 (IMP-4), an explicit DOMAIN decision:** when a Dispute is eligible
   for the fallback Resolver (an Active registered member who is neither
   the settlement's creator nor an admin), that member may uphold it. The
   Settlement is then deleted atomically as part of the resolution command.
   The fallback Resolver gets **no** general edit or delete authority. The
   authority covers only resolving that specific eligible Dispute.
4. **S4 (Q-L):** recording a settlement with the same payer, recipient and
   amount as one recorded within the previous **7 days** shows a warning.
   It never blocks, and it doesn't claim to detect duplicates exactly.
5. **S5 (N7):** a Settlement's payer and recipient must be different
   members. Otherwise the settlement is rejected.
6. **S6:** an Expense total must give every Participant at least one
   smallest unit (total ≥ number of Participants, in smallest units).
   Otherwise there is a clear validation error. A product/domain validation
   rule; the calculation core stays arbitrary-precision.
7. **S7:** group currencies come from the active ISO 4217 set in a
   versioned snapshot taken when the currency is selected. Precision is
   kept per LC1/LQ4. **The maximum stored amount for one Expense or
   Settlement is 10^12 smallest units.** Intermediate values are
   unbounded and exact. Money is never a JavaScript `number`.

### Artifacts

- `prompts/development-log.md`: this entry.
- `docs/architecture.md` §13.1: S3 added as an approved domain decision not
  yet in the frozen documents. S5 and S6 are listed there too, as domain
  validation rules. S1 is noted as a clarification of how A1 applies.
- Frozen documents unchanged. Specifications not yet written (specs/ to be
  created when drafting begins).

### Outcome

Round 1 settled. Round 2 (membership and invitations) was posed.

### Corrections

None.

## Prompt 044 — Specification Round 2 decisions (membership and invitations: S8–S14)

### Phase

Specification discovery

### Purpose

Settle the membership and invitation specification questions.

### Prompt

```text
I've reviewed Round 2. Record the following decisions: S8 — Placeholder
history: Choose (a). After a placeholder is claimed, historical records
display the claiming user's current name. The claim itself remains visible
through the audit trail. Do not display both the old placeholder name and
current user name throughout historical records. S9 — Invitations: Choose
(a). Invitations are addressed to a specific email address and use a
single-use invitation link. The recipient signs in or creates an account
before accepting. Treat the invitation email address as personal data and
include it in the appropriate erasure inventory/retention handling. Do not
implement shareable group links. S10 — Invitation expiry: Approve 14 days.
The specification should define the invitation lifecycle clearly, including
issued, accepted, revoked, expired, and any other necessary terminal states.
Do not allow an expired/revoked invitation to be accepted. S11 — Invitation
revocation: Approve any admin may revoke a pending invitation. A non-admin
member cannot revoke invitations. S12 — Pending items and group/member state
changes: Approve the proposed behavior: When a group is archived, pending
invitations are automatically revoked. When a placeholder is anonymized, its
pending claim invitation is automatically revoked. An open dispute in an
archived group is frozen. It cannot be resolved or withdrawn until the group
is unarchived. If the disputer becomes a former member, the dispute remains
open and follows the existing dispute-resolution rules. The former member
can no longer withdraw it. Make these transitions explicit and auditable
where appropriate. S13 — Rotation and longest-standing: Approve both
recommendations. A rejoining member retains their original rotation
position. A claimed placeholder retains the placeholder's existing rotation
position. "Longest-standing" is determined by original member join date and
therefore follows the established member/rotation ordering. Do not reset
these positions merely because membership temporarily changed state. S14 —
Placeholder display-name uniqueness: Approve the proposed validation rule.
Placeholder display names must be unique among active members of a group,
case-insensitively. Specify the behavior when a claimed user's current name
conflicts with another active member's display name. Do not silently invent
a resolution; surface this as a specification question if it is not already
covered. Record Round 2 as Prompt 044 in the development log. Do not create
the specification files yet. Continue to Round 3: recurring expenses, and
present the questions with recommendations before drafting specs. Keep the
frozen intent/domain/glossary unchanged and keep FR-3 provisional.
```

### Decisions

1. **S8:** after a claim, historical records show the claiming User's
   current name. The claim is visible in the audit trail. Records do not
   show both names.
2. **S9:** each invitation is addressed to a specific email address, with a
   single-use link. The recipient signs in or creates an account before
   accepting. The email address is personal data, covered by the erasure
   inventory and retention rules. No shareable group links.
3. **S10:** invitations expire after **14 days**. The spec must define the
   full lifecycle: pending (issued) → accepted / declined / revoked /
   expired, with every terminal state final. Expired and revoked
   invitations can never be accepted.
4. **S11:** any admin may revoke a pending invitation. Non-admins cannot.
5. **S12:**
   - archiving a group automatically revokes its pending invitations;
   - anonymizing a placeholder automatically revokes its pending claim
     invitation;
   - an open Dispute in an archived group is frozen (it can't be resolved or
     withdrawn) until the group is unarchived;
   - if the disputer becomes a Former member, the Dispute stays open under
     the existing rules, and they can no longer withdraw it.

   These transitions are explicit and audited where appropriate.
6. **S13:**
   - a rejoining member keeps their original rotation position;
   - a claimed placeholder keeps the placeholder's rotation position;
   - "longest-standing" means the original member join date, consistent
     with the rotation order;
   - positions are never reset by temporary changes in membership state.
7. **S14:** placeholder display names are unique among the group's active
   members, ignoring case.

   **Specification question surfaced (not resolved):** what happens when a
   claimed User's current name conflicts with another active member's
   display name. Also, whether uniqueness applies to registered members'
   names at all, since two registered Users can share a real name. Posed in
   Round 3 as S14b.

### Artifacts

- `prompts/development-log.md`: this entry.
- `docs/architecture.md` §13.1: S12, S13 and S14 registered as approved
  decisions that refine domain lifecycle and validation and aren't yet in
  the frozen documents.
- No specification files created. Frozen documents unchanged. FR-3
  provisional.

### Outcome

Round 2 settled. Round 3 (recurring expenses) was posed, together with
S14b.

### Corrections

None.

## Prompt 045 — Specification Round 3 decisions (recurring expenses: S14b, S15–S22)

### Phase

Specification discovery

### Purpose

Settle the name-conflict carry-over and the recurring-expense specification
questions.

### Prompt

```text
I've reviewed Round 3. Record the following decisions: S14b — Name
conflicts: Choose (a). Registered members do not need unique names.
Placeholder display names must remain unique among active members,
case-insensitively. A placeholder claim must never be blocked because the
claiming user's account name matches another member's name. When the UI
would otherwise be ambiguous, specify a minimal distinguishing detail in the
presentation layer rather than creating a separate per-group
identity/display-name system. [...] S15 — Recurring schedules: Approve the
proposed schedule set: weekly on a selected weekday; every N weeks; monthly
on a selected day; every N months; yearly. For monthly schedules using days
29–31, use the last day of the month when the selected day does not exist.
Do not narrow the first release unless implementation complexity later
demonstrates a concrete reason to do so. S16 — Series start/end: Approve. A
series requires a start date and may optionally have: an end date; or a
maximum number of occurrences. If neither is supplied, the series continues
until stopped. Specify validation so incompatible combinations are rejected
rather than silently interpreted. S17 — Series states: Choose (a): Active or
stopped. There is no user-controlled paused state. Archiving a group
temporarily prevents occurrence processing according to the existing
archived-group behavior; it does not change the series itself to a paused
state. Stopping a series is final and produces no further drafts. Drafts
already produced remain pending and can be confirmed, edited, or discarded
according to the normal draft rules. S18 — Editing a series: Approve. A
series edit affects only occurrences that have not yet been produced.
Already-produced drafts retain the series data copied into them at creation
and are independent of subsequent series edits. Series edits must be
audited. S19 — Series permissions: Approve. The series creator may edit or
stop their series while they remain an active member. An admin may manage
the series. No other member may edit or stop it. S20 — Series validation:
Approve, with this wording correction: [...] the total must provide every
participant at least one smallest currency unit, not necessarily one cent;
the total must not exceed the specified maximum of 10^12 smallest currency
units; every payer amount must be greater than zero; payer amounts must sum
exactly to the total; there must be at least one participant; all payers and
participants must be active members when the series is created/edited. If a
payer or participant later becomes a former member, the series remains
active and may continue producing drafts, but the affected draft cannot be
confirmed until it is edited into a valid state. Notify the series creator
and admins as already decided. Do not silently remove former members from
future series occurrences. S21 — Draft visibility/notifications: Approve.
[...] S22 — Confirmed expense date: Approve. A confirmed recurring draft
creates the expense using the occurrence date, not the confirmation date.
The occurrence date must never be in the future, consistent with the
existing expense-date rule. FR-3 constraint: Do not treat the group-timezone
behavior as permanently approved yet. Keep FR-3 explicitly provisional.
Where the recurring-expense specification depends on the group timezone,
mark that dependency clearly so the specification can be finalized once FR-3
is explicitly approved. Record these decisions as the next development-log
prompt and continue to Round 4: notifications and reminders. Do not create
the final specification files yet. Keep the frozen intent/domain/glossary
unchanged.
```

### Decisions

1. **S14b:** registered members' names need not be unique. Placeholder
   display names stay unique among active members, ignoring case. A claim
   is never blocked because of a name. Ambiguity is solved in presentation
   with a minimal distinguishing detail. There is no display name per
   group. The exact mechanism is a UX/spec detail.
2. **S15:** schedules are weekly on a chosen weekday, every N weeks, monthly
   on a chosen day, every N months, and yearly. For days 29–31, the last day
   of a month that doesn't have that day. The list isn't narrowed without a
   concrete reason.
3. **S16:** a start date is required. Optionally an end date **or** a maximum
   number of occurrences; with neither, the series runs until stopped.
   Combinations that don't fit together are rejected, never interpreted
   silently.
4. **S17:** a series is Active or Stopped, with no paused state. Archiving
   the group suspends occurrence processing without changing the series'
   state. Stopping is final. Drafts already produced stay pending under the
   normal Draft rules.
5. **S18:** an edit affects only occurrences not yet produced. Produced
   Drafts are independent of later edits. Series edits are audited.
6. **S19:** the series creator (while Active) or an admin may edit or stop
   a series. Nobody else may.
7. **S20:** a series follows the expense validation rules when created or
   edited:
   - at least one **smallest currency unit** per participant;
   - at most 10^12 smallest units;
   - every payer amount above zero, and payer amounts summing exactly to
     the total;
   - at least one participant;
   - all payers and participants Active.

   If someone later becomes a Former member, the series stays Active and
   still produces Drafts. Affected Drafts can't be confirmed until they're
   edited into a valid state. The creator and admins are notified. Former
   members are never removed silently.
8. **S21:** pending Drafts are visible to all Active registered members.
   When a Draft is produced, the series creator and admins are notified,
   under the mandatory in-app baseline.
9. **S22:** a confirmed Draft's Expense gets the occurrence date, which is
   never in the future.
10. **FR-3 dependency:** occurrence dates and the future-date check depend on
    the group time zone, which is still PROVISIONAL. The recurring spec must
    mark this dependency, so the spec can be finalized once FR-3 is
    explicitly approved.

### Artifacts

- `prompts/development-log.md`: this entry.
- `docs/architecture.md` §13.1: S17 (series states, the open item in domain
  §8.8) and S20 (series with Former members) registered.
- No specification files created. Frozen documents unchanged. FR-3
  provisional.

### Outcome

Round 3 settled. Round 4 (notifications and reminders) was posed.

### Corrections

- S20's wording was corrected by the user: "one smallest currency unit",
  not "one cent".

## Prompt 046 — Specification Round 4 decisions (notifications and reminders: S23–S31)

### Phase

Specification discovery

### Purpose

Settle the notification and reminder specification questions.

### Prompt

```text
I've reviewed Round 4. Record the following decisions: S23 — Notification
channels: Approve. First release supports: in-app notifications; email
notifications. Push notifications and SMS are deferred until a later
release. There are no native mobile apps in the first release. S24 —
Notification recipients: Approve the proposed recipient rules: [Expense
recorded/edited/deleted/restored → everyone whose debts are affected by the
old or new version, including relevant payers and participants, except the
member who made the change; Settlement recorded or changed → payer,
recipient, and any intermediate members affected by the settlement
allocation, except the member who made the change; Membership changes
(join, leave, removal, claim, role change, step-down) → all active
registered members of the group; Payment-details change → apply the
already-approved required-notification rule; Dispute raised or resolved →
the disputer, the settlement's creator, and the admins]. Use the group
state at the event version to determine recipients [...]. Deleted users and
placeholders are not recipients. S25 — Comment notifications: Choose (a).
There are no comment notifications in the first release. [...] S26 —
Notification preferences: Approve. [category: expenses, settlements,
membership, drafts, disputes, reminders; channel: in-app or email].
Required domain notifications remain enabled in-app and cannot be disabled.
Email for required notifications is enabled by default and may be disabled
by the user. Preferences affect delivery only; they never suppress required
in-app delivery. S27 — Notification batching: Approve. In-app immediate,
never batched. Email daily digest by default; users may choose immediate
email delivery instead. Required notices are included in the daily digest
unless the user has selected immediate email delivery. S28 — Settle-up
reminders: Choose (a). [...] weekly reminder to members who have at least
one pairwise debt that has been outstanding for more than 7 days. Users may
disable these reminders. Each reminder lists what the recipient owes to each
creditor and provides a path to settling up. Do not include settlement
suggestions [...]. Do not introduce manual reminder/nudge behavior in the
first release. S29 — Notification timing timezone: Approve (b). Add a
user-level time zone preference used only for notification scheduling,
including: email digest timing; reminder timing. This user time zone must
never affect: expense dates; recurring occurrence dates; debt calculations;
settlement allocation; ledger behavior; any other financial/domain
calculation. The group time zone remains a separate, provisional FR-3
decision [...]. S30 — Former members: Approve. Former members who left or
were removed retain accounts and receive only required notifications
concerning their own debts, through in-app and email according to their
notification preferences. They receive no other group notifications.
Deleted accounts and placeholders receive no notifications. S31 —
Notification history retention: Approve 90 days. [...] The append-only audit
trail remains the authoritative permanent record [...]; notification
history is not an audit record. Record the retention period explicitly as a
product decision so it is not confused with audit retention, which remains a
separate Round 5 decision. Record these as the next development-log prompt.
Then continue to Round 5: privacy and operations [...]. Do not create the
final specification files yet. Keep intent/intent.md, docs/domain.md, and
docs/glossary.md unchanged. If any Round 5 answer genuinely requires changing
a frozen domain decision, stop and surface that explicitly rather than
modifying the frozen documents.
```

### Decisions

1. **S23:** in-app and email in the first release. Push and SMS deferred. No
   native apps.
2. **S24:** recipients (decided from the group state at the event's
   version; never deleted Users or placeholders):
   - **Expense changes:** everyone whose debts the old or new Version
     affects, except the member who made the change.
   - **Settlement changes:** the payer, recipient and affected Intermediate
     members, except the member who made the change.
   - **Membership changes:** all Active registered members.
   - **Payment details:** the required-notification rule.
   - **Disputes:** the disputer, the settlement's creator and the admins.
3. **S25:** no comment notifications in the first release, anywhere.
4. **S26:** preferences by category (expenses, settlements, membership,
   drafts, disputes, reminders) × channel (in-app, email). Required
   notifications stay on in-app and can't be disabled. Email for them is on
   by default and can be disabled. Preferences affect delivery only.
5. **S27:** in-app is immediate, never batched. Email is a daily digest by
   default, or immediate if the user opts in. Required notices go in the
   digest unless the user chose immediate email.
6. **S28:** automatic reminders only. Weekly, to members with at least one
   pairwise debt outstanding for more than 7 days. Users can disable them.
   Each lists what is owed to each creditor, with a path to settle. No
   suggestions, no manual nudges.
7. **S29:** a **user-level time zone preference, used only for
   notification scheduling** (digest and reminder timing). It never affects
   Expense dates, occurrence dates, debts, allocation, the ledger or any
   domain calculation. The group time zone (FR-3) stays separate and
   provisional.
8. **S30:** Former members who left or were removed get only the required
   notifications about their own debts: always in-app, plus email according
   to their preferences (S26). Nothing else. Deleted accounts and
   placeholders get none.
9. **S31 (product decision): in-app notification history is kept 90 days,
   then deleted.** It is in the erasure inventory. It is **not** an audit
   record. Audit retention is a separate decision (Round 5).

### Artifacts

- `prompts/development-log.md`: this entry.
- No specification files created. Frozen documents unchanged. FR-3
  provisional.

### Outcome

Round 4 settled. Round 5 (privacy and operations) was posed.

### Corrections

- S30 is read together with S26 and NQ3: required in-app delivery can't be
  disabled; only email delivery follows preferences.

## Prompt 047 — Specification Round 5 decisions (privacy and operations: S32–S42); discovery complete

### Phase

Specification discovery

### Purpose

Settle the privacy and operations specification questions, and close
specification discovery with the remaining dependencies stated explicitly.

### Prompt

```text
I've reviewed Round 5. Record the following decisions and dependencies:
S32 — [24h normal, 72h hard limit, alert on breach; user-level gate
immediate; internal operational target, not the GDPR response deadline].
S33 — [no grace period; re-authenticate and explicitly confirm; immediate
and final; show open debts and offer a personal-data export first; no
cancel-within-N-days]. S34 — Placeholder erasure: Keep OPEN. Do not choose
(a) or (b) yet. The frozen domain currently permits placeholder
anonymization only by an admin. A separately audited operator action would
change the domain's authorization model and therefore requires an explicit
domain-reopening decision. Flag S34 as requiring legal/privacy review. Do
not modify docs/domain.md or intent/intent.md. The existing admin
confirmation that a placeholder erasure request was received remains valid
and is audited without storing the requester's personal data. S35 —
[JPEG, PNG, HEIC, WebP, PDF; max 10 MB per file; max 5 receipts per expense;
malware-failed files rejected; unattached uploads deleted after 24 hours;
product/operational limits]. S36 — [links expire after 5 minutes; Active
registered members view every receipt; Former members only receipts on
records behind their own historical debts; no separate receipt-specific
authorization model]. S37 — OCR [advisory only; may pre-fill total, date,
merchant/description; never writes financial data automatically; warns on a
different currency and does not populate the amount; no conversion; no
accuracy SLA; acceptance-without-editing metrics; 30-second limit then
manual entry; provider retains no data or only minimum transient data;
languages open pending Q-M]. S38 — Analytics success metric: Approve the
proposed definitions, but treat this as an explicit intent amendment. [User
= registered user who has recorded at least one expense; Returning =
authenticated session on a later calendar day within 30 days of the user's
first expense; IDs only; raw events 12 months; aggregates longer; deleted
accounts' events removed via erasure; first-party opt-out; prior consent a
market-specific legal-review dependency]. [...] record that S38 requires
explicit intent approval/amendment [...]. S39 — [RTO 15 minutes; RPO zero
for committed financial records; PITR 35 days; quarterly restore drills
including tombstone reapplication; derived data rebuilt after restore; keep
the 99.95% availability target separate from the 15-minute RTO]. S40 —
[audit trail retained for as long as the group exists, subject to legally
required erasure/anonymization; distinct from notification history,
operator-access logs, application logs; operator-access audit logs 1 year;
application logs 30 days; do not use the 90-day notification retention as
an audit rule]. S41 — [procedure structure approved; regulatory deadlines,
authorities, thresholds and user-notification requirements marked as
legal-review dependencies; not hard-coded as product guarantees]. S42 —
[personal-data exports: JSON and CSV; generated within 24 hours, normally
sooner; links expire after 24 hours; group exports same formats; on demand,
not retained beyond delivery]. Round 5 status: Specification discovery is
now complete except for explicitly unresolved dependencies: FR-3; S34; S38;
S37/Q-M; S41. Do not silently resolve any of these dependencies. Record
this as the next development-log prompt. Next, propose the exact list of
specification files under specs/, including the responsibility/boundary of
each file and which dependencies each one carries. Do not draft the spec
files yet. [...] Wait for explicit approval of the spec-file structure
before drafting.
```

### Decisions

1. **S32:** per-group anonymization normally finishes within 24 hours, with a
   hard limit of 72 hours and an operational alert if breached. This is an
   internal operational target, not the GDPR deadline. The User-level gate
   is immediate.
2. **S33:** no grace period. The user re-authenticates and explicitly
   confirms. Deletion is immediate and final. Open debts are shown and a
   personal data export is offered first.
3. **S34: OPEN.** Placeholder erasure when no admin acts, pending
   legal/privacy review. An operator action would need a domain-reopening
   decision. Admin confirmation that a request was received stays valid,
   and is audited without storing the requester's data.
4. **S35:** receipts may be JPEG, PNG, HEIC, WebP or PDF; at most 10 MB per
   file and 5 per Expense. Files failing the malware scan are rejected.
   Unattached uploads are deleted after 24 hours. These are product and
   operational limits.
5. **S36:** receipt links expire after 5 minutes. Access follows the
   existing record-visibility model: Active registered members see all
   receipts; Former members see only receipts on records behind their own
   debts. No separate receipt authorization model.
6. **S37:** OCR as listed in the prompt. Languages depend on Q-M.
7. **S38: requires an explicit INTENT AMENDMENT.** The success-metric
   definitions, retention, opt-out and legal-review dependency are as
   listed. They are not authoritative until the intent is amended with
   approval.
8. **S39:** recovery time 15 minutes; zero data loss for committed financial
   records; point-in-time recovery for 35 days; quarterly drills including
   tombstones; derived data rebuilt. Kept separate from the 99.95%
   availability target.
9. **S40:** the audit trail is kept for as long as the group exists, subject
   to legal erasure through anonymization. Operator-access logs are kept 1
   year, application logs 30 days. Notification history (90 days) is not an
   audit retention rule.
10. **S41:** the incident procedure's structure is approved. Legal specifics
    are legal-review dependencies, not product guarantees.
11. **S42:** exports as JSON and CSV, produced within 24 hours, with links
    expiring after 24 hours. Same formats for group exports. Not kept beyond
    delivery.

**Specification discovery is complete**, apart from these explicit
dependencies:

- **FR-3** (provisional; may need domain reopening);
- **S34** (open; legal review; option (b) needs domain reopening);
- **S38** (needs intent amendment);
- **S37 / Q-M** (OCR languages);
- **S41** (legal review).

### Artifacts

- `prompts/development-log.md`: this entry.
- `docs/architecture.md` §13.2 extended: S34 and S38 listed with FR-3 as
  dependencies awaiting separate explicit approval.
- No specification files created. Frozen documents unchanged.

### Outcome

The spec-file structure was proposed for approval (Prompt 047 reply).

### Corrections

- S39 terminology: the RTO is its own target, not derived from the
  availability budget, as the user corrected.
- S32 is an internal target, not the GDPR deadline, as the user corrected.

## Prompt 048 — Specification files drafted (17) and cross-spec consistency review

### Phase

Specification

### Purpose

Draft the approved 17-file specification structure, with the user's
boundary corrections, and run the cross-spec consistency review before
approval.

### Prompt

```text
Approve the proposed 17-file specification structure: README.md plus
00-cross-cutting.md through 15-operations-and-reliability.md. Before
drafting, make these boundary corrections: 1. 01-groups-and-roles.md: Do not
cite S13 as the source for admin succession. [...] only specify a succession
rule if it is already supported by an approved domain decision. Do not
invent a succession algorithm. 2. Retention ownership: feature specs own the
behavioral retention rule for the feature; 13-privacy-and-data-rights.md
owns the cross-system personal-data retention inventory and erasure mapping.
[10 owns 90-day notification history; 04 owns 24-hour unattached-upload
deletion; 12 owns audit retention; 13 records classification.] Do not
duplicate behavioral rules across files. 3. Audit/history versus privacy
workflow: Keep both files. [12 owns historical-record semantics; 13 owns the
privacy operation and references 12.] 4. Provisional/open dependencies: Keep
these explicitly marked: FR-3 PROVISIONAL; S34 OPEN (legal review, possible
domain reopening); S38 OPEN (intent amendment); Q-M / OCR languages OPEN;
S41 OPEN (legal review). Where FR-3 appears in a spec, make clear that the
referenced behavior is provisional and cannot silently become authoritative
through specification drafting. 5. General specification rules: [one owning
spec per fact; references not restatements; frozen glossary terminology; no
new requirements; unresolved items under Open Items; numbered testable
rules; Given/When/Then acceptance criteria; per-file approval status; README
organizational only]. [...] Now draft the 17 specification files using this
approved structure. For every dependency, preserve its status explicitly.
[...] After all files are drafted, stop before approval and perform the
planned cross-spec consistency review. Do not mark the specifications
approved until that review is complete and presented for human approval.
```

### Decisions

- **Correction 1 applied.** Admin succession (GR-9) cites domain §4.2.6 /
  R8, an approved domain decision. It refers to MI-20 (owned by `02`, from
  S13) only for what "longest-standing" means. No new algorithm.
- **Corrections 2–5 applied:**
  - retention behaviour stays in feature specs (NT-15, RO-4, AU-10), and
    `13`'s inventory (PR-14) refers to them;
  - `12` owns history semantics, and `13` refers to it (AU-7, AU-10);
  - dependencies are tagged on every affected rule;
  - the README is organizational only.

### Artifacts

**Created `specs/`** (17 files, all statuses "Reviewed (cross-spec review
complete; awaiting human approval)", except `14`, which is "Provisional"):

- README
- 00 cross-cutting (XC)
- 01 groups and roles (GR)
- 02 membership and invitations (MI)
- 03 expenses (EX)
- 04 receipts and OCR (RO)
- 05 recurring (RE)
- 06 debts and suggestions (DS)
- 07 settlements (ST)
- 08 disputes (DP)
- 09 write-offs (WO)
- 10 notifications and reminders (NT)
- 11 payment details (PD)
- 12 audit and history (AU)
- 13 privacy and data rights (PR)
- 14 analytics and success metric (AN, Provisional)
- 15 operations and reliability (OP)

The frozen documents are unchanged. FR-3, S34, S38, Q-M and S41 are
unresolved and tagged.

### Outcome

**Cross-spec consistency review**

- **Mechanical checks:** every rule reference resolves to a defined rule.
  FR-3 tags are on XC-18, GR-3, GR-4, EX-5, RE-8, RE-13 and RE-20, with
  provisional notes in AC-EX-6 and AC-RE-3. S34 is tagged on MI-24 and
  PR-11; S38 on all of AN; Q-M on RO-15; S41 on OP-11.
- **Defects found and fixed during the review:**
  1. XC-16 had extended the S7 limit to Write-offs and Paid amounts. It now
     covers only Expense totals and Settlement amounts, as decided; WO-3's
     reference was removed. A Write-off of a debt larger than 10^12 must not
     be blocked by a rule nobody decided.
  2. NT-6 had generalized "the member who made the change is not notified"
     to every event. It now applies only to the Expense and Settlement rows,
     as S24 said.
  3. Wrong rule ranges fixed (00 boundaries: GR-11 to GR-13; 02: PR-8 to
     PR-11).
  4. The last-admin leave restriction was stated in both GR-8 and MI-15. It
     is now owned by GR-8, and MI-15 refers to it.
  5. Missing intent constraints added: XC-20 (WCAG 2.2 AA, responsive web)
     and XC-21 (online only).
  6. Missing open items added: XC-OI-1 (sign-in requirements such as MFA
     and recovery), PR-OI-3 (minimum age and parental consent under DPDP).
  7. ST-3 tightened to "payer (Active) or Admin", matching domain §9.
- **Interpretations made while drafting, for human confirmation:**
  - AU-3 audits automatic Invitation revocations (S12 said "auditable where
    appropriate").
  - PD-2: only the owning User changes their own Payment details.
  - RE-5: both an end date and a maximum count, an end date before the start,
    a count or interval below 1, or a day-of-month outside 1–31, are all
    rejected.
  - DS-15: a stale set is never presented as current.
- **Open items across specs:** GR-OI-1, MI-OI-1 to MI-OI-3, EX-OI-1, RE-OI-1
  to RE-OI-3, DS-OI-1, WO-OI-1, NT-OI-1 to NT-OI-3, PD-OI-1, AU-OI-1,
  PR-OI-1 to PR-OI-3, AN-OI-1, OP-OI-1, XC-OI-1.
- **No contradiction** with the frozen documents, the approved architecture,
  or recorded decisions remains after the fixes.

### Corrections

- None beyond the review fixes listed above.

## Prompt 049 — Drafting interpretations approved; specs "Reviewed, awaiting implementation readiness"; open-item question round

### Phase

Specification

### Purpose

Record the approval of the four drafting interpretations and the reviewed
spec set, and pose a focused question round on every remaining open item.

### Prompt

```text
I've reviewed the drafted specifications and the cross-spec consistency
review. Approve the four drafting interpretations as written: AU-3 —
Invitation revocation auditability [approved; keep within existing audit
requirements; no additional audit behavior]. PD-2 — Payment-detail
ownership [approved; only the owning user may change their payment
details]. RE-5 — Recurring-series validation [approved: reject when both an
end date and a maximum occurrence count are supplied, if S16 defines these
as mutually exclusive; end date before start; interval or count below 1;
day of month outside 1–31; no other validation rules unless supported by an
approved requirement]. DS-15 — Stale suggestion sets [approved; an
out-of-date set must never be presented as the current set; it may be
identified as stale where permitted]. Specification approval: Approve the
specification set as Reviewed, awaiting implementation readiness, subject to
the open items below. Do not mark the affected specs as fully
implementation-ready where an unresolved open item is required to implement
their behavior. Keep 14-analytics-and-success-metric.md explicitly
Provisional [...]. Keep all other dependency markers unchanged [...]. Do not
modify the frozen intent, domain, or glossary. Do not commit anything yet.
Next step: resolve specification open items. [List: GR-OI-1 MFA and
account-recovery; MI-OI-1 invitation-email retention; MI-OI-2 other
invitation lifecycle events audited; MI-OI-3 ambiguous-name presentation;
EX-OI-1; RE-OI-1; RE-OI-2; RE-OI-3; DS-OI-1; WO-OI-1 / NT-OI-3; NT-OI-1;
NT-OI-2; PD-OI-1; PR-OI-1 unspecified retention periods; PR-OI-3; AN-OI-1;
OP-OI-1.] For each item: explain the concrete product/technical
consequence; give a recommended choice where a choice is appropriate;
identify whether the decision would modify an existing requirement, require
legal review, or is merely a specification detail; do not silently resolve
it. Record the question round in prompts/development-log.md. Do not create
tickets or implementation plans yet. After the open items are resolved,
update the affected specs, rerun the cross-spec consistency review, and then
present the final specification approval gate.
```

### Decisions

1. **Interpretations approved:**
   - AU-3: automatic invitation revocation is audited, within existing
     audit requirements;
   - PD-2: only the owner changes their Payment details;
   - RE-5, conditional on S16 mutual exclusivity, which is confirmed in this
     round;
   - DS-15: a stale set is never shown as current.
2. **Spec set:** "Reviewed, awaiting implementation readiness". `14` stays
   Provisional. Each spec now has an "Implementation readiness" line naming
   the open items that block it. Specs `07` and `08` have no open items.
3. All dependency markers are unchanged: FR-3, S34, S38, Q-M, S41.

### Artifacts

- Status and readiness lines updated in `specs/00`–`15`.
- The README's status conventions updated.
- Frozen documents unchanged. Nothing committed.

### Outcome

Question round posed.

**ID corrections noted:** the prompt's labels didn't match the drafted
specs, so the round uses the specs' IDs:

- MFA and recovery is **XC-OI-1**; **GR-OI-1** is group-name rules;
- **MI-OI-2** is the ambiguous-name display rule;
- **MI-OI-3** (= AU-OI-1) is auditing other invitation events;
- **PR-OI-1** is export contents; retention gaps are reviewed alongside it.

All are covered.

### Corrections

None.

## Prompt 050 — Open-item recommendations approved; specs updated; final cross-spec review

### Phase

Specification → final approval gate

### Purpose

Apply the approved open-item resolutions, rerun the cross-spec review, and
present the final specification approval gate.

### Prompt

```text
Approve all recommendations from the preceding specification review, exactly
as presented. Update the affected specification files accordingly and rerun
the full cross-spec consistency review. Do not introduce any new
requirements or decisions. Do not modify: intent/intent.md, docs/domain.md,
docs/glossary.md unless an explicitly approved reopening procedure requires
it. In particular, keep RE-OI-1, PR-OI-3, FR-3, S34, and S38 in their
previously stated provisional/reopening/legal-review states. After the
review, present the final specification approval gate for all specs,
including: status; implementation readiness; remaining blocker/dependency;
domain/intent reopening requirement; legal-review requirement. Do not create
implementation tickets yet. Wait for my explicit final approval of the
specification set.
```

### Decisions

All recommendations from Prompt 049 approved as presented:

- **XC-OI-1:**
  - XC-22 to XC-25: verified-email sign-in;
  - optional MFA;
  - email-based recovery through the provider;
  - signing in again before Payment-detail changes (PD-10). This modifies
    the PD flow; approved.
- **GR-OI-1:** GR-14, a required group name of 1–100 characters, not unique.
- **MI-OI-1:** MI-25, invitation email deleted 30 days after the Invitation
  ends (light legal check noted).
- **MI-OI-2:** MI-21, a join-order number for matching names; no partial
  emails.
- **MI-OI-3 / AU-OI-1:** MI-26 and AU-3, auditing Invitation issuance and
  Admin revocation (extends the R5 scope; approved). Declines and expiries
  are not audited.
- **EX-OI-1:** EX-23 (description required, 1–200 characters) and EX-24
  (Comments 1–2,000 characters).
- **RE-OI-1:** RE-24, Draft editing by the series creator (while Active) or
  an Admin. **Domain-level**; registered in architecture §13.1, pending
  incorporation into the frozen domain.
- **RE-OI-2:** RE-4, a yearly series on 29 February uses 28 February in
  non-leap years.
- **RE-OI-3:** RE-25, a past start date is allowed and all due occurrences are
  produced `[FR-3 PROVISIONAL]`. RE-5 mutual exclusivity of end date and
  maximum count is confirmed.
- **DS-OI-1:** DS-16, a "Suggestions updating…" indicator and no suggestions
  until a valid set exists.
- **WO-OI-1 / NT-OI-3:** WO-7 and an NT-6 row, the Former member is notified
  of a Write-off (optional, settlements category).
- **NT-OI-1:** NT-17, digest at 08:00, reminders Monday 09:00, in the User's
  time zone.
- **NT-OI-2:** NT-14, default time zone from the browser at sign-up, or UTC.
- **PD-OI-1:** PD-9, up to 5 labelled free-text entries.
- **PR-OI-1:** PR-12 export contents `[LEGAL REVIEW]`. Remaining retention
  gap closed by MI-25.
- **PR-OI-3:** PR-15, minimum age 18 with no parental-consent flow,
  **PROVISIONAL**: needs legal review and an intent amendment (narrows
  intent §3). Registered in architecture §13.2.
- **AN-OI-1:** AN-2 uses UTC calendar days (still provisional under S38).
- **OP-OI-1:** OP-12, just-in-time operator access with second-person
  approval, at most 4 hours, read-only by default.

### Artifacts

- `specs/`: 00, 01, 02, 03, 05, 06, 09, 10, 11, 12, 13, 14 and 15 updated,
  plus the README dependency register (PR-15, LEGAL REVIEW).
- Readiness lines updated.
- `docs/architecture.md` §13.1 (RE-24) and §13.2 (PR-15) updated.
- Frozen documents unchanged. Nothing committed.

### Outcome

**Final cross-spec review:**

- every rule reference resolves;
- no duplicate rule IDs;
- every spec open item is resolved or carried as a tagged dependency;
- the dependency tags are intact.

**One review finding for human confirmation:** the Prompt 049 recommendation
for WO-OI-1 said notifying the Former member "fits S30". It does not
exactly. S30 says Former members receive only *required* notifications, and
the Write-off notice is optional. Implementing the approved recommendation
therefore adds an explicit exception in NT-7. This was flagged at the gate,
not decided silently.

- **Fixes made during the review:** RE-26 renumbered to RE-25; PR-12 role
  wording clarified.

The final gate was presented. The user's explicit final approval is awaited.

### Corrections

- The assistant's Prompt 049 claim that the WO-OI-1 option (b) "fits S30" was
  inaccurate (see above).

## Prompt 051 — NT-7 exception confirmed (option a)

### Phase

Specification → final approval gate

### Purpose

Resolve the review finding from Prompt 050 about write-off notifications to
Former members.

### Prompt

```text
1. NT-7 exception: choose (a). Former members receive: the existing
required notices; and the optional write-off notification when their debt
is affected by a write-off. The write-off notification remains subject to
the existing notification-channel and preference rules. Do not add it to the
NQ3 universally required-notice list. Record this decision, update the
affected specifications, and rerun the consistency checks. Then show me the
final specification approval gate for all specs. Do not create
implementation tickets yet. I will explicitly approve the specification set
after reviewing the gate.
```

### Decisions

- **NT-7 exception confirmed (option a).** Former members receive the
  required notices (NT-2), plus the optional Write-off notification when a
  Write-off affects their debt. It follows the normal channel and preference
  rules. It is **not** added to the required-notice list (NQ3 / NT-2).

### Artifacts

- `specs/10-notifications-and-reminders.md`: NT-7 reworded to state the
  exception explicitly.
- `specs/09-write-offs.md`: WO-7 clarified as optional and subject to
  preferences.
- Frozen documents unchanged. Nothing committed.

### Outcome

Consistency checks rerun: all references resolve and there are no
duplicate rule IDs. The final approval gate was presented again, with no
remaining confirmation items. Waiting for the user's explicit approval.
No tickets.

### Corrections

None.

## Prompt 052 — Specification set approved

### Phase

Specification → approved

### Purpose

Final approval of the specification set.

### Prompt

```text
Approve the specification set as shown in the gate
```

### Decisions

- The specification set is **approved** as shown in the Prompt 051 gate.
- Specs `00`–`13` and `15`: status **Approved**. Each spec's "Implementation
  readiness" line still governs. Rules blocked by FR-3, S34, Q-M, S41, the
  PR-12 legal review or PR-15 stay blocked until their dependency is
  resolved.
- `14-analytics-and-success-metric.md` stays **Provisional** (S38).
- Domain items to carry into the frozen domain when it is next reopened:
  FR-3 (provisional), and AT-1, FR-1, FR-2, S1, S3, S5, S6, S12, S13, S14,
  S17, S20 and RE-24 (architecture §13.1).
- Intent items to carry into an intent amendment: S38 and PR-15.

### Artifacts

- `specs/00`–`13` and `15`: status lines set to Approved.
- Frozen documents unchanged. Nothing committed. No tickets created.

### Outcome

The specification phase is complete. The next phase (implementation tickets
for ready rules) awaits the user's instruction.

### Corrections

None.
