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

From a **screenshot alone**, you CAN check:

- 1.4.3 contrast minimum - measure the rendered text and background colors.
- 1.4.11 non-text contrast - measure icons, borders, and visible focus indicators.
- 2.4.6 headings and labels - confirm headings and form labels are visually present and make sense out of context.
- 2.5.8 target size - measure tappable element dimensions against the 24x24 CSS px minimum, when the viewport scale is known or stated.
- 3.3.2 labels or instructions - confirm visible instructions exist next to inputs that need them.

From a **screenshot alone**, you CANNOT check, and must list under "Not verifiable from this input":

- 1.1.1 non-text content (alt text lives in the DOM, not the pixels).
- 1.3.1 info and relationships (semantic structure).
- 2.1.1 / 2.1.2 keyboard behavior and keyboard traps.
- 2.4.4 link purpose when it depends on programmatic context.
- 2.4.7 / 2.4.11 focus visibility, unless a focus-state screenshot was provided.
- 3.2.2 on input, 3.3.1 error identification, 3.3.8 accessible authentication - all need interaction or flow, not a static image.
- 4.1.2 name, role, value (needs the accessibility tree).

From **HTML, JSX, or a fetched page**, check all 18 directly against the markup: alt attributes, heading and landmark structure, label associations, ARIA roles and states, tabindex and focus-management code, and any inline color values you can resolve to compute contrast. If CSS is not included, contrast and target size still fall into "not verifiable" unless computed values are given.

## Step 4: assign severity

- **P0 - blocks use.** A user cannot complete the task at all. Missing accessible name on a primary action, a keyboard trap, contrast so low the text is unreadable, a form control with no label at all.
- **P1 - degrades use.** The task is possible but harder. Contrast below the AA threshold but still legible, missing visible focus indicator, a target below the 24x24 CSS px minimum, vague link text ("click here") with no surrounding context.
- **P2 - friction.** Minor confusion or inefficiency. Inconsistent heading levels, low contrast on a decorative element, redundant instructions.

## Step 5: write each finding

Every finding needs three parts, in this order:

1. **Observed** - what you actually saw or read, not an inference. ("The 'Submit' button renders white text (#FFFFFF) on a #7A9CC6 background.")
2. **Fix** - concrete, not generic. ("Darken the background to #3D5A80 or below to reach a 4.5:1 ratio against white text.")
3. **Citation** - `WCAG 2.2 SC <number> (<name>, Level <A/AA>)`.

## Output format

Use exactly this structure:

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

## Scope note
This is an expert-review pass against WCAG 2.2, not a substitute for testing with people who use assistive technology, and not a legal ADA or Section 508 compliance certification.
```

Omit a severity section entirely when it has zero findings - do not pad it with "no issues found" filler under a heading that implies problems exist. Always include "Not verifiable from this input" when the input is a screenshot; when the audit covered HTML/JSX/URL end to end, write "None - full markup was available" instead of omitting the section. Always include the scope note.

## Edge cases

- **Multiple screens or states in one screenshot** - audit each one under its own `##` subheading inside the same report, rather than merging findings across screens.
- **Text over a photo, gradient, or video background** - do not estimate a pass. File a P1 finding for indeterminate contrast and recommend testing the worst-case pixel region against the text color.
- **A component with no visible content** (empty state, loading skeleton) - note it and ask whether a populated state is available, since several criteria (headings, labels, link purpose) cannot be judged from an empty shell.
- **HTML/JSX with inline styles or unresolved CSS variables** - treat contrast and target size as not verifiable rather than guessing computed values.
- **A "quick check" or "just the big ones" request** - still run the full criteria list, but report P0 only, and note in the scope line that P1/P2 were checked but suppressed from the output.
- **A second audit of the same screen after fixes** - re-run the full process; do not assume prior findings still hold.

## Failure modes to avoid

- Do not invent a contrast ratio you did not compute from actual colors.
- Do not mark a criterion "pass" because nothing looked obviously wrong - if it was not checked, it is "not verifiable," not a pass.
- Do not cite a WCAG success criterion you have not actually checked against.
- Do not soften a P0 finding into P1 to make a report read better.
