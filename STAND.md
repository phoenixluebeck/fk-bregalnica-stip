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

## OFFEN – nächste Schritte (mit dem User)
1. **Firestore-Regeln publishen**: falls noch nicht geschehen — Inhalt aus `firestore.rules` in der Firebase
   Console (Firestore Database → Rules) einfügen und **Publish** klicken.
2. **Post-Login testen**: mit echtem Admin-Konto einloggen und prüfen, ob Budget-Daten geladen/gespeichert werden
   (Firestore-Zugriff, Regeln greifen korrekt).
3. **Deploy**: eigene Netlify-Seite / eigenes Git-Repo (dieser Ordner ist noch KEIN Git-Repo).

## Noch aus Phönix-Kontext (bei Bedarf anpassen)
- Team-/Liga-Bezeichnungen „Regionalliga/U23/Oberliga" (rein deutsch).
- Deutsche Abgaben-Felder in der Budgetrechnung: ÜL-Pauschale (275 €), Minijob (1200 €), LNK (23 %), VBG.
  → Für Nordmazedonien ggf. anpassen/entfernen, sobald die Struktur bekannt ist.
