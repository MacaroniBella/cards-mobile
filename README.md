# Cards of Alexandria — Mobile Workshop

A phone-first companion to your desktop dashboard. It reads and writes **live** to the
same master Google Sheet, through the **same Apps Script Web App** the desktop already uses —
so there's nothing new to set up on the data side and no second source of truth.

On your phone you can:

- **Browse facts** synced live from the sheet, filtered by card status
- **Assign facts to decks** (tap deck chips on each fact)
- **Rewrite facts** into the final card Question / Answer
- **Add deck ideas** on the go → writes to the `Decks` tab
- **Add sources** you find day to day → writes to the `Sources` tab

It installs to your home screen as an app (PWA) and is 100% free to host on GitHub Pages.

---

## How it talks to your data

Everything goes through your existing Apps Script `/exec` URL using plain GET requests
(Apps Script redirects POST → GET, so the desktop app already does all writes via GET — this app
follows the exact same contract):

| Action in app | Request |
|---|---|
| Sync / pull everything | `?action=read` |
| Save a fact (decks + Q/A + status) | `?action=updateFact&factId=…&question=…&answer=…&cardStatus=…&assignedDecks=…` |
| Add a deck | `?action=saveItem&sheet=Decks&item={…}` |
| Add a source | `?action=saveItem&sheet=Sources&item={…}` |

`assignedDecks` is stored as a `|`-separated list of **deck titles**, identical to the desktop app —
so a fact assigned here shows up assigned there, and vice versa.

> You do **not** need to change or re-deploy your Apps Script. The desktop "Full Sync API v3"
> script already supports every action above.

---

## One-time setup (about 10 minutes)

### 1. Put these files in a GitHub repo

Files in this folder:

```
index.html
manifest.webmanifest
sw.js
icon-192.png
icon-512.png
icon-512-maskable.png
README.md
```

**Option A — github.com in a browser (no tools):**
1. Go to <https://github.com/new>, name it e.g. `cards-mobile`, set it **Private** if you like
   (GitHub Pages works on private repos for personal/free accounts), click **Create repository**.
2. On the repo page click **Add file → Upload files**.
3. Drag in all the files from this folder. Commit.

**Option B — git command line:**
```bash
cd cards-mobile
git init
git add .
git commit -m "Cards mobile workshop"
git branch -M main
git remote add origin https://github.com/<you>/cards-mobile.git
git push -u origin main
```

### 2. Turn on GitHub Pages
1. Repo → **Settings** → **Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Branch: **main**, folder: **/ (root)**. Click **Save**.
4. Wait ~1 minute. The page shows your live URL, e.g.
   `https://<you>.github.io/cards-mobile/`

### 3. Open it on your phone and connect
1. Open that URL in your phone browser (Safari on iPhone, Chrome on Android).
2. Paste your Apps Script Web App URL — the one ending in **/exec**.
   - Find it on desktop: **dashboard → Facts → Connect Google Sheets**, or in Apps Script:
     **Deploy → Manage deployments → copy the Web app /exec URL**.
3. Tap **Connect**. It syncs immediately.

### 4. Add it to your home screen (makes it feel like an app)
- **iPhone (Safari):** Share button → **Add to Home Screen** → Add.
- **Android (Chrome):** ⋮ menu → **Install app** / **Add to Home screen**.

Now it opens fullscreen from your home screen with its own icon.

---

## Daily use

- **Desktop / laptop:** import facts into the master Google Sheet as you already do.
- **Phone:** open the app, tap **Sync** (top right) to pull the newest facts, then assign,
  rewrite, and capture ideas. Every save writes straight back to the sheet.

The last sync is cached on the phone, so the app still opens and shows your latest pulled data
with no signal — but you need a connection to sync new content or save changes.

---

## Notes & limits

- **Privacy:** the app only stores your sheet URL and a cached copy of your own data in the
  browser on your phone. If your repo is **public**, anyone who finds the URL could load the app,
  but they still can't see your data without your Apps Script URL. Use a **private repo** if you
  want the app page itself to stay private, or keep the repo public but treat the `/exec` URL as the secret.
- **Updating the app:** edit the files, push to GitHub, then in the app pull-to-refresh / reopen.
  If a change doesn't show, bump `CACHE = 'coa-mobile-v1'` → `v2` in `sw.js` so the service worker refreshes.
- **Adding fact rows** is intentionally left to the desktop import flow — the phone is for
  refining and organizing, keeping one clean source of truth.

---

*Built to match the desktop dashboard's "Full Sync API v3" Apps Script contract.*
