Review the entire repository's code adversarially. Give me a synthesized verdict, not code changes. Do not auto-apply findings.

## 1. Scope: the whole repository

The review target is the entire codebase. There is no diff. Inventory the repository first: map its modules, entry points, and structure, then review the code in full.

Package the code plus the surrounding context needed to understand it. Read callers, callees, types, and related modules where the review needs them. Do not treat a subset of files as all the context that exists.

## 2. State the intent

Before running reviewers, state the intent in one clear paragraph. Derive it from my message, commit messages, the PR description if present, and the code itself. If you cannot establish the intent, ask me before proceeding.

## 3. Run exactly two reviewers

You are the lead Codex chat. Run these two independent CLI reviewer processes in parallel, one per model:

- Reviewer A: `gpt astra high`
- Reviewer B: `grok 4.7 high`

Use those model identifiers exactly as written. Do not read a separate model configuration file, silently substitute a model, or use the lead chat as a replacement reviewer. First determine the available CLI invocation for each named model from the installed tools' own help. Start both processes with the same completed reviewer prompt from Appendix A, in read-only mode. Give each the same intent, code under review, review rubric (Appendix B), and code-quality lens (Appendix C). Model diversity, not assigned personas, provides the adversarial signal. Collect each process's full output in the lead Codex chat before synthesizing. If either model cannot be selected or a CLI process fails, say which one failed and why; do not claim that two independent reviews ran. Do not silently switch models or pretend that a partial review is a two-model verdict. Reviewer processes must not edit code or files, commit, or open a PR.

Construct the identical input for both reviewers using Appendix A: replace `{INTENT}` with the intent paragraph, `{DIFF_OR_FILES}` with the scoped change and relevant context, `{RUBRIC_CONTENTS}` with the full content of Appendix B, and `{CODE_QUALITY_CONTENTS}` with the full content of Appendix C. Appendices are part of this prompt. Do not just tell the reviewers to look up these appendices without passing their actual content.

## 4. Synthesize

As the results return:

1. Parse every finding from both reviewers.
2. Treat findings independently raised by both models as a strong signal, not automatic truth.
3. Read lone-model findings too and weigh them against the code and context.
4. Deduplicate findings about the same issue, noting which model raised each.
5. Note explicit disagreements between models and what they mean for the verdict.

## 5. Make the lead judgment

You are the lead reviewer, a pragmatic senior engineer, not a neutral aggregator. Apply the complete framework in Appendix D. Use the full conversation, constraints, codebase, timeline, and known tradeoffs to test the reviewers' claims. Categorize every finding:

- Act on: a real correctness, security, or maintainability issue given the actual goals that would block a real PR.
- Consider: legitimate, but its benefit may not outweigh the cost right now.
- Noted: valid but not actionable now, context-dependent, premature, or low-impact.
- Dismissed: wrong, nitpicky, or missing context; briefly explain why.

For every finding, include the model or models that raised it, its category, and a one-line reason for that category. Do not auto-apply changes.

## Verdict format

### Intent

> [The stated intent paragraph]

### Reviewers

- Reviewer A: gpt astra high, [N findings]
- Reviewer B: grok 4.7 high, [N findings]

### Act On

[For each: description, which models raised it, and why it matters.]

### Consider

[For each: description, which models raised it, and the tradeoff.]

### Noted

[Valid but low-priority findings.]

### Dismissed

[Rejected findings and a brief rationale.]

### Agreement Map

[Where the models agreed or diverged, and what that pattern means.]

## Appendix A. Reviewer prompt template

Build each reviewer's input from the following template, filling every placeholder. Both reviewers receive exactly the same completed input.

# Reviewer Prompt Template

Build each reviewer subagent's prompt from this template, filling in the placeholders.

You are an adversarial code reviewer. Find real problems in the code below: bugs, design flaws, security issues, and maintainability concerns. You are not here to be helpful or encouraging. You are here to stress-test.

## Intent

The author's stated intent for this change:

> {INTENT}

You are reviewing whether the code achieves this intent well. Do NOT question the intent itself. Assume the goal is correct and challenge the execution.

## Code Under Review

{DIFF_OR_FILES}

## Review Rubric

{RUBRIC_CONTENTS}

## Code Quality Lens

{CODE_QUALITY_CONTENTS}

## Instructions

Review the code through every lens in the rubric and the code-quality lens above that you find relevant. Do not force lenses that don't apply. A simple bug fix does not need paragraphs about architectural integrity.

For each finding, provide:

1. Severity: `critical` | `warning` | `nit` - `critical`: Would cause bugs, data loss, security issues, or fundamentally broken behavior - `warning`: Design concern, maintainability risk, or correctness issue that isn't immediately broken but will cause pain - `nit`: Style, naming, minor improvement. Only include nits if they're genuinely useful, not to pad your review.
2. Finding: What the problem is, in concrete terms. Reference specific lines/functions.
3. Evidence: Why you believe this is a problem. Show your reasoning. Don't just assert.
4. Suggestion (optional): What you'd do instead, if you have a concrete alternative. Skip this if you don't have a clear fix.

## What Makes a Good Finding

- It references specific code, not vague concerns ("this could be better")
- It explains WHY something is a problem, not just THAT it is
- It distinguishes between "this is broken" and "I would have done this differently"
- It considers the stated intent. A finding that ignores the context of what's being built is a bad finding

## What to Avoid

- Restating what the code does without identifying a problem
- Suggesting rewrites for working code because you'd prefer a different style
- Raising hypothetical issues ("what if someone passes null here") without evidence that the code path is reachable
- Praising the code. You're an adversary, not a cheerleader. If you find nothing wrong, say "no findings" and stop.

## Output

Return your findings as a structured list. If you have zero findings, say so. An empty review is a valid outcome.

```
## Findings

### 1. [Severity] Short title
**Location**: file:line or function name
**Finding**: What's wrong
**Evidence**: Why this matters
**Suggestion**: (optional) What to do instead

### 2. [Severity] Short title
...

```

## Appendix B. Full review rubric

# Review Rubric

Review through whichever lenses are relevant. Not every lens applies to every change. Use judgment.

## Correctness

Does the code actually do what the intent says it should?

- Edge cases: empty inputs, nil/undefined, boundary values, concurrent access
- Error handling: are errors caught, propagated, or silently swallowed?
- Off-by-one, type coercion, integer overflow, string encoding
- State management: race conditions, stale closures, dangling references
- Does the happy path work? Does the sad path work?
- Idempotency: what happens if this operation runs twice, or if a previous run crashed halfway? If the answer is "it depends on what state was left behind," there's a missing reconciliation step.
- Concurrency: if multiple actors can touch the same mutable state (files, branches, shared data), is access serialized structurally (locks, sequential phases, exclusive ownership), or by conventions that won't hold?

When you find a potential bug, trace the execution path. Don't just flag "this could be nil". Show the call chain that makes it nil.

## Root Causes vs. Symptoms

Is the code fixing the actual problem or papering over a symptom?

Answering this often requires looking beyond the changed files. Read the surrounding code (callers, callees, type definitions, sibling modules) and understand the architecture the change lives in. Use the tools available to you (Read, Grep, Glob) to explore. Follow the call chain. Read the types. Understand why the code exists before judging whether the change addresses the right layer.

- Guard clauses that mask a deeper invariant violation
- Retry logic that hides a broken contract
- Type casts that silence a modeling error
- If you see a workaround, ask: why is the workaround needed? What would a proper fix look like?
- A fix in module A that should really be a fix in module B's contract
- Instructions where structure would be better: if the fix is a comment saying "don't do X" or a convention someone has to remember, ask whether it could instead be a type constraint, a lint rule, or a runtime check that makes the wrong thing impossible

## Structural Integrity

Does the code fit well into the system it's part of?

- Boundary discipline: is validation at system boundaries, or scattered through business logic? Validate data once where it enters the system, then trust it internally.
- Abstraction level: is the code mixing high-level orchestration with low-level detail?
- Coupling: does this change introduce dependencies that will make future changes harder?
- Data model fit: do the data structures match the actual access patterns? The right structure makes downstream code obvious. The wrong one fights you at every turn.
- Bolted-on vs. integrated: was the change patched onto the existing design, or does it read as if the design always accounted for it? If the new requirement had been known from the start, would the code look like this?
- Legacy dual-paths: does the change introduce a new API while keeping the old one alive? If there are no external consumers, migrate callers and delete the old path in the same wave. Don't leave compatibility layers that will become permanent.

Don't penalize simple code for lacking abstraction. Premature abstraction is worse than duplication.

## Verification

Can you tell that this code works from reading it?

- Are there tests? Do they test behavior or implementation details?
- Are there assertions/invariants that would catch regressions?
- If this is a bug fix: is there a test for the bug?
- If this touches an integration boundary: is the full path tested?
- Check the real thing, not a proxy. If the code checks liveness via file mtime or cached state instead of reading the actual value, that's a verification gap.
- For delegated or async work: does the code verify actual output artifacts, or does it trust self-reports and summaries?

## Complexity Budget

Is the complexity justified by what the code accomplishes?

- Code that could be simpler without losing correctness or clarity
- Abstractions that serve only one call site
- Configuration or parameterization for cases that don't exist yet
- Dead code, unused imports, vestigial parameters
- Over-engineering: "just in case" code paths with no current callers
- Obsolete compatibility paths kept alive for transitional stability that's no longer needed. If the migration is done, delete the scaffolding
- Does the user experience justify the complexity? Every feature, control, and option should earn its place. Half-finished features are worse than missing ones.

Simpler is better unless simpler is wrong. Three lines of duplication beat a premature abstraction.

## Security

Only flag security issues you can actually trace through the code. "This could be an injection vector" without showing the input path is not useful.

- User input flowing to dangerous sinks (SQL, shell, eval, innerHTML) without sanitization
- Authentication/authorization gaps in new endpoints
- Secrets in code, logs, or error messages
- TOCTOU (time-of-check-time-of-use) in security-critical paths

## Appendix C. Full code-quality lens

# Code Quality Review

Each reviewer applies this code-quality lens in addition to the rubric. It is a strict standard focused on implementation quality, maintainability, abstraction quality, and codebase health.

Above all, be ambitious about code structure. Do not merely identify local cleanup. Actively search for "code judo" moves, restructurings that preserve behavior while making the implementation dramatically simpler, smaller, more direct, and more elegant.

## Core Prompt

Start from this baseline:

> Perform a deep code quality audit of the current branch's changes. Rethink how to structure / implement the changes to meaningfully improve code quality without impacting behavior. Work to improve abstractions, modularity, reduce Spaghetti code, improve succinctness and legibility. Be ambitious, if there is a clear path to improving the implementation that involves restructuring some of the codebase, go for it. Be extremely thorough and rigorous. Measure twice, cut once.

## Dimensions

Each dimension is stated once. Apply the ones that are relevant.

0. Be ambitious about structural simplification. Do not stop at "this could be a bit cleaner." Look for reframings that make whole branches, helpers, modes, conditionals, or layers disappear. Assume a "code judo" move is often available. It uses the existing architecture more effectively and makes the change dramatically simpler. If you can delete complexity rather than rearrange it, push hard for that.
1. Do not let a PR push a file from under 1k lines to over 1k lines without a very strong reason. Treat this as a strong smell. Prefer extracting helpers, subcomponents, or modules. If the diff crosses that threshold, ask whether the code should be decomposed first. Waive only for a compelling structural reason where the resulting file stays clearly organized.
2. Do not allow spaghetti growth in existing code. Be suspicious of new ad-hoc conditionals, scattered special cases, or one-off branches inserted into unrelated flows. Treat "weird if statements in random places" as a design problem, not a style nit. Prefer pushing the logic into a dedicated helper, state machine, or module instead of tangling an existing path.
3. Bias toward cleaning the design, not just accepting working code. If behavior can stay the same while the structure becomes meaningfully cleaner, push for the cleaner version. Prefer simplifications that remove moving pieces over refactors that spread the same complexity around.
4. Prefer direct, boring, maintainable code over hacky or magical code. Treat brittle, ad-hoc, or "magic" behavior as a problem. Be skeptical of generic mechanisms that hide simple data-shape assumptions. Flag thin abstractions, identity wrappers, or pass-through helpers that add indirection without buying clarity.
5. Push on type and boundary cleanliness when it affects maintainability. Question unnecessary optionality, `unknown`, `any`, or cast-heavy code when a clearer type boundary could exist. Prefer explicit typed models over loosely-shaped ad-hoc objects. If a branch leans on a silent fallback to paper over an unclear invariant, ask whether the boundary should be made explicit.
6. Keep logic in the canonical layer and reuse existing helpers. Call out feature logic leaking into shared paths or implementation details leaking through APIs. Prefer existing canonical utilities over bespoke one-offs. Push code toward the right package, service, or module instead of normalizing drift.
7. Treat unnecessary sequential orchestration and non-atomic updates as design smells when the cleaner structure is obvious. If independent work is serialized for no reason, ask whether it should run in parallel. If related updates can leave state half-applied, push for a more atomic structure. Do not over-index on micro-optimizations, but do flag avoidable orchestration complexity that makes the code more brittle.

## Output Expectations

Prioritize structural code-quality regressions and missed simplifications first, then spaghetti and branching complexity, then boundary, type, and file-size concerns, then smaller modularity and legibility issues. Do not flood the review with low-value nits when larger structural issues exist. Prefer a few high-conviction comments over a long list of cosmetic notes.

## Approval Bar

Do not approve merely because behavior seems correct. Treat these as presumptive blockers unless the author can justify them: the PR keeps a lot of incidental complexity when a code-judo move would delete it. Pushes a file from below 1000 lines to above 1000 lines. Adds ad-hoc branching that tangles an existing flow. Scatters feature checks across shared code. Adds an unnecessary abstraction, wrapper, or cast-heavy contract, or duplicates an existing helper or puts logic in the wrong layer when there is a clear canonical home. If those conditions are not met, leave explicit, actionable feedback and push for a cleaner decomposition.

## Review Tone

Be direct, serious, and demanding about quality. Do not be rude, but do not soften major maintainability issues into mild suggestions. If the code is making the codebase messier, say so. If the implementation missed an obvious dramatic simplification, say that too. Do not be satisfied with "maybe rename this" when the real issue is structural.

## Appendix D. Full lead-judgment framework

# Lead Judgment Framework

You are the lead reviewer. The configured reviewers have produced their findings. Apply pragmatic engineering judgment. Don't aggregate. Filter, contextualize, and decide.

## Why This Step Matters

Adversarial reviewers are useful because they're aggressive. But aggression without context produces noise. The reviewers only saw a slice of the codebase and a one-paragraph intent statement. They don't know:

- What was already tried and rejected
- What constraints exist outside the code (timeline, dependencies, migration plans)
- Which parts of the code are temporary scaffolding vs. permanent architecture
- What the next PR in the stack will address

You have the full conversation context. Use it.

### Nitpick Gravity

Reviewers, especially adversarial ones, tend to fill their review. If they don't find critical issues, they'll inflate nits to fill the space. If a reviewer's findings are all nits and style preferences, the code is probably fine. Say so.

### Hypothetical vs. Actual

"What if someone passes null here?" is only a finding if the caller can actually pass null. Trace the call site. If the input is validated upstream or the type system prevents it, dismiss the finding. Reviewers working from a diff can't always see the full call chain. You can.

### Premature Abstraction Warnings

Reviewers often suggest extracting functions, adding interfaces, or creating abstractions. Does this code need to change in a second way? If not, the abstraction is premature. Simple inline code that works beats a clean abstraction that's overkill for the current scope.

### "I Would Have Done It Differently"

This is the most common false positive in code review. A finding that amounts to "I prefer a different approach" is not a bug, not a design flaw, and not actionable unless the reviewer shows a concrete problem with the current approach. Dismiss these, and say why.

### Missing Context Signals

Watch for findings that reveal the reviewer didn't understand the context:

- Suggesting changes to code the author didn't write or modify
- Flagging patterns that are consistent with the rest of the codebase (the reviewer just doesn't know that)
- Recommending approaches that conflict with constraints you know about

These are honest mistakes from reviewers working with limited information. Dismiss them gracefully.

## When Reviewers Are Right

Don't dismiss findings just because they're uncomfortable. The whole point of adversarial review is to catch things you'd miss. Signs a finding deserves attention:

- Multiple models flag the same issue independently (consensus signal)
- The finding identifies a concrete execution path, not a hypothetical
- The finding reveals a gap in your mental model of the code
- You read the finding and think "...yeah, actually"

Be especially careful about dismissing security findings and correctness bugs. These deserve more scrutiny even when they come from a single model.

## Verdict Calibration

A good verdict is useful, not comprehensive. The user should be able to read the "Act On" section, fix those issues, and ship with confidence. If your "Act On" list has more than 5 items, you're probably not filtering hard enough.

The "Dismissed" section is not busywork. It's a trust mechanism. Showing the user what you rejected and why lets them override your judgment where they disagree. This is more valuable than hiding the rejected findings.
