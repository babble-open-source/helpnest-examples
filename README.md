# HelpNest Examples

Deploy examples for [HelpNest](https://github.com/babble-open-source/helpnest) — the open-source AI-first customer support platform.

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

See [docker/README.md](./docker/README.md) for full details including HTTPS setup, backups, and enabling the AI agent.

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
# Required
DATABASE_URL=postgresql://...
AUTH_SECRET=<run: openssl rand -base64 32>
NEXTAUTH_URL=https://your-domain.com
NEXT_PUBLIC_APP_URL=https://your-domain.com

# For AI-powered search (semantic / vector search)
OPENAI_API_KEY=sk-...          # article embeddings
QDRANT_URL=https://...         # vector database

# Redis — optional, used for rate limiting (degrades gracefully without it)
REDIS_URL=redis://...
```

> **AI chat provider keys** (Anthropic Claude, OpenAI GPT, Google Gemini, Mistral) are **not** environment variables. They are configured per-workspace inside the dashboard after deployment: **Dashboard → Settings → AI Agent**.

---

## Enabling the AI Agent

After your first deployment:

1. **Log in** to your dashboard at `https://your-domain.com/dashboard`
2. Go to **Settings → AI Agent**
3. Toggle **Enable AI Agent** on
4. Choose your **AI Provider** (Anthropic, OpenAI, Google, or Mistral)
5. Select a **Model** (e.g. `claude-sonnet-4-5`, `gpt-4o`, `gemini-2.0-flash`)
6. Paste your **API Key** for that provider
7. Optionally set a custom **Greeting** and **AI Instructions**
8. Click **Save**

The AI agent is now active. Your embedded widget will use conversational AI mode by default.

### Verify it's working

Open your help center at `https://your-domain.com/{workspace}/help`, click the widget, and ask a question. You should see a streaming AI response with source articles.

---

## Embedding the Widget

After deploying, add this to any page on your site:

```html
<script
  src="https://your-domain.com/api/widget.js"
  data-workspace="your-workspace-slug"
  async
></script>
```

The widget runs in **chat mode** by default — customers get conversational AI answers, with a "Talk to a human" button that escalates to your inbox.

---

## Custom Domain + HTTPS

After deploying, set `NEXTAUTH_URL` and `NEXT_PUBLIC_APP_URL` to your custom domain.

For Docker, the simplest HTTPS setup is [Caddy](https://caddyserver.com):

```
help.yourdomain.com {
    reverse_proxy localhost:3000
}
```

Caddy auto-provisions SSL certificates via Let's Encrypt.

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
