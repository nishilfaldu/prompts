# Flow documentation prompts

Use one of these prompts when you need a codebase map that explains how a feature works end to end. Choose the smallest version that will answer the question.

## Deep trace

Use this for security-sensitive systems, unfamiliar codebases, or flows with credentials, tokens, OAuth, async boundaries, retries, and several database records. This is the version to use for agent-passport.

> Write a markdown document that maps every `<system or feature>` flow end to end. Cover `<list the flows, entry points, or user actions>`.
>
> Trace the real code, not the intended architecture. For every flow, show the exact call chain with file paths and function names; where each credential, token, secret, identifier, and database record is created; what is stored, hashed, encrypted, returned, transmitted, read, updated, revoked, or deleted; and what each participant holds after every step. Include trust boundaries, transaction boundaries, network boundaries, retries, idempotency, expiry, failure recovery, and cleanup. Explain how related records are linked and what breaks if a step commits but its response is lost.
>
> Make it readable for someone who has only read parts of the code. Start with a glossary and system map, then use Mermaid sequence diagrams, state diagrams, call-chain tables, and per-flow timelines. For each flow, include: entry point, preconditions, step-by-step execution, database before/after state, client/agent/server state, failure branches, and links to the relevant code. End with one comparison table showing what is shared and what differs across the flows, plus any places where the implementation and docs disagree. Do not guess. Mark anything you cannot prove from the code.

## Standard map

Use this for a normal application when the broad architecture is understood but you need a reliable map of a feature before changing or reviewing it.

> Write a markdown guide to how `<feature>` works end to end across `<relevant folder or service>`.
>
> Follow the main user flows through the real code. Show the entry points, important function calls, external calls, database reads and writes, background jobs or events, and the state returned to the caller. Use real file paths and function names so I can click through. Include a simple diagram for each main flow and call out validation, permissions, transaction boundaries, and important failure paths.
>
> Write for someone who knows the application but has not read this area recently. Keep the overview short, spend detail on the parts that affect behavior, and mark anything the code does not make clear instead of filling gaps with assumptions.

## Quick orientation

Use this before opening an unfamiliar feature or when you only need enough context to start reading the right files.

> Give me a short markdown map of `<feature or folder>`. Identify the main entry points, the core call path, the important data models, external dependencies, and where state changes. Add one Mermaid diagram and a "read these files in order" list with a one-line reason for each file. Keep it focused on the happy path, then list only the failure paths or edge cases I should understand before making a change. Use real file paths and function names, and do not guess.

## Filling the placeholders

- `<system or feature>`: the capability you want explained, such as connection setup, billing, or document sharing.
- `<list the flows, entry points, or user actions>`: name every path that needs its own trace, such as create, claim, activate, rotate, reconnect, revoke, and expire.
- `<relevant folder or service>`: narrow the pass when possible. One focused area produces a better map than a repo-wide survey.
