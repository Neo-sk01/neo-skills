---
name: frontend-ui-engineering
description: Builds production-quality UI with accessibility, responsive design, and component architecture. Use when implementing user-facing interfaces.
---

# Frontend UI Engineering

## Overview

Build UI that works for everyone — accessible, responsive, performant, and maintainable. Components should be composable, testable, and follow the project's design system.

## When to Use

- Implementing user-facing interfaces
- Creating or modifying UI components
- Working with CSS, layouts, or responsive design
- Building forms, navigation, or interactive elements

## When NOT to Use

- Backend-only changes
- CLI tools or scripts
- API-only work — go to `incremental-implementation`

## Integration Points

**Context7:** Verify component APIs and framework patterns before implementing. Frontend frameworks change rapidly — don't trust training data for version-specific syntax.

## Core Process

### 1. Understand the component

Before building:
- What is the component's single responsibility?
- What are its inputs (props) and outputs (events/callbacks)?
- What states can it be in? (loading, empty, error, populated, disabled)
- What accessibility requirements apply?

### 2. Start with semantic HTML

Write the markup first, without styling:
- Use semantic elements (`<nav>`, `<main>`, `<article>`, `<button>`, not `<div>` for everything)
- Add ARIA attributes where semantics alone aren't sufficient
- Ensure keyboard navigation works before adding any JavaScript

### 3. Add styles

Follow the project's CSS approach (modules, Tailwind, styled-components, etc.). Check CODEBASE.md.

Responsive design:
- Mobile-first: start with the smallest viewport, add complexity for larger
- Use relative units (rem, %, viewport units) over fixed pixels
- Test at: 320px, 768px, 1024px, 1440px minimum

### 4. Add behavior

Interactive elements:
- All interactive elements reachable by keyboard (Tab, Enter, Escape, Arrow keys)
- Focus management: focus moves logically, focus traps in modals
- Loading states: show feedback immediately, not after a delay
- Error states: clear, actionable error messages near the relevant input

### 5. Accessibility checklist

Every component must pass:
- [ ] Color contrast ratio >= 4.5:1 for normal text, >= 3:1 for large text
- [ ] All images have meaningful alt text (or empty alt="" for decorative)
- [ ] Form inputs have visible labels (not just placeholders)
- [ ] Interactive elements have minimum 44x44px touch targets
- [ ] Component works with screen reader (meaningful announcements)
- [ ] No content conveyed by color alone
- [ ] Animations respect `prefers-reduced-motion`

### 6. Test in browser

Start the dev server. Verify:
- Golden path works as expected
- Edge cases (empty state, long content, error state)
- Responsive behavior at multiple viewport sizes
- Keyboard navigation
- No console errors

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I'll add accessibility later" | Accessibility retrofits cost 10x more than building it in. |
| "It works on desktop, we'll fix mobile later" | Mobile-first is easier to expand up than desktop-first is to shrink down. |
| "The tests pass, it must look right" | Tests verify logic, not visual correctness. Look at it in a browser. |
| "Divs with click handlers work fine" | They don't. Use buttons for actions, links for navigation. Keyboard and screen reader users can't use click-handler divs. |

## Red Flags

- `<div onClick>` instead of `<button>`
- No alt text on images
- Fixed pixel widths on containers
- No loading or error states
- No keyboard navigation support
- Never tested in a browser during development
- Inline styles everywhere instead of following project CSS patterns

## Verification

- [ ] Semantic HTML used (no `<div>` soup)
- [ ] Accessibility checklist passes
- [ ] Responsive at 320px, 768px, 1024px, 1440px
- [ ] Keyboard navigation works for all interactive elements
- [ ] Loading, empty, and error states handled
- [ ] Tested in browser — golden path and edge cases
- [ ] CSS follows project conventions (from CODEBASE.md)
