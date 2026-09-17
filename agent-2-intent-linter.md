# Intent linter: pre-execution check for every agent run (agent 2)

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

    While working, continuously check: "Have I discovered information that changes my interpretation of what the user probably wants?" If yes: update the plan automatically when low-risk; explicitly notify the user when it materially changes scope, architecture, behavior, or tradeoffs.

    Decision hierarchy: infer -> inspect -> assume -> ask. Only stop and ask when getting it wrong would materially change the outcome.
