# 🏗️ Complete Architecture & Integration Guide

## System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         INTERNET / BROWSER                          │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                    ┌──────────┴──────────┐
                    │                     │
        ┌───────────▼────────────┐   ┌───▼─────────────────────┐
        │  VERCEL (Frontend)     │   │  RAILWAY (Backend)      │
        │  ✅ Production URL      │   │  ✅ Production API URL  │
        ├────────────────────────┤   ├─────────────────────────┤
        │ client/                │   │ server/                 │
        │ ├─ index.html          │   │ ├─ server.js            │
        │ ├─ login.html          │   │ ├─ models/Item.js       │
        │ ├─ signup.html         │   │ ├─ routes/items.js      │
        │ ├─ student-dash.html   │   │ └─ package.json         │
        │ ├─ teacher-dash.html   │   │                         │
        │ ├─ app.js              │   │ ┌─────────────────────┐ │
        │ └─ style.css           │   │ │ Express Routes:     │ │
        │                        │   │ │ ├─ /api/auth/*      │ │
        │ Fetch API calls ──┐    │   │ │ ├─ /api/notices     │ │
        │ to Backend API    │    │   │ │ ├─ /api/timetables  │ │
        └────────────────────────┘   │ │ └─ /api/users       │ │
                          │          │ │                     │ │
                          │          │ └─────────────────────┘ │
                          │          └────────┬─────────────────┘
                          │                   │
                          │    JWT Auth   Mongoose
                          │    CORS       Validation
                          │
        ┌─────────────────┴──────────────────────┐
        │                                        │
        │   HTTPS Communication (REST API)      │
        │                                        │
        └─────────────────┬──────────────────────┘
                          │
        ┌─────────────────▼──────────────────────┐
        │  MONGODB ATLAS (Database)               │
        │  ✅ Cloud Hosted at: atlas.mongodb.com  │
        ├──────────────────────────────────────────┤
        │ Collections:                             │
        │ ├─ users (Students & Teachers)           │
        │ ├─ notices (Announcements)               │
        │ └─ timetables (Schedule Data)            │
        └──────────────────────────────────────────┘
```

---

## Data Flow Diagram

### Signup/Login Flow
```
User Browser              Vercel Frontend           Railway Backend         MongoDB
    │                           │                        │                    │
    │─── Fill Form ────────────→│                        │                    │
    │                           │                        │                    │
    │                           │─── POST /auth/login ──→│                    │
    │                           │                        │                    │
    │                           │                        │─── Query User ────→│
    │                           │                        │                    │
    │                           │                        │←─── User Data ─────│
    │                           │                        │                    │
    │                           │                        │─── Compare Pwd ────│
    │                           │                        │─── Generate JWT ───│
    │                           │                        │                    │
    │                           │←─── JWT Token ────────│                    │
    │←─────── Store Token ──────│                        │                    │
    │                           │                        │                    │
    │─── Redirect Dashboard ───→│                        │                    │
    │                           │                        │                    │
```

### Fetch Data Flow
```
Student Dashboard         Browser               Backend               Database
    │                       │                     │                      │
    │─ Load Timetable ─────→│                     │                      │
    │  (with Token)         │                     │                      │
    │                       │─ GET /timetables ──→│                      │
    │                       │ (with JWT Auth)     │                      │
    │                       │                     │─ Verify Token ───────│
    │                       │                     │─ Query Timetable ───→│
    │                       │                     │                      │
    │                       │                     │←─ Results ──────────│
    │                       │← Return JSON ───────│                      │
    │←─ Parse JSON ─────────│                     │                      │
    │─ Render in UI ───────→│                     │                      │
    │                       │                     │                      │
```

---

## File Integration Map

### Frontend Integration (app.js)
```
app.js (Main Logic)
│
├─ Authentication Block
│  ├─ POST /api/auth/signup
│  └─ POST /api/auth/login
│
├─ LocalStorage Management
│  ├─ Save token
│  ├─ Save user data
│  └─ Check auth status
│
├─ Student Dashboard Logic
│  ├─ Load profile from localStorage
│  ├─ GET /api/timetables (filtered)
│  └─ GET /api/notices
│
└─ Teacher Dashboard Logic
   ├─ Load profile from localStorage
   ├─ GET /api/timetables (all)
   ├─ GET /api/users (students)
   └─ POST /api/notices
```

### Backend Integration (routes/items.js)
```
routes/items.js (All Endpoints)
│
├─ Middleware
│  └─ auth() - Verify JWT token
│
├─ Authentication Routes
│  ├─ POST /auth/signup
│  │  └─ Hash password → Save to MongoDB
│  └─ POST /auth/login
│     └─ Verify password → Generate JWT
│
├─ Notice Routes
│  ├─ GET /notices → Fetch all
│  └─ POST /notices → Create (auth required)
│
├─ Timetable Routes
│  ├─ GET /timetables → Fetch (with filters)
│  └─ POST /timetables → Upload (auth required)
│
└─ User Routes
   ├─ GET /users → Fetch (with filters)
   └─ GET /users/:id → Fetch single
```

---

## Database Schema Integration

### Collections Relationships
```
┌─────────────────────────────────────────────────────────────────┐
│ users                                                            │
├──────────────────────────────────────────────────────────────────┤
│ _id: ObjectId (Primary Key)                                    │
│ name: String                                                     │
│ email: String (unique)                                          │
│ password: String (hashed by bcryptjs)                          │
│ role: "student" | "teacher"                                    │
│ phone: String                                                    │
│ studentId: String (if student)                                  │
│ section: String (if student)                                    │
│ semester: Number (if student)                                   │
│ teacherId: String (if teacher)                                  │
│ department: String                                               │
│ createdAt: Date                                                  │
└──────────────────────────────────────────────────────────────────┘
                      ▲
                      │ Referenced by
                      │
    ┌─────────────────┴────────────────┐
    │                                  │
┌───┴──────────────────────┐  ┌────────┴──────────────────┐
│ notices                  │  │ timetables               │
├──────────────────────────┤  ├──────────────────────────┤
│ _id: ObjectId            │  │ _id: ObjectId            │
│ title: String            │  │ title: String            │
│ message: String          │  │ section: String          │
│ postedBy: ObjectId (FK)  │  │ semester: Number         │
│ posterName: String       │  │ uploadedBy: ObjectId (FK)│
│ createdAt: Date          │  │ fileUrl: String (URL)    │
└──────────────────────────┘  │ fileName: String         │
                               │ createdAt: Date          │
                               └──────────────────────────┘
```

---

## Request/Response Lifecycle

### Complete Login Request
```
1. USER ACTION
   └─ Click "Sign In" button on login.html

2. FRONTEND (client/app.js)
   └─ Validate form inputs
   └─ Create request body: { email, password, role }
   └─ Call: fetch('/api/auth/login', POST)
   └─ Set header: Content-Type: application/json

3. NETWORK
   └─ Browser sends HTTPS request
   └─ Route through internet to Railway server

4. BACKEND (server/routes/items.js)
   └─ Express receives POST /api/auth/login
   └─ Extract body data
   └─ Query MongoDB: Find user by email
   └─ Validate password with bcryptjs
   └─ If valid: Generate JWT token
   └─ Return: { token, user: {...} }

5. DATABASE (MongoDB)
   └─ Find user document in 'users' collection
   └─ Return user data

6. RESPONSE
   └─ Backend sends JSON response
   └─ Response travels back through internet

7. FRONTEND (client/app.js)
   └─ Receive response
   └─ Parse JSON
   └─ localStorage.setItem('authToken', token)
   └─ localStorage.setItem('user', userData)
   └─ window.location.href = '/dashboard.html'

8. USER SEES
   └─ Dashboard page loads
   └─ Sidebar shows user name
   └─ Fetch dashboard data using JWT token
```

---

## Authentication Token Flow

```
┌─────────────────────────────────────────────────┐
│ Login Request                                   │
│ { email, password, role }                       │
└──────────────┬──────────────────────────────────┘
               │
               ▼
        ┌─────────────────┐
        │ Verify Email    │
        │ & Password      │
        └────────┬────────┘
                 │
        ┌────────▼────────┐
        │ Generate JWT    │
        │ header.payload  │
        │ .signature      │
        └────────┬────────┘
                 │
        ┌────────▼────────────────────────┐
        │ Return JWT + User Data          │
        │ {                               │
        │   token: "eyJhbGc...",          │
        │   user: { id, name, role, ... } │
        │ }                               │
        └────────┬────────────────────────┘
                 │
        ┌────────▼────────────────────────┐
        │ Store in localStorage            │
        │ authToken = "eyJhbGc..."        │
        │ user = { ... }                   │
        └────────┬────────────────────────┘
                 │
        ┌────────▼────────────────────────┐
        │ Use Token in Future Requests    │
        │ headers: {                      │
        │   'Authorization': 'Bearer ...' │
        │ }                               │
        └────────┬────────────────────────┘
                 │
        ┌────────▼────────────────────────┐
        │ Backend Verifies Token          │
        │ Middleware: auth()              │
        │ jwt.verify(token, SECRET)       │
        └────────┬────────────────────────┘
                 │
        ┌────────▼────────────────────────┐
        │ Valid? → Allow Request          │
        │ Invalid? → 401 Unauthorized     │
        └────────────────────────────────┘
```

---

## Complete Request Flow Example

### Create a Notice (Teacher Action)
```
1. TEACHER UI
   ├─ Sees "Post Notice" form
   ├─ Enters: title="Exam Update", message="Final exam..."
   └─ Clicks "Post Notice"

2. CLIENT (app.js - event listener)
   ├─ Get authToken from localStorage
   ├─ Get user from localStorage
   └─ Call: fetch('/api/notices', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': 'Bearer ' + authToken
        },
        body: JSON.stringify({
          title: 'Exam Update',
          message: 'Final exam...'
        })
      })

3. NETWORK
   └─ HTTPS POST request to Railway backend

4. BACKEND (Express)
   ├─ Receives request at POST /api/notices
   ├─ Middleware: auth() runs
   │  ├─ Extracts token from headers
   │  ├─ Verifies with jwt.verify()
   │  ├─ Gets userId from token
   │  └─ req.userId = userId
   ├─ Extract body: { title, message }
   ├─ Query MongoDB: User.findById(req.userId)
   ├─ Create Notice document: {
   │    title: 'Exam Update',
   │    message: 'Final exam...',
   │    postedBy: userId,
   │    posterName: 'Dr. Smith'
   │  }
   └─ Save to MongoDB

5. DATABASE (MongoDB)
   ├─ Insert document into 'notices' collection
   └─ Return saved document with _id

6. RESPONSE
   ├─ Backend returns: { _id, title, message, ... }
   └─ Status: 201 Created

7. CLIENT
   ├─ Parse response JSON
   ├─ Show: "Notice posted successfully!"
   ├─ Clear form fields
   └─ Refresh notice list: loadNotices()

8. STUDENTS SEE
   ├─ New notice appears in their "Notices" section
   ├─ Notice shows: title, message, teacher name, date
   └─ Updated automatically (when they refresh)
```

---

## Deployment Integration

### Local Development
```
Your Computer
├─ Terminal 1: npm run dev (in server/)
│  └─ Runs on http://localhost:5000
│     ├─ Models loaded (MongoDB local or Atlas)
│     └─ Routes listening for requests
│
└─ Terminal 2: http-server ./client -p 3000
   └─ Serves frontend on http://localhost:3000
      ├─ app.js uses API_BASE = 'http://localhost:5000'
      └─ Fetch calls go to local backend
```

### Production Deployment
```
GitHub Repository
├─ server/ branch → Railway
│  ├─ npm install
│  ├─ npm run dev
│  ├─ Environment: MONGO_URI, JWT_SECRET
│  └─ Public URL: https://your-backend.railway.app
│
└─ client/ folder → Vercel
   ├─ Deploys static files
   ├─ app.js updated: API_BASE = 'https://your-backend.railway.app'
   └─ Public URL: https://your-frontend.vercel.app
```

---

## Key Integration Points

### 1. Frontend to Backend Communication
```javascript
// In client/app.js
const token = localStorage.getItem('authToken');
const res = await fetch('/api/endpoint', {
  headers: { 'Authorization': `Bearer ${token}` }
});
```

### 2. Backend Authentication
```javascript
// In server/routes/items.js
const auth = (req, res, next) => {
  const token = req.header('Authorization')?.replace('Bearer ', '');
  const decoded = jwt.verify(token, JWT_SECRET);
  req.userId = decoded.id;
  next();
};
```

### 3. Database Operations
```javascript
// In server/models/Item.js
UserSchema.pre('save', async function(next) {
  this.password = await bcrypt.hash(this.password, 10);
  next();
});

// In server/routes/items.js
await User.findOne({ email });
await Notice.find().sort({ createdAt: -1 });
```

### 4. Frontend Data Display
```javascript
// In client/app.js
const res = await fetch('/api/notices');
const notices = await res.json();
// Render in HTML from localStorage user data
```

---

## Error Handling Flow

```
User Action
    │
    ▼
Frontend Validation
    │
    ├─ Valid? → Continue
    └─ Invalid? → Show error message
    │
    ▼
Fetch Request to API
    │
    ├─ Network error? → Catch error
    │  └─ Show: "Network error. Try again."
    │
    ▼
Backend Receives Request
    │
    ├─ No token? → Return 401
    ├─ Invalid token? → Return 401
    ├─ Missing fields? → Return 400
    ├─ Database error? → Return 500
    │
    ▼
Frontend Handles Response
    │
    ├─ Success (200-201)? → Update UI
    ├─ Client error (400-404)? → Show message
    ├─ Server error (500)? → Show message
    │
    ▼
User Sees Result or Error
```

---

This architecture ensures:
✅ Secure authentication
✅ Data integrity
✅ Scalability
✅ Separation of concerns
✅ Production readiness