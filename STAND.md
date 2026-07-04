# FK Bregalnica Štip – Budget-App · Projektstand (Übergabe)

Eigenständige Kopie der Vereins-Budget-App, **nur Admin-Zugang**. Diese Sitzung ist
ausschließlich für **FK Bregalnica Štip** (Phönix Lübeck läuft in einer separaten Sitzung).

## Was die App ist
- Single-File-HTML-App: `index.html` (React 18 + Babel-standalone + Tailwind JIT + Firebase, alles via CDN).
- Dateien im Ordner: `index.html`, `phoenix-logo.js` (enthält das eingebettete Bregalnica-Logo als Base64), `firestore.rules`, `EINRICHTUNG.md`, `icons/` (Logo-Quellen).
- **KRITISCH:** `@babel/standalone` MUSS auf **7.x** gepinnt bleiben (aktuell `@7.26.4`). Babel 8 = weißer Bildschirm.
- Nach Code-Änderungen: verifizieren, indem man `index.html`+`phoenix-logo.js` nach `/tmp/phxcal/` kopiert und über einen lokalen Server rendert (Login-Screen muss erscheinen; `firebase.apps.length` etc.). Post-Login nicht testbar ohne echte Firebase-Config.

## Schon erledigt
- Umbenannt auf „FK Bregalnica Štip" (keine Phönix-Reste).
- **Nur Admin-Login** (Trainer/Spieler-Rollen + Rollen-Auswahl entfernt).
- Auslosungs-Tool entfernt; deutsches Regionalliga-Nord-Logo entfernt.
- **Logo eingebettet** + **Hellblau (#29ABE2 / HSL ~202 84% 42%)** als Theme (CSS-Vars, Login-Verlauf, PDF-Header).
- `firestore.rules` auf **Admin-only** vereinfacht.
- **Admin-UID eingetragen**: `pQLAFiWxe1PqIMuE6LoOoF2ZLKr2` in `firestore.rules` (Zeile 7) statt `HIER_ADMIN_UID`.
  → Regeln dem User geschickt zum Einfügen/Publishen in der Firebase Console.
- **Admin-Konto bestätigt**: `admin@bregalnica-stip.de` ist in Firebase mit Passwort eingerichtet, passt zu `AUTH_EMAIL` im Code.
- **Firebase-Config eingetragen** (Projekt `fk-bregalnica-stip`) in `index.html` Zeile 142-149
  (apiKey, authDomain, projectId, storageBucket, messagingSenderId, appId). `measurementId`/Analytics nicht benötigt (App nutzt kein Firebase-Analytics-SDK).
- **Verifiziert**: Dateien nach `/tmp/bregalnica_verify/` kopiert, per lokalem Server geladen — Login-Screen
  erscheint korrekt (Logo, Titel „FK Bregalnica Štip", Passwortfeld), keine Konsolenfehler.
  Preview-Server-Configs liegen in `~/.claude/launch.json` (`bregalnica-verify` → `/tmp/bregalnica_verify`).
  Hinweis: der Preview-Server kommt nicht direkt an den Desktop-Ordner (Sandbox) – deshalb der Umweg über `/tmp`,
  genau wie beim Phönix-Setup (`phx-verify` → `/tmp/phxcal`). Nach jeder Code-Änderung Dateien erneut nach
  `/tmp/bregalnica_verify/` kopieren, um zu verifizieren.

- **Live deployed via GitHub Pages**: Repo `phoenixluebeck/fk-bregalnica-stip` (eigener Account des Users,
  NICHT das anfangs versuchte `phoenixluebeck/fkbregalnica` — dort gab es nur Read-Zugriff/Token-Probleme).
  → App live unter **https://phoenixluebeck.github.io/fk-bregalnica-stip/** (verifiziert: 200, Login-Screen,
  relative Pfade für `phoenix-logo.js`/`icons/` funktionieren korrekt unter dem Pages-Unterpfad).
  Push erfolgt über einen dedizierten **Deploy Key** (SSH, Write-Access), nicht über HTTPS/PAT:
  - Lokaler Key: `~/.ssh/bregalnica_stip` (+ `.pub`), SSH-Alias in `~/.ssh/config`: `github-bregalnica`
  - Remote: `git@github-bregalnica:phoenixluebeck/fk-bregalnica-stip.git`
  - Deploy Key ist im Repo unter Settings → Deploy keys mit "Allow write access" hinterlegt.
  - Für künftige Deploys: im Projektordner `git add -A && git commit -m "..." && git push` (Remote ist bereits
    per SSH-Alias korrekt gesetzt).
  - GitHub Pages Setting: Branch `main`, Ordner `/ (root)`.

- **Deutsche Abgaben-Felder durch Nordmazedonien-Berechnung ersetzt**: ÜL-Pauschale, Minijob, LNK (23%) und VBG
  komplett entfernt (Checkboxen, Konstanten `UEL/MINIJOB/MINIJOB_NET`, Funktionen `lnkOf/vbgP/vbgS`, alle
  Tabellen-Spalten, CSV-Exporte, Kader-Tabs „Minijob"/„ÜL-Pauschale").
  Neu: `mkNetto(bruttoMonat)` (index.html Zeile ~178) berechnet Sozialabgaben (28% AN-Anteil: Rente 18,8% +
  Kranken 7,5% + Arbeitslosen 1,2% + Zusatz-KV 0,5%) + Lohnsteuer (10% Flat-Tax auf Brutto − Sozialabgaben −
  Freibetrag ~167€/Monat) → Netto. Wichtig: In Nordmazedonien zahlt der **Arbeitgeber keine eigenen
  Lohnnebenkosten** (anders als Deutschland) — AG-Kosten = Bruttogehalt selbst, kein Aufschlag mehr in
  `calcP`/`calcS`. Die Netto-Berechnung erscheint als eigene Box in `PlayerForm`/`StaffForm`; CSV-Exporte
  (Kader + Staff) enthalten jetzt Sozialabgaben/Lohnsteuer/Netto statt LNK/VBG.
  Verifiziert nach `/tmp/bregalnica_verify/`: Login-Screen lädt weiterhin fehlerfrei, keine Konsolenfehler
  (bestätigt, dass Babel das gesamte Script inkl. aller JSX-Änderungen fehlerfrei transpiliert).

- **Spieler-Berechnung durch reale Vertragsstruktur des Vereins ersetzt** (präziser als die generische
  28%/10%-Länderrechnung, da Spielerverträge in der Praxis anders aufgesetzt werden):
  - **Mindestlohn-Vertrag** (Minijob-Äquivalent, **optional** per Kästchen in `PlayerForm`): fixe 450 €
    netto/Monat an den Spieler, Verein zahlt zusätzlich **45%** Sozialabgaben obendrauf → AG-Kosten 652,50 €/M.
    Konstanten `MK_MINIJOB_NETTO`/`MK_MINIJOB_AUFSCHLAG`/`MK_MINIJOB_KOSTEN` (index.html Zeile ~184).
  - **Aneks-Vertrag** (Zusatzvereinbarung, **für alle Spieler**, das bisherige „Brutto/Monat"-Feld, jetzt
    „Aneks-Gehalt/Monat"): Betrag ist für den Spieler netto = brutto (keine Abzüge), Verein zahlt zusätzlich
    **10%** obendrauf. Konstante `MK_ANEKS_AUFSCHLAG`.
  - Beispiel: 2000 € Aneks + Mindestlohn-Vertrag → Spieler erhält 2450 € netto (2000+450), Verein zahlt
    2852,50 € (2000×1,10 + 652,50).
  - `calcP` (index.html Zeile ~188) komplett neu; `calcS`/`StaffForm`/Staff-CSV bleiben unverändert bei der
    generischen `mkNetto` (28% Sozialabgaben + 10% Flat-Tax) — die neue Struktur gilt laut User nur für Spieler,
    nicht für Staff.
  - `PlayerForm` zeigt jetzt eine „Vertragsstruktur Nordmazedonien"-Box mit Aneks-Betrag, Mindestlohn (falls
    aktiv) und Netto-Summe. Kader-CSV-Export angepasst (Spalten: Aneks-Gehalt/M, Mindestlohn-Vertrag ja/nein,
    Netto/M, AG-Kosten/M). Paket-Äquivalent-Spalte in der Kader-Tabelle berücksichtigt jetzt auch die 450€
    Mindestlohn-Netto, falls aktiviert.
  - Verifiziert nach `/tmp/bregalnica_verify/`: Login lädt fehlerfrei, keine Konsolenfehler; Rechenbeispiel
    manuell gegengeprüft (s.o.).

- **Liga/Verband/Ort auf Nordmazedonien umgestellt** + **nur eine Mannschaft** (kein Regionalliga/U23-Split mehr):
  - Liga-Bezeichnung im Kalender: „Regionalliga" → **„Prva Liga"** (Macedonian First Football League), kurz „PL"
    (`TERMIN_TYPEN`, index.html Zeile ~102 — die interne ID `'regionalliga'` blieb unverändert, nur das Label).
  - Einnahmen-Kategorie „DFB/Verband" → **„FFM/Verband"** (Football Federation of North Macedonia).
  - „Landespokalprämie" → **„Pokalprämie"** (Bezug: Kup na Makedonija) — Feld `praemieLandespokal` →
    `praemiePokal` umbenannt (PlayerForm, KaderPage-Tabelle/CSV, OverviewPage).
  - Platzhaltertexte: „VfB Lübeck" → „FK Rabotnički" (Gegner-Beispiel), „Lohmühle, Lübeck" → „Gradski stadion,
    Štip" (Ort-Beispiel), Login-Footer „Lübeck" → „Štip".
  - **Mannschafts-Konzept komplett entfernt** (`team: 'regionalliga'/'u23'`, `TEAM_LBL`, U23/Oberliga existieren
    nicht mehr): kein Mannschafts-Feld mehr in `PlayerForm`, `KaderPage` zeigt einen flachen Kader ohne Tabs,
    `CostForm`/`KostenPage` ohne „Zuordnung"-Feld/Filter (ergab bei nur einer Mannschaft keinen Sinn mehr),
    `OverviewPage` zeigt eine einzelne „Spielerkosten"-Zeile statt RL/U23-Split, `TrainingPage` vereinfacht
    (Team-Filter-Buttons entfernt, da nur noch eine Gruppe existiert).
  - Verifiziert nach `/tmp/bregalnica_verify/`: Login-Screen lädt weiterhin fehlerfrei, keine Konsolenfehler.

- **Spielplan auf 36 Spieltage angepasst** (Prva Liga: 10 Teams, jeder 4x gegen jeden = 9 Gegner × 4 = 36 Spiele,
  vorher 34 nach deutschem Regionalliga-Nord-Vorbild): `DEFAULT_SP` (index.html Zeile 78) und der `tg||36`-Fallback
  in `costYr` (Zeile 197) aktualisiert. Monatsverteilung: `[0,4,3,4,3,2,2,3,4,4,4,3]` (Juli weiterhin 0 =
  Saisonvorbereitung). Verifiziert: Login lädt fehlerfrei, keine Konsolenfehler.

- **Spieler-Vertragsstruktur ersetzt die generische Länder-Berechnung** (nur für Spieler, Staff bleibt bei
  28%/10%): **Mindestlohn-Vertrag** (Kästchen in `PlayerForm`, optional) = 450 € netto/Monat, Verein zahlt
  zusätzlich 45% → 652,50 € AG-Kosten. **Aneks-Vertrag** (Zusatzvereinbarung, für alle Spieler, das bisherige
  „Brutto/Monat"-Feld heißt jetzt „Aneks-Gehalt/Monat") = Betrag ist netto=brutto für den Spieler, Verein zahlt
  zusätzlich 10% obendrauf. Beispiel: 2000 € Aneks + Mindestlohn → Spieler netto 2450 €, Verein zahlt 2852,50 €.
  `calcP` komplett neu (index.html Zeile ~188), Konstanten `MK_MINIJOB_NETTO/_AUFSCHLAG/_KOSTEN`,
  `MK_ANEKS_AUFSCHLAG`. `PlayerForm` zeigt „Vertragsstruktur Nordmazedonien"-Box; Kader-CSV angepasst.

- **Monatlich-Kostenstellen/Einnahmen auf Zeitraum einschränkbar**: Bei Häufigkeit „Monatlich" gibt es jetzt
  zusätzlich „Von Monat"/„Bis Monat" (Felder `monthFrom`/`monthTo`, 0=Jul...11=Jun, inklusive) in `CostForm`
  und `IncomeForm` — z. B. ein Sponsoring nur Aug–Dez statt automatisch das ganze Jahr. Ohne Angabe (alte
  Datensätze) gilt weiterhin die volle Saison (0–11). Helper `monthRangeCount`/`inMonthRange` (index.html
  Zeile ~207) steuern `costYr`/`costMo`/`incYr`/`incMo`. Verifiziert: Login lädt fehlerfrei, keine Konsolenfehler.

## OFFEN – nächste Schritte (mit dem User)
1. **Firestore-Regeln publishen**: falls noch nicht geschehen — Inhalt aus `firestore.rules` in der Firebase
   Console (Firestore Database → Rules) einfügen und **Publish** klicken.
2. **Post-Login testen**: auf der Live-Seite mit echtem Admin-Konto einloggen und alle neuen Berechnungen/Felder
   (Spieler-Vertragsstruktur, Monatlich-Zeitraum bei Kosten/Einnahmen) mit echten Daten prüfen.
