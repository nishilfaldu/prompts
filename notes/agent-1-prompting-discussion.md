# Agent 1 - prompting discussion (Sep 16 2026)

Same question as agent 0: "it's not confusing if its presented clearly" - is lack of clarity the main problem with prompting?

## The take

No - a partial truth being oversold. Many prompt failures are not clarity failures:

1. **Underspecified problems, not underspecified prompts.** The task itself is ambiguous (what counts as "good", undecided edge cases). No amount of prose clarity fixes that - decide the spec first.
2. **Context/attention limits.** A perfectly clear instruction buried in a long prompt still gets ignored or deprioritized. A retrieval/attention problem, not a clarity problem.
3. **Capability ceilings.** Some tasks are clearly specified and still fail because the model cannot do the reasoning.
4. **Distribution mismatch.** The model may never have seen anything like the clearly-stated task in training - clarity does not manufacture capability.

"Just be clearer" implicitly blames the user for failure modes that are architectural.

## Solutions per failure mode

1. **Underspecified problems** - fix before prompting: write a spec / acceptance criteria, decide edge cases yourself, or prompt the model to surface ambiguities first ("ask me clarifying questions first").
2. **Context/attention limits** - trim irrelevant history; put critical instructions at the start/end, not buried mid-prompt; use retrieval/RAG instead of dumping everything; break long tasks into smaller focused calls; repeat key constraints near the output point.
3. **Capability ceilings** - decompose into steps the model can actually do; offload reasoning it is bad at to tools/code execution (math, precise state tracking); use a stronger model; or accept human review/iteration loops instead of one-shot prompting.
4. **Distribution mismatch** - few-shot examples of the exact pattern wanted; fine-tune for recurring niche tasks; do not expect zero-shot success on genuinely novel formats.

Net: solving prompting is spec work + context engineering + task decomposition/tooling + examples. Language clarity is one of four, and often the smallest one.

## Agent 1 on its own standing rule (honest caveats)

Agent 1 proposed flagging before answering when (1) the problem is underspecified, (2) it is losing earlier context, (3) the task needs stronger capability than it has, (4) there is a distribution mismatch. Its own caveats:

- (3) and (4) cannot be reliably self-detected - it has no introspective access to its capability boundaries or training distribution. Treat those flags as hints, not diagnoses.
- (1) and (2) it can do well if told to.
- It cannot spin up subagents itself, but can say explicitly "this needs o1-style reasoning" or "this needs a bigger context window" so the human routes it.

## Agent 0 vs agent 1 (quick map)

- Agent 0's "unwritten context" ~= agent 1's "underspecified problems" - both: the spec in your head never made it out.
- Agent 0's "one-way communication" ~= agent 1's clarifying-questions fix.
- Agent 0's distinctive add: the verification loop (tests/lint/CI let the agent self-correct; gates > prose).
- Agent 1's distinctive adds: capability ceilings and distribution mismatch (failure modes no prompt can fix) + context/attention mechanics (position in prompt matters).
