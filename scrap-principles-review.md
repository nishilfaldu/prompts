Review this codebase thoroughly. Your goal: find simpler solutions for existing features and opportunities to make the codebase more maintainable and reviewable over time.

Work under three rules, which override any attachment to the code as it exists:

1. Scrap when the architecture is wrong. If a part of the system keeps producing friction its design can't absorb, the answer is to throw that design out, not bolt fixes onto it. For every area you review, ask: "if we were writing this from scratch today, knowing everything we know now, what would we build?" When the from-scratch answer is simpler than what exists, say so plainly and sketch the from-scratch version. Sunk work is not a reason to keep anything.
2. Subtract before you add. Remove complexity first, then build. Your proposals should make the codebase smaller before it grows. A proposal that only adds abstraction is not a simplification.
3. Evidence over taste. Every finding must name the file(s), what's wrong, and the concrete cost (review friction, bug surface, duplicated logic, coupling).

Output a written review only - do not change code. Rank findings by leverage: for each, give keep / simplify / scrap-and-redo, the from-scratch sketch if scrapping, and the migration cost. Be honest even when the verdict is fatal to existing work.
