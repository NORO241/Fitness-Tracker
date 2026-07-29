# Two-a-Day — Installable Tracker

Same layout and tracking logic as the Claude artifact version, packaged as a real
installable app (PWA) that puts an icon on both your phones and syncs your entries
between them — independent of Claude.

There are two steps: (1) get free real-time sync working, (2) put the files
somewhere with HTTPS so phones can install it.

---

## 1. Set up free sync (Firebase Realtime Database)

This takes about 5 minutes and costs nothing at this scale.

1. Go to https://console.firebase.google.com and sign in with any Google account.
2. Click **Add project** → give it any name (e.g. "two-a-day") → finish creating it.
3. In the left sidebar, open **Build → Realtime Database**.
4. Click **Create Database**. Choose any region. Start in **test mode** (this makes
   read/write open to anyone with the URL — fine for a private link only the two of
   you have; see the note at the bottom if you want to lock it down further).
5. Once created, you'll see a URL at the top of the database page that looks like:
   `https://two-a-day-xxxxx-default-rtdb.firebaseio.com`
   Copy that whole URL.
6. Keep it handy — you'll paste it into the app on first launch (on **each** phone).

That's it. No SDK, no npm install — the app talks to Firebase directly over plain
HTTPS requests.

---

## 2. Host the files somewhere with HTTPS

Phones will only let you "install" a web app (add it to the home screen as a real
app icon) if it's served over HTTPS. The easiest free option is **GitHub Pages**:

1. Create a free GitHub account if you don't have one: https://github.com
2. Create a new repository (e.g. `two-a-day`), and upload all the files from this
   folder (`index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`)
   to it — you can drag-and-drop them in the GitHub web UI, no command line needed.
3. Go to the repo's **Settings → Pages**, set **Source** to your main branch
   (root folder), and save.
4. GitHub gives you a URL like `https://yourname.github.io/two-a-day/` — that's
   your app's live link.

*(Alternatives if you'd rather not use GitHub: Netlify Drop — netlify.com/drop —
lets you drag this folder into a browser tab and get a live HTTPS link in seconds,
no account needed for a quick test.)*

---

## 3. Install it on both phones

1. Open the HTTPS link from step 2 in your phone's browser (Chrome on Android,
   Safari on iOS).
2. On first load, paste the Firebase Database URL from step 1 into the setup box
   and tap **Save & continue**. Do this on **both** phones, pointing at the
   **same** Firebase URL — that's what makes your entries sync.
3. Install it as an app:
   - **Android (Chrome):** tap the ⋮ menu → **Add to Home screen** / **Install app**.
   - **iPhone (Safari):** tap the Share icon → **Add to Home Screen**.
4. You'll now have a "Two-a-Day" icon on your home screen that opens full-screen,
   like a native app.

The app checks for the other person's new entries every ~8 seconds while it's
open, and also refreshes the moment you reopen it. It's not millisecond-live like
a chat app, but it's fast enough that logging a set at the gym shows up for your
partner within seconds.

---

## Notes

- **This app is now independent of Claude** — it doesn't call Claude or use any
  Anthropic API. It only talks to your Firebase database.
- **"Test mode" security:** Firebase's test mode means anyone with your database
  URL can read/write to it — there's no login. For a private link only shared
  between the two of you, this is low-risk, but test mode also **expires after 30
  days** and reverts to fully locked. If it locks you out, go back to Realtime
  Database → **Rules** tab in the Firebase console and set:
  ```json
  { "rules": { ".read": true, ".write": true } }
  ```
  to keep it open indefinitely. (Real access control would need Firebase Auth,
  which is more setup than this project needs for two people.)
- **Editing later:** if you want Claude to make more layout/feature changes,
  just paste `index.html` back into a chat and ask for the change — the tracking
  logic is unchanged from the artifact version, so edits carry over cleanly.
