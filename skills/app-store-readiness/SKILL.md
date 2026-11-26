---
name: app-store-readiness
description: Use when auditing a prepared iOS release for App Store or TestFlight submission risk, including metadata, privacy, capabilities, and reviewer-facing behavior.
---

# App Store Readiness

Submission quality is product quality plus review hygiene.

This is the submission audit gate. It does not archive, upload, or release the build. Use `solo-ios-release-flow` for the release sequence and `ship-ios-build-one-command` for the archive-to-upload operation.

## Audit In This Order

1. reviewer access and runtime privacy
2. the release candidate and archive
3. purchases, restore, and account flows
4. review notes, listing assets, and App Store Connect readiness

Work through the [App Store preflight](../../checklists/app-store-preflight.md). Tie each result to the version and build, date, and evidence. Record a reason for each check that does not apply.

## Evidence for the Submission Decision

- use source and archive checks to flag issues in manifests, capabilities, and permission descriptions
- reproduce reviewer sign-in, data handling, purchases, restore, and account deletion on the release candidate where applicable
- verify review information, App Privacy answers, screenshot sets, and applicable agreements or declarations in App Store Connect
- for each issue name the current Apple rule, what you observed, and the next check or fix

Keep assumptions marked unchecked until you can inspect the build or store record. Recheck affected results when the candidate, backend, or listing changes. [Review preparation guide](../../guides/how-to-prep-an-indie-ios-app-for-app-store-review.md)

## Metadata Rules

- never claim a feature is live if it is hidden, disabled, or "coming soon"
- title, subtitle, promo text, and description should match the shipped build
- if a capability is conditional or manual, describe it honestly

## Review Risk Areas

- unused background modes
- incomplete privacy manifests
- entitlement drift
- broken paywalls or restore flows
- misleading screenshots
- half-finished imports, notifications, or sync claims
- stale age-rating answers, including the social-media-capability questions required for submissions from September 2026
- published Accessibility Nutrition Labels without evidence from the shipping build

## iCloud And Restore Rules

- do not label a status refresh as a restore
- destructive or recovery actions need explicit user language
- if a restore path exists, explain exactly what device state it rebuilds and when

## Final Standard

Resolve failed and unchecked submission requirements before calling a build ready. Confirm the reviewer can reach the main features and the listing reflects the evidence from the candidate.
