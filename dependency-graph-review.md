Review `<insert_folder>` as one bounded dependency scope.

Map its dependency graph. Infer the intended layer direction from each module's responsibility, then state that direction before judging violations. Find every backward dependency edge and every cycle. Include imports that cross the scope boundary when they are needed to explain a coupling, but do not expand this into a repo-wide review.

Report a full inventory of every backward edge and cycle you found, with nothing omitted. Rank them by severity. For each one, give:

- the dependency path or cycle
- file:line evidence for every edge
- what the coupling costs in concrete terms: change blast radius, review friction, bug surface, test difficulty, or duplicated logic
- the smallest boundary change that would restore one-way dependencies

The goal is a codebase that stays maintainable and reviewable over time, so completeness beats brevity here. Do not cap the list or stop at a "worst few". If the inventory is long, keep the per-item format tight instead of dropping entries.

Output a written review only. Do not change code.

Keep this pass tightly scoped. A focused pass on one folder or tree produces sharper evidence than one diluted repo-wide pass. If more than one area needs review, run this prompt separately for each folder or tree, then compare the reports before deciding what to refactor.
