# Docshare

Simple monorepo for the letter management app.

## Structure

- `apps/frontend`: static React HTML app served by nginx
- `apps/backend`: Node.js API that saves shared app data to `data/data.json`
- `apps/apps-script`: Google Apps Script API for Google Sheets and Drive hosting
- `data`: local persistent data folder mounted into Docker

## Recommended Public Hosting

The recommended free public setup is:

- GitHub Pages for the frontend
- Google Apps Script Web App for the API
- Google Sheets for database tables
- Google Drive for attachments

Setup guide:

```text
docs/google-apps-script-setup.md
```

## Run With Docker

```bash
docker compose up --build
```

Open:

```text
http://localhost:3005
```

The backend saves shared data on your local machine at:

```text
./data/data.json
```

## Public Access

Expose `http://localhost:3005` with a tunnel such as Cloudflare Tunnel or ngrok.

Example:

```bash
cloudflared tunnel --url http://localhost:3005
```

Keep the computer on while people use the app.
