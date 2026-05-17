# Security Policy

Thanks for taking the time to report a security issue responsibly.

## Supported Versions

Only the latest released version of this script receives security updates. If you're running an older version, the first step is usually to update via Tampermonkey (Settings → Installed userscripts → Check for updates).

| Version | Supported |
|---------|-----------|
| Latest `1.x` | Yes |
| Anything older | No  |

## Reporting a Vulnerability

If you've found a security issue in this script, **please do not open a public issue.** Disclose it privately so a fix can be prepared before the issue is widely known.

### Preferred method

Use GitHub's private vulnerability reporting:

1. Go to <https://github.com/alchemycharlie/dvsa-earlier-slot-watcher/security/advisories/new>
2. Fill in the form with a clear description, reproduction steps, and impact assessment
3. I'll acknowledge receipt as soon as I'm able

### What to include

A useful report contains:

- A clear description of the issue
- Step-by-step reproduction (the more specific, the better)
- An assessment of impact, what an attacker could do, and under what conditions
- The version of the script you tested against (check the userscript header `@version` line)
- Your browser and Tampermonkey version
- Any proof-of-concept code or screenshots

## What's in Scope

The following are considered valid security issues:

- **Credential exposure** via the script's storage, logging, or panel UI (e.g. licence number or booking reference leaking into the DOM, console, or network requests)
- **XSS in the settings or history panels**, e.g. unsanitised user-supplied strings reaching `innerHTML`
- **Config import attacks**, e.g. a crafted JSON file causing arbitrary `localStorage` writes outside the allow-listed setting keys
- **Supply-chain risks**, anything that would let an attacker substitute or modify the script as installed by Tampermonkey users (other than compromising the GitHub repo itself, which is on me to secure)
- **Sensitive information disclosure** in scan history exports or auto-discovered test centres

## What's Out of Scope

- **DVSA-side issues**, anything on the actual DVSA booking website. Report those to DVSA.
- **Bot-detection circumvention requests**, the script intentionally operates at human-comparable pace; we won't add evasion techniques.
- **Third-party browser or extension bugs**, Tampermonkey, browser bugs, OS bugs.
- **Social engineering** of the maintainer or other users.
- **Phishing or fake installs** distributed by anyone other than the maintainer, these are not vulnerabilities in this script, but report them to GitHub/Tampermonkey if you find them.
- **Issues that require the user to install a clearly hostile script**, the threat model assumes the user has installed *this* repo's script via the canonical install link.

## Disclosure Timeline

I'll aim to:

- Acknowledge your report within 7 days
- Provide an initial assessment within 14 days
- Release a fix as soon as practical for confirmed issues, usually within 30 days for high-severity issues, longer for lower-severity

This is best-effort. I maintain this script in my spare time alongside a day job, so I can't guarantee tight SLAs. I'll keep you updated either way.

## Public Disclosure

After a fix is released:

- I'll credit you in the [CHANGELOG](CHANGELOG.md) (with your permission and preferred name/handle)
- If a CVE is appropriate, I'll request one
- You're free to publish your own write-up, please coordinate the timing with me so users have a chance to update

## Out-of-band Contact

If GitHub's private vulnerability reporting isn't available or you need to reach me by another route, leave a `[Security]`-prefixed issue title with non-sensitive details and I'll follow up to take the conversation private.
