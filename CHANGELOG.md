# Changelog

## [1.5.0] - 2026-09-17

- The markup path had no worked example anywhere in the repo, and the rule underneath it undercounted what markup alone can prove. Step 3 closed its HTML/JSX paragraph with "if CSS is not included, contrast and target size still fall into not verifiable" - two criteria, where five more need the same styles: 1.4.11 non-text contrast, 1.4.4 resize text, 1.4.10 reflow, 2.4.7 focus visible, and 2.4.11 focus not obscured.
- The Output format rule left that case with nothing to write. A screenshot audit files a ledger and an audit that "covered HTML/JSX/URL end to end" writes "None - full markup was available", while the input Usage advertises - a React component pasted on its own - is neither. "None" on a component whose stylesheet nobody pasted claims a coverage the input never gave, and it is the more convincing false pass of the two, because the rest of the report really is being read off code.
- Step 3 now names all seven style-dependent criteria and states the markup path's two ledger lengths: empty when the styles came with the markup, those seven plus anything the snippet's own scope cannot reach when they did not. A resolvable inline color or a stated computed value moves a criterion back out one at a time; a class name does not. The ledger explainer and the README's accounting bullet carry the same expected lengths, so the short-ledger symptom works on the markup path too.
- README: a second worked example, a JSX order-summary component with its CSS module missing. Four findings - an `onClick` div with no role or tabIndex under 2.1.1, an icon-only button with no accessible name under 4.1.2, an `img` with no alt under 1.1.1, and an h1 to h3 skip under 1.3.1 - six criteria checked and clean on the scope line, and eight in the ledger. It cites no contrast ratio, which is the point: nothing in the input could produce one.
- Step 4's P2 tier listed "inconsistent heading levels" without saying where a level is readable. A screenshot carries the visual hierarchy and not the levels, so a skip filed from an image is an inference that Step 5 rules out; the tier now says so, and the README's severity illustration reads "heading skip in markup". 1.3.1 fixed this inside the worked example on 2026-09-07 and left the rule itself unqualified, which is why the same slip was available again.

## [1.4.0] - 2026-09-12

- A report covering more than one screen had no legal shape. The edge case asks for each screen under its own `##` subheading, which is the level the output format already gives to `## P0 - blocks use` and the ledger, so the screen headings and the severity headings landed at the same depth and nothing in the report marked where one screen's verdict ended.
- The accounting rule collided with it harder than the headings did. Every criterion lands in exactly one of the four homes, stated at report level, while a criterion can be a P0 on the first screen and clean on the second - two readings that one entry cannot hold. Whichever one got written, the other left the report, and a criterion that leaves the report reads as a pass.
- Accounting is now per audited screen. Each screen takes a `##` heading with its severity sections, its ledger and its suppressed roster at `###`, and carries a complete set of the four homes. The scope line moves under the screen heading, since it is the home for that screen's checked-and-clean criteria; the input-type line and the scope note stay at report level, one input and one method for the whole report.
- The ledger's expected length is stated as per screen too. A two-screen screenshot report holds two ledgers of about 13, not one shared between them, so the short-ledger symptom still works when a report covers more than one screen.
- The edge case used to open "Multiple screens or states in one screenshot", putting two cases with opposite handling under one heading. Several states of one screen are one screen with more evidence, and the extra state unlocks the partial criteria it covers under Step 3, so they are now separate bullets: one set of the four homes for the states case, one set per screen for the other.
- README: the accounting bullet says the count runs per screen.

## [1.3.1] - 2026-09-07

- README example: the P2 finding read heading levels off a screenshot. Its Observed line named "Checkout" as an H1 and "Payment details" as an H3 and filed the missing H2 between them as the finding, while the same report's ledger three lines below states that a screenshot shows the visual hierarchy and not the DOM structure behind it. Step 5 requires an Observed line to carry what was seen rather than an inference, so the worked example demonstrated the failure mode the skill opens with.
- The P2 finding is now one a screenshot can prove: two sections carrying the same generic heading, which is what SC 2.4.6 asks about - whether headings and labels describe topic or purpose, per the checklist row. A heading-level skip remains a legal P2 finding from HTML, JSX, or a fetched URL, where the levels are actually readable.
- The ledger's 1.3.1 line said "the heading levels are visible". Reworded to match Step 3: the visual heading hierarchy is readable, the DOM structure behind it is not.
- Criterion accounting unchanged: 2.4.6 is still the P2 finding, 1.4.11 and 2.5.8 still sit on the scope line as checked and clean, and the ledger still holds 13.

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
