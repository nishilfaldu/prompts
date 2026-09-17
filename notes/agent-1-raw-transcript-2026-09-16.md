# Raw transcript: agent 1 prompting discussion (Sep 16 2026)

**Nishil:** "it's not confusing if its presented clearly" - is this what the main problem is with prompting these days? That us developers/makers of software just aren't clear enough?

**Agent 1:** No - that's a partial truth being oversold.

Clarity helps, but a lot of prompt failures aren't clarity failures at all:

- Underspecified problems, not underspecified prompts. Sometimes the task itself is ambiguous (what counts as "good," edge cases undecided) and no amount of prose clarity fixes that - you need to actually decide the spec first.
- Context/attention limits. A perfectly clear instruction buried in a long prompt still gets ignored or deprioritized. That's a retrieval/attention problem, not a clarity problem.
- Capability ceilings. Some tasks are clearly specified and still fail because the model can't do the reasoning, not because it didn't understand what was asked.
- Distribution mismatch. The model may have never seen anything like your clearly-stated task in training - clarity doesn't manufacture capability.

So "just be clearer" is good advice for the subset of failures caused by vague asks, but it's a comforting oversimplification when used as a general theory - it implicitly blames the user for every failure mode, including the ones that are architectural.

**Nishil:** ok now we if we were to solve all 4 what can we do?

**Agent 1:** For each:

- Underspecified problems - fix before prompting: write a spec/acceptance criteria, decide edge cases yourself, or interview-style prompt the model to surface ambiguities before it executes ("ask me clarifying questions first").
- Context/attention limits - reduce and structure what's in context: trim irrelevant history, put critical instructions at the start/end (not buried mid-prompt), use retrieval/RAG instead of dumping everything, break long tasks into smaller focused calls, repeat key constraints near the actual output point.
- Capability ceilings - decompose the task into steps the model can actually do, use tools/code execution to offload reasoning it's bad at (math, precise state tracking), use a stronger model, or accept some tasks need human review/iteration loops rather than one-shot prompting.
- Distribution mismatch - give in-context examples of the exact pattern you want (few-shot), fine-tune if it's a recurring niche task, or don't expect zero-shot success on genuinely novel formats.

Net: "solving prompting" isn't one lever - it's spec work + context engineering + task decomposition/tooling + examples. Clarity of language is only one of those four, and often the smallest one.

**Nishil:** can we also solve one by me doing something now that will make you ask / output something if you think the problem is underspecified or content/attention limit problems or somehow you accepting capability and suggesting or delegating to a subagent with a different higher model capability, or you asking if there seems to be a distribution mismatch and letting me know?

**Agent 1:** Setting up a lasting instruction for ongoing conversations.

Yes - this is just a standing instruction. Something like:

Before answering, flag if you think: (1) the problem itself is underspecified - ask me to clarify or state your assumption explicitly; (2) you're losing track of earlier context - say so and ask me to restate the constraint; (3) this needs stronger reasoning/capability than you have - say that and suggest what a stronger model/subagent should do instead of guessing; (4) this looks like a pattern you haven't seen much - flag the distribution mismatch instead of confidently guessing.

Caveats, honestly:

- I can't reliably self-detect (3) and (4) - I don't have introspective access to my own capability boundaries or training distribution. I can flag low confidence or this smells unusual, but not diagnose the cause accurately. Treat those flags as hints, not diagnoses.
- (1) and (2) I can actually do well if told to.
- "Delegating to a subagent with higher capability" - I can't spin one up myself in this interface, but I can tell you explicitly "this needs o1-style reasoning" or "this needs a bigger context window" so you route it.

Want me to save this as a standing preference so it applies automatically in future chats, or just for this session?
