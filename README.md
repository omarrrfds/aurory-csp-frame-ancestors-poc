# Aurory — CSP `frame-ancestors` overrides `X-Frame-Options`

Bugcrowd program: Aurory Managed Bug Bounty Engagement (Public)
Target: `https://app.aurory.io` (in-scope asset)

`app.aurory.io` sends both headers below on every route tested (`/`, `/profile/settings`,
`/oauth-callback`, `/marketplace`, `/leaderboard`):

```
X-Frame-Options: SAMEORIGIN
Content-Security-Policy: ...; frame-ancestors https://*.dev.aurory.io/; ...
```

In every modern browser, `frame-ancestors` overrides `X-Frame-Options` when both are present.
The real, enforced framing policy is therefore "any `https://` origin on the `*.dev.aurory.io`
wildcard may embed this page" — not same-origin-only, despite what the `X-Frame-Options` header
states.

## `index.html`

A standard clickjacking test harness. Open it (locally, or via GitHub Pages) to observe:

- From any origin **not** matching `https://*.dev.aurory.io` (including this page's own GitHub
  Pages origin), the frame is correctly **blocked** — check DevTools → Console for the browser's
  CSP refusal message naming `frame-ancestors https://*.dev.aurory.io/` as the rule.
- This confirms the policy is real and enforced exactly as the headers state.

**Not demonstrated here:** whether an attacker can get content hosted on a matching
`*.dev.aurory.io` origin (e.g. via a dangling-DNS subdomain takeover or an XSS on an existing dev
host) — that precondition has not been tested. This repo shows the header-level misconfiguration
only; it does not host on a matching origin and cannot itself complete the attack chain.

Submitted for coordinated disclosure per the program's Bugcrowd brief.
