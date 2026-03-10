# HelpNest — Railway Deploy

Deploy HelpNest to Railway in ~3 minutes with managed PostgreSQL and Redis.

## One-click deploy

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/helpnest)

## Manual deploy

### 1. Fork the repo

Fork [helpnest/helpnest](https://github.com/babble-open-source/helpnest) to your GitHub account.

### 2. Create a Railway project

[railway.app](https://railway.app) → New Project → Deploy from GitHub repo → select your fork.

### 3. Add database services

In your Railway project dashboard:
- Click **+ New** → **Database** → **PostgreSQL**
- Click **+ New** → **Database** → **Redis**

### 4. Set environment variables

In Railway → your web service → **Variables**, add the following:

#### Required

```env
# Auth — generate with: openssl rand -base64 32
AUTH_SECRET=<your-secret>

# App URLs — replace with your Railway-assigned domain
NEXTAUTH_URL=https://<your-app>.up.railway.app
NEXT_PUBLIC_APP_URL=https://<your-app>.up.railway.app

# Database + Redis — Railway injects these automatically from linked services
DATABASE_URL=${{Postgres.DATABASE_URL}}
REDIS_URL=${{Redis.REDIS_URL}}

# Admin account created on first db:seed
ADMIN_SEED_EMAIL=admin@example.com
ADMIN_SEED_PASSWORD=<choose-a-strong-password>
```

#### Optional — GitHub OAuth

```env
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=
```

> In your GitHub OAuth app settings, set the callback URL to:
> `https://<your-app>.up.railway.app/api/auth/callback/github`

#### Optional — AI search (semantic / RAG)

Without these, HelpNest falls back to Postgres full-text search.

```env
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
QDRANT_URL=https://...       # Qdrant Cloud free tier works
QDRANT_API_KEY=              # if your Qdrant instance requires auth
```

#### Optional — Image uploads (S3-compatible)

```env
S3_BUCKET=
S3_REGION=us-east-1
S3_ACCESS_KEY=
S3_SECRET_KEY=
```

### 5. Deploy

Railway auto-builds from the `Dockerfile`. First deploy takes ~3–4 minutes.

### 6. Run migrations + seed

Railway dashboard → your service → **Shell**:

```bash
# Apply schema migrations
npx prisma@5 migrate deploy --schema packages/db/prisma/schema.prisma

# Seed the database (creates your account + starter content)
npx prisma@5 db seed --schema packages/db/prisma/schema.prisma
```

> **Tip:** If Railway Shell isn't visible, go to **Deployments** → click the active deployment → **Shell** button (top right).
>
> If running from your local machine instead, expose the DB first:
> Railway → PostgreSQL → **Settings** → **Networking** → **Add Public Networking**, then run:
> ```bash
> export DATABASE_URL="postgresql://postgres:<password>@<public-host>:<port>/railway"
> npx prisma@5 migrate deploy --schema packages/db/prisma/schema.prisma
> npx prisma@5 db seed --schema packages/db/prisma/schema.prisma
> ```

### 6a. First login

After seeding, sign in at `https://<your-app>.up.railway.app/login` using the credentials you set in `ADMIN_SEED_EMAIL` and `ADMIN_SEED_PASSWORD`.

If you did not set those env vars, the seed used the public defaults:

| Field | Default |
|-------|---------|
| Email | `admin@helpnest.cloud` |
| Password | `helpnest` |

> **Security:** The login page shows these defaults and the dashboard displays a banner until you change your password. Go to **Settings → Your profile → Change password** immediately after first login. Password must be at least 12 characters.

### 7. Custom domain

#### Option A — Railway-managed domain (default)

Railway gives you a free `*.up.railway.app` subdomain automatically. No DNS changes needed.

#### Option B — Your own domain (e.g. `help.yourcompany.com`)

1. Railway → your service → **Settings** → **Domains** → **Add Custom Domain**
2. Enter your domain (e.g. `help.yourcompany.com`)
3. Railway shows a CNAME target — add it to your DNS:

   | Type | Name | Value |
   |------|------|-------|
   | CNAME | `help` | `<provided-by-railway>.up.railway.app` |

4. Update your env vars to the new domain:

   ```env
   NEXTAUTH_URL=https://help.yourcompany.com
   NEXT_PUBLIC_APP_URL=https://help.yourcompany.com
   ```

5. Redeploy (Railway → **Deploy** → **Redeploy**). Railway provisions TLS automatically.

#### Option C — Per-workspace custom domain (multi-tenant / cloud)

To serve a specific workspace's help center at a custom root domain (e.g. `support.acme.com` → `acme` workspace):

```env
HELPNEST_CUSTOM_DOMAIN=support.acme.com
HELPNEST_CUSTOM_DOMAIN_SLUG=acme
```

The middleware rewrites `support.acme.com` → `/acme/help` transparently.

---

## Cost estimate

| Resource | Railway plan | ~Cost |
|----------|-------------|-------|
| App (512MB) | Hobby | ~$3/mo |
| PostgreSQL | Hobby | ~$2/mo |
| Redis | Hobby | ~$1/mo |
| **Total** | | **~$6/mo** |

The Hobby plan ($5 credit included) covers small help centers.
