# HelpNest — Docker Deploy

Self-host HelpNest on any server with Docker Compose.

## What's included

- HelpNest app (Next.js)
- PostgreSQL 16
- Redis 7
- Qdrant (for AI search)

## Quick start

```bash
git clone https://github.com/helpnest/helpnest-examples.git
cd helpnest-examples/docker
cp .env.example .env
```

Edit `.env` — at minimum set:

| Variable | Description |
|----------|-------------|
| `POSTGRES_PASSWORD` | Choose a strong password |
| `NEXTAUTH_SECRET` | Run `openssl rand -base64 32` |
| `NEXTAUTH_URL` | Your public URL e.g. `https://help.acme.com` |
| `NEXT_PUBLIC_APP_URL` | Same as above |

Then start all services:

```bash
docker compose up -d
```

Visit `http://localhost:3000` (or your domain).

## First-time setup

Run database migrations after the first start:

```bash
docker exec helpnest_app npx prisma migrate deploy
```

Then open the dashboard at `/dashboard` and create your first workspace.

## Updating HelpNest

```bash
docker compose pull
docker compose up -d
docker exec helpnest_app npx prisma migrate deploy
```

## HTTPS with Caddy (recommended)

Install [Caddy](https://caddyserver.com) on your server:

```bash
# Ubuntu/Debian
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update && sudo apt install caddy
```

Create `/etc/caddy/Caddyfile`:

```
help.yourdomain.com {
    reverse_proxy localhost:3000
}
```

```bash
sudo systemctl reload caddy
```

Caddy handles SSL certificates automatically via Let's Encrypt.

## Backup & restore

```bash
# Backup database
docker exec helpnest_postgres pg_dump -U helpnest helpnest > backup-$(date +%Y%m%d).sql

# Restore
docker exec -i helpnest_postgres psql -U helpnest helpnest < backup.sql
```

## Monitoring

Check service health:

```bash
docker compose ps
curl http://localhost:3000/api/health
```

View logs:

```bash
docker compose logs -f app
docker compose logs -f postgres
```
