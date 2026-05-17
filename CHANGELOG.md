# Changelog

All notable changes to this project are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.4], 2026-05-17

### Changed

- **Error 15 intervention banner reworded to match the project's stated security posture.** The banner previously advised users to "switch to a private browsing window" to work around DVSA's rate-limit response, which directly contradicted the project's stated principle that the Software does not attempt to circumvent DVSA's security measures (see [docs/SECURITY-POSTURE.md](docs/SECURITY-POSTURE.md)). The banner now reads: *"Standard DVSA rate-limit response. Script paused. Wait for the block to clear naturally (typically 1-2 hours), then resume."* The expected wait time was also corrected (previously "~15 minutes", which contradicted the 1-2 hour figure stated in the README and SECURITY-POSTURE doc).
- **Centre-mismatch intervention instruction** no longer leaks the internal `EXPECTED_CENTRE` variable name. Reworded to *"Calendar loaded a different test centre. Monitoring is paused. Verify your configured test centre and reload."*
- **Settings-panel refresh-interval hint** reworded to describe DVSA's response in consistent terms: *"Faster than 5 minutes is likely to trip DVSA's standard rate-limiting (Error 15) and is not recommended."*

[1.0.4]: https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/releases/tag/v1.0.4

---

## [1.0.3], 2026-05-17

### Added

- **Import-existing-config option on the first-run wizard.** A new callout on step 1 (Welcome) lets users with a previously-exported JSON config restore their settings directly, skipping the rest of the wizard. The import flow reuses the same strict validator as the settings panel's Backup &amp; restore section (rejects unknown keys, type mismatches, files without the `_meta.source` marker). A `window.confirm` shows the parsed settings summary before applying. If the imported config has `AUTO_BOOK` enabled, the user's OK click on the summary serves as informed acknowledgement and the auto-book consent flag is written, analogous to the in-wizard consent modal on step 5.
- Use case: setting up the script on a new browser, device, or browser profile after exporting from a previous install. Previously the user had to walk through all 5 wizard steps and then import via the settings panel; now they can do it in one click on the welcome screen.

[1.0.3]: https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/releases/tag/v1.0.3

---

## [1.0.2], 2026-05-17

### Fixed

- **Config-export `_meta.version` field is now dynamic.** Previously, exporting your settings to JSON would write a hardcoded `"version": "1.0.0"` regardless of which version of the script was actually running, so exports from a 1.0.1 install mis-identified themselves as 1.0.0 in the file. The export now reads `SCRIPT_VERSION` so the stamp always reflects the install that produced it.

### Changed

- **README version badge is now dynamic.** Switched from a hardcoded shields.io badge to one that auto-queries GitHub for the latest tag. Future version bumps now propagate to the badge without manual README edits.
- **DISCLAIMER footer no longer carries a software-version stamp.** The disclaimer's terms apply across versions, so tying its footer to a single release number risked drift exactly like this. The date stamp remains so users can still see when terms last changed.

[1.0.2]: https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/releases/tag/v1.0.2

---

## [1.0.1], 2026-05-17

### Fixed

- **Auto-book consent modal now fires from the wizard.** Previously, ticking the auto-book checkbox in step 5 of the first-run wizard would silently enable the feature without firing the explicit-consent modal that the post-wizard settings panel uses. Both paths now share the same modal with the same three acknowledgements and link to DISCLAIMER section 11. The wizard's onboarding can no longer be a quieter path to enable an elevated-risk feature.
- **Skip button on the wizard's final step was a no-op.** The Skip handler clamped at step 5, so clicking Skip on step 5 stayed on step 5. The button is now hidden on the final step (the Finish button is the correct action there).
- **Removed a now-redundant implicit ack-setter in `finishWizard()`.** It existed as a workaround for the wizard not firing the consent modal; with the modal now firing from the wizard, the workaround is obsolete and was masking the model.

### Documentation

- README install steps now include an explicit "Chrome / Edge / Brave: enable Allow User Scripts" step between installing Tampermonkey and installing the script. This catches a common Chromium-only gotcha where Tampermonkey can install the script but won't actually run it until the user toggles "Allow User Scripts" in the extension's Details page. Firefox is unaffected.
- New troubleshooting entry covering the "I installed the script but nothing happens" symptom that this Chromium quirk causes, with a direct fix.
- Both sections link to the official Tampermonkey FAQ Q209 for canonical guidance.

[1.0.1]: https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/releases/tag/v1.0.1

---

## [1.0.0], 2026-05-17

Initial public release.

### Default search flow

The auto-cycling search now uses the **multi-centre test-centre search** rather than the older single-centre date-focused walk. On the booking management page, the script clicks the test-centre "Change" link (`#test-centre-change`), which lands on the postcode-search page where `handleTestCentreSearch` runs a single request and parses a summary showing availability at the target centre plus nearby centres. Matches drill straight into the calendar; otherwise the script self-cycles on the search page.

The older date-focused flow remains available as a manual path: if a user clicks the date-time "Change" link (`#date-time-change`) themselves, the script handles the resulting pages (`handleTestDateChoice` then `handleCalendar`) exactly as before. Only the *automatic* trigger has changed.

Reasons:
- The centre-search response shows multiple centres' availability per request, so `ALERT_ANY_CENTRE` becomes meaningful.
- One request per cycle instead of a full calendar walk: lighter load on DVSA's site, more in line with normal human use of the page.
- Per-cycle navigation stays on the search page rather than reloading `/manage` each time.
- The script only drills into a calendar when a match is actually found, rather than always loading one.

### Monitoring
- Randomised refresh cycles (5–60 min, default 7–12) with human-pacing jitter
- Date-window matching with weekend exclusion toggle
- Instructor-unavailable-date filter
- Walk-backwards through earlier dates each cycle to extend search beyond the loaded month
- Self-recovery from session expiry (manual prompt or auto-login)

### Alerts
- Red banner overlay + browser/OS notification + audio chime + tab-title flash
- "Show me" jump-to-slot button on the floating cluster
- Test alert mode for verifying alerts work before relying on them
- Hammer-protection on the test-alert button (cancellable mid-burst)

### Auto-book (opt-in)
- Auto-clicks date → time → Warning! Continue, stops on the Confirm changes page
- 15-minute slot-hold countdown with yellow highlight on the Confirm button
- Forced off in Test mode; refuses to ever click Confirm or Abandon
- One-time informed-consent modal fires before auto-book can be enabled: explains exactly what auto-book does, points to the disclaimer's auto-book waiver (§8), and requires explicit "I understand" before the feature activates
- Consent acknowledgement is stored as an ISO timestamp in localStorage and surfaced in the self-test diagnostic for audit purposes
- Save-time guard catches users who already had auto-book on from before the consent flow existed
- Wizard's auto-book step counts as implicit acknowledgement (the welcome step covers disclaimer acceptance), so wizard users aren't prompted twice

### Settings panel
- In-page panel, no code editing required for day-to-day use
- Live field validation with red error rings
- Searchable test-centre dropdown (~330 UK centres bundled, self-healing from real DVSA H1)
- Instructor-date picker with pill UI + bulk paste mode
- Live "What you're monitoring" preview with stacked-bar breakdown
- Health snapshot card (notifications/audio/last scan/auto-login status)
- Pause/resume from the floating cluster

### Scan history
- Persistent log of every match, nearby alert, and spotted date
- KPI tile grid (scans, matches, nearby, spotted, find rate, last 7 days, avg lead, last spotted)
- Filter by finding type + group-duplicates toggle
- CSV export with sighting + total-sightings columns for duplicate analysis
- Copy-to-clipboard + clear history

### Quality of life
- Monoline inline-SVG icons across all section headers and cluster buttons (no OS emoji drift)
- Floating status pill with live countdown + cycle count
- "Alert on any centre" toggle for informational non-target-centre alerts
- Test-centre mismatch detection (intervention alert if wrong centre loads)
- Layout-broken detection (intervention alert if DVSA changes selectors)
- Manual-trigger mode for active monitoring without auto-clicking
- Console API: `dvsaWatcher.history()`, `dvsaWatcher.clear()`, etc.
- Privacy reassurance strip in the settings panel footer
- 100% local, no analytics, no telemetry, no external calls beyond DVSA itself

### Keyboard shortcuts
- `S`, toggle settings panel
- `P`, pause/resume monitoring
- `H`, toggle scan history modal
- `Esc`, close any open modal
- Ignores keypresses inside inputs and when any modifier is held (so browser shortcuts like Cmd+S aren't hijacked)
- Visible hint row in the settings panel showing all four shortcuts

### Credential privacy
- Driving licence and booking reference inputs are masked (`type="password"`) by default
- Eye-icon reveal toggle per input, flips to plaintext for verification
- Defaults back to masked every time the settings panel reopens
- `autocomplete="off"` so browsers don't prompt to save the values

### Backup & restore
- Export panel config to a downloadable JSON file (`dvsa-watcher-config-YYYY-MM-DD.json`)
- Optional checkbox: *Include auto-login credentials* (default **off** for safety when sharing)
- Restore from a JSON file with strict validation:
  - Rejects files missing the `_meta.source` marker
  - Rejects any unknown settings keys (no arbitrary localStorage writes)
  - Rejects type mismatches (e.g. string where a number is expected)
- Restore shows a confirm dialog with a summary of the settings before applying
- Restore merges with current config, missing keys keep their existing values

### Legal and terms

Hardened legal posture across the project ahead of public release:

- **Acceptable Use Policy** added as DISCLAIMER §3, defining permitted use (individual, own booking, UK jurisdiction, own credentials) and prohibited use (on behalf of others, multiple accounts, commercial use of any kind, malicious or unlawful purposes, headless or automation-framework wrapping, use outside the UK)
- **Distribution, Modification and Forks** added as DISCLAIMER §4, asking forks and derivatives to remain non-commercial and clearly distinguished from the original, and giving users the canonical install URL plus instructions for verifying authenticity
- **Project Philosophy** added as DISCLAIMER §5, stating that the script is and will remain free for genuine individual users, that source remains visible, and that the project exists to help individuals rather than to be commercialised
- **User Responsibilities** (DISCLAIMER §8) expanded with eligibility, own-booking-only use, authenticity verification, and acceptance of project lifecycle
- **Indemnification** (DISCLAIMER §14) broadened to cover breaches of the Acceptable Use Policy and the Distribution rules
- **Modifications, Discontinuation, and Changes to these Terms** (DISCLAIMER §15) reorganised to explicitly cover script discontinuation
- Prominent **Permitted and prohibited use** callout added near the top of the README with cross-references to the new DISCLAIMER sections
- All cross-references between docs updated for the new section numbering (auto-book waiver is now §11)

### Style

- All em dashes removed from documentation and the script, replaced with commas, colons, or sentence breaks depending on context
- Colour emojis removed from documentation and user-facing strings (the wizard, consent modal, status pill, credit footer). The script's existing monoline SVG icons (date, centre, clock, lock, etc.) remain in place. Monochrome text glyphs (`✓`, `⚠`, `✗`, `○`) are retained because they read as icons rather than emoji in most fonts.
- README's three top-level callouts (disclaimer warning, existing-bookings-only, permitted use) converted to GitHub native alert syntax (`[!WARNING]`, `[!IMPORTANT]`, `[!CAUTION]`) for proper coloured callout boxes
- MIT licence ([LICENSE](LICENSE)) with a brief disclaimer summary
- Comprehensive disclaimer and limitation of liability ([DISCLAIMER.md](DISCLAIMER.md)) covering: no affiliation with DVSA / UK Gov, **explicit scope-of-operation clause (existing bookings only, does not book new tests)**, "as is" no-warranty, no-liability for missed slots or account issues, user responsibilities, no guarantees, assumption of risk, auto-book-specific waiver, indemnification, governing law (England and Wales)
- Acceptance prompt in the README's install steps
- Prominent "for existing bookings only" callout near the top of the README, with matching language in the wizard welcome screen, FAQ, and userscript `@description`

### First-run setup wizard
- 5-step guided onboarding for new users (Welcome / Date window / Test centre / Instructor dates / Final options)
- Fires automatically the first time the script loads with invalid or placeholder config
- Progress dots, Back / Skip / Next / Finish navigation
- Live monitoring preview on the date-window step
- Native datalist combobox for the test centre (search across 356 bundled UK centres)
- Pill UI for instructor unavailable dates (skippable)
- Optional auto-book opt-in and auto-login fields on the final step
- Summary recap before Finish
- "Use the full settings panel instead →" escape hatch on every step
- Disclaimer acceptance prompt on the welcome step
- `localStorage` flag (`dvsaWatcher.wizardCompleted`) prevents the wizard from re-firing once completed or explicitly skipped

### Self-test diagnostic
- One-click diagnostic in the About section that probes: script version, environment, Tampermonkey presence, notification permission, audio context state, localStorage writability, panel config presence, cycles + findings counts, configuration validity, and DVSA selector presence on the current page
- Output formatted as plain text with `✓` / `⚠` / `✗` / `○` glyphs
- Copy-to-clipboard button for pasting straight into bug reports
- Re-run button for after-fix verification

### About pane in settings
- Script version, license, author shown prominently
- Links to GitHub repo, CHANGELOG, DISCLAIMER, Report issue
- "Check for updates" instructions for Tampermonkey users
- Inline "Run self-test diagnostic" button
- Inline "Re-run setup wizard" button, walk through the wizard again with current values pre-filled (cancelling mid-wizard preserves existing config)

### Tab-focus indicator
- 5th Health card tile shows current tab focus state
- Tracks `visibilitychange` events: warns the user if the tab has been backgrounded recently, since browsers throttle `setTimeout` in inactive tabs and may delay scan cycles
- Health grid uses `auto-fit` columns so tiles flow gracefully across panel widths

### Userscript header hardening
- `@noframes` directive added, prevents the script from accidentally loading in iframes (DevTools panels, OAuth popups, etc.)

### Project infrastructure
- [SECURITY.md](SECURITY.md), responsible-disclosure policy, scope, timeline
- [CONTRIBUTING.md](CONTRIBUTING.md), local dev setup, coding conventions, what's welcome / not welcome, PR checklist
- `.github/workflows/syntax-check.yml`, GitHub Actions workflow that runs `node --check` on every PR touching the public userscript
- Bug report and feature request issue templates

### Documentation
- Expanded README with a Quick Start TL;DR at the top
- Comprehensive Troubleshooting section (notifications, audio, Error 15, layout-broken, centre mismatch, missing cluster, validation errors)
- FAQ covering legality, test types, mobile, multi-tab, updates, manual-confirm rationale, config sharing, restore-to-different-browser, site changes, headless / server-side scanning, telemetry/privacy

[1.0.0]: https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/releases/tag/v1.0.0
