# Security – Sportfreunde Lauffen Homepage

Diese Datei dokumentiert, wie die Webseite der Sportfreunde Lauffen abgesichert ist und wie Sicherheitslücken gemeldet werden können.

## Sicherheitslücken melden

Bitte melde mögliche Schwachstellen **nicht öffentlich** (z. B. nicht als GitHub Issue), sondern direkt per E-Mail an:

> **info@sportfreunde-lauffen.de** (Betreff: „Security")

Wir bestätigen den Eingang innerhalb von 7 Tagen und beheben kritische Probleme zeitnah.

## Was im Code abgesichert ist

### 1. Content Security Policy (CSP)

In jeder HTML-Datei ist eine restriktive Content-Security-Policy als Meta-Tag gesetzt:

```html
<meta http-equiv="Content-Security-Policy" content="
  default-src 'self';
  script-src 'self' 'unsafe-inline';
  style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
  font-src 'self' https://fonts.gstatic.com;
  img-src 'self' data:;
  frame-src https://www.openstreetmap.org;
  form-action 'self' https://formspree.io;
  base-uri 'self';
  object-src 'none';
">
```

Damit dürfen Ressourcen ausschließlich von der eigenen Domain, Google Fonts (CSS + Schriftdateien) und OpenStreetMap (für die Karte) geladen werden. Plugins, Flash, Java-Applets sind komplett deaktiviert (`object-src 'none'`).

### 2. Referrer-Policy

Per Meta-Tag `<meta name="referrer" content="strict-origin-when-cross-origin">` wird kontrolliert, welche Informationen beim Klick auf externe Links übertragen werden – nur Origin (Domain), keine Pfade oder Query-Parameter.

### 3. Externe Links

Alle Links mit `target="_blank"` haben `rel="noopener noreferrer"`, womit:

- **noopener** verhindert, dass die Zielseite per `window.opener` Code in unserer Seite ausführen kann (Schutz vor _Tabnabbing_)
- **noreferrer** unterdrückt zusätzlich den Referer-Header

### 4. Keine sensiblen Daten im Code

Im Repository sind **keine** API-Keys, Passwörter, Datenbanken oder Login-Daten abgelegt. Die Seite ist rein statisch.

### 5. .gitignore

OS-Müll (`.DS_Store`, `Thumbs.db`), Editor-Files (`.vscode`, `.idea`) und Build-Artefakte (`node_modules/`) sind ausgeschlossen. So kommt nichts versehentlich ins öffentliche Repository.

## Was du als Vereinsadmin selbst tun musst

Diese Punkte kann **kein Code** für dich erledigen – sie sind aber genauso wichtig:

### A. GitHub-Account absichern

1. **2-Faktor-Authentisierung (2FA)** aktivieren:
   GitHub → Settings → Password and authentication → Enable two-factor authentication.
   Bevorzugt mit einer Authenticator-App (Google Authenticator, Authy) oder einem Hardware-Key (YubiKey).
2. **Starkes, einzigartiges Passwort** verwenden (Passwort-Manager: Bitwarden, 1Password, KeePass).
3. **Backup-Codes** ausdrucken und sicher verwahren.

### B. Domain-Account absichern (Provider von `sportfreunde-lauffen.de`)

1. **2FA aktivieren** beim Domain-Provider (1&1, IONOS, Strato, etc.).
2. **DNSSEC aktivieren**, falls dein Provider das anbietet (verhindert DNS-Spoofing).
3. **WHOIS-Daten** prüfen – wenn möglich, mit einem Domain-Privacy-Service.

### C. Cloudflare als kostenloser Schutzschild (empfohlen)

Cloudflare bietet eine kostenlose Stufe, die für Vereinsseiten ideal ist und folgende Vorteile bringt:

- **DDoS-Schutz** und **Web Application Firewall (WAF)**
- **HTTPS / TLS** mit modernen Ciphers
- **HTTP-Security-Header** (HSTS, X-Content-Type-Options, X-Frame-Options, Permissions-Policy) lassen sich per Klick aktivieren
- **Rate-Limiting** (z. B. 100 Anfragen pro IP pro Minute)
- **Bot-Fight-Mode** gegen Spam-Bots

**Setup in 10 Minuten:**

1. Auf [cloudflare.com](https://cloudflare.com) Account erstellen.
2. Domain `sportfreunde-lauffen.de` hinzufügen.
3. Cloudflare zeigt dir zwei Nameserver (z. B. `ana.ns.cloudflare.com`, `bob.ns.cloudflare.com`) – die im Domain-Provider eintragen.
4. In Cloudflare DNS: zwei `A`-Records (oder einen `CNAME`) auf GitHub Pages zeigen lassen.
5. **SSL/TLS-Modus** auf "Full (strict)" setzen.
6. **Security → Settings → Security Level**: "Medium".
7. **Rules → Transform Rules → Modify Response Header**: folgende Header hinzufügen
   - `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`
   - `X-Content-Type-Options: nosniff`
   - `X-Frame-Options: SAMEORIGIN`
   - `Permissions-Policy: geolocation=(), microphone=(), camera=(), payment=(), usb=()`

### D. Kontaktformular absichern (sobald es live geht)

Das Formular auf `kontakt.html` ist aktuell ein Demo. Wenn es echte Mails versenden soll:

1. **Formspree** ([formspree.io](https://formspree.io)) oder **Netlify Forms** einsetzen – beide bieten eingebauten Spam-Schutz.
2. **Honeypot-Feld** zusätzlich einbauen (versteckt für Menschen, sichtbar für Bots).
3. **Cloudflare Turnstile** oder **Google reCAPTCHA v3** als Bot-Schutz.
4. **Rate-Limiting** über Formspree-/Netlify-Einstellungen aktivieren.

### E. Regelmäßige Checks

- 1x pro Quartal: [securityheaders.com](https://securityheaders.com) gegen die Domain laufen lassen → Note A oder besser anstreben.
- 1x pro Quartal: [ssllabs.com](https://www.ssllabs.com/ssltest/) zur TLS-Konfiguration → Grade A oder besser.
- Bei Bedarf: [observatory.mozilla.org](https://observatory.mozilla.org) für Komplett-Audit.
- GitHub: aktivierte **Dependabot Alerts** (auch bei statischen Sites sinnvoll).

### F. Backups

Da das gesamte Repo bei GitHub liegt, ist ein Verlust nahezu ausgeschlossen. Trotzdem:

- 1x im Monat lokales `git pull` auf einen privaten Rechner.
- Optional: Mirror-Repo bei einem zweiten Anbieter (GitLab, Codeberg).

## Wer hat Zugriff?

| Bereich | Zugriff |
|---|---|
| GitHub-Repo | Nur eingeladene Mitglieder mit 2FA |
| Domain-Account | Nur der/die Domain-Verwalter:in |
| Cloudflare-Account (falls genutzt) | Nur der/die Admin:in |

Beim Wechsel von Verantwortlichen Zugänge **sofort** widerrufen.

## Was eine statische Seite **nicht** angreifbar macht

Zur Beruhigung: Eine rein statische HTML-Seite ohne Backend, ohne Datenbank, ohne Userlogin und ohne Adminbereich ist **um Größenordnungen sicherer** als ein typisches WordPress oder Joomla. Klassische Angriffe wie:

- SQL-Injection
- Login-Bruteforce
- Plugin-Schwachstellen
- Dateiupload-Lücken

…haben hier schlicht **keinen Anwendungspunkt**, weil die Seite nichts mehr ausführt als statische Dateien an den Browser auszuliefern.

Die größten realen Risiken sind daher:

1. **Übernahme deines GitHub- oder Domain-Accounts** (Phishing, schwaches Passwort)
2. **Spam über das Kontaktformular** (sobald es live ist)
3. **DDoS** (von GitHub Pages und Cloudflare automatisch abgefangen)

Mit den Maßnahmen oben sind alle drei abgedeckt.
