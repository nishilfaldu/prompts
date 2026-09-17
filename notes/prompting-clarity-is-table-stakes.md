# Prompting: clarity is table stakes, not the main problem

Source: discussion with Instinct, Sep 16 2026.

## The question

"it's not confusing if its presented clearly" - is lack of clarity the main problem with prompting these days? Are developers just not clear enough?

## The take

No. Clarity is table stakes. Three problems beat it:

1. **Unwritten context.** Most "bad prompting" is not unclear phrasing, it is context that exists only in the prompter's head. English cannot fully specify behavior - that is why code exists. The skill is knowing what to specify (constraints, acceptance criteria, examples) and what to leave open.

2. **The verification loop matters more than the prompt.** A vague prompt with tests/lint/CI beats a perfect prompt with nothing, because the agent can self-correct. Gates > prose. (This one is solvable via pstack or manually.)

3. **It is one-way.** "Not confusing if presented clearly" is true of people too, but people ask back. Agents mostly do not, so the prompt has to be complete on the first shot.

## Solving 1 - unwritten context

- Treat wrong output as a context bug report: do not just correct it, ask "what didn't it know?" and write THAT into the repo (AGENTS.md, decisions.md, prompts repo). Context compounds when stored, evaporates in chat.
- Voice-note briefs: people say 3x more context talking than typing. Record the brief, transcribe it, that transcript is the prompt.

## Solving 3 - one-way communication

- Two-phase prompting. Phase 1: "read the repo, ask me every question whose answer changes the implementation, propose an approach, stop." Phase 2: implement, only after approval.
- Proof from practice: the scrap-principles flow - review the codebase first, then implement the review's findings (see scrap-principles-implementation.md).

## Making 1 + 3 the default (no per-prompt effort)

- Always-on instruction layer: AGENTS.md for agent CLIs, .cursor/rules (always apply) for Cursor. Rule text: see ask-first-full-context.md.
- Caveat: instructions drift in long sessions. Rules ask, harnesses force.
  - Cursor Plan mode / Claude Code plan mode: the agent cannot edit until the plan is approved. Default to plan mode; the rule becomes backup.
  - Claude Code UserPromptSubmit hooks inject text into every prompt automatically. Cursor has no hooks yet - rules are the closest.
