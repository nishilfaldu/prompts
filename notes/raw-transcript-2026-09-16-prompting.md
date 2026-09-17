# Raw transcript: prompting discussion (Sep 16 2026)

**Nishil:** I have a genuine curious question

**Instinct:** Go

**Nishil:** Answer honestly? Because its about you and agents

**Instinct:** Honestly, always. And if the honest answer is "I don't know," that's what you'll get. Shoot.

**Nishil:** "it's not confusing if its presented clearly" - is this what the main problem is with prompting these days? That us developers/makers of software just aren't clear enough?

**Instinct:** Honest take: clarity is table stakes, not the main problem. Three things beat it:

1. Unwritten context. Most "bad prompting" isn't unclear phrasing, it's stuff that exists only in the prompter's head. English can't fully specify behavior - that's literally why code exists. The skill isn't writing clearer, it's knowing what to specify (constraints, acceptance criteria, examples) and what to leave open.

2. The verification loop matters more than the prompt. A vague prompt with tests/lint/CI behind it beats a perfect prompt with nothing, because the agent can self-correct. Your husky hooks tonight enforce commit style no matter how any agent is prompted. Gates > prose.

3. It's one-way. "Not confusing if presented clearly" is true of people too, but people ask back. Agents mostly don't, so the prompt has to be complete on the first shot. The interesting products are the ones where the agent asks clarifying questions before burning an hour.

So no - developers aren't failing at clarity. They're failing to build the loop around the prompt.

**Nishil:** ok verification loop can be setup by pstack or manually. in your opinion how do I solve 1 + 3?

**Instinct:** For 1 (unwritten context) - the fix is treating wrong output as a context bug report. Every time an agent misses, don't just correct it, ask "what didn't it know?" and write THAT into the repo (AGENTS.md, decisions.md, or now your prompts repo). Context compounds when it's stored, evaporates when it's in chat. Shortcut you already have: you think out loud by voice. Record the brief as a voice note - people say 3x more context talking than typing - transcribe, that's the prompt.

For 3 (agents don't ask back) - force the question round. Two-phase prompting: phase 1 is "read the repo, ask me every question whose answer changes the implementation, propose an approach, stop." Phase 2 is implement, only after you approve. You already did this tonight instinctively - review first, then the scrap prompt implemented the review. Make it the default shape and 3 mostly disappears.

And honestly, 3 is a product gap more than a prompting gap. The agent that knows when to stop and ask is a thing worth building. You know where your ideas file is.

**Nishil:** leave "what can I build" out of this current discussion pls

**Nishil:** For 1 - I like voice, I do that already great. Sure I can also ask "what I didn't know" but can we reverse it to that agent becomes so adamant on knowing the full context before even beginning work is that possible? For 3 - this one too, can I somehow auto instruct the agent to do that by default?

**Instinct:** Yes to both, same mechanism: standing instructions instead of per-prompt instructions.

Put it in the always-on layer - AGENTS.md for CLIs, .cursor/rules (always apply) for Cursor. Something like:

"Before implementing anything: list the facts you relied on, the assumptions you're making, and the questions you can't answer from the repo. If any assumption touches a load-bearing choice, STOP and ask. Do not write code until answered. Propose an approach first, get approval, then implement."

That's both of your asks in one rule - it demands full context before starting (1) and makes two-phase the default (3).

Two honest caveats:

1. Instructions drift in long sessions. Rules ask, harnesses force. Cursor's Plan mode and Claude Code's plan mode are the enforced version of 3 - the agent literally cannot edit until you approve the plan. Default to plan mode and the rule becomes backup.

2. For true "every single prompt" coverage, Claude Code has hooks - a UserPromptSubmit hook injects text into every submission automatically. Cursor doesn't have that yet, rules are the closest.
