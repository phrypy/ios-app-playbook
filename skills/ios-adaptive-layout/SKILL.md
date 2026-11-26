---
name: ios-adaptive-layout
description: Use when adapting an iOS app across iPhone and iPad, handling safe areas, readable widths, compact versus regular size classes, split layouts, and SwiftUI structures that need to stay native across device contexts.
---

# iOS Adaptive Layout

Adaptability should feel natural, not like a stretched phone UI.

## Rules

- design for small iPhone first
- expand with intention on larger devices
- keep readable width under control
- avoid edge-to-edge text walls on iPad
- use available space to improve clarity, not just fit more widgets
- with the iOS 27.1 SDK or later, test iPhone Duo outer and inner displays, partial folds, and Split View
- let system bars change axis and lay out custom content using window geometry, safe areas, and applicable reserved regions
- preserve selection, draft input, and task state while moving between compact and expanded layouts

Use `NavigationSplitView` for navigation hierarchy where it fits. Consider `ArrangementView` on iOS 27.1 and later for a primary/secondary content pair. Check which content stays visible in compact poses and review the API's container nesting guidance. [ArrangementView](https://developer.apple.com/documentation/swiftui/arrangementview) · [iOS 27 layout and fallback example](../../guides/ios-27-update-playbook.md#3-test-real-resizing-on-iphone-and-ipad)

## Review Questions

- does the iPad version feel intentionally laid out
- do safe areas and system bars still read correctly
- does content become more readable as width grows
- are touch targets still easy to reach
