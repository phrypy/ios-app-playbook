# How To Prep An Indie iOS App For App Store Review

Start with the build you plan to submit. Give App Review a short route through the app and back up your submission answers with evidence from that build. Use the [App Store preflight](../checklists/app-store-preflight.md) to track the checks.

## Rehearse the Review

Start signed out on a device with a fresh install. Use the demo credentials you will provide, where applicable, and follow the steps in your review notes without relying on internal setup. Make sure the review account can get to meaningful data and that its services remain available during review. If the app offers auto-renewable subscriptions, Apple asks for a demo account with an expired subscription so the reviewer can see the purchase flow. [Apple's review information guide](https://developer.apple.com/help/app-review/before-submitting-for-review/complete-review/)

Keep the review notes short enough to follow on a phone.

- Describe the app's purpose or what changed in the update.
- Give the exact taps to reach a feature, the input the reviewer needs, and the result they should see.
- Include any account roles, sample QR codes, device requirements, or regional differences that affect the review. Enter demo account details in App Review Information.

## Record Evidence for the Candidate

Record the version, build number, and date in the release checklist you use. For fixes to the build, backend, or store listing, repeat the affected checks against the new state.

| Where to check | What you can verify |
|---|---|
| Archive and source | Inspect the archive for bundled privacy manifests, entitlements, and permission descriptions. A search can flag a missing key or a dependency to investigate. |
| Running release candidate | Use test data to verify reviewer sign-in, purchase and restore, account deletion, and data handling where applicable. Record the device and OS alongside the result. |
| App Store Connect | Check the selected build, review details, published privacy answers, screenshot sets, and account or storefront requirements that apply. |

The community App Store Compliance Playbook catalogs common rejection patterns and identifies which ones a scan can surface. Consult it for checks to investigate. For a submission decision, record the applicable Apple rule, what you observed, and what needs to change or be checked next. [What the scan proves](https://github.com/mjmirza/app-store-compliance/blob/305826691cefa594d6638d03375d2ae69e1a6bad/docs/WHAT-THE-SCAN-PROVES.md) · [Apple App Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)
