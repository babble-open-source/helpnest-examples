# HelpNest Examples

Deploy examples for [HelpNest](https://github.com/babble-open-source/helpnest) — the open-source customer help center.

## Deployment Options

| Platform | Cost | Setup time | Best for |
|----------|------|-----------|----------|
| [Docker](#docker) | Free (your server) | ~5 min | Self-hosting on VPS/bare metal |
| [Railway](#railway) | ~$6/mo | ~3 min | Quickest managed deploy |
| [Vercel + Neon](#vercel) | Free tier available | ~10 min | Serverless / JAMstack |
| [Render](#render) | Free tier available | ~5 min | Simple PaaS |

---

## Docker

The simplest self-hosted option. Runs everything (app, postgres, redis, qdrant) in containers.

### Prerequisites
- Docker + Docker Compose installed
- A server or VPS (DigitalOcean, Hetzner, Linode, etc.)

### Quick start

```bash
# Option A — use the HelpNest setup script
git clone https://github.com/babble-open-source/helpnest.git
cd helpnest
chmod +x scripts/self-host-setup.sh
./scripts/self-host-setup.sh

# Option B — use the compose file from this repo
git clone https://github.com/babble-open-source/helpnest-examples.git
cd helpnest-examples/docker
cp .env.example .env
# Edit .env with your values
docker compose up -d
```

See [docker/README.md](./docker/README.md) for full details including HTTPS setup and backups.

---

## Railway

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/helpnest)

One-click deploy to Railway with PostgreSQL and Redis included.

See [railway/README.md](./railway/README.md) for full details.

---

## Vercel

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/babble-open-source/helpnest&project-name=helpnest&repository-name=helpnest)

Deploy the Next.js app to Vercel with a managed PostgreSQL (Neon or Supabase).

See [vercel/README.md](./vercel/README.md) for full details.

---

## Render

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy)

One-click deploy using the `render.yaml` blueprint — provisions app, database, and Redis automatically.

See [render/README.md](./render/README.md) for full details.

---

## Environment Variables

All deployments require these core variables:

```env
DATABASE_URL=postgresql://...
NEXTAUTH_SECRET=<random 32+ char string — run: openssl rand -base64 32>
NEXTAUTH_URL=https://your-domain.com
NEXT_PUBLIC_APP_URL=https://your-domain.com
```

Optional (for AI-powered search):

```env
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
QDRANT_URL=https://...
```

---

## Custom Domain + HTTPS

After deploying, set `NEXTAUTH_URL` and `NEXT_PUBLIC_APP_URL` to your domain.

For Docker, the easiest HTTPS setup is [Caddy](https://caddyserver.com):

```
help.yourdomain.com {
    reverse_proxy localhost:3000
}
```

Caddy auto-provisions SSL certificates.

---

## Contributing

Missing a deployment platform? Open a PR! We'd love examples for:
- Fly.io
- Coolify
- Kamal
- AWS ECS / Fargate
- GCP Cloud Run

## License

MIT
