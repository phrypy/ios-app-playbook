---
name: ios-accessibility-design-review
description: Use when auditing an iOS interface for accessibility, including Dynamic Type, contrast, VoiceOver clarity, hit targets, reduced motion, and interaction patterns that need to remain usable for a wider range of people.
---

# iOS Accessibility Design Review

Accessibility is part of product quality, not a separate pass.

## Review Areas

- Dynamic Type resilience
- contrast and readability
- hit target size
- VoiceOver labels and values
- reduced motion friendliness
- error and validation clarity

## Fast Audit

1. enlarge text
2. inspect contrast
3. test main controls without relying on color
4. check labels on important controls
5. review motion intensity

On layouts with vertical bars or overflow, use VoiceOver to find and invoke the same primary actions before and after resizing. Check that users can reach essential swipe and reorder actions through visible controls or accessibility actions. Use the tested tasks to assess any Accessibility Nutrition Labels the app publishes. [Accessibility checklist](../../checklists/accessibility-review.md)

If the flow only works for visually precise, fully sighted, calm conditions, it is not ready.
