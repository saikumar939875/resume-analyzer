# Setup Instructions for Smart Resume Analyzer

Since npm registry access may be restricted in this environment, here's how to set up the project locally:

## Quick Setup (Recommended)

### Step 1: Install Dependencies

```bash
cd /path/to/resume-analyzer

# Install root dependencies first
npm install

# Install client dependencies
cd client
npm install
cd ..

# Install server dependencies
cd server
npm install
cd ..
```

If npm registry is blocked, you can manually install each dependency:

```bash
# Root
npm install concurrently@latest

# Client
cd client
npm install react@18 react-dom@18 react-router-dom@6 lucide-react@0.408.0 react-hot-toast@2.4.1 react-dropzone@14
npm install --save-dev vite@5 tailwindcss@3 @tailwindcss/forms autoprefixer postcss @vitejs/plugin-react typescript

# Server
cd server
npm install express cors mongodb dotenv openai multer mammoth jsonwebtoken nodemailer express-rate-limit zod
npm install --save-dev @types/express @types/node @types/multer @types/jsonwebtoken typescript tsx @types/cors nodemon
```

### Step 2: Configure Environment Variables

**Server Configuration** (`server/.env`):
```env
# Basic
PORT=3000
NODE_ENV=development
JWT_SECRET=your-64-character-secret-key-change-me-in-production-now-please-do-it-very-long

# MongoDB - Get from https://cloud.mongodb.com or run locally
MONGODB_URI=mongodb://localhost:27017/resume-analyzer

# OpenAI API Key - Get from https://platform.openai.com/api-keys
OPENAI_API_KEY=sk-your-openai-key-here

# Optional: Email (for magic links)
# SMTP_HOST=smtp.gmail.com
# SMTP_PORT=587
# SMTP_USER=your-email@gmail.com
# SMTP_PASS=your-app-password
# EMAIL_FROM=Resume Analyzer <noreply@resumeanalyzer.app>

# Client URL
CLIENT_URL=http://localhost:5173
```

**Client Configuration** (`client/.env`):
```env
VITE_API_URL=http://localhost:3000/api
```

### Step 3: Get a MongoDB Instance

Option A: Local MongoDB
```bash
# On Windows with WSL or install MongoDB directly
mongod --port 27017 --dbpath ./data
```

Option B: MongoDB Atlas (cloud)
1. Go to https://cloud.mongodb.com
2. Create a free cluster
3. Create a database user
4. Get the connection string: `mongodb+srv://username:password@cluster.mongodb.net/resume-analyzer`

### Step 4: Get an OpenAI API Key

1. Go to https://platform.openai.com/api-keys
2. Click "Create new secret key"
3. Copy the key and add it to your `.env` file

### Step 5: Start the Project

```bash
# From the root directory
npm run dev
```

This starts both:
- **Backend**: http://localhost:3000
- **Frontend**: http://localhost:5173

## Testing the App

### Without Email (Development Mode)
When you sign up with an email in development mode without SMTP config:
1. Check your terminal/console for the magic link
2. Copy the link and open in your browser
3. Magic link format: `http://localhost:5173/auth/verify?token=abc123...`

### Sample Job Description to Test
Try this job description for a frontend developer:

```markdown
Job Title: Senior Frontend Developer

Requirements:
- 3+ years experience with React and TypeScript
- Experience with Next.js or similar frameworks
- Proficient with state management (Redux, Zustand, Context API)
- Experience with CSS-in-JS (styled-components, Emotion) or Tailwind CSS
- Familiarity with testing libraries (Jest, React Testing Library)
- Experience with REST APIs and GraphQL
- Knowledge of build tools (Webpack, Vite)
- Git version control

Responsibilities:
- Build reusable UI components
- Implement responsive, accessible web pages
- Collaborate with UX designers
- Write unit and integration tests
- Optimize application performance
- Participate in code reviews

Nice to have:
- Experience with mobile development (React Native, Flutter)
- Knowledge of web performance optimization
- Understanding of accessibility standards (WCAG)
```

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    Frontend (Client)                        │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │  React + Vite + TypeScript + Tailwind CSS              │ │
│  │  - Auth Context (JWT handling)                         │ │
│  │  - React Router v6                                     │ │
│  │  - Components: Layout, ProtectedRoute, Analyzer        │ │
│  │  - Pages: Landing, Login, Analyzer, History, AuthCallback│ │
│  └─────────────────────────────────────────────────────────┘ │
│                           │                                  │
│                           │ HTTP API                          │
│                           ▼                                  │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                 Backend (Server)                          │ │
│  │  ┌─────────────────────────────────────────────────────┐  │ │
│  │  │  Express + TypeScript                               │  │ │
│  │  │  - Auth middleware (JWT verification)             │  │ │
│  │  │  - File upload (multer)                             │  │ │
│  │  │  - Rate limiting                                    │  │ │
│  │  │  - Error handling                                   │  │ │
│  │  └─────────────────────────────────────────────────────┘  │ │
│  │                           │                              │ │
│  │                  ┌────────┴────────┐                     │ │
│  │                  ▼                 ▼                     │ │
│  │  ┌──────────────────────┐ ┌──────────────────────┐    │ │
│  │  │    OpenAI Service    │ │ MongoDB Service        │    │ │
│  │  │  - Resume analysis   │ │  - Users               │    │ │
│  │  │  - JSON output       │ │  - Analyses (history)  │    │ │
│  │  │  - Structured prompt │ │  - Magic links         │    │ │
│  │  └──────────────────────┘ │  - TTL for expired links│    │ │
│  │                           └──────────────────────┘    │ │
│  │  ┌─────────────────────────────────────────────────────┐  │ │
│  │  │         Services Layer                              │  │ │
│  │  │  - fileParser.ts (PDF + DOCX parsing)             │  │ │
│  │  │  - openai.ts (AI analysis)                        │  │ │
│  │  │  - email.ts (SMTP integration)                    │  │ │
│  │  │  - mongodb.ts (DB connection)                       │  │ │
│  │  └─────────────────────────────────────────────────────┘  │ │
│  └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

## Production Deployment

### Backend (Railway/Render/Vercel)
```bash
# Build and start
cd server
npm run build
npm run start
```
Set environment variables in the hosting platform.

### Frontend (Vercel/Netlify)
```bash
cd client
npm run build
# Deploy the `dist` folder
```

Make sure `VITE_API_URL` points to your deployed backend.