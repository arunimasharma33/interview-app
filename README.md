# InterviewMaster 🚀
### AI-Powered Interview Preparation & Resume Tailoring Platform

InterviewMaster is a full-stack web application designed to help candidates prepare for job interviews. By analyzing job descriptions (JDs) and candidate profiles (resumes or self-descriptions), the platform leverages Google's Gemini 2.5 Flash model to generate personalized preparation strategies. This includes match scoring, highlighting key skill gaps, creating a day-by-day roadmap, compiling tailored question banks (both technical and behavioral), and rendering a custom, downloadable resume PDF.

---

## 🌟 Key Features

1. **AI-Driven Mock Preparation Strategy**
   - **Match Score**: Analyzes how well your profile aligns with a specific job description.
   - **Skill Gap Analysis**: Identifies missing skills and rates their impact severity (`low`, `medium`, `high`).
   - **Day-by-Day Roadmaps**: Tailors a day-by-day prep plan, providing targeted daily tasks.

2. **Custom Question Banks**
   - **Technical Questions**: Generates direct technical interview questions with details on *why* they are asked (the interviewer's intent) and *how* to answer them (model answers).
   - **Behavioral Questions**: Prepares behavioral prompts complete with detailed guidelines and key points to hit in your response.

3. **ATS-Friendly Resume Tailoring**
   - Automatically drafts and styles a customized resume based on the target job requirements.
   - Uses Puppeteer on the backend to render the responsive HTML content into a clean, downloadable A4 PDF.

4. **Secure Authentication & Management**
   - Complete registration and login system with JWT tokens.
   - Token blacklisting on logout for enhanced session security.
   - Persistent history of previous preparation plans.

---

## 🛠️ Technology Stack

### Frontend
- **Framework**: React 19 (via Vite)
- **Routing**: React Router v7
- **State Management**: React Context API
- **Styling**: Vanilla CSS & Sass (SCSS)
- **HTTP Client**: Axios

### Backend
- **Runtime**: Node.js & Express
- **Database**: MongoDB & Mongoose
- **AI Core**: Google GenAI SDK (`@google/genai`) using the `gemini-2.5-flash` model
- **Schema Validation**: Zod & `zod-to-json-schema` (enforces strict structure parsing from AI)
- **File Parsing & Rendering**: `pdf-parse` (extracts text from uploaded PDF resumes) and `Puppeteer` (renders custom HTML resumes to high-quality PDFs)
- **Security & Session**: JSON Web Tokens (JWT), `bcryptjs`, and HTTP-only cookies

---

## 📐 Architecture & Project Structure

The project is structured into a frontend client and a backend Express server, maintaining a clear separation of concerns.

### Project Layout

```text
resume_app/
├── Backend/                 # Express backend server
│   ├── src/
│   │   ├── config/          # DB connection configuration
│   │   ├── controllers/     # Controller handlers (auth, interview reports)
│   │   ├── middlewares/     # Middlewares (auth verification, multer file uploads)
│   │   ├── models/          # Mongoose database models (User, Report, Blacklist)
│   │   ├── routes/          # Express route definitions
│   │   ├── services/        # AI orchestration and PDF generation
│   │   └── app.js           # App initialization
│   ├── server.js            # Server entry point
│   └── .env                 # Environment secrets template
├── Frontend/                # Vite + React frontend client
│   ├── src/
│   │   ├── features/        # Feature-driven module breakdown
│   │   │   ├── auth/        # Auth pages, components, services, context
│   │   │   └── interview/   # Home, Reports, detailed boards, custom hooks
│   │   ├── style/           # Global styles and layout sheets
│   │   ├── App.jsx          # React app wrapper
│   │   ├── app.routes.jsx   # Route registry
│   │   └── main.jsx         # React DOM mount point
│   ├── package.json
│   └── vite.config.js
└── package.json             # Workspace dependencies
```

### 4-Layer Frontend Architecture
As outlined in the architecture plan, the React app uses a clean, separation-of-concerns layout:
1. **UI Layer** (`features/*/pages`, `components`): Contains visual layout and handles UI states.
2. **Hook Layer** (`features/*/hooks`): Manages component-level operations, connects services to React context, and prevents direct API calls inside visual components.
3. **State Layer** (`features/*/*.context.jsx`): Centralized store context for global session user state and active interview plan states.
4. **API Layer** (`features/*/services`): Direct HTTP client integrations utilizing Axios instance request/response handlers.

---

## ⚙️ Configuration & Setup

### Prerequisites
- [Node.js](https://nodejs.org/) (v18+ recommended)
- [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) (or a local MongoDB instance running)
- [Google Gemini API Key](https://aistudio.google.com/)

### 1. Clone & Install Workspace
Navigate to the project root and install workspace dependencies:
```bash
npm install
```

### 2. Backend Environment Configuration
Navigate to the `Backend` directory and configure the environment variables:
```bash
cd Backend
```
Create a `.env` file (or edit the existing one) with the following structure:
```env
PORT=3000
MONGO_URI=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/<database_name>
JWT_SECRET=your_jwt_signing_secret_here
GOOGLE_GENAI_API_KEY=your_gemini_api_key_here
```

Install backend dependencies:
```bash
npm install
```

### 3. Frontend Installation
Navigate to the `Frontend` directory:
```bash
cd ../Frontend
npm install
```

---

## 🚀 Running the Application

For a smooth local development workflow, run both servers concurrently:

### Run Backend
In the `Backend` directory:
```bash
npm run dev
```
The server will start running on `http://localhost:3000` (monitored with Nodemon).

### Run Frontend
In the `Frontend` directory:
```bash
npm run dev
```
The client app will launch at `http://localhost:5173`. Open this URL in your web browser.

---

## 📡 API Reference

### 🔐 Authentication (`/api/auth`)

| Method | Endpoint | Access | Description |
|:---|:---|:---|:---|
| **POST** | `/api/auth/register` | Public | Register a new user profile. Expects `username`, `email`, `password`. |
| **POST** | `/api/auth/login` | Public | Authenticates credentials. Returns user detail and sets HTTP-only `token` cookie. |
| **GET** | `/api/auth/logout` | Public | Clears token cookie and registers token to blacklist database. |
| **GET** | `/api/auth/get-me` | Private | Fetches profile metadata of the currently logged-in user. |

### 💼 Interview Prep & Reports (`/api/interview`)

| Method | Endpoint | Access | Description |
|:---|:---|:---|:---|
| **POST** | `/api/interview/` | Private | Analyzes input to generate a report. Accepts form-data with `jobDescription`, `title`, `selfDescription`, and `resume` (PDF file). |
| **GET** | `/api/interview/` | Private | Fetches summary list of all reports previously generated by the logged-in user. |
| **GET** | `/api/interview/report/:interviewId` | Private | Fetches full, structured details of a specific report. |
| **POST** | `/api/interview/resume/pdf/:interviewReportId` | Private | Tailors a resume for the target job description and returns a downloadable PDF binary buffer. |
