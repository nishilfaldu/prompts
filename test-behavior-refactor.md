Refactor the test suite in `<insert_scope>` so every test earns its keep: it calls the code the way its users do and asserts a result they observe. Tests exist to catch defects, not to restate the code.

The bar for keeping a test: it would fail if the behavior it covers broke. The quick check - if the test would still pass when every function it imports returned `undefined`, it observes no behavior and cannot fail for a defect. Rewrite the assertion or delete the test.

Shapes that fail the bar:

- Weak or no assertion: no `expect`, or only `toBeDefined`, `toBeTruthy`, `not.toThrow`, `toBeInstanceOf`.
- Mock or absence only: asserts that a mock was called, or only that something is undefined, empty, or not the wrong value.
- Self-referential: the expected value comes from the code under test.
- Constant pin: restates a hand-maintained constant, config default, table row, or prompt string.
- Fixture asserts fixture: asserts on data the test built while the subject never runs inside the test body.

Where real behavior exists underneath, fix the test: call the subject inside the test body with one concrete input and assert the literal output or the observable effect. For an absence, assert the presence on the other input in the same test. For a constant, test the mechanism that reads it instead of restating the value. For a mock, assert the payload it received or the state after the call, not that it was called. When no such assertion exists, delete the test. Prefer no test over a bad test.

Keep tests of relations across rows (a key present in two tables, a parent that exists) and compile-time checks in `*.test-d.ts` files.

Constraints:

- This is a test-suite pass. Do not change production behavior. If a failing test exposes a genuine bug, do not edit the test to match the wrong implementation - fix the bug in its own labeled commit with evidence, or stop and report it.
- Judge every test in scope. No caps and no "worst few" - a full inventory, nothing omitted. If the report gets long, tighten the per-item format instead of dropping entries.

Proof: run `<insert_test_command>` before you start and after you finish, and show both runs. The suite must be green after your changes. Then report the full inventory: every test kept (the defect it would catch), rewritten (the behavior it now pins), or deleted (the shape that disqualified it).
