# HelpNest — Vercel Deploy

Deploy HelpNest's Next.js app to Vercel with a managed database.

> **Note:** Vercel is serverless — you need an external PostgreSQL. [Neon](https://neon.tech) and [Supabase](https://supabase.com) both have free tiers. For AI search, use [Qdrant Cloud](https://cloud.qdrant.io) (free tier available).

## One-click deploy

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/babble-open-source/helpnest&project-name=helpnest&repository-name=helpnest)

## Manual deploy

### 1. Set up a database

**Neon (recommended)**
1. [neon.tech](https://neon.tech) → Create project
2. Copy the connection string from the dashboard

**Supabase**
1. [supabase.com](https://supabase.com) → New project
2. Settings → Database → copy the URI (use the pooled connection string)

### 2. Deploy

```bash
npm i -g vercel
git clone https://github.com/babble-open-source/helpnest.git
cd helpnest
vercel --cwd apps/web
```

### 3. Set environment variables

Vercel dashboard → your project → **Settings → Environment Variables**:

```env
DATABASE_URL=postgresql://...
NEXTAUTH_SECRET=<openssl rand -base64 32>
NEXTAUTH_URL=https://your-project.vercel.app
NEXT_PUBLIC_APP_URL=https://your-project.vercel.app
```

### 4. Run migrations

```bash
DATABASE_URL="your-db-url" npx prisma migrate deploy
```

### 5. Custom domain

Vercel → Settings → Domains → add your domain → update DNS.

Then update your env vars:
```env
NEXTAUTH_URL=https://help.yourdomain.com
NEXT_PUBLIC_APP_URL=https://help.yourdomain.com
```

## Limitations on Vercel

| Limitation | Workaround |
|-----------|------------|
| No persistent disk | Use S3/R2 for image uploads |
| Serverless cold starts | Upgrade to Vercel Pro or use Railway |
| No Qdrant locally | Use [Qdrant Cloud](https://cloud.qdrant.io) free tier |
| 60s function timeout | Increase in `vercel.json` or use streaming |
