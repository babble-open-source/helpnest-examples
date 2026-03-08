# HelpNest — Railway Deploy

Deploy HelpNest to Railway in ~3 minutes with managed PostgreSQL and Redis.

## One-click deploy

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/helpnest)

## Manual deploy

### 1. Fork the repo

Fork [helpnest/helpnest](https://github.com/helpnest/helpnest) to your GitHub account.

### 2. Create a Railway project

[railway.app](https://railway.app) → New Project → Deploy from GitHub repo → select your fork.

### 3. Add database services

In your Railway project dashboard:
- Click **+ New** → **Database** → **PostgreSQL**
- Click **+ New** → **Database** → **Redis**

### 4. Set environment variables

In Railway → your web service → **Variables**:

```env
NEXTAUTH_SECRET=<run: openssl rand -base64 32>
NEXTAUTH_URL=https://<your-railway-domain>.up.railway.app
NEXT_PUBLIC_APP_URL=https://<your-railway-domain>.up.railway.app
DATABASE_URL=${{Postgres.DATABASE_URL}}
REDIS_URL=${{Redis.REDIS_URL}}
```

Optional AI features:
```env
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
QDRANT_URL=https://...  # Use Qdrant Cloud free tier
```

### 5. Deploy

Railway auto-builds from the `Dockerfile`. First deploy takes ~3–4 minutes.

### 6. Run migrations

Railway dashboard → your service → **Shell**:

```bash
npx prisma migrate deploy
```

### 7. Custom domain

Railway → Settings → Domains → **Add Custom Domain** → follow DNS instructions.

## Cost estimate

| Resource | Railway plan | ~Cost |
|----------|-------------|-------|
| App (512MB) | Hobby | ~$3/mo |
| PostgreSQL | Hobby | ~$2/mo |
| Redis | Hobby | ~$1/mo |
| **Total** | | **~$6/mo** |

The Hobby plan ($5 credit included) covers small help centers.
