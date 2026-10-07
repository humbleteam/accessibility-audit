<div align="center">

<h1>Accessibility audit</h1>

**A WCAG 2.2-grounded accessibility audit skill for Claude Code that reviews a screenshot, URL, or HTML/JSX snippet and cites the exact success criterion behind every finding.**

[![CI](https://img.shields.io/github/actions/workflow/status/humbleteam/accessibility-audit/validate.yml?branch=main&style=for-the-badge&logo=github&label=CI)](https://github.com/humbleteam/accessibility-audit/actions/workflows/validate.yml)
[![GitHub stars](https://img.shields.io/github/stars/humbleteam/accessibility-audit?style=for-the-badge&logo=github&color=181717)](https://github.com/humbleteam/accessibility-audit/stargazers)
[![Last commit](https://img.shields.io/github/last-commit/humbleteam/accessibility-audit?style=for-the-badge&color=339933)](https://github.com/humbleteam/accessibility-audit/commits/main)
[![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude_Code-skill-D97757?style=for-the-badge&logo=anthropic&logoColor=white)](https://github.com/humbleteam/accessibility-audit/blob/main/SKILL.md)

</div>

accessibility-audit turns a screenshot, a live URL, or an HTML/JSX snippet into a WCAG 2.2 accessibility review: findings grouped by severity, each tied to the exact success criterion it violates. The rule that keeps it honest: it never claims a pass on something the input can't prove. A screenshot can confirm contrast and target size; it can't confirm alt text, DOM order, or keyboard behavior, and the report says so instead of guessing.

## Table of contents

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Usage](#usage)
- [Example output](#example-output)
- [How it works](#how-it-works)
- [How is this different from just asking the model?](#how-is-this-different-from-just-asking-the-model)
- [FAQ](#faq)
- [Related skills](#related-skills)
- [Who maintains this](#who-maintains-this)

## What it does

- Reviews a screenshot, live URL, or HTML/JSX snippet against WCAG 2.2's success criteria, not a generic "looks fine" pass.
- Groups every finding by severity: P0 blocks use, P1 degrades use, P2 friction.
- Cites the exact success criterion and level for each finding: `WCAG 2.2 SC 1.4.3 (Contrast minimum, Level AA)`.
- States plainly what the input can't prove - DOM order, alt text, ARIA, and keyboard behavior need HTML or a URL.
- Covers three of the nine success criteria new in WCAG 2.2: focus not obscured, target size minimum, accessible authentication.
- Reads a compact WCAG 2.2 checklist as a reference file before every audit so criteria don't drop.

## Quick start

### Personal (all projects)

```bash
git clone https://github.com/humbleteam/accessibility-audit ~/.claude/skills/accessibility-audit
```

### Project (this repo only)

```bash
git clone https://github.com/humbleteam/accessibility-audit .claude/skills/accessibility-audit
```

### Other agents

This skill is plain markdown, in the Agent Skills format. Paste [`SKILL.md`](SKILL.md) into the system prompt of Cursor, Codex, or any LLM agent that accepts custom instructions.

Restart Claude Code after installing, then confirm it loaded - Claude Code reads skills from `~/.claude/skills/` and `.claude/skills/`, and it should appear in the skill list.

## Usage

- "Audit this screenshot of our settings page for accessibility issues." - checks what a static image can prove: contrast, target size, labels, and heading structure.
- "Run an accessibility audit on https://example.com/pricing." - fetches the page and runs the full criteria list, including alt text and ARIA roles.
- "Check this React component for WCAG issues before we ship it." (paste the JSX) - reads the markup to confirm accessible names and structure alongside the visual criteria.

## Example output

Two runs against a fictional app, "Acme Checkout" (not a real client or product). The
first reads a screenshot, where five criteria are checkable outright and the ledger does
most of the work. The second reads a JSX component whose stylesheet was not pasted with
it, which is what the markup path usually looks like in practice: ten criteria come off
the code, and the eight that need a computed value or a rendered page stay in the
ledger.

### Screenshot input

```
# Accessibility audit: Acme Checkout - payment step (screenshot)

**Input type:** screenshot
**Scope:** contrast, non-text contrast, target size, visible labels, heading and label text. Non-text contrast and target size were checked and came back clean. Alt text, DOM order, ARIA, and keyboard behavior not checked - no HTML provided.

## P0 - blocks use
1. **"Pay now" button fails contrast**
   - Observed: white text (#FFFFFF) on a #8FA8D6 background.
   - Fix: darken background to #3D5A80 or below for 4.5:1 against white text.
   - WCAG 2.2 SC 1.4.3 (Contrast minimum, Level AA)

## P1 - degrades use
1. **Card number field has no visible label**
   - Observed: placeholder text only ("1234 5678 9012 3456"), no visible label.
   - Fix: add a persistent label ("Card number") separate from the placeholder.
   - WCAG 2.2 SC 3.3.2 (Labels or instructions, Level A)

## P2 - friction
1. **Two sections share one generic heading**
   - Observed: "Details" sits above the card fields, and "Details" again above the billing-address fields below them, so neither heading names the section it introduces.
   - Fix: rename them "Payment details" and "Billing address" so each heading describes its own section.
   - WCAG 2.2 SC 2.4.6 (Headings and labels, Level AA)

## Not verifiable from this input
- 1.1.1 Non-text content - needs the DOM to confirm alt text on the card-brand icons
- 1.3.1 Info and relationships - the visual heading hierarchy is readable, the DOM structure behind it is not
- 1.4.4 / 1.4.10 Resize text and reflow - need a screenshot at 200% text size and one at a 320px-wide viewport
- 2.1.1 / 2.1.2 Keyboard and keyboard trap - need interaction or code
- 2.4.4 Link purpose - needs the surrounding DOM context for the two footer links
- 2.4.7 / 2.4.11 Focus visible and focus not obscured - need a focus-state screenshot
- 3.2.2 On input - needs the interaction
- 3.3.1 Error identification - needs an error-state screenshot
- 3.3.8 Accessible authentication - needs the login flow
- 4.1.2 Name, role, value - needs the accessibility tree

## Scope note
Expert-review pass against WCAG 2.2, not a substitute for assistive-technology testing, and not a legal ADA or Section 508 compliance certification.
```

### JSX input, styles not supplied

An order-summary component pasted as JSX. The class names resolve to a CSS module that
was not supplied, so nothing that needs a computed color, size, or focus style can be
checked. Ten criteria still resolve off the markup - four as findings, six checked and
clean on the scope line - and the remaining eight fill the ledger, which is where the
count comes back to 18.

```
# Accessibility audit: Acme Checkout - order summary panel (JSX)

**Input type:** HTML/JSX
**Scope:** the full markup - alt attributes, heading structure, keyboard reachability and traps, link purpose, heading and label text, on-input behavior, error identification, visible labels, and accessible names. Checked and clean: 2.1.2 no keyboard trap, 2.4.4 link purpose, 2.4.6 headings and labels, 3.2.2 on input, 3.3.1 error identification, 3.3.2 labels or instructions. The class names resolve to a CSS module that was not supplied, so no computed color, size, or focus style was available.

## P0 - blocks use
1. **Order-details row cannot be reached by keyboard**
   - Observed: `<div className="summary-row" onClick={() => setOpen(!open)}>` with no `role`, no `tabIndex`, and no key handler.
   - Fix: render it as `<button type="button">`, or add `role="button"`, `tabIndex={0}`, and a handler for Enter and Space.
   - WCAG 2.2 SC 2.1.1 (Keyboard, Level A)

## P1 - degrades use
1. **Remove-item button has no accessible name**
   - Observed: `<button onClick={remove}><TrashIcon /></button>` - no `aria-label`, no text child, and the inline SVG carries no `title`.
   - Fix: add `aria-label="Remove <item name>"` to the button and `aria-hidden="true"` to the icon.
   - WCAG 2.2 SC 4.1.2 (Name, role, value, Level A)
2. **Card-brand logo has no alt attribute**
   - Observed: `<img src="/img/visa.svg" />` beside the masked card number, with no `alt`.
   - Fix: `alt="Visa"` where the brand is not named in adjacent text, `alt=""` where it is.
   - WCAG 2.2 SC 1.1.1 (Non-text content, Level A)

## P2 - friction
1. **Heading level skips from h1 to h3**
   - Observed: `<h1>Order summary</h1>` followed by `<h3>Items</h3>` and `<h3>Totals</h3>`, with no h2 between them.
   - Fix: promote both section headings to `<h2>`, or add the h2 they belong under.
   - WCAG 2.2 SC 1.3.1 (Info and relationships, Level A)

## Not verifiable from this input
- 1.4.3 / 1.4.11 Contrast minimum and non-text contrast - the colors are in the unsupplied CSS module; a class name is not a value
- 1.4.4 / 1.4.10 Resize text and reflow - need the rendered page at 200% text size and at a 320px-wide viewport
- 2.4.7 / 2.4.11 Focus visible and focus not obscured - the focus styles are in the same unsupplied CSS, and obscuring needs the rendered layout
- 2.5.8 Target size - needs computed dimensions; the JSX carries class names, not sizes
- 3.3.8 Accessible authentication - this component holds no sign-in step; the criterion is reached from the authentication flow, which was not supplied

## Scope note
Expert-review pass against WCAG 2.2, not a substitute for assistive-technology testing, and not a legal ADA or Section 508 compliance certification.
```

The two headings tell the criteria apart: 2.4.6 asks whether heading and label text describes its
topic, and "Items" and "Totals" do, so it sits on the scope line as clean. The level skip underneath
them is 1.3.1, and it is a finding here only because JSX carries the levels - the screenshot run
above files its heading problem under 2.4.6 for exactly that reason.

## How it works

- The input type - screenshot, URL, or markup - decides which success criteria apply, so identifying it comes first.
- The skill reads a fixed checklist ([`references/wcag22-checklist.md`](references/wcag22-checklist.md)) every run, so criteria stay consistent across audits.
- Screenshot-only audits check contrast, non-text contrast, target size, and visible labels or headings - what pixels can prove. Everything else goes under "Not verifiable from this input" instead of being skipped silently.
- **All 18 criteria are accounted for on every run**, each in exactly one place: a severity finding, the scope line when it was checked and came back clean, the suppressed roster when a P0-only report withholds a lower-severity finding, or the not-verifiable ledger. A screenshot audit normally files 13 criteria under not-verifiable, so a short ledger means criteria went missing rather than that the screen was clean. Markup has its own expected lengths: three when the stylesheet came with it, because resize text, reflow, and focus not obscured need the page rendered under a condition and source code is never that, and at least the seven criteria that need a computed value or a rendered page when the stylesheet did not come. The accounting runs per screen: a report covering two screens gives each its own `##` heading and its own complete set, since a criterion can fail on one and come back clean on the other.
- HTML, JSX, and fetched URLs are read straight off the code for everything code carries: alt attributes, label associations, ARIA roles, heading structure, and focus-management code. A component pasted without its stylesheet keeps contrast, resize, reflow, focus visibility, and target size in the ledger, because a class name is not a value - and a fetch returns the document rather than the stylesheets it links, so a URL is that same case unless those were fetched too. Both URL lengths assume the document carries the screen's markup: a client-rendered app, a sign-in wall or a consent interstitial answers the fetch with a shell, and an audit of a shell reports on a document rather than on the screen, so the skill checks what came back and says so instead. Three criteria stay in the ledger even when every style arrived: resize text, reflow, and focus not obscured are outcomes of layout, and no source file is a rendered page.
- Findings sort into three severity tiers - P0 blocks use, P1 degrades use, P2 friction (keyboard trap = P0, missing focus ring = P1, heading skip in markup = P2) - each carries an observed fact, a concrete fix, and a citation shaped `WCAG 2.2 SC x.x.x (Name, Level A/AA)`.
- The checklist covers three of the nine success criteria new in WCAG 2.2 - 2.4.11 focus not obscured, 2.5.8 target size minimum, 3.3.8 accessible authentication - plus a closing scope note on every report: expert review, not assistive-technology testing, not a legal certification.

## How is this different from just asking the model?

A bare "is this accessible?" prompt usually returns a plausible paragraph that mixes real issues with guesses, skips criteria that need markup instead of flagging them as unknown, and rarely cites which WCAG rule is in play. This skill runs the same 18-criterion checklist every time, so nothing gets dropped because the model noticed something else first. It draws a hard line between what a screenshot can prove and what it can't, instead of assuming a pass on anything unchecked. Every finding carries a citation you can verify against the spec.

## FAQ

**Can AI do an accessibility audit?**
It can do a useful first pass, not the whole job. An LLM can check contrast, spot missing labels, and cite the right WCAG criterion, but it can't replace testing with assistive-technology users.

**What changed in WCAG 2.2?**
WCAG 2.2 added nine success criteria over 2.1; this skill checks three directly: 2.4.11 focus not obscured, 2.5.8 target size minimum, and 3.3.8 accessible authentication. It also removed 4.1.1 parsing, so this skill skips it.

**What contrast ratio does WCAG require?**
WCAG 2.2 SC 1.4.3 requires 4.5:1 for normal text and 3:1 for large text (18pt regular or 14pt bold+) at Level AA. SC 1.4.11 requires 3:1 for non-text elements like icons, borders, and UI-state indicators.

**How do I check accessibility from a screenshot?**
Measure what pixels can prove: contrast, non-text contrast, target size against the 24x24 CSS px minimum, and whether labels and headings are visually present. Everything else needs HTML or a live URL, and gets listed as not verifiable rather than guessed.

**Can I ask for just the critical accessibility issues?**
Yes - ask for a quick check or just the blockers, and only P0 gets written up. The full 18-criterion pass still runs underneath it, and any criterion that produced a P1 or P2 finding is named on one line under "Suppressed at this depth" with the tier it landed in. A shorter report never turns a finding into a silent pass; ask for the detail on any of those lines and it gets written up.

**Can it audit a single-page app from its URL?**
Only as far as the fetch reaches. A client-rendered app answers a fetch with a near-empty document - a mount node and its scripts - so no heading structure, no labels and no ARIA arrive with it, and auditing that would be reporting on a shell rather than on the screen. The same happens behind a sign-in wall or a consent interstitial, which answer with a different page than the one you asked for. The skill checks what came back before auditing it and says so in the scope line. Copy the rendered DOM out of your browser's element inspector, or add a screenshot, and the audit has the real page to read.

**Does this replace a legal ADA or Section 508 audit?**
No. It's an expert-review pass grounded in WCAG 2.2, not a legal compliance certification. Legal compliance audits typically require testing with assistive-technology users and a documented methodology beyond one review pass.

## Related skills

Part of a 10-skill open-source kit for design teams by Humbleteam.

- [design-review](https://github.com/humbleteam/design-review) - structured UX critique with a 0-4 score, Before/After/Why fixes, and a citation for every claim.
- [ascii-wireframes](https://github.com/humbleteam/ascii-wireframes) - three distinct layout hypotheses as ASCII wireframes before any hi-fi work.
- [html-mockup](https://github.com/humbleteam/html-mockup) - census-first HTML mockups that match a reference screenshot: exact palette, item counts, component states.
- [extract-design-tokens](https://github.com/humbleteam/extract-design-tokens) - pull palette, type, spacing, radii, and shadows from a URL or screenshot into CSS variables and JSON.
- [audit-design-tokens](https://github.com/humbleteam/audit-design-tokens) - find token drift in a codebase: raw hex values, off-scale spacing, near-duplicate colors.
- [design-qa](https://github.com/humbleteam/design-qa) - a pre-ship design QA gate: states, contrast, touch targets, breakpoints, keyboard paths.
- [design-handoff](https://github.com/humbleteam/design-handoff) - turn a finished mockup into a dev-ready spec: tokens, states, accessibility annotations, open questions.
- [ux-writing](https://github.com/humbleteam/ux-writing) - interface copy that reads human: plain-verb microcopy rules and an AI-tell strip pass.
- [design-brief](https://github.com/humbleteam/design-brief) - extract a 5-bullet design brief from messy project inputs, with a gap report for what is missing.

## Who maintains this

[Humbleteam](https://humbleteam.com/) is a digital product design and AI-engineering studio: founded in 2017, working from Prague and Dubai, with 80+ digital awards to the name, including 14 Awwwards wins, a Webby, and a Red Dot. We design digital products for startups and enterprises in fintech, healthtech, sports, and AI, and we build AI infrastructure for design teams - agents, workflows, and skills like this one.

This skill is distilled from the internal playbooks we run on client work: the same checklists behind the case studies at [humbleteam.com/work](https://humbleteam.com/work), for clients like Tinder and Acronis.

- The full 10-skill kit: [Related skills](#related-skills) above, or all repos at [github.com/humbleteam](https://github.com/humbleteam)
- What we do with AI for design teams: [humbleteam.com/ai](https://humbleteam.com/ai)
- Design and AI writing: [humbleteam.com/blog](https://humbleteam.com/blog)
- LinkedIn: [linkedin.com/company/humbleteam](https://www.linkedin.com/company/humbleteam/)
- Talk to us: [hi@humbleteam.com](mailto:hi@humbleteam.com)

Issues and PRs welcome.

MIT - see [LICENSE](LICENSE).
