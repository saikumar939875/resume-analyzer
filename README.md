# Smart Resume Analyzer

AI-powered resume analysis tool that matches your resume against job descriptions and provides actionable feedback.

## Features

- 📄 **PDF & DOCX Support** - Upload resumes in either format
- 🎯 **Match Percentage** - AI calculates how well your resume matches the job
- 🔍 **Missing Skills Detection** - Identifies skills from JD not in your resume
- ✏️ **Resume Rewrite Suggestions** - Specific bullet point improvements with reasons
- 🔐 **Magic Link Auth** - Secure passwordless authentication
- 📜 **Analysis History** - Save and revisit past analyses
- 📱 **Responsive UI** - Works on desktop and mobile

## Tech Stack

- **Frontend**: React 18 + TypeScript + Vite + Tailwind CSS
- **Backend**: Node.js + Express + TypeScript
- **Database**: MongoDB
- **AI**: OpenAI GPT-4o-mini
- **Auth**: JWT with magic links (email)
- **File Parsing**: pdf-parse (PDF) + mammoth (DOCX)

## Project Structure

```
resume-analyzer/
├── client/                 # React frontend
│   ├── src/
│   │   ├── components/     # Reusable components
│   │   ├── context/        # React context (Auth)
│   │   ├── pages/          # Page components
│   │   ├── App.tsx
│   │   └── main.tsx
│   └── ...
├── server/                 # Express backend
│   ├── src/
│   │   ├── config/         # Configuration
│   │   ├── db/             # MongoDB connection
│   │   ├── middleware/     # Auth, error handling
│   │   ├── routes/         # API routes
│   │   ├── services/       # Business logic
│   │   └── index.ts        # Entry point
│   └── ...
└── package.json            # Root workspace config
```

## Getting Started

### Prerequisites

- Node.js 18+
- MongoDB (local or Atlas)
- OpenAI API key
- SMTP credentials (for email) - optional for development

### Installation

1. **Clone and install dependencies**
```bash
# From the root directory
npm install

# This installs dependencies for both client and server
```

2. **Configure environment variables**

**Server** (`server/.env`):
```env
PORT=3000
NODE_ENV=development
MONGODB_URI=mongodb://localhost:27017/resume-analyzer
JWT_SECRET=your-super-secret-jwt-key-min-32-chars
OPENAI_API_KEY=sk-your-openai-api-key
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-password
EMAIL_FROM=Resume Analyzer <noreply@resumeanalyzer.app>
CLIENT_URL=http://localhost:5173
```

**Client** (`client/.env`):
```env
VITE_API_URL=http://localhost:3000/api
```

3. **Start MongoDB**
```bash
# If using local MongoDB
mongod

# Or use MongoDB Atlas cloud connection string in MONGODB_URI
```

4. **Run development servers**
```bash
# From root - runs both client and server
npm run dev

# Or separately:
npm run dev:server  # Backend on http://localhost:3000
npm run dev:client  # Frontend on http://localhost:5173
```

### Development Notes

- **Without SMTP**: In development mode without email config, magic links are logged to the server console instead of being emailed
- **Rate Limiting**: API is rate-limited to 100 requests per 15 minutes per IP
- **File Upload**: Max 5MB, PDF/DOCX only

## API Endpoints

### Auth
- `POST /api/auth/magic-link` - Send magic link to email
- `POST /api/auth/verify` - Verify magic link, return JWT
- `GET /api/auth/me` - Get current user (protected)
- `POST /api/auth/logout` - Logout

### Analysis
- `POST /api/analysis` - Analyze resume (multipart: resume file + jobDescription)
- `GET /api/analysis/history` - Get user's analysis history (protected)
- `GET /api/analysis/:id` - Get specific analysis (protected)
- `DELETE /api/analysis/:id` - Delete analysis (protected)

## Hackathon Tips

1. **Demo Flow**: 
   - Sign up with email → Check console for magic link → Sign in
   - Upload a resume PDF → Paste a real job description → Click Analyze
   - Show match score, missing skills, and rewrite suggestions

2. **Impressive Talking Points**:
   - "Real AI analysis, not just keyword matching"
   - "Specific, actionable resume improvements with reasons"
   - "Secure magic-link auth, no passwords stored"
   - "Full history tracking for iterative improvement"

3. **Stretch Goals** (if time permits):
   - Export to PDF report
   - LinkedIn profile import
   - ATS compatibility score
   - Multiple resume versions comparison

## Deployment

### Backend (Railway/Render/Fly.io)
```bash
cd server
npm run build
npm start
```

### Frontend (Vercel/Netlify)
```bash
cd client
npm run build
# Deploy dist/ folder
```

Set environment variables in your hosting platform.

## License

MIT - Built for hackathons