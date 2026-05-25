# Docshare

Simple monorepo for the letter management app.

## Structure

- `apps/frontend`: static React HTML app served by nginx
- `apps/backend`: Node.js API that saves shared app data to `data/data.json`
- `data`: local persistent data folder mounted into Docker

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
