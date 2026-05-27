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

## 2. Optional Apps Script Configuration

The Apps Script has safe defaults, but a copied deployment can override names with Script Properties before running `initSystem`.

In Apps Script, open **Project Settings > Script properties** and add any values you want to customize:

```text
DOCSHARE_WORKSPACE_FOLDER_NAME
DOCSHARE_SPREADSHEET_NAME
DOCSHARE_ATTACHMENTS_FOLDER_NAME
DOCSHARE_INCOMING_FOLDER_NAME
DOCSHARE_OUTGOING_FOLDER_NAME
```

If these are not set, the defaults in `apps/apps-script/Code.js` are used.

By default, Apps Script creates this Drive structure automatically:

```text
Docshare/
  Docshare Letter Management
  Attachments/
    Incoming Letters/
    Outgoing Letters/
```

If you already had older folders from a previous deployment, running `initSystem` keeps the stored IDs and moves those folders into the workspace where possible.

## 3. Initialize Sheets And Drive

In the Apps Script editor, select the function:

```text
initSystem
```

Run it once.

Google will ask for permissions. Approve them. This creates:

- a workspace Drive folder named `Docshare`
- a Google Sheet named `Docshare Letter Management` inside that workspace
- an `Attachments` folder inside that workspace
- child folders for incoming and outgoing attachments

The Sheet uses these tabs:

- `settings`
- `users`
- `incoming`
- `outgoing`
- `assignments`
- `notifications`

The ERD is available at:

```text
docs/erd.md
```

## 4. Deploy The API

In Apps Script:

1. Click **Deploy > New deployment**
2. Select **Web app**
3. Set **Execute as** to `Me`
4. Set **Who has access** to `Anyone`
5. Deploy
6. Copy the Web App URL ending in `/exec`

## 5. Configure The Frontend

Open:

```text
apps/frontend/config.js
```

Set values for the deployment:

```js
window.DOCSHARE_CONFIG = {
  appTitle: 'Your Office Name',
  appSubtitle: 'Letter Management System',
  appBranch: 'Office Branch',
  copyrightText: '2026 Office',
  driveUrl: '',
  gasWebAppUrl: 'YOUR_APPS_SCRIPT_WEB_APP_URL',
  localStorageKey: 'your_office_lms_v1',
  sessionStorageKey: 'your_office_sess_v1',
  outgoingFrom: 'Your Office Name',
  refPrefix: 'YOUR_REF_PREFIX',
};
```

`driveUrl` can be left blank. After the API initializes, the frontend will use the workspace folder created by Apps Script for the Drive button. Set it only if you want the button to open a specific folder.

Commit and push:

```bash
git add apps/frontend/config.js
git commit -m "Configure runtime app settings"
git push
```

For GitHub Actions deployments, you can set repository variables instead of editing `config.js` manually:

```text
DOCSHARE_APP_TITLE
DOCSHARE_APP_SUBTITLE
DOCSHARE_APP_BRANCH
DOCSHARE_COPYRIGHT_TEXT
DOCSHARE_DRIVE_URL
DOCSHARE_GAS_WEB_APP_URL
DOCSHARE_LOCAL_STORAGE_KEY
DOCSHARE_SESSION_STORAGE_KEY
DOCSHARE_OUTGOING_FROM
DOCSHARE_REF_PREFIX
PAGES_BASE_URL
```

`PAGES_BASE_URL` is only used by the pull request preview workflow, for example:

```text
OWNER.github.io/REPOSITORY
```

## 6. Enable GitHub Pages

In the GitHub repository:

1. Go to **Settings > Pages**
2. Set **Source** to **GitHub Actions**
3. Push to `main`
4. Open the GitHub Pages URL shown in the Actions deployment

## Data And Files

Letter data is stored in the Google Sheet using proper table tabs.

Attachments are uploaded into Google Drive and the Sheet stores the Drive file ID and link.

The upload forms allow manual reference numbers. If the reference field is left blank, the app generates a reference using `refPrefix` from `apps/frontend/config.js`.

## Safe Schema Updates

Column changes should be done in code, then applied by running `initSystem`.

The Apps Script backend now has migration-aware sheet setup:

- `SHEETS` defines the current expected columns
- `SHEET_ALIASES` maps old column names to new column names
- `initSystem` creates missing sheets and columns
- if a sheet header changes, the old sheet is copied to a timestamped backup tab first
- data is then migrated into the new header structure by column name or alias

Safe examples:

```js
// Rename an incoming column from "subject" to "subj"
const SHEET_ALIASES = {
  incoming: {
    subj: ['subject', 'Subject']
  }
};
```

After updating `SHEETS` or `SHEET_ALIASES`:

1. update Apps Script code
2. run `initSystem`
3. confirm the active sheet data
4. keep the generated backup tab until you are confident

Adding columns is safe. Renaming columns is safe when an alias is added. Removing columns is backed up first, but the removed values will only remain in the backup tab.

## Security Notes

The frontend no longer stores passwords. Login is checked by Apps Script against the `users` tab.

For stronger public security later, replace plain-text passwords in the Sheet with password hashes or Google account sign-in.
