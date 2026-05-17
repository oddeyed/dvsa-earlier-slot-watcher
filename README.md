# DVSA Earlier Slot Watcher

> A Tampermonkey userscript for UK learner drivers with an **existing DVSA practical driving test booking**. Watches the "Change your test" page for an earlier cancellation slot at the same test centre, alerts the moment one appears in your target date window, and can optionally auto-reschedule up to the final confirmation page. **Does not book new tests**, you must already have a booking.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/github/v/tag/alchemycharlie/dvsa-earlier-slot-watcher?label=version&color=brightgreen)](CHANGELOG.md)
[![Userscript](https://img.shields.io/badge/userscript-Tampermonkey-orange.svg)](https://www.tampermonkey.net/)
[![Disclaimer](https://img.shields.io/badge/read-disclaimer-yellow.svg)](DISCLAIMER.md)

<p align="center">
  <img src="docs/screenshots/hero.png" alt="The DVSA Earlier Slot Watcher running on a DVSA booking page, showing the floating status pill and settings gear in the bottom-right corner" width="900">
</p>

---

> [!WARNING]
> ### Important: read before installing
>
> This is a **free, unofficial, community-built tool**. It is **not affiliated with, endorsed by, or connected to the DVSA, gov.uk, or the UK Government** in any way.
>
> By installing or using this script, you accept that:
>
> - It comes with **no warranty whatsoever**, "as is", "as available", no guarantees of any kind.
> - The author **accepts no liability** for missed test slots, lost bookings, DVSA account issues, incorrect bookings, missed alerts, or any other consequence of using it.
> - **You are solely responsible** for complying with the DVSA's terms and conditions and for verifying every booking detail before clicking Confirm.
> - The full terms are in **[DISCLAIMER.md](DISCLAIMER.md)**, please read them before installing.
>
> If you do not accept these terms, **do not install or use this script**.

---

> [!IMPORTANT]
> ### For existing bookings only, this is not a fresh-booking tool
>
> This script helps people with an **existing, paid, confirmed DVSA practical driving test booking** find an earlier cancellation slot at the same test centre and **reschedule** their booking to that slot.
>
> It does **not**, and **cannot**:
>
> - Book a new test from scratch
> - Skip the DVSA application or payment flow
> - Help anyone without a booking get one
> - Snap up newly-released slots before they reach DVSA's public booking page
>
> Technically: the script only operates on `driverpracticaltest.dvsa.gov.uk/manage*` and `/login*`, the "Change your test" management flow. It never touches the fresh-booking application URL.
>
> If you don't already have a test booked, you'll need to go through DVSA's normal booking process first. Then come back here.

---

> [!CAUTION]
> ### Permitted and prohibited use
>
> This script is for **individual personal use only**, by people in the UK who hold their own DVSA practical driving test booking and wish to reschedule it to an earlier date.
>
> **Permitted**: using the script on your own device, with your own DVSA login, for your own booking.
>
> **Not permitted**:
>
> - Use on behalf of any other person (friends, family, pupils, clients).
> - Use across multiple DVSA accounts.
> - Commercial use of any kind. Driving instructors and schools must not run the script on behalf of pupils.
> - Use for malicious or unlawful purposes, or anything that breaches DVSA terms, DVLA terms, the Computer Misuse Act 1990, or any other applicable UK law.
> - Wrapping the script inside headless browsers, automation frameworks, or unattended server-side automation.
> - Use outside the UK.
>
> The script is, and will remain, free for genuine individual users. It must not be copied, modified, redistributed, or forked for financial gain. Forks intended to break, weaponise, or maliciously alter the script's behaviour are not endorsed by the author.
>
> Full Acceptable Use Policy: [DISCLAIMER §3](DISCLAIMER.md#3-acceptable-use-policy). Distribution and fork rules: [DISCLAIMER §4](DISCLAIMER.md#4-distribution-modification-and-forks). Project philosophy: [DISCLAIMER §5](DISCLAIMER.md#5-project-philosophy).

---

## Quick start

Skip ahead to [Install](#install) for the full walkthrough. The 60-second version:

1. **Install [Tampermonkey](https://www.tampermonkey.net/)** for your browser.
2. **Click this link** to install the script: [Install DVSA Earlier Slot Watcher](https://raw.githubusercontent.com/alchemycharlie/dvsa-earlier-slot-watcher/main/dvsa-slot-watcher.user.js)
3. **Open your DVSA booking page** ([driverpracticaltest.dvsa.gov.uk/manage](https://driverpracticaltest.dvsa.gov.uk/manage)) and log in.
4. **A setup wizard appears.** Walk through it, pick your date range, test centre, instructor dates. Save.
5. **Leave the tab open** in a corner of your screen. The script handles the rest.

Press `S` at any time to open settings, `H` for history, `P` to pause.

---

## Why this exists

I was caring for my elderly mother who lives around three hours away by public transport, and my assigned driving-test date was months away. Passing the test was an absolute priority, driving would mean the difference between a full-day round-trip and being able to actually be there when she needs me. DVSA only allows two reschedules per booking and I had one left, so I needed to find an earlier cancellation. The problem: cancellations get snapped up within seconds.

I built this script because I wanted something that:
- Watched the booking page for me, around my schedule, without me having to refresh it
- Filtered out dates my instructor couldn't do
- Alerted me loudly when a slot appeared so I could finish the booking before someone else did
- Stayed honest, no scraping data, no analytics, no "premium tier", no external servers

It's saved as a Tampermonkey userscript so anyone with a browser can run it. No accounts, no installs beyond the userscript manager itself, and nothing about your booking ever leaves your machine.

If it helps you the way it helped me, [a coffee](https://buymeacoffee.com/charlie.martina) is appreciated but never expected.

---

## What it does

For users with an existing DVSA test booking who want to reschedule to an earlier date:

- **Monitors your DVSA "Change your test" page** on a randomised 5–60 minute cycle (default 7–12 min)
- **Filters by date window**, weekends, and your instructor's unavailable dates
- **Alerts you four ways at once** when a match appears: red banner, browser notification, audio chime, and a tab-title flash
- **Auto-books (opt-in)** through to DVSA's "Confirm changes" page, the final commit stays manual so you can verify the slot
- **Logs every finding** to local browser storage so you can review later or export to CSV
- **Speaks Spring WebFlow**, handles DVSA's session-expired flow, auto-login if you provide credentials, and Imperva bot-protection cycle pacing
- **Self-heals** discovered test centre names into the settings dropdown
- **Stays 100% local**, no analytics, no telemetry, no external network calls beyond DVSA itself

<p align="center">
  <img src="docs/screenshots/settings-panel.png" alt="The settings panel showing the Health card, Date window, monitoring preview, test centre dropdown, refresh interval, and other configuration options" width="720">
  <br>
  <em>The in-page settings panel. Everything's configured here, no code editing required.</em>
</p>

---

## Install

### 1. Install Tampermonkey

[Tampermonkey](https://www.tampermonkey.net/) is the userscript manager. It runs as a browser extension. Install the version for your browser:

- [Chrome / Brave / Edge](https://chromewebstore.google.com/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo)
- [Firefox](https://addons.mozilla.org/firefox/addon/tampermonkey/)
- [Safari (paid)](https://apps.apple.com/app/tampermonkey/id1482490089)

### 2. Chrome / Edge / Brave / other Chromium browsers: enable "Allow User Scripts"

> [!IMPORTANT]
> Since Chrome's Manifest V3 enforcement tightened, Chromium-based browsers require you to **explicitly opt in to running userscripts**. Without this step, Tampermonkey can install the script but won't actually run it on DVSA pages, so nothing will happen and you'll wonder why.

1. In your browser, go to the extensions page:
   - Chrome: `chrome://extensions/`
   - Edge: `edge://extensions/`
   - Brave: `brave://extensions/`
   - Opera / other Chromium: equivalent `<browser>://extensions/`
2. In the **top-right corner** of that page, toggle **Developer mode** ON.
3. Find **Tampermonkey** in the list and click its **Details** button.
4. Scroll down and toggle **Allow User Scripts** ON.

Official Tampermonkey guidance for this step: <https://www.tampermonkey.net/faq.php?q=Q209>.

**Firefox users**: skip this step entirely. Tampermonkey on Firefox runs userscripts without needing additional permissions.

### 3. Install the script

Click this link in a browser with Tampermonkey installed:

**[Install DVSA Earlier Slot Watcher](https://raw.githubusercontent.com/alchemycharlie/dvsa-earlier-slot-watcher/main/dvsa-slot-watcher.user.js)**

Tampermonkey will open its install screen, click **Install**.

Once installed, it'll auto-update from this same URL whenever a new version is released.

### 4. Configure

1. Open your DVSA test booking change page: <https://driverpracticaltest.dvsa.gov.uk/manage>
2. Log in to find your current test
3. You'll see a small **status pill** and **gear icon** in the bottom-right corner, click the gear
4. Fill in:
   - **Date window**, earliest + latest dates you'd accept
   - **Test centre**, pick from the searchable dropdown (~330 UK centres bundled)
   - **Search term**, your postcode or centre name (whatever finds your centre in DVSA's search results)
   - **Instructor unavailable dates**, paste or pick the dates your instructor can't do
   - **Auto-book** (optional), opt in if you trust the script to click through automatically
   - **Auto-login** (optional), paste your licence number + booking ref to recover from session expiry without manual intervention
5. Click **Save and reload**
6. Leave the tab open in a corner of your screen, the script does the rest

> By completing installation and saving any configuration, you confirm that you have read and accepted the terms in [DISCLAIMER.md](DISCLAIMER.md).

<p align="center">
  <img src="docs/screenshots/monitoring-preview.png" alt="The 'What you're monitoring' card showing a live breakdown: 47 dates would alert, 72 total in range, with a coloured stacked bar showing alertable, weekend-excluded, and instructor-blocked portions" width="600">
  <br>
  <em>The settings panel's "What you're monitoring" card updates live as you adjust the date window, weekend toggle, and instructor dates.</em>
</p>

<p align="center">
  <img src="docs/screenshots/status-pill.png" alt="The floating status pill in the bottom-right of the screen showing scanning state with a live countdown to the next refresh cycle" width="380">
  <br>
  <em>Always know what the script is doing. The status pill shows the current state and a countdown to the next refresh.</em>
</p>

---

## Troubleshooting

### "I installed the script but nothing happens on the DVSA page" (Chromium browsers)

Almost always the **Allow User Scripts** toggle in Tampermonkey's extension settings hasn't been enabled. Chrome, Edge, Brave, Opera, and other Chromium-based browsers require this since Manifest V3 enforcement tightened.

Fix:

1. Go to your browser's extensions page (`chrome://extensions/`, `edge://extensions/`, etc.)
2. Toggle **Developer mode** ON (top-right of that page)
3. Click **Details** on the Tampermonkey card
4. Toggle **Allow User Scripts** ON

Then reload your DVSA tab and the floating pill + gear icon should appear in the bottom-right corner.

Tampermonkey's own page on this: <https://www.tampermonkey.net/faq.php?q=Q209>.

Firefox users: this isn't required, you have a different issue. Check that the script is enabled in Tampermonkey's dashboard.

### "Notifications aren't firing"

Most likely causes, in order of likelihood:

1. **Permission denied.** Click the gear icon → check the Health card → look at the Notifications status. If it says *"Denied"*, you'll need to re-grant permission in your browser's site settings (the padlock icon next to the URL).
2. **Tab is in the background and the OS is suppressing.** macOS Focus, Windows Focus Assist, and Do Not Disturb modes will silently swallow notifications. Disable them or whitelist your browser.
3. **Notifications API unsupported.** Some heavily-locked-down browsers or in-app browser views don't support the API at all. Use Chrome/Firefox/Edge on a desktop OS.

### "Audio chime isn't playing"

The Web Audio API requires a user gesture before audio can play. After installing the script:

1. Click anywhere on the page once (literally anywhere, a blank part of the page is fine)
2. The Health card should now show **Audio: Ready** ✓
3. Use the **Test alert** button in the settings panel to verify the chime fires

If audio is still silent: check your browser's site-level audio permission, system volume, and any "auto-mute background tabs" settings.

### "Error 15" / Temporary block from DVSA

DVSA's Imperva bot protection has flagged your IP. This usually clears in **1–2 hours** without intervention. To reduce the chance of it happening again:

- Increase the refresh interval in the settings panel (the default 7–12 min is comfortably human-paced; faster is asking for trouble)
- Don't run multiple instances of the script across multiple tabs / browsers / devices simultaneously
- Avoid mashing the **Test alert** button to verify things work, that doesn't trigger Imperva, but it's a sign you might be over-refreshing

### "Layout broken" intervention alert

DVSA changed their page structure (CSS classes, IDs, or markup). The script bails safely instead of clicking the wrong thing.

1. **First**: check if a script update is available, open Tampermonkey's dashboard, click **Check for updates**
2. If you're on the latest version and the issue persists, [file an issue](https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/issues/new/choose), include the body ID DVSA is now using and which selector broke

### "Test centre mismatch" intervention

The H1 on the DVSA calendar page doesn't contain the centre name you configured. Either:

- You typed the centre name slightly differently, open settings, check the dropdown for the exact official wording
- DVSA renamed the centre, pick from the dropdown again (the new name will be auto-discovered after one cycle if not bundled)
- You navigated to the wrong centre via DVSA's own search

### "I can't see the floating cluster (status pill + gear)"

The cluster is in `position: fixed` at the bottom-right. Possible blockers:

- A browser extension overlay (translate, password manager) at the same position, try toggling extensions
- A zoom level above 200%, the cluster may be off-screen; zoom back down
- An ancestor CSS transform somewhere in DVSA's markup (rare; the cluster is in a single container specifically to avoid this)

### "The script's saying 'configure to start' but my values are filled in"

Some field is failing validation. Open the settings panel, the offending field will have a red ring around it and an error message just below its fieldset. Common causes:

- Date range with start > end
- Auto-book time window with earliest > latest
- Search term shorter than 2 characters or still on `AA1 1AA` placeholder
- Test centre still on `Your Test Centre (Location)` placeholder

### "Auto-book clicked the wrong slot / time"

Open the settings panel → set **Test mode** on temporarily → reload → verify the script's reading the calendar correctly → turn Test mode off. If it's still misbehaving, [file an issue](https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/issues/new/choose) with:

- The body ID DVSA was on
- A console log snippet (`[DVSA Earlier Slot Watcher]` lines)
- A screenshot of the calendar at the time

**Important**: per the [DISCLAIMER](DISCLAIMER.md), you remain solely responsible for verifying the slot before clicking Confirm changes. The auto-book stops short of that final click on purpose.

---

## FAQ

**Does this book new tests for me?**
**No.** This script is for people who already have a confirmed DVSA practical driving test booking and want to find an earlier cancellation slot to *reschedule* to. It only operates inside DVSA's "Change your test" management flow (`/manage*` URLs). It does not, and technically cannot, book a fresh test from scratch, skip the DVSA application/payment process, or help anyone without a booking get one. If you don't have a test booked, you'll need to go through DVSA's normal booking process first.

**Is this legal?**
The script automates clicks you would otherwise make manually on the existing-booking management section of DVSA's site, using your own login, for a booking that's already yours. Whether your specific use complies with DVSA's terms is your responsibility, see the [DISCLAIMER](DISCLAIMER.md). If you're unsure, seek independent legal advice before installing.

**Will this work for HGV, motorcycle, or other test types?**
Currently the script's `@match` rules and selectors target the **car practical** test booking flow (`driverpracticaltest.dvsa.gov.uk/manage*`). Other test types use different DVSA subdomains and page structures, the script won't work without changes. Contributions welcome.

**Does it work on mobile?**
Tampermonkey runs in mobile browsers (Kiwi on Android, some Safari workarounds on iOS), but the script's UI is sized for desktop. Mobile is **untested and unsupported**.

**Can I run multiple tabs / instances simultaneously?**
**No.** Each instance refreshes independently and they'd collide on Imperva's pacing thresholds. Pick one browser, one tab.

**How do I update?**
Tampermonkey checks for updates automatically based on the `@updateURL` in the script header. To force an update: Tampermonkey dashboard → click the script row → **Check for updates**. Your panel-saved settings are preserved across updates.

**Why doesn't it just book the slot for me?**
Auto-book deliberately stops on DVSA's "Confirm changes" page. DVSA holds the slot for 15 minutes once you reach this page, giving you time to verify. With one wasted reschedule possibly meaning months until the next opportunity, the human-in-the-loop gate is intentional. See [DISCLAIMER §11](DISCLAIMER.md#11-auto-book-feature-specific-waiver).

**How do I share my config with a friend without sharing my credentials?**
Use the **Backup & restore** section in the settings panel. The export checkbox *"Include auto-login credentials in export"* is **off by default** specifically for this case, your shared JSON will have blank licence/booking-ref fields.

**Can I restore an exported config to a different browser?**
Yes. Install the script in the new browser, open settings → Backup & restore → **Restore from file…** → pick your exported JSON. The script merges with whatever's already saved (if anything) and reloads.

**What happens if DVSA changes their site?**
The script's selector-resilience checks fire a *"layout broken"* intervention alert. Monitoring pauses safely until you confirm what changed. Update via Tampermonkey, or [file an issue](https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/issues/new/choose) with the new selectors.

**Can I run this on a Raspberry Pi / always-on home server?**
The script is designed to run in a logged-in browser as you. Headless / server-side scanning would:
- Probably violate DVSA's terms
- Make Imperva's bot detection trivially aware (no real browser fingerprint)
- Require storing your credentials on that server
- Risk locking your account

Use a desktop browser tab. That's the right level of automation for this.

**Does the script send any data anywhere?**
No. Zero. Nothing leaves your browser beyond the same requests you'd make manually on the DVSA site. No analytics, no telemetry, no error reporting, no CDN. See the [Privacy section](#privacy) below.

**Why does the script's status pill say "configure to start"?**
You're missing required configuration (date range, test centre, or search term) or you have a validation error. Click the gear icon, look for fields with red rings.

**Can I disable auto-book once enabled?**
Yes, open settings, uncheck the *Auto-book through to the confirmation page* checkbox, click Save and reload. The script falls back to alert-only mode immediately.

**My instructor sent me a list of dates they can't do. What's the fastest way to add them?**
Settings → Instructor unavailable dates → click *"Paste multiple…"* → paste the dates one per line (e.g. `2026-05-26`). Invalid lines are silently skipped. The pill UI shows you what was added.

---

## Privacy

The script runs entirely in your browser. Specifically:

- **Nothing is sent to me or anyone else.** No analytics, no telemetry, no error reporting, no "phone home" anything.
- **All settings are stored in `localStorage`** in your own browser. Clearing browser data wipes them.
- **Findings (scan history) live in `localStorage` too.** You can export them to CSV from the History panel.
- **Auto-login credentials are optional** and stored locally only. They're sent to DVSA's own login form, same as if you typed them manually.
- **No external CDNs, fonts, or scripts** are loaded. The whole thing is one self-contained `.user.js` file. Read it before you install it if you want.

The only network calls the script triggers are the same ones you'd make manually on the DVSA site.

---

## Auto-book safety

Auto-book is **opt-in** and disabled by default. When enabled, the script will:

1. Click the matching date on the calendar
2. Click an available time slot within your accepted time window
3. Click **Continue** on DVSA's "Warning! You'll lose your current booking" modal
4. **STOP** on the "Confirm changes" review page

The final **Confirm changes** button stays manual. DVSA holds the slot for 15 minutes once you reach this page, giving you time to verify the date, time, and centre are correct before you commit. The script will:
- Flash the page title with a red countdown
- Pulse the Confirm button with a yellow highlight
- Refuse to ever click Confirm or Abandon automatically

If you're not comfortable with auto-book, leave it off. The alerts (banner, sound, OS notification) will still fire so you can complete the booking manually.

<p align="center">
  <img src="docs/screenshots/alert-fired.png" alt="The red alert banner that appears when a matching slot is detected, with a 'Show me' button to jump to the slot on the calendar" width="720">
  <br>
  <em>What you'll see the moment a matching slot is found. Banner, browser notification, audio chime, and a tab-title flash all fire together.</em>
</p>

<p align="center">
  <img src="docs/screenshots/history-panel.png" alt="The scan history modal showing KPI tiles (total findings, find rate, last 7 days, avg lead time, last spotted) and a table of every detected slot" width="720">
  <br>
  <em>Every match, nearby alert, and spotted date is logged locally. Filter, group, export to CSV, or clear from here.</em>
</p>

---

## Configuration tips

- **Don't go faster than 5 min cycles.** DVSA's Imperva bot detection will issue a temporary block (Error 15) if you scan too frequently. The script's 7–12 min default is comfortably human-paced.
- **Keep the tab focused if you can.** Background tabs get aggressive `setTimeout` throttling in most browsers, which can stretch cycles.
- **Use Test Mode to verify alerts.** In the Advanced section of the settings panel, toggle Test mode on, the next scan will fire a fake alert so you know banners, sound, and notifications all work. Don't forget to turn it off.
- **Allow notifications.** When the script first prompts, click Allow. Without notifications the only alert you'll get is in the tab itself.

---

## Contributing

Issues and PRs welcome. Please use the templates in [.github/ISSUE_TEMPLATE](.github/ISSUE_TEMPLATE/).

Before submitting a bug, please include:
- Your Tampermonkey version and browser
- A console log snippet (`[DVSA Earlier Slot Watcher]` lines)
- The page state when it happened (which DVSA page were you on?)

---

## Support development

This script is free, open source, and ad-free. If it's helped you get an earlier test date, or just saved you hours of refreshing, a coffee is genuinely appreciated.

[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-%E2%98%95-yellow.svg)](https://buymeacoffee.com/charlie.martina)

---

## License and disclaimer

- **Code license**: MIT, see [LICENSE](LICENSE).
- **Disclaimer and limitation of liability**: see [DISCLAIMER.md](DISCLAIMER.md). Installing or using the Software constitutes acceptance of these terms in full.

This is an independent, unofficial tool. Not affiliated with, endorsed by, or connected to the DVSA, the UK Government, or gov.uk in any way.
