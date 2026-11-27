# Custom Stands Wiki — deploy notes

A fan-made JoJo wiki + community: custom Stands, stories, creepypasta,
powers and general posts, with accounts, upvotes, reports and featuring.
Static site (GitHub Pages) backed by Firebase Auth + Firestore (Spark).

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app (HTML/CSS/JS in one file). |
| `firebase-config.js` | Firebase web config — **you must fill this in** (step 3). |
| `assets/fortunate-son.webp` | Portrait for the seeded Fortunate Son entry. |
| `assets/dead-mans-hand.webp` | Portrait for the seeded Dead Man's Hand entry. |
| `firestore.rules` | Security rules — **paste into the Firebase console** (step 4). |

## Deploy steps

1. **Create the Firebase project** at <https://console.firebase.google.com>
   (Spark / free plan is fine). Add a **Web app** to it.
2. **Turn on the services:**
   - Authentication → Sign-in method → enable **Email/Password**.
   - Firestore Database → **Create database** (production mode).
3. **Fill in `firebase-config.js`:** Project settings → General → your
   Web app → copy the config values (`apiKey`, `authDomain`, `projectId`,
   `storageBucket`, `messagingSenderId`, `appId`) over the `REPLACE_...`
   placeholders. Until this is done the site runs in a friendly
   "not connected yet" preview mode (announcement + the two seed Stands,
   no accounts/posting).
4. **Publish the rules:** Firestore Database → Rules → paste the whole
   `firestore.rules` file → Publish.
5. **Ship the site:** put this whole folder in a GitHub repo and enable
   **GitHub Pages** (Settings → Pages → deploy from branch), or drop the
   folder on any static host. In Firebase console → Authentication →
   Settings → **Authorized domains**, add your Pages domain
   (e.g. `yourname.github.io`) so sign-in works there.
6. **Claim the Owner account:** register on the live site with the
   account that should own the wiki. The **first** account to sign in
   claims `config/owner` and becomes Owner forever (👑 badge, Owner tab,
   ban/feature/report-queue powers). Do this before sharing the link.
   The two seed Stands (Fortunate Son + Dead Man's Hand, by Rizz) are
   written automatically the first time anyone signs in while the wiki
   is empty — they never overwrite later edits.

## How it works

- **Accounts:** register with an email (Gmail) + username + password.
  After that, sign-in is **username + password only** — the site resolves
  the username to the account email via the `usernames/` collection.
- **Sections:** Stands (full wiki-style entries with infobox, stat
  grades and abilities), Stories, Creepypasta, Powers, Everything.
- **Upvotes:** one per signed-in user, toggle on/off. At **10 upvotes**
  a post becomes **Featured for 3 days** on the Home page. The Owner can
  also feature/unfeature anything by hand.
- **Reports:** one per signed-in user per post. At **3 reports** a post
  is auto-hidden and lands in the Owner's reported queue; the Owner can
  restore it or delete it.
- **Bans:** the Owner can ban/unban from the Owner tab. Banned users are
  signed out, blocked, and can't write anything (enforced in
  `firestore.rules`). The Owner can never be banned.
- **Search** filters the visible lists by title. No composite Firestore
  indexes are needed — public lists query `hidden == false` and sort
  client-side on purpose.
