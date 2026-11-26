# iOS 27 Update Playbook

Rebuild your existing app, find what changed, and fix the flows people already use. Start with the toolchain and launch checks, then review layouts and release requirements.

## 1. Establish the build and support baseline

| What to check | Status as of 26 November 2026 |
|---|---|
| Stable toolchain | Xcode 27.2 includes the iOS and iPadOS 27.2 SDKs and the Swift 6.4 compiler. It runs on macOS Tahoe 26.6 or later. [Xcode support](https://developer.apple.com/xcode/system-requirements/) |
| Deployment and testing | Xcode 27.2 supports deployment to iOS and iPadOS 15 and later. Its device debugging and Simulator support start at iOS and iPadOS 17. [Xcode support](https://developer.apple.com/xcode/system-requirements/) |
| Current uploads | App Store Connect accepts builds from Xcode 27.2 for TestFlight and App Review. The current minimums are the iOS and iPadOS 26 SDK and an iOS 13 deployment target. [App Store Connect release notes](https://developer.apple.com/help/app-store-connect/release-notes/) · [Current minimums](https://developer.apple.com/news/upcoming-requirements/) |
| From April 2027 | Uploads need the iOS and iPadOS 27 SDK or later and a deployment target of iOS 15 or later. Apple also lists iPhone Duo screenshots as required for apps built with the iOS 27.1 SDK or later. [Submission requirements](https://developer.apple.com/app-store/submitting/) · [Screenshot specifications](https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications) |

Confirm which Xcode and SDK your shell and CI actually use.

```sh
xcodebuild -version
xcrun --sdk iphoneos --show-sdk-version
```

- Rebuild the app, extensions, and test targets with the same toolchain locally and in CI. Keep your current Swift language mode and deployment target where Xcode supports them. If you support iOS 13 or 14, assess how a move to iOS 15 would affect those customers.
- Test a clean install and an upgrade over the last shipped build. Use a current iOS 27 device and the oldest OS you support. For iOS 15 or 16, arrange a compatible way to install the signed build on hardware.
- Walk through first run, opening existing data, permissions, purchases and restores, and returning from the background. Check known issues in the [Xcode 27.2 release notes](https://developer.apple.com/documentation/xcode-release-notes/xcode-27_2-release-notes) and [iOS and iPadOS 27.2 release notes](https://developer.apple.com/documentation/ios-ipados-release-notes/ios-ipados-27_2-release-notes).

## 2. Check what the rebuild changes

- **UIKit launch and routing.** On iOS 27, UIKit apps built with the new SDK must adopt the scene-based lifecycle to launch. Check `UIApplicationSceneManifest` or the scene configuration in your app delegate. Migrate window setup and handling for deep links, restoration, and Home Screen quick actions to the appropriate scene callbacks. Cold-launch the release build directly and from a link. [Scene lifecycle migration](https://developer.apple.com/documentation/technotes/tn3187-migrating-to-the-uikit-scene-based-life-cycle) · [UIKit updates](https://developer.apple.com/documentation/updates/uikit)
- **UIKit resizing.** Review any use of `UIScreen.main.bounds` and the deprecated `UIRequiresFullScreen`. With the iOS 27 SDK, that key can change when the system delivers a resize. Use the actual view or scene bounds to lay out content. [Apple's resizing technote](https://developer.apple.com/documentation/technotes/tn3192-migrating-your-app-from-the-deprecated-uirequiresfullscreen-key)
- **SwiftUI build and state.** The new `@State` macro can expose initialization-order errors. Lazy class initialization back-deploys to iOS 17. If an initializer used to start work, give that work an explicit lifecycle. For errors in generic view builders, check the `ContentBuilder` migration guidance. [Apple's SwiftUI migration technote](https://developer.apple.com/documentation/technotes/tn3211-resolving-swiftui-source-incompatibilities-for-state-and-contentbuilder) · [WWDC26 SwiftUI session](https://developer.apple.com/videos/play/wwdc2026/269/)
- **System appearance.** Review any screens that relied on `UIDesignRequiresCompatibility`. Apple says the system ignores this key when you build for iOS 27 or later. Check navigation, tab bars, sheets, and menus in light and dark appearances, with Increase Contrast and Reduce Transparency. Compare the last shipped build on the same OS when you need to isolate a change. [UI compatibility key](https://developer.apple.com/documentation/bundleresources/information-property-list/uidesignrequirescompatibility)

## 3. Test real resizing on iPhone and iPad

The iOS 27.1 SDK and later give apps full-display layouts and vertical system bars on iPhone Duo. Keep the same task available as the device moves between its outer display, inner display, and partial folds. Also check iPad window resizing if you ship on iPad. [Preparing for iPhone Duo](https://developer.apple.com/iphone-duo/prepare/)

- Scan for fixed screen-size assumptions. Xcode's App Resizability skill can help locate those before you test the main flows. [Apple's resizability walkthrough](https://developer.apple.com/videos/play/tech-talks/111461/?time=552)
- Let system tab and toolbar placement adapt. Give actions both a meaningful title and a symbol. Keep the primary action reachable and check the menu that appears when space runs out.
- Start with safe areas and layout margins. Check the fold and camera regions for custom full-bleed content or controls. For custom layouts that need their geometry, use the `reservedRegions` APIs available from iOS 27.1. [iPhone Duo HIG](https://developer.apple.com/design/human-interface-guidelines/designing-for-iphone-duo) · [SwiftUI reserved regions](https://developer.apple.com/documentation/swiftui/geometryproxy/reservedregions(kind:options:layoutdirectionbehavior:))
- Choose `NavigationSplitView` for list/detail navigation. Choose `ArrangementView` on iOS 27.1 or later when two related content views need to adapt around available space and the fold. Check the API's nesting guidance before combining containers. [ArrangementView](https://developer.apple.com/documentation/swiftui/arrangementview)
- Open a detail or start entering a draft, then close, open, partially fold, and resize in Split View. Repeat with a sheet or keyboard visible. Check that selection, input, and key actions survive. Include a short outer-display layout, large Dynamic Type, VoiceOver, and right-to-left text. [Adaptive layout skill](../skills/ios-adaptive-layout/SKILL.md) · [Accessibility checklist](../checklists/accessibility-review.md)

### SwiftUI example with an older-OS layout

This editor keeps the draft in the parent view and uses a secondary preview that can safely disappear in a compact arrangement. Build it with an iOS 27.1 SDK or later. The availability check keeps the stacked layout available on earlier OS versions.

```swift
import SwiftUI

struct DraftEditor: View {
    @State private var title = ""

    var body: some View {
        if #available(iOS 27.1, *) {
            ArrangementView {
                ScrollView { editor.padding() }
            } secondary: {
                ScrollView { preview.padding() }
            }
            .arrangementViewStyle(.split)
        } else {
            ScrollView {
                VStack(alignment: .leading, spacing: 24) {
                    editor
                    preview
                }
                .padding()
            }
        }
    }

    private var editor: some View {
        TextField("Title", text: $title)
            .textFieldStyle(.roundedBorder)
    }

    private var preview: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("Preview").font(.headline)
            if title.isEmpty {
                Text("Your title appears here.").foregroundStyle(.secondary)
            } else {
                Text(title)
            }
        }
        .frame(maxWidth: .infinity, alignment: .leading)
    }
}
```

## 4. Choose SwiftUI APIs for specific flows

Use an availability check for each new API and keep a usable path on earlier systems. See the [SwiftUI updates](https://developer.apple.com/documentation/updates/swiftui) and [WWDC26 SwiftUI guide](https://developer.apple.com/wwdc26/guides/swiftui/).

| Existing need | iOS 27 option | Check before adopting |
|---|---|---|
| Crowded toolbars | `visibilityPriority(_:)`, `ToolbarOverflowMenu`, and `.topBarPinnedTrailing` | Keep the primary action reachable and give overflow items useful titles. |
| Reordering outside a `List` | `reorderable()` and `reorderContainer(for:isEnabled:move:)` | Provide an accessible move action and check that the new order persists. |
| Swipe actions in custom scroll layouts | `swipeActionsContainer()` | Offer another way to reach important actions. |
| Large documents | `ReadableDocument`, `WritableDocument`, and `DocumentCreationSource` | Test open, save, cancel, and recovery with documents created by the old version. |
| Remote images | `AsyncImage` respects HTTP caching on iOS 27 | Check server cache headers and how replaced images become visible. |

## 5. Review the integrations your app uses

- **App Intents and Siri.** Use the App Intents testing framework for entities and actions you expose. Verify stable identifiers and try the action from the surface people will use, such as Shortcuts or Siri. [WWDC26 iOS guide](https://developer.apple.com/wwdc26/guides/ios/)
- **Widgets.** Review the WidgetKit changes relevant to your existing formats. Check configured and unconfigured states, stale data, and what people see on a locked device. [WidgetKit updates](https://developer.apple.com/documentation/updates/widgetkit)
- **Models.** Foundation Models adds support for different model providers and multimodal prompts. Core AI supports running custom models on-device. Verify availability and what data goes to the provider, then test how the flow behaves when the model is unavailable or returns an unusable answer. [WWDC26 iOS guide](https://developer.apple.com/wwdc26/guides/ios/)

## 6. Prepare the App Store submission

- Capture the actual release UI. For iPhone Duo, prepare dedicated images of the outer and inner displays and preview the sets in App Store Connect. [Screenshot specifications](https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications) · [Screenshot pipeline](../skills/app-store-screenshot-pipeline/SKILL.md)
- Answer the current age rating questionnaire, including the social media capability questions required for new apps and updates since September 2026. Check App Privacy details and bundled manifests against the app and its dependencies. Review any Accessibility Nutrition Labels you publish against the features tested on each device. [Age rating update](https://developer.apple.com/news/?id=tlur8uvi) · [Accessibility Nutrition Labels](https://developer.apple.com/help/app-store-connect/manage-app-accessibility/overview-of-accessibility-nutrition-labels)
- Use the [App Store preflight](../checklists/app-store-preflight.md) on the release candidate before submission.

## Done when

Core flows work on iOS 27 and the oldest supported OS. Editing, navigation, and key actions survive resizing. The App Store assets and declarations match the release candidate. Record the toolchain, OS versions, and fallbacks you actually tested.
