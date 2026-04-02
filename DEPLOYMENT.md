# Deployment Guide for Anominie

This guide walks you through deploying the Anominie chat application to make it live on the internet.

## Overview

The application has two parts:
1. **Frontend** (HTML/CSS/JS) - Deployed on Vercel
2. **Backend** (Node.js + Socket.IO) - Deployed on Render/Railway/Heroku

## Step 1: Deploy the Backend

### Option A: Deploy to Render.com (Recommended)

1. Go to [render.com](https://render.com) and sign up/login
2. Click "New +" → "Web Service"
3. Connect your GitHub account and select the `Anominie-` repository
4. Configure the service:
   - **Name**: `anominie-backend`
   - **Root Directory**: `backend`
   - **Environment**: `Node`
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Plan**: Free

5. Add Environment Variables:
   - `NODE_ENV` = `production`
   - `PORT` = `3000` (Render will override this automatically)
   - `FRONTEND_URL` = `*` (we'll update this after frontend deployment)

6. Click "Create Web Service"
7. Wait for deployment to complete
8. Copy your backend URL (e.g., `https://anominie-backend.onrender.com`)

### Option B: Deploy to Railway.app

1. Go to [railway.app](https://railway.app) and sign up/login
2. Click "New Project" → "Deploy from GitHub repo"
3. Select the `Anominie-` repository
4. Railway will auto-detect the backend service
5. Go to Settings → Change root directory to `/backend`
6. Add environment variables in the Variables tab:
   - `NODE_ENV` = `production`
   - `FRONTEND_URL` = `*`
7. Deploy and copy the generated URL

## Step 2: Deploy the Frontend

### Deploy to Vercel

1. Install Vercel CLI (if not installed):
   ```bash
   npm install -g vercel
   ```

2. In your project root directory, run:
   ```bash
   vercel
   ```

3. Follow the prompts:
   - **Set up and deploy**: Yes
   - **Which scope**: Your account
   - **Link to existing project**: No
   - **Project name**: `anominie` (or your choice)
   - **Directory**: `./` (current directory)
   - **Override settings**: No

4. Vercel will deploy your frontend and give you a URL (e.g., `https://anominie.vercel.app`)

### Alternative: Deploy via Vercel Dashboard

1. Go to [vercel.com](https://vercel.com) and sign up/login
2. Click "Add New..." → "Project"
3. Import your GitHub repository
4. Configure:
   - **Framework Preset**: Other
   - **Root Directory**: `./`
   - **Build Command**: Leave empty
   - **Output Directory**: `./`
5. Click "Deploy"

## Step 3: Connect Frontend to Backend

After both are deployed, you need to update the frontend to point to your backend:

1. Open `chat_script.js`
2. Update line 4 with your backend URL:
   ```javascript
   const SOCKET_SERVER_URL = window.location.hostname === 'localhost'
       ? 'http://localhost:3000'
       : 'https://your-actual-backend-url.onrender.com'; // Replace with your URL
   ```

3. Commit and push the changes:
   ```bash
   git add chat_script.js
   git commit -m "Update backend URL for production"
   git push
   ```

4. Vercel will automatically redeploy with the new changes

## Step 4: Update Backend CORS Settings

1. Go back to your backend deployment (Render/Railway)
2. Update the `FRONTEND_URL` environment variable:
   - Set it to your Vercel URL: `https://anominie.vercel.app`
3. Redeploy or restart the backend service

## Step 5: Test Your Live Application

1. Open your Vercel URL in a browser
2. Try logging in with email or phone
3. Set up a profile with username and country
4. Open the same URL in another browser/tab (incognito mode)
5. Log in with a different username
6. Send messages between the two sessions
7. Verify:
   - ✅ Online user count updates
   - ✅ Messages appear in real-time
   - ✅ Join/leave notifications work
   - ✅ Typing indicators show up

## Troubleshooting

### Frontend can't connect to backend

**Issue**: "Connection error. Retrying..." message appears

**Solutions**:
1. Check that backend URL in `chat_script.js` is correct
2. Ensure backend is running (visit backend URL directly)
3. Check backend logs for errors
4. Verify CORS settings allow your frontend domain

### Backend deployment fails

**Issue**: Build or start command fails

**Solutions**:
1. Check that `backend/package.json` has correct dependencies
2. Run `npm install` locally in backend folder to verify
3. Check backend logs for specific errors
4. Ensure Node.js version is compatible (v14+)

### Messages not appearing

**Issue**: Can send but not receive messages

**Solutions**:
1. Check browser console for JavaScript errors
2. Verify WebSocket connection is established (check Network tab)
3. Check backend logs for Socket.IO errors
4. Try refreshing the page

### Online count not updating

**Issue**: Shows 0 users or doesn't update

**Solutions**:
1. Verify Socket.IO connection is successful
2. Check that `user_login` event is being emitted
3. Check backend logs for user tracking
4. Ensure `online_count` event is being received

## Monitoring Your Application

### Backend Monitoring (Render)
- Go to your service dashboard
- Check "Logs" tab for real-time logs
- Monitor "Metrics" for CPU/memory usage
- Check "Events" for deployment history

### Frontend Monitoring (Vercel)
- Go to your project dashboard
- Check "Deployments" for deployment status
- View "Analytics" for traffic data
- Check "Logs" for runtime logs

## Updating Your Application

To deploy updates:

1. Make changes to your code
2. Commit and push to GitHub:
   ```bash
   git add .
   git commit -m "Your update message"
   git push
   ```
3. Both Vercel and Render/Railway will auto-deploy the changes

## Custom Domain (Optional)

### For Frontend (Vercel)
1. Go to Project Settings → Domains
2. Add your custom domain
3. Follow DNS configuration instructions
4. Update backend `FRONTEND_URL` to your custom domain

### For Backend (Render)
1. Go to Settings → Custom Domain
2. Add your custom domain (requires paid plan)
3. Update frontend `SOCKET_SERVER_URL` in `chat_script.js`

## Security Recommendations

1. **Use HTTPS**: Both Vercel and Render provide free SSL certificates
2. **Environment Variables**: Never commit `.env` files to Git
3. **CORS**: Set specific frontend URL in production (not `*`)
4. **Rate Limiting**: Consider adding rate limiting to prevent abuse
5. **Input Validation**: Already implemented in the code
6. **Monitoring**: Set up uptime monitoring (e.g., UptimeRobot)

## Cost Considerations

### Free Tier Limits

**Vercel (Frontend)**:
- Unlimited deployments
- 100GB bandwidth/month
- Perfect for this project

**Render (Backend)**:
- 750 hours/month (enough for 24/7)
- Goes to sleep after 15 minutes of inactivity
- Wakes up on first request (may take 30 seconds)

**Railway (Backend)**:
- $5 free credit/month
- Charged per usage
- No sleep mode

### Keeping Backend Active (Free)

If using Render free tier, backend sleeps after inactivity. To keep it active:

1. Use a service like [cron-job.org](https://cron-job.org)
2. Set up a cron job to ping your backend every 10 minutes
3. URL to ping: `https://your-backend.onrender.com/api/stats`

## Support

If you encounter issues:
1. Check the logs on both Vercel and Render/Railway
2. Review this guide's troubleshooting section
3. Open an issue on GitHub
4. Check Socket.IO documentation for WebSocket issues

---

**Congratulations! Your chat application is now live!** 🎉

Share your Vercel URL with friends and start chatting anonymously!
