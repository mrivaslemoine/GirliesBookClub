# Girlies Book Club

A free, account-free monthly book nomination and selection tool. Anyone with the link can nominate books and see the current pool — no sign-up, no login, no app install.

## What it does

- Members submit up to 3 book nominations per month (title + author) under their name.
- A winner is **automatically drawn at random** on the monthly deadline (15th by default, with support for one-off date overrides).
- Past winners are saved permanently in a **History** log.
- Every past nomination is saved in a **Submission Archive**, tagged "Read" (it won) or "Not yet read" (it didn't).
- Organizers can manually redraw, reset the month, or roll specific old submissions into the next month's pool.
- Data is stored in a Google Sheet — free, no database needed.

## How it works (architecture)

- **`index.html`** — the actual web page, hosted free on GitHub Pages. Pure HTML/CSS/JS, no build step, no frameworks.
- **Google Sheet + Apps Script** — acts as the free backend/database. The page talks to a deployed Apps Script "Web App" URL to read and write data.

These are two separate pieces that must both be set up and connected.

---

## Setup Instructions

### Part 1: Google Sheet + Apps Script (the backend)

1. Go to [sheets.google.com](https://sheets.google.com) → create a blank sheet. Name it anything, e.g. "Book Club Data."
2. Don't create tabs manually — the script auto-creates `Nominations`, `Winner`, `History`, `SubmissionArchive`, and `Meta` on first run.
3. Go to **Extensions → Apps Script**. Delete any starter code.
4. Paste in the full `Code.gs` script (kept in this repo / project as a separate file).
5. Save (disk icon).
6. Click **Deploy → New deployment**. Click the gear icon → choose **Web app**.
7. Set **Execute as: Me**. Set **Who has access: Anyone**.
8. Click **Deploy**. Authorize when prompted (click through the "unverified app" warning — normal for personal scripts).
9. Copy the **Web app URL** — ends in `/exec`.

### Part 2: Connect the HTML file

1. Open `index.html`.
2. Find this line near the top of the `<script>` section:
   ```javascript
   const SCRIPT_URL = "PASTE_YOUR_DEPLOYED_SCRIPT_URL_HERE";
   ```
3. Replace the placeholder with your real `/exec` URL from Part 1, keeping the quotes.
4. Save.

### Part 3: Host on GitHub Pages (free)

1. Go to [github.com](https://github.com) → **New repository**. Make it **Public**. Add a README if prompted (this file can replace it).
2. **Add file → Upload files** → upload your edited `index.html`. Commit.
3. Go to **Settings → Pages**. Under "Source," choose **Deploy from a branch**. Under "Branch," choose **main**, folder `/ (root)`. Save.
4. Wait a minute, then click **Visit site** for your live public URL (e.g. `yourname.github.io/bookclub`).
5. Share that URL with the group.

### Part 4: Set up the auto-draw trigger

This makes the winner get picked automatically at the deadline, even if nobody has the page open.

1. In the Apps Script editor, click the **clock icon ("Triggers")** in the left sidebar.
2. Click **Add Trigger**.
3. Function: `autoDrawIfDue`. Event source: **Time-driven**. Type: **Hour timer**, every hour.
4. Save. Authorize again if prompted.

---

## Making future code changes

Whenever the script (`Code.gs`) is edited:
1. Paste the updated code into the Apps Script editor. Save.
2. **Deploy → Manage deployments** → pencil icon → Version: **New version** → Deploy.
   (Just saving does *not* update the live URL — you must redeploy a new version.)

Whenever `index.html` is edited:
1. Re-upload it to the GitHub repo (same filename, it overwrites).
2. GitHub Pages updates automatically within ~1 minute.

## Organizer tools

Click "⚙ Club organizer options" at the bottom of the page for:
- **Select a new book now** — force an immediate redraw.
- **Reset nominations for new month** — wipes the current pool.
- **Clear selected book** — removes the current winner without resetting nominations.
- **Roll specific submissions into next pool** — checkbox-pick old unread nominations to carry forward.

## Known limitations

- No login/authentication — anyone with the link can nominate or use organizer tools. Fine for a small trusted group; not designed for public/adversarial use.
- One-off deadline overrides (e.g. extending a single month's deadline) are hardcoded in both `index.html` and `Code.gs` and must be kept in sync manually.
- Auto-draw trigger checks hourly, so the draw may land up to ~1 hour after the exact deadline, not to-the-second.
