# Quick Setup Guide - 30 Minutes to Deployment! 🚀

## ⏱️ Timeline
- **5 min**: Account Setup
- **10 min**: Backend Deployment (Heroku)
- **8 min**: Frontend Deployment (Vercel)
- **5 min**: Database Setup & Testing
- **2 min**: Environment Configuration

## 🎯 Step-by-Step

### Phase 1: Account Setup (5 minutes)

#### 1. Vercel Account
```bash
# Visit and sign up (free)
https://vercel.com/signup

# Install Vercel CLI
npm install -g vercel

# Login
vercel login
```

#### 2. Heroku Account
```bash
# Visit and sign up (free trial, then ~$7/month)
https://heroku.com/signup

# Install Heroku CLI
npm install -g heroku

# Login
heroku login
```

#### 3. AWS Account (Optional - for S3)
```bash
# Visit and sign up (free tier)
https://aws.amazon.com/free

# Or use Vercel Blob instead (simpler!)
```

---

### Phase 2: Backend Deployment (10 minutes)

#### Step 1: Create Heroku App

```bash
# Login first
heroku login

# Create your app (change "my-platform" to your app name)
heroku create my-learning-platform-api

# Output will show:
# Creating ⬢ my-learning-platform-api... done
# https://my-learning-platform-api.herokuapp.com/ | https://git.heroku.com/my-learning-platform-api.git
```

**⚠️ Save your app URL: `https://my-learning-platform-api.herokuapp.com`**

#### Step 2: Add PostgreSQL Database

```bash
# Add Heroku Postgres (hobby tier = ~$9/month)
heroku addons:create heroku-postgresql:hobby-dev --app my-learning-platform-api

# Verify
heroku config --app my-learning-platform-api

# You'll see DATABASE_URL automatically set!
```

#### Step 3: Set Environment Variables

```bash
# Set critical env vars
heroku config:set \
  NODE_ENV=production \
  JWT_SECRET=your-jwt-secret-key-change-this \
  --app my-learning-platform-api

# Verify they're set
heroku config --app my-learning-platform-api
```

#### Step 4: Deploy Code

```bash
# In your project root
git remote add heroku https://git.heroku.com/my-learning-platform-api.git

# Deploy (from deployment-setup branch)
git push heroku deployment-setup:main

# Watch the logs
heroku logs --app my-learning-platform-api --tail

# When you see "State changed from starting to up" → Success! ✅
```

#### Step 5: Run Initial Database Setup

```bash
# Create database tables
heroku pg:psql --app my-learning-platform-api < migrations/001_initial_schema.sql

# Verify connection
heroku config:get DATABASE_URL --app my-learning-platform-api
```

#### ✅ Backend Ready!

Test it:
```bash
curl https://my-learning-platform-api.herokuapp.com/health

# Should return:
# {"status":"ok","timestamp":"2026-05-10T12:00:00Z","uptime":3600}
```

---

### Phase 3: Frontend Deployment (8 minutes)

#### Step 1: Create Next.js Project

```bash
# From your project root
npx create-next-app@latest frontend --typescript

# Choose:
# ✓ Would you like to use ESLint? → y
# ✓ Would you like to use Tailwind CSS? → y
# ✓ Would you like your code inside a `src/` directory? → n
```

#### Step 2: Configure API Connection

Edit `frontend/.env.local`:
```
NEXT_PUBLIC_API_URL=https://my-learning-platform-api.herokuapp.com
NEXT_PUBLIC_APP_NAME=Visual Learning Platform
```

#### Step 3: Deploy to Vercel

```bash
# In frontend directory
cd frontend

# Deploy
vercel

# Follow prompts:
# ? Set up and deploy "~/path/to/frontend"? [Y/n] → y
# ? Which scope do you want to deploy to? → Select your personal account
# ? Link to existing project? [y/N] → n
# ? What's your project's name? → visual-learning-platform
# ? In which directory is your code located? → .
# ? Want to modify vercel.json? [y/N] → n
# ? Auto-detected Project Settings for Next.js
# ✓ Deployed to https://visual-learning-platform-<xxx>.vercel.app
```

**⚠️ Save your frontend URL!**

#### ✅ Frontend Ready!

Visit your app:
```
https://visual-learning-platform-<xxx>.vercel.app
```

---

### Phase 4: Database & Testing (5 minutes)

#### Step 1: Verify Backend-Database Connection

```bash
# Check backend can reach database
curl https://my-learning-platform-api.herokuapp.com/health

# Should return healthy status
```

#### Step 2: Test API Endpoints

```bash
# Test health check
curl https://my-learning-platform-api.herokuapp.com/health

# Create a test user (if endpoint exists)
curl -X POST https://my-learning-platform-api.herokuapp.com/api/users \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","email":"test@example.com","password":"testpass123"}'
```

#### Step 3: Update Frontend API URL (if needed)

In `frontend/.env.local`:
```
# Ensure this matches your Heroku URL exactly
NEXT_PUBLIC_API_URL=https://my-learning-platform-api.herokuapp.com
```

Then redeploy:
```bash
cd frontend
vercel --prod
```

#### ✅ Connected!

---

### Phase 5: Environment Configuration (2 minutes)

#### Required Environment Variables

**Backend (Heroku):**
```bash
heroku config --app my-learning-platform-api

# Should show:
# DATABASE_URL: postgres://...
# NODE_ENV: production
# JWT_SECRET: your-secret-key
# API_URL: https://my-learning-platform-api.herokuapp.com
```

**Frontend (Vercel):**
Visit Vercel Dashboard → Project Settings → Environment Variables
```
NEXT_PUBLIC_API_URL=https://my-learning-platform-api.herokuapp.com
```

#### Optional: Add Media Storage

**Option A: Vercel Blob (Easiest)**
1. Go to Vercel Dashboard
2. Storage tab → Create Blob Storage
3. Copy the token to `.env`

**Option B: AWS S3**
1. Create AWS IAM user
2. Get Access Key & Secret
3. Create S3 bucket
4. Add to Heroku config

---

## 📊 Verification Checklist

- [ ] Heroku app created: `heroku apps`
- [ ] PostgreSQL added: `heroku addons --app my-learning-platform-api`
- [ ] Backend deployed: `heroku logs --app my-learning-platform-api`
- [ ] Health check works: `curl https://my-learning-platform-api.herokuapp.com/health`
- [ ] Next.js frontend created: `ls frontend/`
- [ ] Frontend deployed to Vercel: `vercel --prod`
- [ ] Frontend can reach backend (check Network tab in browser DevTools)
- [ ] Environment variables set on both platforms
- [ ] Database migrations ran: `heroku pg:psql --app my-learning-platform-api`

---

## 🔄 Making Changes & Redeploying

### Backend Changes (Heroku)

```bash
# Make your code changes
git add .
git commit -m "Update backend"

# Deploy to Heroku
git push heroku deployment-setup:main

# Watch logs
heroku logs --tail --app my-learning-platform-api
```

### Frontend Changes (Vercel)

```bash
cd frontend

# Make your code changes
git add .
git commit -m "Update frontend"
git push

# Vercel auto-deploys from main branch
# Or manually:
vercel --prod
```

---

## 🚨 Quick Troubleshooting

| Problem | Solution |
|---------|----------|
| **Backend won't start** | `heroku logs --app my-learning-platform-api` - check for errors |
| **Can't connect to database** | `heroku config --app my-learning-platform-api` - verify DATABASE_URL |
| **Frontend shows "Cannot find API"** | Check `NEXT_PUBLIC_API_URL` in `.env.local` |
| **Deployment fails** | Check Node version: `heroku stack:set heroku-22` |
| **503 errors** | Check dyno is sleeping: `heroku logs --dyno=web --app my-learning-platform-api` |

**Detailed troubleshooting:** See DEPLOYMENT_GUIDE.md → Troubleshooting section

---

## 📞 Need Help?

1. Check logs: `heroku logs --tail --app my-learning-platform-api`
2. Read DEPLOYMENT_GUIDE.md for detailed info
3. Visit official docs:
   - Heroku: https://devcenter.heroku.com/
   - Vercel: https://vercel.com/docs
   - Next.js: https://nextjs.org/docs

---

## 🎉 You Did It!

Your visual learning platform is now live at:

```
Frontend: https://visual-learning-platform-<xxx>.vercel.app
Backend:  https://my-learning-platform-api.herokuapp.com
```

### Next Steps:
1. Create courses & upload media
2. Set up user authentication
3. Add CI/CD pipeline (.github/workflows/deploy.yml included)
4. Monitor performance & scale as needed

**Happy teaching! 🎓**
