# Sportfreunde Lauffen – Neue Vereinswebseite

Modernes, mehrseitiges Webseiten-Konzept für die **Sportfreunde Lauffen e.V.**, basierend auf den Inhalten von [www.sportfreunde-lauffen.de](https://www.sportfreunde-lauffen.de).

## Highlights

- Vollständig responsive (Smartphone, Tablet, Desktop)
- Sticky-Navigation mit Mobile-Menü
- Animierter News-Ticker
- Hero-Bereich mit Live-Countdown auf das nächste Event
- Reveal-Animationen beim Scrollen
- Filter für die News-Übersicht
- Tabellen, Spielpaarungen, KPI-Karten
- Sponsoring-Pakete (Bronze / Silber / Gold)
- OpenStreetMap-Einbindung
- Kontaktformular mit Validierung
- Saubere, kommentierte CSS-Variablen-Architektur (einfach umfärbbar)
- Keine Build-Tools nötig – läuft direkt im Browser

## Projektstruktur

```
SFL Homepage/
├─ index.html              ← Startseite
├─ verein.html             ← Über uns / Vorstand / Werte
├─ mannschaften.html       ← 1. & 2. Mannschaft, Tabelle, Trainingszeiten
├─ jugend.html             ← Jugendabteilung (Bambini bis A-Jugend)
├─ news.html               ← Alle Berichte mit Filter
├─ sponsoren.html          ← Sponsoren & Sponsoring-Pakete
├─ kontakt.html            ← Kontaktformular, Anfahrt, Impressum
├─ assets/
│  ├─ styles.css           ← Globales Design-System
│  ├─ script.js            ← Navigation, Animationen, Countdown
│  └─ img/                 ← (Platzhalter für Vereins-Bilder)
└─ README.md
```

## Lokal starten

Einfach `index.html` im Browser öffnen – kein Server, kein Build nötig.

Für lokale Entwicklung empfehlen wir einen einfachen Webserver (z. B. die VS Code "Live Server"-Extension oder):

```powershell
# PowerShell
python -m http.server 8000
# oder
npx serve .
```

Anschließend `http://localhost:8000/` aufrufen.

## Anpassungen

### Vereinsfarben

Alle Farben sind in `assets/styles.css` ganz oben als CSS-Variablen definiert:

```css
:root {
  --brand: #14161b;       /* Hauptfarbe (nahezu Schwarz) */
  --brand-dark: #000000;  /* Hover / Akzent dunkel */
  --brand-light: #2a2f3a; /* Heller Akzent */
  --accent: #ffffff;      /* Akzent auf dunklem Untergrund (Weiß) */
  /* … */
}
```

Aktuell ist die Seite in einer eleganten Schwarz-Weiß-Optik gehalten. Wenn die Sportfreunde echte Vereinsfarben (z. B. Rot/Weiß) verwenden möchten, einfach diese vier Werte austauschen – das ganze Theme zieht automatisch nach.

### Logo

Das offizielle SFL-Wappen wird in zwei Varianten verwendet:

- `assets/SFLweiss.jpeg` — schwarzes Wappen auf weißem Grund → für **helle Hintergründe** (Header, Favicon)
- `assets/SFLschwarz.jpeg` — weißes Wappen auf schwarzem Grund → für **dunkle Hintergründe** (Footer)

Da beide JPEGs einen einfarbigen Hintergrund haben, fügt sich das Wappen nahtlos in den jeweiligen Bereich ein. Wer eine transparente PNG-Version hat, kann sie einfach unter dem gleichen Dateinamen austauschen.

### Inhalte

Alle Texte stehen direkt im jeweiligen HTML – einfach editieren.
Die Spielberichte und Daten sind aktuell **Platzhalter** auf Basis der bestehenden Webseite. Sie sollten mit aktuellen Vereinsdaten ersetzt werden.

### Kontaktformular

Das Formular auf `kontakt.html` ist aktuell ein **Demo** (kein Mailversand). Für den Produktiveinsatz:

- Backend-Endpunkt einrichten (z. B. PHP-Mailer, Formspree, Netlify Forms)
- `<form data-fake-form>` zu `<form action="…" method="post">` ändern
- Den Demo-Handler in `assets/script.js` (Block "Simple form fake submit") entfernen

## Tech-Stack

- HTML5 (semantisch, barrierearm)
- Modernes CSS (Custom Properties, Grid, `clamp()`, Container-Queries-ready)
- Vanilla JavaScript (kein Framework, < 100 Zeilen)
- Web-Fonts: Inter + Bebas Neue (via Google Fonts)
- OpenStreetMap (für Karte; ohne Tracking)

## Browser-Support

Getestet in den aktuellen Versionen von Chrome, Firefox, Edge, Safari (Desktop & Mobile).

## Deployment auf GitHub Pages

Die Seite ist für **automatisches Deployment** auf GitHub Pages vorbereitet. Bei jedem Push auf `main` baut der Workflow `.github/workflows/deploy.yml` die Seite und veröffentlicht sie automatisch.

### Erstmaliges Einrichten (einmalig)

1. **Repository auf GitHub anlegen**

   Auf [github.com/new](https://github.com/new) ein neues, leeres Repository erstellen, z. B. `sfl-homepage`. Beschreibung optional, **kein README, kein .gitignore**, **keine Lizenz** (haben wir bereits) auswählen.

2. **Lokales Git-Repo anlegen und Push**

   In der PowerShell im Projektordner (`D:\Fussball\Website\SFL Homepage`):

   ```powershell
   git init
   git add .
   git commit -m "Initial commit: SFL Homepage"
   git branch -M main
   git remote add origin https://github.com/<DEIN-USERNAME>/sfl-homepage.git
   git push -u origin main
   ```

   Beim ersten Push fragt Git nach Login. Falls du noch keinen GitHub-Account verbunden hast: GitHub Desktop installieren oder Personal Access Token erstellen ([Anleitung](https://docs.github.com/de/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)).

3. **GitHub Pages aktivieren**

   Im neuen GitHub-Repo:
   - **Settings** → **Pages**
   - **Source**: `GitHub Actions` auswählen (NICHT „Deploy from a branch")
   - Speichern

4. **Workflow läuft automatisch**

   Sobald der erste Push durch ist, startet der GitHub-Action-Workflow automatisch (siehe Tab **Actions** im Repo). Nach 1–2 Minuten ist die Seite unter

   ```
   https://<DEIN-USERNAME>.github.io/sfl-homepage/
   ```

   erreichbar.

### Eigene Domain (sportfreunde-lauffen.de) verbinden

1. **Im Repo auf Settings → Pages → Custom domain**

   Dort `www.sportfreunde-lauffen.de` (oder `sportfreunde-lauffen.de`) eintragen und speichern. GitHub legt automatisch eine `CNAME`-Datei im Repo an.

2. **DNS-Einträge beim Domain-Provider setzen**

   Beim aktuellen Hoster der Domain (vermutlich der bisherige Webhoster):

   - **Apex-Domain** (`sportfreunde-lauffen.de`) → vier `A`-Records auf:

     ```
     185.199.108.153
     185.199.109.153
     185.199.110.153
     185.199.111.153
     ```

   - **www-Subdomain** (`www.sportfreunde-lauffen.de`) → ein `CNAME`-Record auf:

     ```
     <DEIN-USERNAME>.github.io
     ```

   DNS-Änderungen brauchen erfahrungsgemäß 15 Minuten bis ein paar Stunden.

3. **HTTPS aktivieren**

   In Settings → Pages den Haken bei **Enforce HTTPS** setzen, sobald GitHub das Zertifikat ausgestellt hat (kann nach DNS-Umstellung bis zu 24 Std. dauern).

### Updates veröffentlichen

Nach Änderungen einfach committen und pushen:

```powershell
git add .
git commit -m "Inhalt aktualisiert"
git push
```

Der Workflow deployt automatisch nach 1–2 Minuten.

### Troubleshooting

- **404 nach Push**: ein paar Minuten warten. Ggf. unter **Actions** prüfen, ob der Workflow erfolgreich war.
- **CSS oder Bilder fehlen**: Pfade müssen relativ sein (`assets/...`), nicht absolut (`/assets/...`). Aktuell ist das in allen Dateien korrekt eingestellt.
- **Logo erscheint nicht**: Datei-Endung exakt prüfen – `SFLweiss.jpeg` ist nicht das Gleiche wie `SFLweiss.JPEG` (Linux ist case-sensitive).

## Sicherheit

Eine ausführliche Anleitung zur Absicherung der Seite steht in [`SECURITY.md`](SECURITY.md). Kurzfassung:

**Bereits im Code aktiv:**
- Content-Security-Policy (CSP) als Meta-Tag in jeder HTML-Datei
- Strikte `referrer`-Policy
- `rel="noopener noreferrer"` an allen externen Links
- `.gitignore` schließt sensible Dateien aus

**Was du selbst zusätzlich tun solltest:**
1. **2-Faktor-Authentisierung** für GitHub und Domain-Provider aktivieren
2. Optional **Cloudflare** vorschalten (kostenlos) – bringt WAF, DDoS-Schutz und HTTP-Security-Header per Klick
3. Sobald das Kontaktformular live geht: **Honeypot + Bot-Schutz** (Formspree, Cloudflare Turnstile)
4. Quartalsweise Check unter [securityheaders.com](https://securityheaders.com) und [ssllabs.com/ssltest](https://www.ssllabs.com/ssltest/)

Sicherheitslücken bitte vertraulich an `info@sportfreunde-lauffen.de` melden (siehe `SECURITY.md`).

## Lizenz

Vorlage erstellt für die Sportfreunde Lauffen e.V. – freie Nutzung im Verein.
