Create a new branch and implement the findings from the completed dependency review of `<insert_folder>`. The review output should already be in this session; if it is not, stop and ask for it instead of repeating the review from memory.

Address `<insert_severity_scope: all findings | high and medium findings only>`.

Use this change policy: `<insert_change_policy: pre-production, so make clean breaks with no compatibility shims or migration code | production, so preserve compatibility and include the required migration or rollout plan>`.

Treat the dependency direction established in the review as the target. Remove the selected backward edges and cycles with the smallest boundary changes that restore one-way dependencies. Move code to the layer that owns the responsibility instead of hiding the dependency behind re-exports, wrappers, renamed imports, or duplicated logic. Where the review found one policy enforced inconsistently, put that policy in one shared owner and enforce it on every path, including internal paths.

Keep behavior unchanged except where the review identified inconsistent behavior that must be made uniform. Delete code made obsolete by the moves, update imports and tests, and do not leave compatibility code unless the selected change policy requires it.

When done, re-run `dependency-graph-review.md` on the same `<insert_folder>` scope. Show the new dependency direction and the full inventory table. If all findings were selected, the inventory must be empty. If only high and medium findings were selected, the high-and-medium inventory must be empty and any remaining low findings must still be listed explicitly.
