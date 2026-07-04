# FK Bregalnica Štip – Budget-App · Einrichtung

Eigenständige Kopie der Vereins-App, **nur mit Admin-Zugang**.
Damit sie läuft, einmalig ein **eigenes Firebase-Projekt** anlegen (eigene, getrennte Datenbank).

## 1. Firebase-Projekt anlegen
1. https://console.firebase.google.com → **Projekt hinzufügen** (z. B. `fk-bregalnica-stip`)
2. Google Analytics kann aus bleiben.

## 2. Web-App + Config
1. Projektübersicht → **</> (Web-App hinzufügen)** → Name vergeben → registrieren
2. Firebase zeigt ein `firebaseConfig`-Objekt. Diese Werte in **index.html** eintragen
   (ganz oben im `firebase.initializeApp({...})`-Block, aktuell stehen dort Platzhalter):
   - `apiKey` → DEIN_API_KEY
   - `authDomain` → DEIN_PROJEKT.firebaseapp.com
   - `projectId` → DEIN_PROJEKT_ID
   - `storageBucket` → DEIN_PROJEKT.firebasestorage.app
   - `messagingSenderId` → DEINE_SENDER_ID
   - `appId` → DEINE_APP_ID

## 3. Anmeldung aktivieren + Admin-Konto
1. **Authentication** → Get started → **Email/Password** → aktivieren → Save
2. **Users** → **Add user**: E-Mail + Passwort für den Admin.
   - Standard-E-Mail im Code: `admin@bregalnica-stip.de`
   - Entweder GENAU diese E-Mail anlegen – oder in index.html bei `AUTH_EMAIL` deine E-Mail eintragen.
3. Die **User-UID** des Admin-Kontos kopieren.

## 4. Firestore + Sicherheitsregeln
1. **Firestore Database** → Datenbank erstellen (Produktionsmodus)
2. Reiter **Rules** → Inhalt aus **firestore.rules** einfügen
3. `HIER_ADMIN_UID` durch die kopierte Admin-UID ersetzen → **Publish**

## 5. Veröffentlichen (Netlify)
- Eigene Netlify-Seite (neues Git-Repo oder Ordner per Drag & Drop).
- Wichtig: `index.html` und `phoenix-logo.js` (Logo) müssen zusammen liegen.

## Noch offen / anpassbar
- **Logo & Farben**: Sende mir das Vereinslogo + Farben – ich baue sie ein (aktuell Platzhalter „B" + rotes Design).
- **Ligen/Team-Bezeichnungen** (Regionalliga, U23) und die **deutschen Abgaben-Felder**
  (ÜL-Pauschale, Minijob, LNK, VBG) sind noch aus dem Phönix-Kontext – sag mir eure
  Struktur, dann passe ich das an.
