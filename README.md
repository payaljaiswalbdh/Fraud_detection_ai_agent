# 🛡️ AI Agent for Fraud Detection in Financial Transactions

A full-stack, production-ready fraud detection platform that combines a
**Machine Learning microservice** (Isolation Forest anomaly detection) with a
**Generative AI reasoning agent** (Google Gemini) to detect, explain, and
recommend actions for suspicious financial transactions in real time.

---

## ✨ Features

- 🔐 JWT authentication (register / login / protected routes / role-based access)
- 📊 Modern dark-mode SaaS dashboard with live stats, fraud trend, risk score
  distribution, and fraud vs. safe pie chart (Recharts)
- 💳 Transaction submission form that runs each transaction through:
  1. **ML Service** (Python/Flask + Scikit-learn Isolation Forest) → `Fraud` / `Normal` + probability
  2. **AI Agent** (Gemini API) → risk score (0-100), plain-English explanation,
     recommended action (`Approve` / `Require OTP Verification` / `Temporarily
     Hold` / `Block Transaction`), and prevention tips
- 🚨 Real-time toast notifications + red highlighting for fraudulent transactions
- 🗂️ Admin panel: view/filter/search/sort/paginate/delete all transactions,
  monthly fraud reports, PDF export
- 📥 CSV bulk upload (each row runs through the full fraud pipeline)
- 📧 Automated email fraud alerts (Nodemailer)
- 🧱 Clean MVC architecture, reusable React components, centralized error handling

---

## 🧰 Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React (Vite), Tailwind CSS, React Router, Axios, Recharts, React Hot Toast, Framer Motion |
| Backend | Node.js, Express, MongoDB + Mongoose, JWT, bcrypt, Multer, Nodemailer, PDFKit |
| ML Service | Python, Flask, Scikit-learn (Isolation Forest), Pandas, NumPy, Joblib |
| AI | Google Gemini API |
| Deployment | Vercel (frontend), Render (backend + ML service), MongoDB Atlas |

---

## 📁 Folder Structure

```
fraud-detection-ai-agent/
├── client/                 # React + Vite frontend
│   ├── src/
│   │   ├── api/             # Axios instance
│   │   ├── components/      # Reusable UI components (charts, table, sidebar...)
│   │   ├── context/         # Auth context
│   │   └── pages/           # Route-level pages
│   └── ...
├── server/                 # Node.js + Express backend (MVC)
│   ├── config/               # DB connection
│   ├── controllers/          # Route handlers
│   ├── middleware/           # auth, roleCheck, errorHandler
│   ├── models/                # Mongoose schemas
│   ├── routes/                # Express routers
│   ├── services/              # mlService, aiAgentService, emailService
│   └── server.js
├── ml-service/              # Python Flask ML microservice
│   ├── model/
│   │   ├── preprocess.py     # Feature engineering
│   │   ├── train_model.py    # Isolation Forest training script
│   │   └── fraud_model.pkl   # Trained model (generated)
│   └── app.py
├── docs/
│   └── API.md               # Full API reference
└── README.md
```

---

## ⚙️ Environment Variables

Copy each `.env.example` to `.env` and fill in your values.

### `server/.env`
```
PORT=5000
NODE_ENV=development
MONGO_URI=mongodb+srv://<user>:<pass>@cluster0.mongodb.net/fraud_detection
JWT_SECRET=replace_this_with_a_long_random_secret
JWT_EXPIRES_IN=7d
ML_SERVICE_URL=http://localhost:5001
GEMINI_API_KEY=your_gemini_api_key_here
GEMINI_MODEL=gemini-1.5-flash
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=your_email@gmail.com
EMAIL_PASS=your_app_password
EMAIL_FROM="Fraud Detection AI <alerts@frauddetect.ai>"
ALERT_RECEIVER_EMAIL=admin@example.com
CLIENT_URL=http://localhost:5173
```

### `client/.env`
```
VITE_API_BASE_URL=http://localhost:5000/api
```

### `ml-service/.env`
```
PORT=5001
```

> **Note:** If `GEMINI_API_KEY` is not set, the AI agent automatically falls
> back to a deterministic rule-based analysis so the app remains fully
> functional for local development/demos.

---

# 🏗️ System Architecture

The platform follows a **microservices-based architecture** where the MERN backend manages authentication, business logic, and database operations, while a dedicated Python ML service performs fraud detection. A Gemini-powered AI agent analyzes ML predictions to generate explainable insights and recommendations.

```text
                        +----------------------+
                        |    React Frontend    |
                        | Dashboard & Charts   |
                        +----------+-----------+
                                   |
                               REST API
                                   |
                                   ▼
                     +-------------------------+
                     |  Node.js + Express API  |
                     +-----------+-------------+
                                 |
           +---------------------+----------------------+
           |                                            |
           ▼                                            ▼
    MongoDB Database                           Python ML Service
 (Users, Transactions, Alerts)         (Isolation Forest Model)
           |                                            |
           |                Fraud / Safe + Confidence Score
           |                                            |
           +---------------------+----------------------+
                                 |
                                 ▼
                  Gemini AI Agent (Reasoning Layer)
                                 |
        +------------------------+-------------------------+
        |                        |                         |
        ▼                        ▼                         ▼
 Fraud Explanation        Risk Score (0–100)      Recommended Action
                                                  (Approve / OTP /
                                                       Block)
                                 |
                                 ▼
                    Save Results to MongoDB
                                 |
                                 ▼
             Dashboard • Reports • Email Alerts • Analytics
```

---

# 🔄 Fraud Detection Workflow

```text
User
 │
 ▼
React Frontend
 │
 ▼
Node.js Backend
 │
 ├── Validate Transaction
 ├── Store Transaction
 │
 ▼
Python ML Service
(Isolation Forest)
 │
 ▼
Prediction
(Fraud / Safe)
 │
 ▼
Gemini AI Agent
 │
 ├── Explain Prediction
 ├── Generate Risk Score
 ├── Recommend Action
 └── Prevention Tips
 │
 ▼
MongoDB
 │
 ▼
Dashboard & Reports
```

## 🚀 How to Run Locally

### 1. Clone & install dependencies
```bash
# From the project root
npm run install:all

# ML service
cd ml-service
pip install -r requirements.txt
```

### 2. Set up environment variables
```bash
cp server/.env.example server/.env
cp client/.env.example client/.env
cp ml-service/.env.example ml-service/.env
# then edit each .env with your real values
```

### 3. Train the ML model (first run only)
```bash
cd ml-service/model
python3 train_model.py
```
This generates `ml-service/model/fraud_model.pkl`. The Flask service will
also auto-train a model on first request if the file is missing.

### 4. Start all three services (in separate terminals)
```bash
# Terminal 1 - ML service
cd ml-service && python3 app.py

# Terminal 2 - Backend
cd server && npm run dev

# Terminal 3 - Frontend
cd client && npm run dev
```

- Frontend: http://localhost:5173
- Backend API: http://localhost:5000/api
- ML Service: http://localhost:5001

### 5. Create an account
Register a user via the UI. To test admin features, register a user then
manually update their `role` field to `"admin"` in MongoDB (or pass
`"role": "admin"` in the `/auth/register` request body during development).

---

## 📡 API Documentation

See [`docs/API.md`](./docs/API.md) for the full endpoint reference.

Quick reference:
| Endpoint | Method | Description |
|---|---|---|
| `/api/auth/register` | POST | Register a new user |
| `/api/auth/login` | POST | Login |
| `/api/transactions` | GET / POST | List / create transactions |
| `/api/transactions/:id` | GET / DELETE | View / delete a transaction |
| `/api/transactions/upload-csv` | POST | Bulk CSV upload |
| `/api/predict` | POST | Run ML+AI analysis without saving |
| `/api/dashboard` | GET | Dashboard stats & chart data |
| `/api/reports` | GET | Monthly fraud reports (admin) |
| `/api/reports/export-pdf` | GET | Export fraud report as PDF (admin) |

---

## 🖼️ Screenshots

> _Add screenshots of the Dashboard, Transaction Detail, and Admin Reports
> pages here once you run the app locally._

```
docs/screenshots/dashboard.png
docs/screenshots/transaction-detail.png
docs/screenshots/admin-reports.png
```

---

## ☁️ Deployment Guide

### Frontend → Vercel
1. Push the repo to GitHub.
2. Import the project in Vercel, set the **root directory** to `client`.
3. Build command: `npm run build`, Output directory: `dist`.
4. Add environment variable `VITE_API_BASE_URL` pointing to your deployed
   backend, e.g. `https://your-backend.onrender.com/api`.

### Backend → Render
1. Create a new **Web Service**, root directory `server`.
2. Build command: `npm install`, Start command: `npm start`.
3. Add all variables from `server/.env.example` in Render's environment
   settings (use your MongoDB Atlas URI, Gemini key, etc).
4. Set `ML_SERVICE_URL` to your deployed ML service URL.
5. Set `CLIENT_URL` to your deployed Vercel frontend URL (for CORS).

### ML Service → Render
1. Create another **Web Service**, root directory `ml-service`.
2. Build command: `pip install -r requirements.txt`
3. Start command: `gunicorn app:app` (a `Procfile` is included).
4. Render will run `train_model.py` automatically on first `/predict` call
   if no model file exists, or you can run it manually via Render's shell.

### Database → MongoDB Atlas
1. Create a free cluster at https://www.mongodb.com/atlas.
2. Whitelist `0.0.0.0/0` (or Render's IPs) under Network Access.
3. Create a database user and copy the connection string into `MONGO_URI`.

---

## 🧪 Code Quality Notes

- **MVC architecture**: models / controllers / routes / services are cleanly
  separated in `server/`.
- **Validation**: request payloads are validated before hitting the database
  or external services (`server/utils/validators.js`).
- **Error handling**: centralized Express error handler + `express-async-handler`
  wraps every controller to avoid unhandled promise rejections.
- **Fail-safes**: if the ML service or Gemini API is unreachable, the backend
  falls back to safe defaults instead of crashing.
- **ESLint-friendly**: consistent formatting, no unused imports, functional
  React components with hooks only.

---
## 📄 License
MIT — feel free to use this project as a learning reference or a starting
point for your own fraud detection platform.
