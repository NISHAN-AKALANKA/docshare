# Docshare

## Structure

- `apps/frontend`: static HTML/React frontend deployed to GitHub Pages
- `apps/apps-script`: Google Apps Script API for Google Sheets and Drive
- `docs`: setup notes and ERD

## Hosting

This project uses:

- GitHub Pages for the frontend
- Google Apps Script Web App for the API
- Google Sheets for database tables
- Google Drive for attachments

Setup guide:

```text
docs/google-apps-script-setup-guide.md
```

ERD:

```text
docs/erd.md
```

## GitHub Pages And PR Previews

This repo deploys the frontend from `apps/frontend` to a `gh-pages` branch.

In GitHub, configure:

```text
Settings > Pages > Deploy from a branch > gh-pages > / (root)
```

Pull requests get preview URLs under:

```text
https://NISHAN-AKALANKA.github.io/docshare/pr-preview/pr-<PR_NUMBER>/
```

The preview is updated on every PR push and removed when the PR is closed.

If the root URL shows a GitHub Pages 404, run or merge the main Pages deployment first. The preview URL itself must include `/pr-preview/pr-<PR_NUMBER>/`.

If the PR preview workflow logs `Timed out waiting for build to start`, the preview files were still pushed to `gh-pages`; GitHub Pages just did not report a matching deployment in time. Wait a minute and open the preview URL directly.

## Runtime Configuration

Frontend runtime values live in:

```text
apps/frontend/config.js
```

Use `apps/frontend/config.example.js` as the template when copying this app to another repository, Apps Script project, Sheet, or Drive folder.

`driveUrl` may be left blank. The frontend will use the workspace folder returned by Apps Script after the API initializes.

For GitHub Actions deployments, set repository variables and the workflows will generate `config.js` during deployment:

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
