# Raw transcript: agent 2 prompting discussion (Sep 16 2026)

**Nishil:** "it's not confusing if its presented clearly" - is this what the main problem is with prompting these days? That us developers/makers of software just aren't clear enough?

**Agent 2:** Partly - but I'd phrase it as: the main problem is usually underspecification, not bad wording.

Developers often know what they mean because they have a huge amount of context in their heads. The model only sees what you externalize. So a prompt like "refactor this properly" feels clear to you, but hides decisions like: preserve abstractions? optimize readability? avoid new dependencies? follow existing patterns? touch tests? change APIs?

The strongest prompting usually makes five things explicit: what you want, why you want it, relevant context, constraints, and what "good" looks like.

But there's an important second half: we probably shouldn't expect humans to become amazing prompt writers. Good AI software should automatically gather context, infer defaults, expose ambiguity, and make intent easy to specify.

So I think prompting is slowly turning into context/interface engineering more than "find the magical sentence." The problem often really is: the model wasn't confused; we gave it multiple reasonable interpretations and never told it which one we wanted.

**Nishil:** can we also solve both in a way that whenever those 2 problems arise you can automatically inform me about it? i want you to specify the solution of how we might solve this when i am operating agents

**Agent 2:** Yes. I'd solve it with an "intent linter" that sits in front of every agent run.

Its job is to detect two things before the agent starts doing work:

1. Underspecified intent - there are multiple reasonable interpretations of what you asked.
2. Missing context - the agent probably cannot make the right decision without information that exists elsewhere or only in your head.

Then instead of silently guessing, the system surfaces it.

For example:
"Ambiguous intent - 'Clean up this component' could mean simplify the code, improve performance, or restructure the architecture. I'm assuming simplify without changing behavior."
Or:
"Missing context - This change touches authentication, but I don't know whether backward compatibility with existing sessions matters. I can inspect the auth implementation before proceeding."

The important part is not making you answer questions constantly. The agent should use this hierarchy:

infer -> inspect -> assume -> ask

Meaning:

- If the intent is obvious, infer it.
- If the answer exists in the repo/docs/history, go find it.
- If there's a reasonable low-risk default, state the assumption and continue.
- Only stop and ask when getting it wrong would materially change the outcome.

For your coding agents specifically, I'd have a pre-execution step roughly like:

Before executing the task:

1. Translate the user's request into an explicit objective.
2. Identify decisions that are currently ambiguous.
3. Search the repository, git history, docs, existing patterns, and relevant skills for information that resolves those ambiguities.
4. Identify any important context that is still missing.
5. Classify every unresolved item:
   - safe to infer
   - safe to assume and disclose
   - must ask before proceeding
6. Tell the user only about assumptions or ambiguities that could materially affect the result.
7. Then execute.

And I'd add a second check during execution, because ambiguity often appears only once the agent starts exploring the code:

While working, continuously check:

"Have I discovered information that changes my interpretation of what the user probably wants?"

If yes:
- update the plan automatically when low-risk;
- explicitly notify the user when it materially changes scope, architecture, behavior, or tradeoffs.

So you effectively get two automatic alerts:

"I don't fully understand what you mean."
and
"I understand what you mean, but I don't have enough context to do it correctly."

That could become a reusable skill you attach to every agent. I think it would be particularly useful for you because you could make agents less obedient to the literal prompt and more attentive to the actual intent behind it.
