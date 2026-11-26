# App Store Preflight

Record the version and build number, date, and evidence for each applicable check. Use **Pass**, **Fail**, **Unchecked**, or **N/A with a reason**. Resolve failed and unchecked submission requirements before submitting.

## Reviewer Access and App Privacy

- [ ] From a fresh install, walk the main task as App Review will see it. For gated features, use the demo account you will supply or a full demo mode agreed with App Review. Test that the backend is reachable and the demo data is usable. [App Review Guideline 2.1](https://developer.apple.com/app-store/review/guidelines/#app-completeness)
- [ ] Explain what changed and give the path to non-obvious features, purchases, and any required setup in App Review Information. Provide test credentials, sample inputs, and applicable supporting documents there. [Apple's review information guide](https://developer.apple.com/help/app-review/before-submitting-for-review/complete-review/)
- [ ] Check the published App Privacy answers against the release build, including data collected via SDKs and services. Verify that the privacy policy is accurate and reachable from the app and its listing. [App Privacy Details](https://developer.apple.com/app-store/app-privacy-details/) · [App Review Guideline 5.1](https://developer.apple.com/app-store/review/guidelines/#privacy)

## Release Candidate

- [ ] Verify the archive's capabilities, permission purpose strings, and privacy manifests. Use the release configuration when checking what actually ships.
- [ ] Test first launch, main flows, and upgrades with existing data on iOS 27 and the oldest supported iOS version. Exercise older-OS fallbacks for new APIs.
- [ ] For apps that support account creation, verify that people can start deletion in the app. Complete the flow with a test account and check the resulting data and access. [Apple's account deletion guidance](https://developer.apple.com/help/app-review/guideline-reference/5-1-1-account-deletion)
- [ ] For apps with in-app purchases, test the purchase and any applicable restore flow. Verify that App Review can reach the products and review the intended offering. [App Review Guideline 3.1](https://developer.apple.com/app-store/review/guidelines/#payments)

## App Store Connect

- [ ] Check that the listing describes what ships, the support and legal links work, and the icon and screenshots match the candidate. Verify required display sets, including iPhone Duo where applicable. [App Review Guideline 2.3](https://developer.apple.com/app-store/review/guidelines/#accurate-metadata) · [Screenshot specifications](https://developer.apple.com/help/app-store-connect/reference/app-information/screenshot-specifications)
- [ ] Check current age-rating answers, including questions about social media capabilities, and verify any published Accessibility Nutrition Labels against tested tasks. [Age rating update](https://developer.apple.com/news/?id=tlur8uvi) · [Accessibility label criteria](https://developer.apple.com/help/app-store-connect/manage-app-accessibility/overview-of-accessibility-nutrition-labels)
- [ ] Confirm the selected storefronts and any required declarations or documents. For paid apps and in-app purchases, check the Paid Apps Agreement and applicable tax and banking setup. [App Store Connect workflow](https://developer.apple.com/help/app-store-connect/get-started/app-store-connect-workflow) · [Apple's review information guide](https://developer.apple.com/help/app-review/before-submitting-for-review/complete-review/)

From **April 2027**, Apple lists the iOS and iPadOS 27 SDK and an iOS 15-or-later deployment target as minimums. Check the current requirements before upload. [Apple submission requirements](https://developer.apple.com/app-store/submitting/) · [iOS 27 release baseline](../guides/ios-27-update-playbook.md#1-establish-the-build-and-support-baseline)

## Privacy Manifest

- Audit the app, extensions, and bundled SDKs for required reason APIs and applicable manifest requirements. Verify the relevant bundles contain their `PrivacyInfo.xcprivacy` files in the final archive. [Privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files)
- In `NSPrivacyAccessedAPITypes`, declare the categories you use and the approved reasons that match how you use them. [Apple's required reason API list](https://developer.apple.com/documentation/bundleresources/describing-use-of-required-reason-api)
- Use the archive's privacy report when checking App Privacy answers. Exercise the data flows in the release configuration to confirm how the app and its integrations handle data. If personal data is shared with third parties, including third-party AI, verify the disclosure and explicit permission before sharing. [Privacy manifests and reports](https://developer.apple.com/documentation/bundleresources/adding-a-privacy-manifest-to-your-app-or-third-party-sdk) · [App Review Guideline 5.1](https://developer.apple.com/app-store/review/guidelines/#privacy)

## Export Compliance

- Determine which export compliance answers and documents apply to your app's encryption use. Set `ITSAppUsesNonExemptEncryption` accordingly. [Apple's export compliance guidance](https://developer.apple.com/help/app-store-connect/manage-app-information/overview-of-export-compliance/)
