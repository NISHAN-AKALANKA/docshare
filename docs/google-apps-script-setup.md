# Google Apps Script Setup

This deployment uses:

- GitHub Pages for the frontend
- Google Apps Script as the API
- Google Sheets as the database
- Google Drive as attachment storage

## 1. Create Apps Script Project

1. Go to https://script.google.com
2. Create a new project named `Docshare API`
3. Copy the code from `apps/apps-script/Code.js` into `Code.gs`
4. Add the manifest from `apps/apps-script/appsscript.json`

In Apps Script, open **Project Settings** and enable **Show "appsscript.json" manifest file in editor** if the manifest is hidden.

## 2. Initialize Sheets And Drive

In the Apps Script editor, select the function:

```text
initSystem
```

Run it once.

Google will ask for permissions. Approve them. This creates:

- a Google Sheet named `Docshare Letter Management`
- a Drive folder named `Docshare Attachments`
- child folders for incoming and outgoing attachments

The Sheet uses these tabs:

- `settings`
- `users`
- `incoming`
- `outgoing`
- `assignments`

## 3. Deploy The API

In Apps Script:

1. Click **Deploy > New deployment**
2. Select **Web app**
3. Set **Execute as** to `Me`
4. Set **Who has access** to `Anyone`
5. Deploy
6. Copy the Web App URL ending in `/exec`

## 4. Configure The Frontend

Open:

```text
apps/frontend/index.html
```

Set:

```js
const GAS_WEB_APP_URL="YOUR_APPS_SCRIPT_WEB_APP_URL";
```

Commit and push:

```bash
git add apps/frontend/index.html
git commit -m "Configure Apps Script API URL"
git push
```

## 5. Enable GitHub Pages

In the GitHub repository:

1. Go to **Settings > Pages**
2. Set **Source** to **GitHub Actions**
3. Push to `main`
4. Open the GitHub Pages URL shown in the Actions deployment

## Data And Files

Letter data is stored in the Google Sheet using proper table tabs.

Attachments are uploaded into Google Drive and the Sheet stores the Drive file ID and link.

## Security Notes

The frontend no longer stores passwords. Login is checked by Apps Script against the `users` tab.

For stronger public security later, replace plain-text passwords in the Sheet with password hashes or Google account sign-in.
