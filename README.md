# 🎓 Academic Assistant - Complete MERN Stack

A full-featured academic management platform built with MERN (MongoDB, Express, React-like frontend, Node.js). Includes authentication, student/teacher dashboards, timetable management, and notices system.

---

## 📋 Project Structure

```
project-root/
├── client/                       # Frontend (Static + JavaScript)
│   ├── index.html               # Landing page
│   ├── login.html               # Student/Teacher login
│   ├── signup.html              # Registration page
│   ├── student-dashboard.html   # Student dashboard
│   ├── teacher-dashboard.html   # Teacher dashboard
│   ├── app.js                   # All frontend logic & API calls
│   └── style.css                # Complete styling (consolidated)
│
├── server/                       # Backend (Node.js + Express)
│   ├── package.json             # Backend dependencies
│   ├── server.js                # Express server entry
│   ├── .env.example             # Environment variables template
│   ├── models/
│   │   └── Item.js              # Mongoose schemas (User, Notice, Timetable)
│   └── routes/
│       └── items.js             # All API routes (auth, notices, timetables, users)
│
├── package.json                 # Root package (optional - for dev with concurrently)
└── README.md                    # This file
```

---

## 🚀 Quick Start (Local Development)

### Prerequisites
- Node.js (v14+)
- npm
- MongoDB (local or cloud - Atlas)
- Code editor (VS Code recommended)

### Step 1: Clone/Download Project
```bash
cd /path/to/fronted
```

### Step 2: Setup Backend

```bash
cd server
npm install
```

Create `.env` file in the `server` folder:
```env
MONGO_URI=mongodb+srv://YOUR_USERNAME:YOUR_PASSWORD@cluster.mongodb.net/academic-assistant?retryWrites=true&w=majority
PORT=5000
JWT_SECRET=your_super_secret_key_12345
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
```

**Getting MongoDB Connection String:**
1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free account
3. Create a cluster
4. Create a database user
5. Copy connection string and replace YOUR_USERNAME and YOUR_PASSWORD

Start the backend:
```bash
npm run dev
```

Expected output:
```
✅ Connected to MongoDB
🚀 Server running on port 5000
📡 API: http://localhost:5000/api
💚 Health: http://localhost:5000/health
```

### Step 3: Setup Frontend

Option A - Using http-server (Recommended for Development):
```bash
# At project root
npm install

# Start frontend on port 3000
npm run client
```

In another terminal, the backend is already running on 5000.

Open your browser: **http://localhost:3000**

Option B - Open Directly in Browser:
```bash
# Simply open client/index.html in your browser
```

---

## 🔐 Authentication Flow

### User Registration (Signup)
1. User visits `/client/signup.html`
2. Selects role (Student or Teacher)
3. Fills required fields (Name, Email, Password, Phone)
4. For students: Student ID, Section, Semester, Department
5. For teachers: Teacher ID, Department
6. Submits → Backend creates user, hashes password, stores in MongoDB
7. Redirects to login page

### User Login
1. User visits `/client/login.html`
2. Enters Email, Password, and selects role
3. Backend verifies credentials using bcrypt
4. JWT token generated
5. Token & user data stored in localStorage
6. Redirects to respective dashboard

### Dashboard Access
- **Students** → `student-dashboard.html`
- **Teachers** → `teacher-dashboard.html`
- Logout clears localStorage and redirects to home

---

## 📡 API Endpoints

### Authentication
```
POST /api/auth/signup
  Body: { name, email, password, role, phone, studentId?, section?, semester?, department?, teacherId? }
  Response: { token, user }

POST /api/auth/login
  Body: { email, password, role }
  Response: { token, user }
```

### Notices
```
GET /api/notices
  Response: [{ _id, title, message, postedBy, posterName, createdAt }, ...]

POST /api/notices (Auth Required)
  Headers: Authorization: Bearer <token>
  Body: { title, message }
  Response: { _id, title, message, ... }
```

### Timetables
```
GET /api/timetables?section=A1&semester=3
  Response: [{ _id, title, section, semester, fileUrl, ... }, ...]

POST /api/timetables (Auth Required)
  Headers: Authorization: Bearer <token>
  Body: { title, section, semester, fileUrl }
  Response: { _id, title, ... }
```

### Users
```
GET /api/users?role=student
  Response: [{ _id, name, email, role, section, semester, ... }, ...]

GET /api/users/:id
  Response: { _id, name, email, ... }
```

---

## 🧪 Testing with curl

### Signup
```bash
curl -X POST http://localhost:5000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "password": "password123",
    "role": "student",
    "phone": "9876543210",
    "studentId": "BCA001",
    "section": "A1",
    "semester": 3,
    "department": "BCA"
  }'
```

### Login
```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "password123",
    "role": "student"
  }'
```

### Get All Notices
```bash
curl http://localhost:5000/api/notices
```

### Post Notice (with auth token)
```bash
curl -X POST http://localhost:5000/api/notices \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -d '{
    "title": "Exam Schedule",
    "message": "Final exams start from next week"
  }'
```

---

## 🌐 Production Deployment

### Option 1: Deploy on Vercel + Railway

#### Backend on Railway (Free Tier)
1. Go to [railway.app](https://railway.app)
2. Click "New Project"
3. Select "Deploy from GitHub" or "Deploy from Repo"
4. Connect your repository
5. Add environment variables in Railway:
   - `MONGO_URI`: Your MongoDB Atlas connection string
   - `JWT_SECRET`: Your secret key
   - `NODE_ENV`: production
6. Railway auto-deploys on push
7. Your backend URL: `https://your-project-xyz.railway.app`

#### Frontend on Vercel
1. Go to [vercel.com](https://vercel.com)
2. Click "New Project"
3. Import your GitHub repository
4. Select `client` as the "Root Directory"
5. Add environment variables:
   - `REACT_APP_API_URL`: `https://your-project-xyz.railway.app/api`
6. Click "Deploy"
7. Your frontend URL: `https://your-project.vercel.app`

#### Update Frontend API Base
In `client/app.js`, update:
```javascript
const API_BASE = 'https://your-project-xyz.railway.app/api';
```

### Option 2: Deploy on Heroku + Heroku

**Note**: Free tier is deprecated. Use Railway, Render, or Fly.io instead.

### Option 3: Self-hosted (VPS/Cloud Server)

1. Use DigitalOcean, Linode, or AWS EC2
2. SSH into your server
3. Install Node.js & MongoDB
4. Clone your repo
5. Run `npm install` in both `server` and `client`
6. Use PM2 for process management
7. Setup Nginx as reverse proxy
8. Use SSL (Let's Encrypt)

---

## 📁 Frontend Pages

### `index.html` - Landing Page
- Hero section with features
- Role-based CTAs (Student/Teacher)
- Feature cards
- Footer

### `login.html` - Login
- Email, Password, Role selection
- Error/success messages
- Link to signup

### `signup.html` - Registration
- Form with dynamic fields based on role
- Student: ID, Section, Semester, Department
- Teacher: ID, Department
- Validation

### `student-dashboard.html`
**Sidebar Navigation:**
- Profile (View personal details)
- Timetable (View schedule)
- Notices (View all notices)

**Features:**
- View personal profile
- Access timetable for their section/semester
- Read notices from teachers
- Logout

### `teacher-dashboard.html`
**Sidebar Navigation:**
- Profile (View personal details)
- Upload Timetable (Upload new timetable)
- View Timetables (All uploaded timetables)
- Post Notice (Create and post notices)
- View Students (Filter and view registered students)

**Features:**
- Manage timetables
- Post notices to all students
- View and filter students
- Logout

---

## 🎨 Styling

All CSS is consolidated in `client/style.css`:
- Modern color scheme (Indigo primary)
- Responsive design (mobile-first)
- CSS variables for theming
- Smooth animations and transitions
- Light/Dark utilities

### CSS Variables
```css
--primary: #6366f1       /* Indigo */
--secondary: #8b5cf6     /* Purple */
--success: #10b981       /* Green */
--danger: #ef4444        /* Red */
--dark: #1e293b          /* Charcoal */
--light: #f1f5f9         /* Light Gray */
```

---

## 🔒 Security Features

- Password hashing with bcryptjs
- JWT token authentication
- CORS enabled
- Role-based access control
- Input validation
- SQL injection prevention (MongoDB + Mongoose)

---

## 🛠️ Troubleshooting

### "Cannot connect to MongoDB"
- Check MONGO_URI in `.env`
- Verify MongoDB Atlas IP whitelist (allow 0.0.0.0/0 for development)
- Check internet connection

### "CORS Error"
- Ensure backend is running on port 5000
- Frontend on port 3000
- CORS middleware is configured in server.js

### "API not found (404)"
- Check API endpoint URLs in `app.js`
- Verify backend is running
- Check route files in `server/routes/`

### "Token expired"
- Login again to get a new token
- Token stored in localStorage

---

## 📚 Technologies Used

**Frontend:**
- HTML5
- CSS3 (Custom, no frameworks)
- Vanilla JavaScript
- Fetch API

**Backend:**
- Node.js
- Express.js
- MongoDB
- Mongoose ODM
- JWT (Authentication)
- bcryptjs (Password hashing)

---

## 📝 Features Checklist

- ✅ User Authentication (Signup/Login)
- ✅ Role-based Access (Student/Teacher)
- ✅ Student Dashboard
- ✅ Teacher Dashboard
- ✅ Timetable Management
- ✅ Notice System
- ✅ User Management
- ✅ Responsive Design
- ✅ Error Handling
- ✅ JWT Authentication
- ✅ MongoDB Integration
- ✅ CORS Configuration

---

## 🚀 Future Enhancements

- [ ] File upload (PDF/Images)
- [ ] Real-time notifications (Socket.io)
- [ ] Email verification
- [ ] Forgot password functionality
- [ ] Grade management
- [ ] Assignment submission
- [ ] Chat between students and teachers
- [ ] Mobile app (React Native)
- [ ] Admin dashboard
- [ ] Analytics and reporting

---

## 📄 License

MIT - Free to use and modify

---

## 💬 Support

For issues or questions:
1. Check troubleshooting section above
2. Review API documentation
3. Check browser console for errors
4. Check server logs in terminal

---

## 🎉 Ready to Go!

Your complete MERN Academic Assistant platform is ready to deploy!

**Development:** `npm run dev` (from root)
**Production:** Deploy backend to Railway, frontend to Vercel

Happy coding! 🚀
