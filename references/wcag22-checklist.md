# WCAG 2.2 checklist for accessibility audits

Read this before every audit run inside `SKILL.md`. Each row names the success criterion, its conformance level, whether a screenshot alone can verify it, and what to actually look for. The wording here is original; the SC numbers and names are the public WCAG 2.2 identifiers.

| SC | Name | Level | Screenshot-checkable | What to look for |
|---|---|---|---|---|
| 1.1.1 | Non-text content | A | No | Alt attribute present and meaningful on every informative image; decorative images marked empty - needs the DOM |
| 1.3.1 | Info and relationships | A | Partial | Heading levels, list markup, and table structure carry the same relationships visually and in the DOM |
| 1.4.3 | Contrast minimum | AA | Yes | Text vs. background at least 4.5:1 for normal text, 3:1 for large text (18pt+/14pt+ bold) |
| 1.4.4 | Resize text | AA | Partial | Content stays usable and unclipped when text is resized to 200% - verifiable only from a screenshot taken at 200% text size, otherwise not verifiable |
| 1.4.10 | Reflow | AA | Partial | Content reflows to a single column at 320px wide with no horizontal scrolling - verifiable only from a screenshot at a 320px-wide viewport, otherwise not verifiable |
| 1.4.11 | Non-text contrast | AA | Yes | Icons, borders, and UI-state indicators at least 3:1 against the adjacent color |
| 2.1.1 | Keyboard | A | No | Every interactive element reachable and operable by keyboard alone - needs interaction or code |
| 2.1.2 | No keyboard trap | A | No | Keyboard focus can always move away from a component - needs interaction |
| 2.4.4 | Link purpose (in context) | A | Partial | Link text plus its surrounding sentence or list item makes the destination clear without "click here" |
| 2.4.6 | Headings and labels | AA | Yes | Headings and form labels describe the topic or purpose, not generic filler text |
| 2.4.7 | Focus visible | AA | Partial | A visible focus indicator exists on every interactive element - needs a focus-state screenshot |
| 2.4.11 | Focus not obscured (minimum) | AA | Partial | The focused element is not fully hidden behind sticky headers, footers, or overlays - needs a focus-state screenshot. New in WCAG 2.2 |
| 2.5.8 | Target size (minimum) | AA | Yes | Tappable targets are at least 24x24 CSS px, or have enough spacing to avoid accidental taps. New in WCAG 2.2 |
| 3.2.2 | On input | A | No | Changing a setting or filling a field does not trigger an unexpected context change - needs interaction |
| 3.3.1 | Error identification | A | Partial | Errors are described in text, not by color alone - needs an error-state screenshot |
| 3.3.2 | Labels or instructions | A | Yes | Inputs that need a specific format or value show a visible label or instruction |
| 3.3.8 | Accessible authentication (minimum) | AA | No | Login does not require solving a cognitive test with no alternative, such as remembering a puzzle - needs the flow. New in WCAG 2.2 |
| 4.1.2 | Name, role, value | A | No | Every custom control exposes an accessible name, role, and state - needs the DOM or accessibility tree |

## Reading this table

- **Screenshot-checkable: Yes** - evaluate directly from a static image. Five criteria: 1.4.3, 1.4.11, 2.4.6, 2.5.8, 3.3.2.
- **Partial** - evaluate what you can, but flag the rest under "Not verifiable from this input" unless the right screenshot state was provided. The qualifying states are a focus state (2.4.7, 2.4.11), an error state (3.3.1), a 320px-wide viewport (1.4.10), and 200% text size (1.4.4); 1.3.1 and 2.4.4 are partial because the visual layer is readable and the DOM layer is not. Seven criteria in total.
- **No** - always file under "Not verifiable from this input" unless HTML, JSX, or a live URL was given instead of a screenshot. Six criteria: 1.1.1, 2.1.1, 2.1.2, 3.2.2, 3.3.8, 4.1.2.

The three grades cover 5 + 7 + 6 = all 18 rows, and that arithmetic is the point: `SKILL.md` Step 3 sorts every criterion into one of these three groups, so a criterion missing from the grades is a criterion that can leave an audit without a verdict. If you add a row here, add it to Step 3 in the same edit and update these counts.
