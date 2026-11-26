---
name: ship-ios-build-one-command
description: Use when you want to archive, sign, export, and upload an iOS App Store build in one command, or when a build is missing from App Store Connect and you need the local archive-to-upload step automated. Complements solo-ios-release-flow, which holds the judgment and the checklist; this skill is the automation underneath it.
---

# Ship iOS Build, One Command

The helper archives the current app project and can attach the uploaded build in App Store Connect.

This is the machine. `solo-ios-release-flow` is the judgment. Decide there, run here.

## The Flow

1. `xcodebuild archive` - Release, `-destination "generic/platform=iOS"`, `-allowProvisioningUpdates`
2. `xcodebuild -exportArchive` - App Store `ExportOptions.plist`, automatic signing, detected team
3. `asc publish appstore --ipa ... --version ... --wait` - uploads and attaches the build, no `--submit` by default

No submit on purpose. Uploading and attaching is reversible. Submitting is not. You submit when the readiness work is done, not as a side effect of a build.

## The Tool

Find [archive-ios-build.sh](reference/archive-ios-build.sh) next to the loaded `SKILL.md` in its `reference/` directory. Use its absolute path from the app's project directory so project and scheme detection run in the right place.

```sh
archive_script="/absolute/path/to/ship-ios-build-one-command/reference/archive-ios-build.sh"
"$archive_script" --help
"$archive_script" --dry-run     # detect settings and print the plan without archiving or uploading
"$archive_script"               # archive, export, upload, attach
"$archive_script" --no-upload   # stop after the signed .ipa
"$archive_script" --submit      # also submit for review (explicit)
```

Auto-detected: `.xcworkspace`/`.xcodeproj`, scheme, `DEVELOPMENT_TEAM`, `MARKETING_VERSION`, `CURRENT_PROJECT_VERSION`, `PRODUCT_BUNDLE_IDENTIFIER`, and the ASC app id resolved from the bundle id.

Run `--dry-run` first and confirm the project, working tree, version, and build number. Dry run can look up the app in App Store Connect when planning an upload.

## Signing Model

- Signing is automatic. `-allowProvisioningUpdates` lets `xcodebuild` create and download the Apple Distribution certificate and App Store provisioning profile using the Xcode-logged-in account.
- The first App Store archive on a machine creates a Distribution cert. Expected, not an error.
- Two separate auths. Signing uses the Xcode account. Upload uses asc's own keychain auth. They do not share credentials and do not need to.

## Gotchas, Baked In

These are the failures that cost hours. The script already handles them. Know them anyway.

- **Never pipe `xcodebuild` to `tail`/`grep` for the result.** The pipe's exit code is `tail`'s, not `xcodebuild`'s, so a failed build reads as success. Write to a log, check `${PIPESTATUS[0]}` and grep for "ARCHIVE SUCCEEDED" / "EXPORT SUCCEEDED". Then verify the `.xcarchive` and `.ipa` actually exist on disk. "SUCCEEDED" in the log is not proof a file landed.
- **First archive auto-creates the Distribution cert.** Via `-allowProvisioningUpdates` and the Xcode-logged-in account. Expected. If `xcodebuild` cannot create signing assets headlessly, sign in to the account in Xcode (Settings, Accounts), then rerun.
- **Never scan credential stores for upload keys.** Signing uses the Xcode account; upload uses asc's own keychain auth. Do not hunt the keychain, env, or config files for API issuer ids or `.p8` keys. If upload auth is missing, fix it with `asc auth`, not by guessing.
- **Re-uploading a changed binary needs a new build number.** App Store Connect rejects duplicate build numbers. Bump `CURRENT_PROJECT_VERSION` before you rebuild, or the upload fails late.
- **macOS ships bash 3.2.** Under `set -u`, expanding a possibly-empty array errors with "unbound variable". Guard it: `${arr[@]+"${arr[@]}"}`.
- **Older Xcode names the method "app-store", not "app-store-connect".** The script writes `app-store-connect` first and falls back to `app-store` if Xcode rejects it.

## When NOT To Use

- macOS app for outside-the-store distribution. Use notarization, not this.
- Deciding whether the release is ready, or doing the readiness work, contacts, App Privacy, age rating, availability. That is `solo-ios-release-flow` and `app-store-readiness`.
- TestFlight-only distribution to a group. Use `asc publish testflight --group ...`.

## Verify After

```
asc builds list --app <APP_ID> --sort -uploadedDate --limit 1
```

Newest build should read VALID, with the version you expected. If it does not, the upload is not done, no matter what the script printed.
