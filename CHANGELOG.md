# Changelog

## [1.3.0] - 2026-08-19

- A "quick check" or "just the big ones" request had no legal output. The edge case said to report P0 only, while the accounting rule says every criterion lands in exactly one of a severity finding, the scope line when checked and clean, or the not-verifiable ledger - and a criterion that produced a P1 finding withheld by the requested depth fits none of them. Dropping it is forbidden by name in the failure modes; putting it on the scope line states it came back clean, which is false; printing it disobeys the request.
- Added a fourth home, "Suppressed at this depth": one line per criterion whose finding the requested depth withholds, naming the SC number and the tier. Depth now limits what a finding says, never whether the report admits one exists.
- Brought the three statements of the accounting rule into agreement. Step 3 and the failure-modes list still named two homes, from before the three-home rule was added in 1.2.0, so a criterion that was checked and came back clean was "dropped" under one statement and correctly filed under another. Under the two-home wording the only way to satisfy them was to file a passing criterion in the not-verifiable ledger, which asserts the input could not reach it. That move is now ruled out explicitly.
- README: the accounting bullet lists all four homes, and a new FAQ answer covers asking for blockers only.

## [1.2.0] - 2026-08-12

- Step 3 now accounts for all 18 criteria. Its two lists covered 16: 1.4.4 resize text and 1.4.10 reflow appeared in neither the screenshot-checkable group nor the not-verifiable one, so a screenshot audit could drop two Level AA criteria without a finding, a not-verifiable line, or any trace in the report.
- Step 3 is now three groups that add up to 18 - 5 checkable, 7 checkable only when the matching state was supplied, 6 never checkable from an image - replacing the inconsistent handling where 2.4.4, 2.4.7, and 2.4.11 carried "unless" clauses while 1.3.1 and 3.3.1 did not.
- Added the accounting rule: every criterion ends in exactly one of a severity finding, the scope line when checked and clean, or the not-verifiable ledger. A screenshot audit files 13 criteria under not-verifiable, so a short ledger is now a documented symptom rather than a tidy report.
- `references/wcag22-checklist.md`: 1.4.4 and 1.4.10 regraded from No to Partial, since a 200% text-size screenshot and a 320px-wide screenshot are exactly the states that verify them. The reading key already named "narrow viewport" as a qualifying state while no row used it. Per-grade counts added so a future row cannot be added to the table without being sorted in Step 3.
- README example output completed: its not-verifiable section listed 3 criteria where the rule requires 13, which was the bug in miniature.

## [1.1.0] - 2026-08-06

- New edge case: a screenshot with an unknown scale. Target size (SC 2.5.8) is measured in CSS pixels, so a 2x or 3x screenshot measured in raw image pixels produces false findings in both directions. The audit now settles the scale first, or reports 2.5.8 as not verifiable.

## [1.0.0] - 2026-07-12

- Initial release: WCAG 2.2 accessibility audit skill for screenshots, URLs, and HTML/JSX.
- Findings grouped by severity (P0 blocks use, P1 degrades use, P2 friction), each with a success-criterion citation.
- Ships a compact WCAG 2.2 checklist covering all three criteria new in 2.2: focus not obscured, target size minimum, accessible authentication.
- CI workflow validates SKILL.md frontmatter and the README-to-SKILL.md link.
