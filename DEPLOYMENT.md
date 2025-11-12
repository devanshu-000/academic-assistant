# 🚀 Deployment Guide - Academic Assistant

## Quick Deploy to Railway + Vercel (5 minutes)

### Part 1: Deploy Backend to Railway

1. **Initialize Git Repository** (if not already done)
   ```bash
   cd /path/to/fronted
   git init
   git add .
   git commit -m "Initial commit: Academic Assistant MERN stack"
   ```

2. **Push to GitHub**
   - Create a new repository on GitHub
   - Push your code
   ```bash
   git remote add origin https://github.com/YOUR_USERNAME/academic-assistant.git
   git branch -M main
   git push -u origin main
   ```

3. **Go to [railway.app](https://railway.app)**
   - Click "New Project"
   - Choose "Deploy from GitHub"
   - Select your repository
   - Select `server` as the "Root Directory"
   - Wait for deployment to complete
   - Copy the deployment URL (e.g., `https://academic-assistant-prod-xyz.railway.app`)

4. **Add Environment Variables in Railway Dashboard:**
   - Go to "Variables" tab
   - Add:
     ```
     MONGO_URI=mongodb+srv://YOUR_USER:YOUR_PASSWORD@cluster.mongodb.net/academic-assistant?retryWrites=true&w=majority
     JWT_SECRET=your_super_secret_key_12345
     NODE_ENV=production
     PORT=3000
     ```

5. **Test Backend:**
   - Visit: `https://your-railway-url.railway.app/health`
   - Should see: `{"status":"ok","message":"Server is running"}`

---

### Part 2: Deploy Frontend to Vercel

1. **Go to [vercel.com](https://vercel.com)**
   - Click "New Project"
   - Select your GitHub repository
   - Choose "client" as "Root Directory"
   - Click "Deploy"
   - Wait for deployment to complete
   - Copy your frontend URL (e.g., `https://academic-assistant.vercel.app`)

2. **Update Frontend API Configuration**
   - In `client/app.js`, find line with `API_BASE`:
   ```javascript
   const API_BASE = 'https://your-railway-url.railway.app/api';
   ```
   - Replace with your actual Railway backend URL

3. **Redeploy Frontend**
   ```bash
   git add client/app.js
   git commit -m "Update API endpoint to production"
   git push
   ```
   - Vercel will auto-redeploy

4. **Test Frontend:**
   - Visit your Vercel URL
   - Click "Get Started"
   - Signup with test account
   - Should redirect to dashboard

---

## Your Working Website Links

After deployment, your site will be live at:

### 🌍 Frontend (Vercel)
```
https://YOUR_VERCEL_URL.vercel.app
```

### 🔗 Backend (Railway)
```
https://YOUR_RAILWAY_URL.railway.app
```

---

## MongoDB Atlas Setup (Important!)

If you haven't created MongoDB connection string yet:

1. Go to [mongodb.com/atlas](https://www.mongodb.com/cloud/atlas)
2. Click "Try Free"
3. Create account
4. Create Organization
5. Create Project (name: "academic-assistant")
6. Create Cluster (select FREE tier)
7. Wait for cluster to deploy (2-5 minutes)
8. Go to "Database Access" → Create database user
9. Go to "Network Access" → Add IP Address (or allow 0.0.0.0/0 for development)
10. Go to "Connect" → Choose "Drivers" → Copy connection string
11. Replace `<username>` and `<password>` with your database user credentials
12. Use this in your `.env` file as `MONGO_URI`

---

## Troubleshooting Deployment

### Backend not connecting to MongoDB
- Check `MONGO_URI` format
- Verify IP whitelist in MongoDB Atlas
- Check if database user password is correct

### Frontend getting 502/503 errors
- Wait 30 seconds after deploying backend
- Check backend logs in Railway dashboard
- Verify `MONGO_URI` is set in Railway

### Login not working
- Clear browser localStorage: `localStorage.clear()` in console
- Check browser DevTools Network tab for failed requests
- Check backend logs in Railway

### API endpoint 404 errors
- Verify API base URL matches your backend URL
- Make sure `/api` path is included in URL
- Test with: `curl https://YOUR_BACKEND/api/notices`

---

## Maintenance & Updates

### To update code after deployment:

1. Make changes locally
2. Test locally with `npm run dev`
3. Push to GitHub:
   ```bash
   git add .
   git commit -m "Update: description of changes"
   git push
   ```
4. Both Vercel and Railway will auto-redeploy

### View Logs

**Vercel:**
- Go to Deployments → Click deployment → Logs

**Railway:**
- Go to Project → Deployments → Click deployment → Logs

---

## Environment Variables Summary

### Backend (.env or Railway Dashboard)
```env
MONGO_URI=mongodb+srv://user:pass@cluster.mongodb.net/academic-assistant?retryWrites=true&w=majority
JWT_SECRET=your_secret_key_min_32_chars
NODE_ENV=production
PORT=3000
FRONTEND_URL=https://your-vercel-url.vercel.app
```

### Frontend (client/app.js)
```javascript
const API_BASE = 'https://your-railway-url.railway.app/api';
```

---

## Performance Tips

1. **Enable Compression** - Already in Express
2. **Use CDN** - Vercel provides free CDN for static files
3. **Database Indexing** - Add indexes to frequently queried fields
4. **Caching** - Add cache headers in Express

---

## Security Checklist

- ✅ JWT for authentication
- ✅ Password hashing with bcryptjs
- ✅ CORS configured
- ✅ Environment variables for secrets
- ✅ Input validation
- ✅ HTTPS (automatic with Railway & Vercel)

**Recommended additions:**
- [ ] Rate limiting
- [ ] Request validation middleware
- [ ] File upload security
- [ ] HTTPS headers

---

## Cost Breakdown

**Free Tier:**
- ✅ Vercel Frontend: FREE
- ✅ Railway Backend: ~$10/month for Web service (first month FREE)
- ✅ MongoDB Atlas: FREE (512MB database)

**Total: ~$10/month** (after first month)

---

## Support & Next Steps

1. **Testing:**
   - Open your Vercel URL
   - Signup as both Student and Teacher
   - Test dashboard features
   - Try creating notices

2. **Customization:**
   - Update branding in `index.html`
   - Modify colors in `style.css`
   - Add more features in `app.js`

3. **Scaling:**
   - Add more routes in `server/routes/items.js`
   - Add more features in frontend
   - Upgrade MongoDB plan as needed

---

**Congratulations! Your Academic Assistant platform is now live on the internet! 🎉**