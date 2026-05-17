# Security Posture

How the DVSA Earlier Slot Watcher behaves around DVSA's security and protection layer.

This document is for people who want to understand exactly what the script does and does not do when it encounters CAPTCHAs, rate limits, page-structure changes, or other security-related responses from DVSA's website. The short canonical statement of intent lives in [DISCLAIMER §5.6](../DISCLAIMER.md#56-no-circumvention-of-dvsa-security-measures); this is the longer-form treatment.

---

## Core principle

The script exists to **remove manual effort**, not to gain any technical advantage. Its sole purpose is to save the user the time and tedium of repeatedly refreshing DVSA's "Change your test" page in the hope of catching an earlier cancellation slot.

It is **not** designed to, and will not be designed to, circumvent, evade, interfere with, or otherwise work around any of DVSA's security measures, rate limits, bot-detection systems, CAPTCHA challenges, or protection layers. Every interaction the script performs on the DVSA site is one a logged-in human could perform manually through the same site, in the same browser tab, using the same login session.

When DVSA's protection layer responds in a way that would interrupt a human user, the script's response is to interrupt itself in exactly the same way and hand control back to the user.

---

## How the script responds to DVSA's protection layer

### CAPTCHA challenges

If DVSA's site presents a CAPTCHA challenge, the script:

1. Recognises the page state.
2. Stops its monitoring cycle.
3. Surfaces a red intervention banner asking the user to complete the challenge.
4. Waits indefinitely. No automated retry, no attempt to solve the CAPTCHA, no attempt to dismiss or bypass it.

Once the user completes the CAPTCHA and the page returns to the booking management area, the script's next scheduled refresh cycle resumes normally. Solving CAPTCHAs is the user's responsibility, full stop.

The script does not, and will not, attempt to:

- Solve any CAPTCHA programmatically.
- Click through, dismiss, or hide CAPTCHA elements.
- Reroute requests to avoid the CAPTCHA endpoint.
- Use third-party CAPTCHA-solving services.

### Error 15 / temporary rate-limit block

DVSA returns an "Error 15" response when its security layer determines that too many requests are arriving from a particular IP in a short period. This is a standard protection of DVSA's booking system, designed to prevent automated scraping and abuse.

When the script encounters this response, it:

1. Recognises the response.
2. Stops its monitoring cycle.
3. Surfaces a red intervention banner explaining the block.
4. Waits for the block to clear naturally (typically 1-2 hours).

The script does not, and will not, attempt to:

- Retry the request immediately.
- Rotate IP addresses, user agents, or any other request fingerprint.
- Continue making requests at a slower pace to "fly under" the block.
- Spoof or modify any headers DVSA's security layer might inspect.

Common causes of an Error 15 response when running the script:

- Refresh interval set faster than the 7-12 minute default.
- Script running in multiple tabs, browsers, or devices simultaneously.
- Other traffic from the same IP also hitting DVSA's site (other tools, other household members browsing manually).

The user's appropriate response is the same as a human's: wait it out, then resume.

### Layout / page-structure changes

If DVSA changes the markup of a page the script monitors (CSS classes, element IDs, structure of the calendar grid, etc.), the script's selector-resilience checks fire a "layout broken" intervention banner. The script does not guess. It does not attempt to click neighbouring elements in the hope that one of them is the right one. It does not fall back to mouse-coordinate clicks or scraped-text matching. It halts cleanly and asks the user to file an issue against the project so a proper fix can be released.

The same principle applies when the configured test centre does not match the centre rendered on the calendar page (the "test centre mismatch" intervention), the script refuses to act on ambiguous state.

### Session expiry

DVSA logs users out after a period of inactivity. When this happens, the script:

- Detects the redirect to the login page.
- If auto-login credentials are configured: fills in the standard login form fields and submits, exactly as a human would.
- If auto-login is not configured: surfaces an intervention banner so the user can log in manually.

Auto-login uses DVSA's own published login form. It does not reuse session tokens, manipulate cookies, or interact with any non-public authentication endpoint.

---

## Pacing

The script's monitoring cycle is randomised between 7 and 12 minutes by default, configurable up to 60 minutes. The minimum allowed interval in the settings panel is 5 minutes.

This pacing is set specifically to fall comfortably within DVSA's expected use patterns. It is comparable to a person manually checking the page periodically throughout the day. The randomisation exists for the same reason a human's refresh cadence is irregular, a person does not refresh on an exact schedule.

The pacing is not chosen to evade detection. It is chosen to keep the script's behaviour proportionate to its purpose: a person who would otherwise be refreshing the page every few minutes anyway, automating that exact task and nothing more.

---

## What the script is not

For the avoidance of doubt, the script is not, and will never become:

- A headless scraper or server-side agent. It only runs in an attended, logged-in browser tab as the user.
- A multi-account or shared-account tool. It is for the user's own single DVSA booking.
- A traffic generator. It performs the same volume of requests a person checking the same page periodically would perform.
- A CAPTCHA-solving tool, automated or otherwise.
- A rate-limit-evading tool. When DVSA rate-limits the user's IP, the script stops.
- A fingerprint-spoofing or anti-detection tool. It runs as the user's normal browser, with the user's normal fingerprint, in the user's normal session.

If any of these capabilities are ever proposed, they will be declined. The script's value proposition is the elimination of tedious manual refreshing, not the circumvention of anything.

---

## Reporting concerns

If you believe the script's behaviour around DVSA's security layer departs from the principles described in this document, please file a bug report on the project's [issue tracker](https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/issues/new/choose). Reports describing the script doing something this document says it will not do are treated as priority correctness bugs.

For security vulnerabilities in the project itself (e.g. ways an attacker could exploit the script as installed in your browser), see the project's [SECURITY.md](../SECURITY.md) for the private disclosure process.

---

## Related

- [DISCLAIMER §2: Scope of Operation: Existing Bookings Only](../DISCLAIMER.md#2-scope-of-operation-existing-bookings-only)
- [DISCLAIMER §5: Project Philosophy](../DISCLAIMER.md#5-project-philosophy)
- [DISCLAIMER §5.6: No Circumvention of DVSA Security Measures](../DISCLAIMER.md#56-no-circumvention-of-dvsa-security-measures) (canonical short statement)
- [README: Before you install](../README.md#before-you-install)
- [SECURITY.md](../SECURITY.md), vulnerability disclosure for the project itself
