Raise the UI in `<insert_scope>` to the quality bar set by `<insert_reference_product>` while staying on `<insert_component_library>`. Reuse its components and patterns by default; install or add more from that library when needed instead of replacing it with a new design system.

Work in two phases:

## Phase 1: Audit

Inspect every screen and state in scope before changing code. State the visual bar you inferred from the reference product, then report a full ranked inventory of what falls below it. For every finding, give:

- the screen, route, or component
- file:line evidence
- what is visually weak or default-looking
- the concrete cost to hierarchy, comprehension, trust, or usability
- the specific change you will make

At minimum, check hierarchy, typography, spacing, density, alignment, color, contrast, borders, elevation, component variety, responsive behavior, and empty, loading, error, disabled, and success states. Avoid walls of identical cards, placeholder-feeling layouts, and decoration without purpose. Prefer restrained color with one deliberate accent unless the product already has a stronger system.

Completeness beats brevity. Do not stop at a worst few. If the inventory is long, keep each item tight instead of dropping entries.

## Phase 2: Upgrade

Implement the audit findings in severity order. Keep behavior and information architecture intact unless a finding shows they are part of the problem. Use the smallest coherent set of design decisions so the result feels like one product, not a collection of polished fragments.

Evidence over vibes. For every completed finding, report:

- what changed and where
- the component-library primitive or pattern used
- the before/after reason the change meets the stated bar
- the state and viewport you verified

Render and visually inspect every changed screen at representative desktop and mobile widths. Exercise the real empty, loading, error, disabled, and success states where they exist. Fix visible regressions before calling the work complete. End with any audit findings that remain and why; do not silently omit them.
