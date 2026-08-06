# Changelog

## [1.1.0] - 2026-08-06

- New edge case: a screenshot with an unknown scale. Target size (SC 2.5.8) is measured in CSS pixels, so a 2x or 3x screenshot measured in raw image pixels produces false findings in both directions. The audit now settles the scale first, or reports 2.5.8 as not verifiable.

## [1.0.0] - 2026-07-12

- Initial release: WCAG 2.2 accessibility audit skill for screenshots, URLs, and HTML/JSX.
- Findings grouped by severity (P0 blocks use, P1 degrades use, P2 friction), each with a success-criterion citation.
- Ships a compact WCAG 2.2 checklist covering all three criteria new in 2.2: focus not obscured, target size minimum, accessible authentication.
- CI workflow validates SKILL.md frontmatter and the README-to-SKILL.md link.
