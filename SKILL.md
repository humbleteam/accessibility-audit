---
name: accessibility-audit
description: Runs a WCAG 2.2 accessibility audit of a screenshot, URL, or HTML/JSX and reports findings by severity (P0 blocks use, P1 degrades use, P2 friction), each with a success-criterion citation. Use for "check accessibility", "run an a11y audit", "is this WCAG compliant", "is this accessible", or "audit contrast and keyboard support". Skip for axe-core/Lighthouse scans or legal ADA/Section 508 certification - this is expert review, not a scanner.
---

# Accessibility audit

## Step 1: identify the input

Determine what was actually provided before checking anything:

- **A screenshot or image** - proceed with screenshot-only scope (see Step 3).
- **A URL** - fetch the page if a fetch tool is available, and use the rendered HTML as the source of truth. If a screenshot of the page is also available, use both. If no fetch tool is available, ask the user to paste the page's HTML instead of guessing at markup.
- **HTML, JSX, or another markup/component snippet** - read it directly as the source of truth.
- **Anything else** (a plain description, a Figma link with no exported image, a vague "check our app") - stop and ask for a screenshot, URL, or markup. Do not guess at a UI you cannot see.

If more than one input type is given (for example a URL plus a screenshot of one of its states), use both and check against the wider scope.

## Step 2: read the checklist

Before evaluating anything, read `references/wcag22-checklist.md`. It lists all 18 success criteria this audit checks, one line each: SC number, name, level, whether a screenshot alone can verify it, and what to look for. Re-read it on every audit, even a second one in the same session, so criteria don't quietly drop out of memory.

## Criteria this audit covers

| SC | Name | Level |
|---|---|---|
| 1.1.1 | Non-text content | A |
| 1.3.1 | Info and relationships | A |
| 1.4.3 | Contrast minimum | AA |
| 1.4.4 | Resize text | AA |
| 1.4.10 | Reflow | AA |
| 1.4.11 | Non-text contrast | AA |
| 2.1.1 | Keyboard | A |
| 2.1.2 | No keyboard trap | A |
| 2.4.4 | Link purpose (in context) | A |
| 2.4.6 | Headings and labels | AA |
| 2.4.7 | Focus visible | AA |
| 2.4.11 | Focus not obscured (minimum) - new in WCAG 2.2 | AA |
| 2.5.8 | Target size (minimum) - new in WCAG 2.2 | AA |
| 3.2.2 | On input | A |
| 3.3.1 | Error identification | A |
| 3.3.2 | Labels or instructions | A |
| 3.3.8 | Accessible authentication (minimum) - new in WCAG 2.2 | AA |
| 4.1.2 | Name, role, value | A |

See `references/wcag22-checklist.md` for what to look for under each one.

## Step 3: separate what the input can prove from what it can't

This is the rule that keeps the audit honest. Never claim a pass or a fail on a criterion the input cannot demonstrate.

**Every criterion leaves the audit somewhere.** All 18 in the table above must end up in exactly one of the four homes defined under Output format: a finding under P0, P1, or P2; the scope line, for a criterion that was checked and came back clean; "Suppressed at this depth", for a criterion whose finding the requested depth withholds; or a line under "Not verifiable from this input". A criterion in none of the four has been dropped, and a dropped criterion reads as a silent pass, which is the one thing this step exists to prevent. The homes are not interchangeable: a criterion that was checked and passed belongs on the scope line and never in the ledger, which says the input could not reach it. The three groups below are exhaustive and add up to 18; anything you cannot place in the first two belongs in the third. The count runs per audited screen, not per report: a criterion can fail on one screen and come back clean on the next, so a report covering two screens carries two complete sets of the four homes. One report-wide verdict would have to drop one of the two readings, and the dropped one reads as a pass.

From a **screenshot alone**, you CAN check (5 criteria):

- 1.4.3 contrast minimum - measure the rendered text and background colors.
- 1.4.11 non-text contrast - measure icons, borders, and visible focus indicators.
- 2.4.6 headings and labels - confirm headings and form labels are visually present and make sense out of context.
- 2.5.8 target size - measure tappable element dimensions against the 24x24 CSS px minimum, when the viewport scale is known or stated.
- 3.3.2 labels or instructions - confirm visible instructions exist next to inputs that need them.

From a **screenshot alone**, you can check these only when the matching state was supplied (7 criteria). Check what the image actually shows, and put the rest under "Not verifiable from this input" naming the state you would need:

- 1.3.1 info and relationships - a visible heading hierarchy, list, or table is readable from the pixels; whether the DOM carries the same relationships is not.
- 1.4.4 resize text - needs a screenshot of the page at 200% text size. One image at the default size cannot show whether content clips, overlaps, or stays usable when text scales.
- 1.4.10 reflow - needs a screenshot at a 320px-wide viewport. One image at one width says nothing about whether the layout reflows to a single column without horizontal scrolling.
- 2.4.4 link purpose - link text and its surrounding sentence are visible; purpose that depends on programmatic context is not.
- 2.4.7 focus visible and 2.4.11 focus not obscured - both need a focus-state screenshot.
- 3.3.1 error identification - needs an error-state screenshot, and then shows only whether the error is described in text rather than by color alone.

From a **screenshot alone**, you CANNOT check, and must list under "Not verifiable from this input" (6 criteria):

- 1.1.1 non-text content (alt text lives in the DOM, not the pixels).
- 2.1.1 / 2.1.2 keyboard behavior and keyboard traps.
- 3.2.2 on input, 3.3.8 accessible authentication - both need interaction or a flow, not a static image.
- 4.1.2 name, role, value (needs the accessibility tree).

From **HTML, JSX, or a fetched page**, check against the code everything the code carries: alt attributes, heading and landmark structure, label associations, ARIA roles and states, tabindex and focus-management code, and any inline color values you can resolve to compute contrast. That is not all 18. The two paragraphs below say which criteria the code cannot carry and why, and three of them stay out of reach even when every stylesheet arrived.

Styles are a separate input from markup, and when they do not arrive with it, every criterion that needs a computed value or a rendered page stays in "Not verifiable from this input": 1.4.3 contrast and 1.4.11 non-text contrast, 1.4.4 resize text, 1.4.10 reflow, 2.4.7 focus visible and 2.4.11 focus not obscured, and 2.5.8 target size. A component snippet whose class names point at a stylesheet nobody pasted is the common case rather than an edge one. Those seven are two kinds, and a stylesheet settles only one of them:

- **Four need a computed value, and the styles carry it**: 1.4.3 and 1.4.11 from declared colors, 2.4.7 from a declared focus style, 2.5.8 from declared box dimensions. These leave the ledger when the styles arrive with the markup.
- **Three need the page rendered under a condition, and no source file is one**: 1.4.4 needs the screen at 200% text size, 1.4.10 needs it at a 320px-wide viewport, 2.4.11 needs a component focused under the real stacking and scroll position. A media query and a `position: sticky` header are the risk, not the result: whether content clips, overlaps, or hides the focused element is an outcome of layout, and source code is the input to layout rather than its output. These stay in the ledger on every source-only audit, styles or not, and leave it only when the matching rendered view arrives, which is the partial-criteria test above applied inside the markup path.

So the markup path has three ledger lengths, not two: those three when the styles came with the markup; those three plus the four computed-value criteria plus anything the snippet's own scope cannot reach when they did not; and fewer than three only when a rendered view of the missing condition arrived alongside the code. No source-only audit files an empty ledger. Resolvable inline colors or stated computed values move a computed-value criterion back out one at a time; a class name does not, and neither of them moves a rendered-condition criterion. A screenshot of the same screen moves criteria out the same way, for what the image renders rather than for the path as a whole - see Edge cases.

A fetched URL sits on this rule rather than beside it. A fetch returns the document, not the stylesheets it links and not the rendered page, so a URL audit holding the HTML alone is the styleless-markup length - its class names point at a file the fetch did not follow, exactly as a pasted snippet's do - and a URL audit that fetched the linked stylesheets too is the three-criterion length. "A URL was supplied" is not a coverage. What the fetch actually returned is, and the scope line says which.

## Step 4: assign severity

- **P0 - blocks use.** A user cannot complete the task at all. Missing accessible name on a primary action, a keyboard trap, contrast so low the text is unreadable, a form control with no label at all.
- **P1 - degrades use.** The task is possible but harder. Contrast below the AA threshold but still legible, missing visible focus indicator, a target below the 24x24 CSS px minimum, vague link text ("click here") with no surrounding context.
- **P2 - friction.** Minor confusion or inefficiency. Inconsistent heading levels read off markup, low contrast on a decorative element, redundant instructions. Heading levels belong to this tier only where they are readable: HTML, JSX, or a fetched page carries them, and a screenshot carries the visual hierarchy instead, so a level skip filed from an image is an inference and Step 5 rules it out.

## Step 5: write each finding

Every finding needs three parts, in this order:

1. **Observed** - what you actually saw or read, not an inference. ("The 'Submit' button renders white text (#FFFFFF) on a #7A9CC6 background.")
2. **Fix** - concrete, not generic. ("Darken the background to #3D5A80 or below to reach a 4.5:1 ratio against white text.")
3. **Citation** - `WCAG 2.2 SC <number> (<name>, Level <A/AA>)`.

## Output format

Use exactly this structure for a report covering one screen:

```
# Accessibility audit: <name or URL of what was reviewed>

**Input type:** screenshot | URL | HTML/JSX
**Scope:** <one line on what was actually checked>

## P0 - blocks use
1. **<short title>**
   - Observed: <fact>
   - Fix: <concrete fix>
   - WCAG 2.2 SC <x.x.x> (<name>, Level <A/AA>)

## P1 - degrades use
(same structure)

## P2 - friction
(same structure)

## Not verifiable from this input
- <SC number and name> - requires <HTML / URL / keyboard test / focus-state screenshot>

## Suppressed at this depth
(only when the request limited the report to P0 - omit this heading otherwise)
- <SC number and name> - <P1 / P2> finding found, write-up withheld at the requested depth

## Scope note
This is an expert-review pass against WCAG 2.2, not a substitute for testing with people who use assistive technology, and not a legal ADA or Section 508 compliance certification.
```

When the input shows more than one screen, each screen takes its own `##` heading and the four homes drop to `###` underneath it. The screen headings and the severity headings cannot share a level: at the same depth there is nothing in the report that says where one screen's verdict stops and the next one's begins.

```
# Accessibility audit: <name or URL of what was reviewed>

**Input type:** screenshot | URL | HTML/JSX

## <Screen 1 name>

**Scope:** <what was checked on this screen>

### P0 - blocks use
### P1 - degrades use
### P2 - friction
### Not verifiable from this input
### Suppressed at this depth

## <Screen 2 name>

**Scope:** <what was checked on this screen>

### P0 - blocks use
(the same sections, filled in for this screen, under the same omission rules)

## Scope note
<written once at the end - it describes the method, not a screen>
```

The scope line moves under each screen heading, because it is one of the four homes and carries that screen's checked-and-clean criteria. The input-type line and the scope note stay at report level: one input was supplied, and the method is the same for every screen in it.

Omit a severity section entirely when it has zero findings - do not pad it with "no issues found" filler under a heading that implies problems exist. Always include "Not verifiable from this input" when the input is a screenshot, and on every source-only audit too, markup or fetched URL, with its styles or without them: the three rendered-condition criteria from Step 3 never leave a source-only ledger, so "None - full markup was available" is not a sentence a source-only audit can write. Markup that arrives without its styles keeps the longer ledger, per Step 3 - "None" there would claim a coverage the input never gave, and it is the more convincing false pass because the rest of the report shows criteria being read straight off real code. Markup with a screenshot of the same screen keeps a ledger too: the image resolves what it renders and no more, so "None" there claims the states it never showed. One input empties the ledger: code plus a rendered view of each of the three conditions, the screen at 200% text size, at 320px wide, and in a focus state. The line then reads "None - the code and the rendered views together reached all 18", and the scope line names those views, because that is what makes the claim checkable. Include "Suppressed at this depth" only when the request limited the output depth, and never as a stand-in for a severity section that genuinely had no findings - an omitted section says there was nothing to report, which is the opposite of what suppression means. Always include the scope note.

"Not verifiable from this input" is the ledger that makes Step 3 checkable. Every one of the 18 criteria is accounted for exactly once, across four places: a finding under P0, P1, or P2; the scope line, for a criterion that was checked and came back clean; "Suppressed at this depth", for a criterion that produced a finding the requested depth withholds; or this ledger, for a criterion the input could not reach. A criterion in none of the four has been dropped silently. On a screenshot audit the ledger normally holds 13 criteria - the 7 partial ones whose matching state was not supplied, plus the 6 a static image can never reach - so a short ledger is the symptom of criteria going missing, not of a clean screen. That 13 is per screen: a two-screen report holds two ledgers of about that length, not one shared between them. A markup audit has its own expected lengths: three when the styles came with the markup - 1.4.4, 1.4.10, and 2.4.11, which need the page rendered rather than a value computed - and at least the seven style-dependent criteria from Step 3 when they did not, plus any criterion the snippet's scope cannot reach, such as 3.3.8 on a component that holds no sign-in step. A fetched URL takes whichever of those two lengths matches what the fetch actually returned. Group entries on one line where they share a reason (`1.4.4 / 1.4.10 Resize text and reflow - need a 200% text-size screenshot and a 320px-wide one`) rather than dropping them to keep the report tidy.

## Edge cases

- **Multiple screens in one screenshot** - audit each screen under its own `##` heading inside the same report, with its severity sections, its ledger, and its suppressed roster at `###` underneath, so each screen carries a complete set of the four homes. Accounting runs per screen: the same criterion can be a P0 on the first screen and clean on the second, and one report-wide entry can only record one of those. Findings are never merged across screens, and no screen inherits a verdict from the one above it: a criterion missing from a screen's set was not checked on that screen, which is the silent pass the accounting exists to catch.
- **Several states of one screen in one image** - not the case above. Two states of the same screen are one screen with more evidence, and the extra state unlocks the partial criteria it covers per Step 3: an error state reaches 3.3.1, a focus state reaches 2.4.7 and 2.4.11. One screen, one set of the four homes, and the scope line names the states that were supplied.
- **A screenshot with an unknown scale** - target size is defined in CSS pixels, but a screenshot from a 2x or 3x display stores device pixels, so a 44 CSS px button arrives 88 or 132 px wide in the file. Measuring 2.5.8 straight off image pixels turns a comfortable target into a violation, and the same arithmetic the other way hides a real one. Ask for the device pixel ratio or the CSS viewport width, or derive the ratio from a known device width (a 1170 px wide iPhone screenshot is 390 CSS px at 3x). Until the scale is settled, 2.5.8 goes under "Not verifiable from this input". Contrast is unaffected - colors do not change with scale.
- **Text over a photo, gradient, or video background** - do not estimate a pass. File a P1 finding for indeterminate contrast and recommend testing the worst-case pixel region against the text color.
- **A component with no visible content** (empty state, loading skeleton) - note it and ask whether a populated state is available, since several criteria (headings, labels, link purpose) cannot be judged from an empty shell.
- **HTML/JSX with inline styles or unresolved CSS variables** - treat contrast and target size as not verifiable rather than guessing computed values.
- **Markup plus a screenshot of the same screen** - the pair Step 1 asks you to use when both arrive, and it is none of the markup path's three ledger lengths. The image is the styles for what it renders: 1.4.3 and 1.4.11 come out of the ledger for the text pairs, icons and borders actually visible in it, and 2.5.8 once the scale is settled per the unknown-scale case above. The other four style-dependent criteria stay in the ledger unless the matching image was supplied - 1.4.4 needs the screen at 200% text size, 1.4.10 needs it at 320px wide, 2.4.7 and 2.4.11 need a focus state - which is the partial-criteria test from Step 3 applied inside the markup path. 2.4.7 travels with 2.4.11 only here, where no stylesheet arrived: a declared focus style resolves 2.4.7 and nothing declared resolves 2.4.11, so with the styles in hand the pair splits and 2.4.11 stays. The ledger holds whichever of the seven the supplied images do not reach, and the scope line names which images arrived, because "a screenshot was supplied" and "the focus state was supplied" are different coverages and only the second one clears 2.4.7.
- **A "quick check" or "just the big ones" request** - still run the full criteria list, still write up P0 in full, and hold back the P1 and P2 write-ups. Depth limits what a finding says, never whether the report admits one exists: every criterion that produced a withheld finding gets one line under "Suppressed at this depth" naming its SC number and the tier it landed in, and the scope line says the depth was limited. The other two moves are both illegal. Dropping those criteria leaves them in none of the four homes, and a criterion that leaves the report silently reads as a pass - the fastest way this audit can certify a screen it actually failed. Putting them on the scope line is worse, because that line is reserved for criteria that were checked and came back clean, and a withheld finding is not clean. Offer to write up any suppressed line on request.
- **A second audit of the same screen after fixes** - re-run the full process; do not assume prior findings still hold.

## Failure modes to avoid

- Do not invent a contrast ratio you did not compute from actual colors.
- Do not mark a criterion "pass" because nothing looked obviously wrong - if it was not checked, it is "not verifiable," not a pass.
- Do not let a criterion leave the report without landing in one of the four homes: a severity section, the scope line as checked and clean, the suppressed roster, or the not-verifiable ledger. Silence is the most convincing false pass this audit can produce, because nothing in the output points at the gap. Do not file a criterion that was checked and passed under the ledger either - the ledger claims the input could not reach it, and that claim would be false.
- Do not cite a WCAG success criterion you have not actually checked against.
- Do not soften a P0 finding into P1 to make a report read better.
