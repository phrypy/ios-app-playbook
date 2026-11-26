---
name: app-store-screenshot-pipeline
description: Use when producing App Store screenshots from real app renders instead of faked CSS mockups, covering the headless simulator export stage, the composition-to-device-sizes stage, and the gotchas that quietly waste hours. Pairs with ios-app-store-optimization, which covers what the screenshots should say.
---

# App Store Screenshot Pipeline

Marketing screenshots should be the real app, rendered, not a div pretending to be the app.

A CSS mockup drifts from the product the moment you ship a UI change. A real render stays honest, and the listing keeps matching the first-run experience. Build screenshots in two stages: render real views from the app, then compose those PNGs into App Store sizes.

For what the screenshots should say, see `ios-app-store-optimization`. This skill only produces the files from that approved direction; it does not decide the listing message or visual identity.

## The Two Stages

1. headless render from the app, offscreen, on a booted simulator
2. compose those renders into App Store device sizes and upload

Keep the stages separate. Stage one owns pixels of real UI. Stage two owns layout, headlines, and final dimensions.

## Stage 1: Headless Render From The App

Add export functions behind an env gate so they never touch production paths.

```swift
if ProcessInfo.processInfo.environment["APP_EXPORT_SHOTS"] == "1" {
    renderOffscreenAndWritePNGs()
}
```

Inside that path, rasterize specific views with SwiftUI `ImageRenderer` (or `UIGraphicsImageRenderer`) and write PNGs into the app's Documents directory. Capture the running build when system chrome or the current device layout is part of the story, especially for iPhone Duo.

Trigger it from a booted simulator and pass the env with the `SIMCTL_CHILD_` prefix:

```sh
SIMCTL_CHILD_APP_EXPORT_SHOTS=1 xcrun simctl launch "$SIMULATOR_UDID" "$APP_BUNDLE_ID"
```

Then pull the PNGs out of the app's data container.

## Stage 1 Gotchas

These are the ones that cost real time. Read them before you start.

- `ImageRenderer` can produce placeholders for platform-backed controls and containers. If a navigation container, scroll view, or menu picker fails offscreen, export its plain SwiftUI content or capture the running screen. [ImageRenderer documentation](https://developer.apple.com/documentation/swiftui/imagerenderer)
- `simctl install` changes the app's data container id. Call `xcrun simctl get_app_container <device> <bundleid> data` after install, never before, or you will read the wrong folder.
- pass env to the launched app with the `SIMCTL_CHILD_` prefix. Without it the variable is set on `simctl`, not on your app.
- render at scale 2 to 3, and set `isOpaque = false` when you need transparency.
- transparent intermediate PNGs are useful for composition. Flatten all final submission screenshots onto an opaque background.
- when inspecting renders as an agent, downscale first with `sips` before viewing. Very large PNGs fail to display.

## Stage 2: Compose To App Store Sizes

Use a small Next.js plus Playwright generator. Lay out each slide in the browser using the real PNGs from stage one, then capture each slide at literal device pixel dimensions.

Review the required display sizes for the app's platform and locale in App Store Connect. Apple's current specifications include a medium Dynamic Island iPhone set and a 13-inch iPad set for apps that support iPad. [Apple screenshot specifications](https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications)

For iPhone Duo, capture the actual outer and inner layouts. The portrait sizes are **1398 × 2034** for the outer display and **2007 × 2853** for the inner display. Use the transposed dimensions for landscape. Apple's April 2027 requirement applies to apps using the iOS 27.1 SDK or later. [iPhone Duo preparation](https://developer.apple.com/iphone-duo/prepare/) · [Apple screenshot specifications](https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications)

Capture at exact pixels. Do not capture at a CSS size and scale up, or text and device frames go soft.

One sync trap: if a Node export script keeps its own hardcoded slug or slide list, it will silently skip any slide you add to the manifest but forget to add to the script. Either drive the export from the manifest, or treat the two lists as one thing you change together. A new slide that never exports is easy to miss until upload.

## Validate and Upload

Check which display sets your installed `asc` supports. This example validates and previews an upload for the medium Dynamic Island display. Set `VERSION_LOCALIZATION_ID` to the App Store version localization ID and use the folder containing that locale's images.

```sh
asc screenshots sizes --all
asc screenshots validate --device-type APP_IPHONE_61 --path metadata/screenshots/iphone-medium/en-US
asc screenshots upload --version-localization "$VERSION_LOCALIZATION_ID" \
  --device-type APP_IPHONE_61 --path metadata/screenshots/iphone-medium/en-US --dry-run
```

For a display missing from your CLI, upload its set in App Store Connect. Check the resulting order, locale, and dimensions before submitting. If replacing an existing set with `asc`, include `--replace` in the dry run and confirm the replacement set before applying it.

## Avoid

- div and CSS mockups standing in for real UI
- ignoring blank or placeholder output from offscreen renders
- reading the data container path before install
- forgetting the `SIMCTL_CHILD_` prefix and assuming the env reached the app
- uploading transparent screenshots or reusing a capture from a different layout
- a manifest slide that never reaches the exporter because a hardcoded list went stale
- leaving screenshots from a previous version in the set
