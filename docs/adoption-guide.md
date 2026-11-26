# Adoption Guide

## Start Simple

Do not import every skill into every project by default.

If you're upgrading an existing app, begin with the [iOS 27 Update Playbook](../guides/ios-27-update-playbook.md) and pick the skills for the parts of the app you need to review.

Start with:

- `ios-app-architecture`
- `swiftui-product-ui`
- `ios-debug-and-stabilize`

Add `ios-hig-design`, `app-store-readiness`, and `solo-ios-release-flow` once the product is approaching polish and ship.

## Google Antigravity

Open the playbook checkout in Antigravity to discover its skills. The checked-in [manifest](../.agents/skills.json) points to `skills/` in the same checkout. Keep the checkout available so the skills can open linked guides, checklists, and scripts. [Google's discovery guide](https://antigravity.google/docs/customization-discovery/#discovery-manifests)

When working on your own app, use **Add Folder** in the Antigravity App to add the playbook checkout to the app's project. With the CLI, start from your app directory and add the checkout to the workspace. [Google's project setup](https://antigravity.google/docs/getting-started/) · [CLI discovery paths](https://antigravity.google/docs/customization-discovery/#workspace-folders-and-parent-directories)

```sh
cd "/absolute/path/to/your-ios-app"
agy --add-dir "/absolute/path/to/ios-app-playbook"
```

Check that `app-store-readiness` comes from the playbook checkout. In the App, look under your project's **Customizations**. In the standalone IDE, use **Customizations** in the agent side panel. In the CLI, run `/skills` and open the skill's details. Use `/skills reload` after changing the setup. [Google's discovery checks](https://antigravity.google/docs/customization-discovery/#check-what-antigravity-loaded) · [IDE skill locations](https://antigravity.google/docs/skills/#antigravity-ide-skill-locations)

In the App or CLI, try `/app-store-readiness` and ask for a read-only summary of its linked App Store preflight checklist. This checks that the agent can open the resource as well as discover the skill. [Google's skill invocation guide](https://antigravity.google/docs/skills/#skills-by-surface)

### Personal setup

To make selected skills available across projects, use `~/.gemini/config/skills.json`. Add the entry to your existing `entries` array if you already have a manifest. Use the absolute path to your checkout. [Google's shared customization guide](https://antigravity.google/docs/customization-discovery/#share-customizations-across-projects)

```json
{
  "entries": [
    {
      "path": "/absolute/path/to/ios-app-playbook/skills",
      "include_only": ["ios-app-architecture", "swiftui-product-ui", "app-store-readiness"]
    }
  ]
}
```

If linked files are outside the active workspace, add the playbook checkout as a project folder so the agent can read them in the same session. [Google's file access settings](https://antigravity.google/docs/permissions/)

## Best Fit Projects

These skills work best for:

- SwiftUI-first iPhone apps
- solo-built consumer apps
- productivity, finance, wellness, creator, and utility products
- apps where UX polish and shipping quality matter

## What To Customize First

Before using these skills heavily in a repo, adapt:

- your app's design tokens
- your naming conventions
- your folder structure
- your monetization and release model
- your preferred verification commands

## What Not To Copy Blindly

- exact architecture labels
- every service boundary
- every release step
- any App Store copy pattern that does not match your product

The point is reusable judgment, not cargo culting.

## Suggested Pairing

Pair these skills with a repo-level instruction file that defines:

- app identity
- bundle IDs and targets
- platform minimums
- data model rules
- design tokens
- release conventions

## Maintenance

When a skill becomes vague, too long, or too general:

- tighten trigger conditions
- remove repeated advice
- move detailed examples into a reference file only if truly needed
- keep the main `SKILL.md` compact
