---
name: swiftui-product-ui
description: Use when composing a new or materially changed SwiftUI product screen or flow that needs clear hierarchy, state ownership, and a focused user journey.
---

# SwiftUI Product UI

The target is product quality, not generic demo UI.

## Core Principles

- preserve the app's existing visual language when one exists
- if there is no strong language yet, pick one intentionally
- optimize for scanability, confidence, and speed
- keep screens calm even when feature-rich

## Owns

- screen hierarchy, primary action, and state presentation
- SwiftUI composition and local state ownership
- product-specific screen flow and content priorities

Use specialist skills for navigation structure, forms, first-run, trust-sensitive settings, adaptive layout, accessibility, motion, or post-build polish. This skill coordinates those concerns; it does not replace them.

## Visual Rules

- one strong primary action per screen
- clear hierarchy before decoration
- spacing should separate meaning, not just fill space
- avoid default-looking stacks with interchangeable cards
- use color semantically, not decoratively

## Product Patterns

- dashboards should answer "what matters right now"
- settings should feel operational, not overwhelming; use `ios-settings-and-data-safety-ux` for sync, restore, permissions, or destructive actions
- onboarding should get users to value quickly
- empty states should teach the next move
- forms should reduce uncertainty before submission

## SwiftUI Composition

- break long bodies into small readable sections
- use dedicated subviews when a section has distinct intent
- avoid computed view helpers that hide too much logic
- keep state ownership obvious
- when building with Xcode 27, review `@State` initialization and any generic result builders that stop compiling. [SwiftUI migration technote](https://developer.apple.com/documentation/technotes/tn3211-resolving-swiftui-source-incompatibilities-for-state-and-contentbuilder)
- use the new iOS 27 toolbar, swipe, and reorder APIs when they simplify a flow. Preserve an accessible fallback on earlier OS versions. [iOS 27 API choices](../../guides/ios-27-update-playbook.md#4-choose-swiftui-apis-for-specific-flows)

## Polish Checklist

- typography has visible hierarchy
- interactive elements look tappable
- loading, empty, success, and error states exist
- destructive actions are clearly separated
- sheets and navigation destinations feel intentional
- hand off iPhone and iPad adaptation to `ios-adaptive-layout`

## Avoid

- purple-on-white defaults
- glassmorphism as a substitute for hierarchy
- too many equal-weight cards
- over-animated transitions for utility screens
- hiding product risk behind vague button copy
