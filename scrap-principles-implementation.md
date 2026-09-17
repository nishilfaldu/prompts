# Scrap Principles Implementation

1500 is the requirement.

Implement all of the findings from the review you just produced. Work under the same three rules, which override any attachment to the code as it exists:

1. Scrap when the architecture is wrong. If a part of the system keeps producing friction its design can't absorb, throw that design out instead of bolting fixes onto it. For every area you touch, ask: "if we were writing this from scratch today, knowing everything we know now, what would we build?" When the from-scratch answer is simpler than what exists, build the from-scratch version. Sunk work is not a reason to keep anything.

2. Subtract before you add. Remove complexity first, then build. The codebase should be smaller after your changes before it grows. A change that only adds abstraction is not a simplification.

3. Evidence over taste. Every change must name what was wrong and the concrete cost it removed (review friction, bug surface, duplicated logic, coupling).

When implementation is done, review the entire codebase one more time under the same three rules, as if seeing it fresh. Report: what you implemented, what the second pass found, and what (if anything) still fails the rules.
