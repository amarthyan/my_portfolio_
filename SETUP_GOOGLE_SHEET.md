# Connect Contact Form to Google Sheets

Follow these steps **once** — takes about 5 minutes.

---

## Step 1 — Open your Google Sheet

Go to your spreadsheet:
https://docs.google.com/spreadsheets/d/1JgactM7-tAEKwW17va6dQgNhtpZ1LbWDEMug-COxauQ/edit

In **Row 1**, add these headers in columns A, B, C, D:

| A | B | C | D |
|---|---|---|---|
| Timestamp | Name | Email | Message |

---

## Step 2 — Open Apps Script

In your Google Sheet, click the menu:
**Extensions → Apps Script**

A new tab opens with a code editor.

---

## Step 3 — Paste this code

**Delete everything** in the editor and paste the following:

```javascript
const SHEET_NAME = 'Sheet1'; // Change if your sheet tab has a different name

function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);

    sheet.appendRow([
      new Date().toLocaleString('en-IN', { timeZone: 'Asia/Kolkata' }),
      data.name    || '',
      data.email   || '',
      data.message || '',
    ]);

    return ContentService
      .createTextOutput(JSON.stringify({ status: 'ok' }))
      .setMimeType(ContentService.MimeType.JSON);

  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: err.message }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

// Optional: test by running this function manually in the editor
function testSheet() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);
  sheet.appendRow([new Date(), 'Test Name', 'test@example.com', 'Test message']);
  Logger.log('Row added!');
}
```

Click **Save** (Ctrl+S / Cmd+S).

---

## Step 4 — Deploy as a Web App

1. Click **Deploy → New deployment**
2. Click the ⚙️ gear icon next to "Type" → select **Web app**
3. Fill in the settings:
   - **Description:** Portfolio Contact Form
   - **Execute as:** Me
   - **Who has access:** Anyone
4. Click **Deploy**
5. Google will ask you to **Authorize** — click through and allow access
6. Copy the **Web App URL** — it looks like:
   `https://script.google.com/macros/s/AKfycb.../exec`

---

## Step 5 — Paste the URL into script.js

Open `script.js` and find this line near the top:

```javascript
const APPS_SCRIPT_URL = 'YOUR_APPS_SCRIPT_WEB_APP_URL';
```

Replace `'YOUR_APPS_SCRIPT_WEB_APP_URL'` with the URL you just copied:

```javascript
const APPS_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycb.../exec';
```

Save the file, then push to GitHub. Vercel will auto-redeploy.

---

## Done! 🎉

Every time someone submits the contact form, a new row will appear in your Google Sheet with:
- Timestamp (India time)
- Name
- Email
- Message
