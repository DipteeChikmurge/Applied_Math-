# Linear Algebra Assessment — Web App

A small, fully static 2-page web app:

- **`index.html`** — landing page with an overview of the assessment and a "Start Assessment" button.
- **`assessment.html`** — the 25-question gamified, self-grading assessment (XP, levels, streaks,
  badges, CSV export, and optional Google Sheet submission).
- **`GOOGLE_SHEET_SETUP.md`** — one-time setup so results can be recorded automatically into a
  Google Sheet (optional — CSV export works with zero setup).

No build step, no server, no dependencies — it's plain HTML/CSS/JS, so it can be hosted anywhere
that serves static files.

## Option A — Run it locally (fastest way to try it)
1. Unzip this folder.
2. Double-click `index.html` to open it in your browser.
   *(Or, for the most reliable experience, serve it locally instead of using `file://`:)*
   ```bash
   cd path/to/this/folder
   python3 -m http.server 8000
   ```
   Then open `http://localhost:8000` in your browser.

## Option B — Deploy for free (so students can access it from any device)

### Netlify (drag-and-drop, no account needed for a quick share)
1. Go to [app.netlify.com/drop](https://app.netlify.com/drop).
2. Drag this whole folder onto the page.
3. Netlify gives you a live URL (e.g. `https://random-name.netlify.app`) instantly.

### GitHub Pages
1. Create a new GitHub repository and upload these files (`index.html`, `assessment.html`,
   `GOOGLE_SHEET_SETUP.md`).
2. Go to **Settings → Pages**, set the source branch to `main` and folder to `/ (root)`.
3. Your site will be live at `https://<your-username>.github.io/<repo-name>/`.

### Vercel
1. Go to [vercel.com/new](https://vercel.com/new) and import the folder/repo.
2. Leave the framework preset as "Other" (no build command needed).
3. Deploy — Vercel gives you a live URL.

Any of these work equally well since the app has no backend of its own.

## Recording Results in Google Sheets (optional)
By default, students can click **Download CSV** on the results screen with zero setup.

For fully automatic Google Sheet recording (no button click, no pasting a URL), open
`assessment.html`, find this line near the top of the `<script>` section:
```javascript
const GOOGLE_SHEET_URL = "";
```
and paste your Apps Script Web App URL between the quotes:
```javascript
const GOOGLE_SHEET_URL = "https://script.google.com/macros/s/PASTE_YOUR_URL/exec";
```
Once set, every submission is saved to the Sheet automatically the moment a student finishes —
the manual "Send to Google Sheet" button and URL box disappear entirely. See
`GOOGLE_SHEET_SETUP.md` for the one-time Sheet + Apps Script setup (~5 minutes).

## Customizing
- **Add/edit questions:** open `assessment.html` and edit the `QUESTIONS` array near the top of
  the `<script>` section. Each question needs `q`, `opts`, `correct` (index), `marks`, and
  `difficulty` (`"core"`, `"beginner"`, `"average"`, or `"advanced"`).
- **Change the passing thresholds / grade labels:** search for `grade = "Needs Improvement"` in
  `assessment.html`.
- **Rebrand colors:** the palette is defined as CSS variables (`--navy`, `--terra`, `--ice`, etc.)
  at the top of each file's `<style>` block.
