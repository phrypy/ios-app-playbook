---
name: solo-ios-release-flow
description: Use when coordinating the end-to-end release sequence for a prepared SwiftUI app, from final verification through TestFlight or App Store submission.
---

# Solo iOS Release Flow

Ship with a repeatable loop. Do not improvise the final mile.

## Recommended Sequence

1. clean up real warnings
2. run the relevant tests
3. build the app in release conditions
4. smoke test the main user flows
5. use `app-store-readiness` for checks you can complete before upload
6. prepare screenshots and metadata for the candidate, then archive and upload
7. for App Store submission, complete `app-store-readiness` on the selected build before submitting

When moving to the iOS 27 SDK, record the Xcode and SDK versions used for the archive. Test the release candidate on iOS 27 and the oldest supported OS, including upgrades with existing data and any feature fallbacks. [iOS 27 Update Playbook](../../guides/ios-27-update-playbook.md)

Use `ship-ios-build-one-command` for archiving, signing, exporting, and uploading. Return to `app-store-readiness` for the submission decision once the archive, processed build, listing assets, and review notes are available.

## Before You Upload

- remove debug-only behavior
- confirm version and build numbers
- hand App Store metadata, privacy, capability, and reviewer-risk checks to `app-store-readiness`

## Solo Builder Priorities

- stability beats extra features
- reduce ambiguity in settings, paywalls, and sync
- do one final pass on first-run experience
- keep release notes tight and honest

## Go / No-Go Questions

- can a new user understand the core loop
- can a paying user understand what they are buying
- can a reviewer reach the important surfaces without confusion
- does the app promise only what the build actually does

If any answer is no, stop and fix that first.

## After Upload

- watch build processing
- select the processed build and verify its metadata, screenshots, and review notes in App Store Connect
- keep one short list of post-submit follow-ups instead of thrashing the release
