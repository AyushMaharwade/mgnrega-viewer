# Deploy MGNREGA Viewer to Vercel

This guide will help you deploy the entire MGNREGA Chhattisgarh Data Viewer to Vercel (both frontend and backend together).

## Why Vercel?

- **All-in-One**: Deploy both frontend and backend API in one place
- **Free Tier**: Generous free tier for personal projects
- **Serverless**: Backend runs as serverless functions (no server management)
- **Auto Deploy**: Automatically deploys from GitHub on every push
- **Fast**: Global CDN for frontend, edge functions for backend

## Prerequisites

1. A GitHub account
2. A Vercel account (sign up at https://vercel.com - free)
3. Your DATA_GOV_API_KEY from data.gov.in

## Step 1: Push Code to GitHub

If you haven't already, push this project to GitHub:

```bash
# Initialize git (if not already)
cd MGNREGS
git init

# Add all files
git add .

# Commit
git commit -m "Initial commit - MGNREGA Viewer"

# Create a new repository on GitHub (yourusername/mgnrega-viewer)
# Then link it:
git branch -M main
git remote add origin https://github.com/yourusername/mgnrega-viewer.git
git push -u origin main
```

## Step 2: Deploy to Vercel

### Option A: Deploy via Vercel Dashboard (Recommended)

1. Go to https://vercel.com and sign in with GitHub
2. Click **"Add New"** → **"Project"**
3. **Import** your `mgnrega-viewer` repository
4. Vercel will auto-detect the settings:
   - **Framework Preset**: Vite
   - **Root Directory**: `./MGNREGS` (important!)
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
5. Click **"Deploy"**

### Option B: Deploy via Vercel CLI

```bash
# Install Vercel CLI globally
npm install -g vercel

# Login to Vercel
vercel login

# Navigate to the MGNREGS folder
cd MGNREGS

# Deploy
vercel

# Follow the prompts:
# - Set up and deploy? Yes
# - Which scope? Choose your account
# - Link to existing project? No
# - Project name? mgnrega-viewer
# - Directory? ./ (current directory)
# - Override settings? No
```

## Step 3: Add Environment Variable (API Key)

Your backend needs the DATA_GOV_API_KEY to work.

### Via Vercel Dashboard:

1. Go to your project on Vercel
2. Click **Settings** → **Environment Variables**
3. Add a new variable:
   - **Name**: `DATA_GOV_API_KEY`
   - **Value**: Your actual API key from data.gov.in
   - **Environment**: All (Production, Preview, Development)
4. Click **Save**
5. **Redeploy** the project (Deployments → click "..." → Redeploy)

### Via Vercel CLI:

```bash
# Set the environment variable
vercel env add DATA_GOV_API_KEY

# When prompted:
# - Enter the value: [paste your API key]
# - Expose to: All environments (Production, Preview, Development)
```

## Step 4: Test Your Deployment

Your app will be live at: `https://mgnrega-viewer.vercel.app` (or similar)

### Test the API:

```bash
# Test health endpoint
curl https://mgnrega-viewer.vercel.app/api/ping

# Test data endpoint
curl "https://mgnrega-viewer.vercel.app/api/mgnregs/chhattisgarh/monthly?district=Raipur&month=01&year=2025"
```

### Test the Frontend:

Open `https://mgnrega-viewer.vercel.app` in your browser and try fetching data for any district.

## How It Works

### Project Structure for Vercel:

```
MGNREGS/
├── api/                         # Backend serverless functions
│   ├── ping.js                  # /api/ping endpoint
│   ├── healthz.js               # /api/healthz endpoint
│   └── mgnregs/
│       └── chhattisgarh/
│           └── monthly.js       # /api/mgnregs/chhattisgarh/monthly endpoint
├── index.html                   # Frontend entry point
├── public/                      # Static assets
├── src/                         # React source (unused, using vanilla JS)
├── vercel.json                  # Vercel configuration
└── package.json                 # Dependencies
```

### Routing (vercel.json):

```json
{
  "cleanUrls": true,
  "routes": [
    { "src": "^/api/(.*)$", "dest": "/api/$1" },
    { "src": "^(?!/api/).*", "dest": "/index.html" }
  ]
}
```

This configuration:
- Routes `/api/*` requests to serverless functions
- Routes all other requests to `index.html` (SPA)

## Custom Domain (Optional)

To use your own domain:

1. Go to Project → **Settings** → **Domains**
2. Add your domain (e.g., `mgnrega.yourdomain.com`)
3. Follow Vercel's instructions to update your DNS records
4. SSL certificate is automatically provisioned

## Troubleshooting

### Issue: API returns 500 error

**Solution**: Check environment variables
- Go to Settings → Environment Variables
- Verify `DATA_GOV_API_KEY` is set
- Redeploy after adding/changing variables

### Issue: "No data found" for all districts

**Solution**: 
1. Check API key is valid on data.gov.in
2. View Function Logs in Vercel dashboard
3. Check if the data.gov.in API is accessible

### Issue: Build fails

**Solution**:
```bash
# Test build locally
cd MGNREGS
npm install
npm run build

# If it works locally, push to GitHub again
git push
```

### Issue: CORS errors

Vercel automatically handles CORS for same-origin requests. If you're calling the API from a different domain, update the serverless function to add CORS headers.

## Continuous Deployment

Once set up, Vercel automatically:
- Deploys production from `main` branch
- Creates preview deployments for pull requests
- Provides unique URLs for each deployment

Every time you push to GitHub, Vercel will automatically rebuild and deploy!

```bash
# Make changes
git add .
git commit -m "Update feature"
git push

# Vercel automatically deploys!
```

## Monitoring

View logs and analytics:
- **Logs**: Project → Functions → View logs
- **Analytics**: Project → Analytics
- **Usage**: Account Settings → Usage

## Limits (Free Tier)

- **Bandwidth**: 100 GB/month
- **Serverless Function Execution**: 100 GB-Hours
- **Edge Requests**: Unlimited
- **Build Minutes**: 6000 minutes/month

This is more than enough for personal/small projects!

## Support

- Vercel Docs: https://vercel.com/docs
- Vercel Discord: https://vercel.com/discord
- GitHub Issues: Create an issue in your repository

---

## Quick Reference

```bash
# Deploy to production
vercel --prod

# View logs
vercel logs

# List deployments
vercel ls

# Remove deployment
vercel remove [deployment-url]
```

Your MGNREGA viewer is now live and accessible to anyone! 🎉
