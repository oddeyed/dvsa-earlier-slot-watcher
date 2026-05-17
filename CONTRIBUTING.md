# Contributing

Thanks for your interest in helping out. This script is maintained by one person in spare time, so contributions that fit the project's scope are very welcome.

## Before you start

1. **Read the [DISCLAIMER](DISCLAIMER.md)**, particularly the *No Endorsement of Methods* section. We're not going to merge anything that helps users circumvent DVSA's terms or evade their bot detection beyond the existing human-comparable pacing.
2. **Skim recent [issues](https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/issues)**, your idea might already be tracked, or might have been previously discussed.
3. **For features**, open an issue describing what you'd like to add **before writing code**. The roadmap is intentionally tight; we may not accept every feature, and it's better to find that out before you've put hours in.
4. **For bugs**, an issue + a fix in the same PR is fine.

## Getting set up locally

There's no build step or test framework, it's a single-file Tampermonkey userscript.

```bash
# Clone
git clone https://github.com/alchemycharlie/dvsa-earlier-slot-watcher.git
cd dvsa-earlier-slot-watcher

# Verify the public script parses cleanly
node --check dvsa-slot-watcher.user.js
```

To test changes:

1. Open Tampermonkey's dashboard
2. Disable the production install of the script (so the two don't conflict)
3. Create a new userscript and paste your local file's contents
4. Save and reload your DVSA booking page

Your real config (date window, test centre, instructor dates, login credentials) lives in the browser's `localStorage`, not in the script file. Use the in-page settings panel to set up your environment, never hardcode personal values into `dvsa-slot-watcher.user.js`. The `.gitignore` blocks `*.personal.user.js` for anyone who wants a local copy of the script with their own bundled defaults, but it's no longer required: the settings panel + JSON import/export are the supported way to configure.

## Coding conventions

This is a userscript, not a SPA, keep it simple:

- **One file**, no build step, no bundler, no transpilation. The script ships as-is.
- **Vanilla JS**, no jQuery, no React, no external libraries. Tampermonkey runs in many browser versions; stick to features supported in evergreen browsers from the last ~3 years.
- **No external network calls.** This is a hard rule. The script must not load fonts, icons, analytics, or anything else over the network beyond DVSA's own site. Inline SVGs, inline CSS, no CDNs.
- **CSS classes are `dvsa-` prefixed**, no chance of colliding with DVSA's own stylesheet.
- **No `eval`, no `new Function`**, both for security and because Tampermonkey's `unsafeWindow` access requires care.
- **Comments matter.** Explain *why*, particularly for anything that interacts with DVSA's quirks (Spring WebFlow URLs, cycle pacing, body IDs, etc.). The next maintainer will thank you.
- **Match existing patterns.** The script has consistent patterns for things like rendering panel sections, wiring handlers, status pill updates, and finding storage. New code should look like existing code.
- **Personal data must never appear in the public file.** Don't commit licence numbers, booking refs, real test centres in your config, dates from your booking, etc. Your config lives in the browser's `localStorage`, set it up via the in-page settings panel after install, or import a JSON config.

## What's welcome

- **Bug fixes** to existing features
- **Selector resilience**, making the script more tolerant of DVSA page changes
- **Accessibility improvements**, ARIA labels, keyboard navigation, focus management
- **Documentation improvements**, clearer instructions, more troubleshooting tips, additional FAQ entries
- **Performance improvements** that don't introduce build steps
- **New features explicitly on the roadmap** (file an issue first to confirm scope)
- **Testing infrastructure**, if you have a clean way to add unit tests for the pure helpers without adding a build step or a test framework dependency, I'm interested

## What probably won't be accepted

- **Bot-detection evasion** beyond the existing human-pacing approach
- **Fully automatic booking** that clicks past the Confirm changes page
- **Headless / server-side scanning** (out of scope; see ROADMAP)
- **Adding external network calls** (analytics, Discord webhooks, etc.), even opt-in. Hard rule.
- **Replacing the rendering layer** (no React/Vue/Svelte/etc.)
- **Build-system additions** (no webpack, vite, rollup, etc.)
- **Breaking the public install URL**, renames or path changes break auto-update for everyone

## Pull request checklist

Before you click "Create pull request":

- [ ] The public file (`dvsa-slot-watcher.user.js`) parses: `node --check dvsa-slot-watcher.user.js`
- [ ] You've tested the change manually in a real browser against the real DVSA flow (or noted in the PR description if you weren't able to)
- [ ] No personal data in the diff (no real licence numbers, booking refs, postcodes, instructor dates, or specific test centres beyond what's already there for examples)
- [ ] No new external network calls
- [ ] No new external dependencies / CDN references
- [ ] CSS changes are scoped (`dvsa-` prefix)
- [ ] You've updated the [CHANGELOG](CHANGELOG.md) under an `[Unreleased]` section (or the next version) if your change is user-visible
- [ ] You've kept the diff focused, one concern per PR. Refactors and feature additions in separate PRs.

## Review and merging

I'll try to respond to PRs within a week, but this isn't a guarantee. Don't take silence personally, life happens.

I may request changes, suggest a different approach, or close PRs that don't fit the project's scope. That's not a rejection of you, just a scoping decision.

## Commit messages

No strict format. Keep them clear:

```
Good:
  Fix instructor date pill removal on slow render
  Add per-day-of-week filter to settings panel
  Document Error 15 in troubleshooting

Less good:
  fix bug
  update
  changes
```

## Reporting security issues

Don't file public bug reports for security issues, see [SECURITY.md](SECURITY.md) for the responsible disclosure process.

## Code of conduct

Be kind. Be patient. Assume good faith. Help newer contributors. If you can't, walk away.

---

Thanks for contributing. Even reading this far is appreciated.
