# Connecting the Assessment to a Google Sheet

The quiz page (`Linear_Algebra_Student_Assessment.html`) can send each student's result
straight into a Google Sheet using a small Google Apps Script "Web App" as the receiver.
Google Sheets has no public no-login API that a plain web page can call directly, so this
Apps Script bridge is the standard, no-cost way to do it. One-time setup, ~5 minutes.

## Step 1 — Create the Sheet
1. Create a new Google Sheet (e.g. "Linear Algebra — Assessment Results").
2. Rename the first tab to `Results`.
3. In row 1, add these headers across columns A–K:
   `Timestamp | Name | Roll No | Score | Total | Percentage | XP | Level | Best Streak | Time (s) | Badges`

## Step 2 — Add the Apps Script
1. In the Sheet, go to **Extensions → Apps Script**.
2. Delete any starter code and paste this:

```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Results");
  const data = JSON.parse(e.postData.contents);

  sheet.appendRow([
    data.timestamp,
    data.name,
    data.roll,
    data.score,
    data.total,
    data.percentage,
    data.xp,
    data.level,
    data.bestStreak,
    data.timeSeconds,
    data.badges
  ]);

  // Optional: log the per-question breakdown to a second tab called "Answers"
  let answersSheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Answers");
  if (!answersSheet) {
    answersSheet = SpreadsheetApp.getActiveSpreadsheet().insertSheet("Answers");
    answersSheet.appendRow(["Timestamp", "Name", "Roll No", "Question", "Difficulty", "Marks", "Selected", "Correct Answer", "Result"]);
  }
  data.answers.forEach(a => {
    answersSheet.appendRow([data.timestamp, data.name, data.roll, a.question, a.difficulty, a.marks, a.selected, a.correctAnswer, a.result]);
  });

  return ContentService.createTextOutput(JSON.stringify({ status: "ok" }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

3. Click **Save** (name the project anything, e.g. "LA Assessment Receiver").

## Step 3 — Deploy as a Web App
1. Click **Deploy → New deployment**.
2. Click the gear icon next to "Select type" and choose **Web app**.
3. Set:
   - **Execute as:** Me
   - **Who has access:** Anyone
4. Click **Deploy**, then **Authorize access** and approve the permissions (this is your own script acting on your own sheet).
5. Copy the generated **Web app URL** (ends in `/exec`).

## Step 4 — Connect the Quiz Page
1. Open `Linear_Algebra_Student_Assessment.html` in a browser (or host it — see note below).
2. After a student submits and sees their score, paste the Web app URL into the
   **"Teacher setup"** box near the bottom of the results panel.
3. Click **Send to Google Sheet**. A new row appears in the `Results` tab (and a full
   answer breakdown in the `Answers` tab) within a few seconds.

> Tip: If you want the URL pre-filled for every student automatically, open the HTML file,
> find the line `const scriptUrl = document.getElementById("scriptUrlInput").value.trim();`
> near the bottom of the `<script>` section, and replace it with
> `const scriptUrl = "https://script.google.com/macros/s/PASTE_YOUR_URL/exec";` — then the
> box and typing step disappear entirely for students.

## Notes
- Because the request uses `mode: "no-cors"`, the browser cannot confirm the row was
  written — it can only confirm the request didn't fail outright. Check the Sheet directly
  the first time to confirm it's wired up correctly.
- **Download CSV** always works with no setup, as a reliable fallback or for offline use —
  the CSV can also be dragged straight into Google Sheets via File → Import.
- To host the HTML page so every student can reach it from their own device (instead of
  emailing the file around), upload it to Google Drive and publish it, or host it on any
  static web host (GitHub Pages, Netlify, etc.).
