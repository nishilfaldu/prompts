# Agent 2 - prompting discussion (Sep 16 2026)

Same question as agents 0 and 1.

## The take

Partly - but better phrased: the main problem is usually underspecification, not bad wording. Developers know what they mean because of the context in their heads; the model only sees what gets externalized. "Refactor this properly" feels clear but hides decisions: preserve abstractions? optimize readability? avoid new dependencies? follow existing patterns? touch tests? change APIs?

Strong prompts make five things explicit: what you want, why you want it, relevant context, constraints, and what "good" looks like.

The second half: we should not expect humans to become amazing prompt writers. Good AI software should automatically gather context, infer defaults, expose ambiguity, and make intent easy to specify. Prompting is turning into context/interface engineering, not "find the magical sentence." Often the model was not confused - we gave it multiple reasonable interpretations and never said which one we wanted.

## The solution: an intent linter in front of every agent run

Detects two things before work starts:

1. **Underspecified intent** - multiple reasonable interpretations of the ask.
2. **Missing context** - the agent cannot make the right decision without information that exists elsewhere or only in your head.

Then surfaces it instead of silently guessing:

- "Ambiguous intent: 'clean up this component' could mean simplify the code, improve performance, or restructure the architecture. Assuming simplify without changing behavior."
- "Missing context: this change touches authentication, but I don't know whether backward compatibility with existing sessions matters. I can inspect the auth implementation before proceeding."

Key: do NOT make the user answer questions constantly. Decision hierarchy - infer -> inspect -> assume -> ask:

- Intent obvious: infer it.
- Answer exists in repo/docs/history: go find it.
- Reasonable low-risk default: state the assumption and continue.
- Only stop and ask when getting it wrong would materially change the outcome.

Plus a second check during execution, because ambiguity often appears only once the agent starts exploring the code: "Have I discovered information that changes my interpretation of what the user probably wants?" Low-risk: update the plan automatically. Material change to scope, architecture, behavior, or tradeoffs: explicitly notify.

Result: two automatic alerts - "I don't fully understand what you mean" and "I understand what you mean, but I don't have enough context to do it correctly." Reusable as a skill attached to every agent; makes agents less obedient to the literal prompt and more attentive to the actual intent behind it.

## Agent 2 vs agents 0 and 1 (quick map)

- All three converge on the same root: the spec in your head never made it out (agent 0's unwritten context, agent 1's underspecified problems, agent 2's underspecification).
- Agent 2's distinctive adds: the infer -> inspect -> assume -> ask hierarchy (ask only when material), the intent-linter framing as a reusable pre-flight skill, and the continuous mid-execution re-check.
- Agent 2 is the only one who said the burden should move off the human entirely: good software gathers context and exposes ambiguity itself.
