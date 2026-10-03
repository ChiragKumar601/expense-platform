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
