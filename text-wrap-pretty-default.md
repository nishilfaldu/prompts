Set `text-wrap: pretty;` as the app-wide default in the shared stylesheet so ordinary text gets better line breaks and fewer orphaned words. Use one global rule rather than repeating it across components, and preserve any existing intentional wrapping behavior.

Then inspect the running UI at desktop and mobile widths. Check headings, navigation, buttons, badges, tables, code, and other compact or constrained text for awkward breaks or layout regressions. Where the global default is wrong, add a narrow, explicit override at the relevant component and explain why. Don't exempt whole categories preemptively: keep `pretty` unless a real case calls for different wrapping.

Report the global rule, each exception and its reason, and the viewports and states you checked. Fix visible regressions before finishing.
