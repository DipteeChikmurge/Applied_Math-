# Deploy the Weblink with Automatic Google Sheet Recording

This is the full path from "nothing deployed yet" to "students click a link, take the
assessment, and every result saves to your Google Sheet automatically" — no button click,
no pasting a URL, no manual steps for students.

Total time: ~10 minutes, one-time.

---

## Step 1 — Set up the Google Sheet + Apps Script (get your Web App URL)

If you haven't done this yet, follow `GOOGLE_SHEET_SETUP.md` (or `Deploy_On_Another_Gmail_Account.md`
if you're using a different Gmail account than usual). At the end of that process you'll have a
URL that looks like:
```
https://script.google.com/macros/s/AKfycb........................../exec
```
Keep this URL handy — it's the only thing you need for the next step.

## Step 2 — Paste the URL into `assessment.html`

1. Open `assessment.html` in any text editor (Notepad, VS Code, TextEdit — anything works).
2. Find this line near the top of the `<script>` section (use Ctrl/Cmd+F to search for
   `GOOGLE_SHEET_URL`):
   ```javascript
   const GOOGLE_SHEET_URL = "";
   ```
3. Paste your URL between the quotes:
   ```javascript
   const GOOGLE_SHEET_URL = "https://script.google.com/macros/s/AKfycb.../exec";
   ```
4. Save the file.

That's it — this one edit is what makes saving automatic. With this set, the moment a student
finishes and their score is calculated, the page silently sends the result to your Sheet in the
background. The "Send to Google Sheet" button and the URL-paste box disappear from the page
entirely, since they're no longer needed.

## Step 3 — Put the page online (get a weblink students can open)

Pick whichever is easiest for you — all three are free and need no coding:

### Fastest: Netlify Drop (no account required)
1. Go to **[app.netlify.com/drop](https://app.netlify.com/drop)**.
2. Drag the whole `Linear_Algebra_Assessment_WebApp` folder (the one containing `index.html`
   and `assessment.html`) onto the page.
3. Netlify gives you a live link immediately, e.g. `https://cheerful-narwhal-123.netlify.app`.
4. Share that link with students. Done.

### GitHub Pages (good if you already use GitHub)
1. Create a new repository, upload `index.html`, `assessment.html`, and the two `.md` files.
2. Go to **Settings → Pages**, set source to branch `main`, folder `/ (root)`.
3. Your link: `https://<your-username>.github.io/<repo-name>/`

### Vercel
1. Go to **[vercel.com/new](https://vercel.com/new)**, import the folder or repo.
2. Leave the framework preset as "Other" — no build command needed.
3. Deploy; Vercel gives you a live link.

## Step 4 — Test the full flow once yourself

1. Open the live link in a private/incognito window (so it behaves like a fresh student).
2. Complete the assessment.
3. On the results screen, you should see a status line reading
   **"✓ Result saved to Google Sheet automatically."**
4. Open your Google Sheet — a new row should already be there.

If the row doesn't appear, see the Troubleshooting table in `Deploy_On_Another_Gmail_Account.md`
(most common cause: deployment access wasn't set to "Anyone").

## Step 5 — Share the link

Send students the one weblink from Step 3 (email, LMS announcement, QR code, etc.). Nothing
else is required on their end — no account, no setup, no extra clicks. Every submission lands
in your Sheet as they finish.

---

## Optional: verify without waiting for a real student

Use the `curl` command from the earlier troubleshooting steps to manually POST a test row to
your Apps Script URL and confirm it lands in the Sheet, before ever sharing the link:

```bash
curl -X POST -H "Content-Type: text/plain" \
  -d '{"timestamp":"2026-07-26T10:00:00Z","name":"Curl Test","roll":"C-001","score":30,"total":50,"percentage":"60.0","xp":150,"level":"2 - Learner","bestStreak":3,"timeSeconds":400,"badges":"","answers":[]}' \
  "https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec"
```

## Reverting to manual mode

If you ever want students to see the "paste URL" box again (e.g. testing with different Sheets
per class section), just set `GOOGLE_SHEET_URL` back to an empty string:
```javascript
const GOOGLE_SHEET_URL = "";
```
The manual button and input box will reappear automatically.
