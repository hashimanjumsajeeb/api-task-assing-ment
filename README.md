# Full-Stack Task Management API & Dashboard

A production-ready full-stack Task Management application built with **Flask 3**, **Flask-SQLAlchemy**, **PostgreSQL** (with local SQLite fallback), **Flask-JWT-Extended**, and a responsive **Vanilla JavaScript/CSS** frontend. 

Configured for **local Windows PowerShell development** and **serverless deployment on Vercel** for API testing assessments in **Postman**.

---

## 📋 Table of Contents
1. [Tech Stack & Architecture](#tech-stack--architecture)
2. [Project Structure](#project-structure)
3. [Prerequisites](#prerequisites)
4. [Windows PowerShell Setup & Run Instructions](#windows-powershell-setup--run-instructions)
5. [Automated Testing & Verification](#automated-testing--verification)
6. [API Endpoint Reference](#api-endpoint-reference)
7. [Testing with Postman](#testing-with-postman)
8. [Vercel & PostgreSQL Deployment Guide](#vercel--postgresql-deployment-guide)
9. [Troubleshooting](#troubleshooting)

---

## 🛠 Tech Stack & Architecture

- **Backend Framework**: Flask 3.x (Modular Blueprints architecture)
- **ORM & Database Layer**: Flask-SQLAlchemy 3.x with `psycopg2-binary` (PostgreSQL) and built-in SQLite fallback
- **Authentication**: JWT (JSON Web Tokens) via `Flask-JWT-Extended`
- **Password Security**: Cryptographic hashing via `werkzeug.security`
- **Environment Management**: `python-dotenv`
- **Deployment Platform**: Vercel Serverless (`@vercel/python`) with external PostgreSQL (Neon / Supabase / Render / Vercel Postgres)
- **Frontend**: Single Page Application served by Flask (`templates/index.html`, `static/css/style.css`, `static/js/app.js`)

---

## 📁 Project Structure

```text
task-manager-api/
│
├── app.py                   # Main Flask application entry point & WSGI handler
├── models.py                # SQLAlchemy models (User, Task) with schema relations
├── db.py                    # Database configuration (PostgreSQL & SQLite fallback)
├── schema.sql               # Reference SQL schema definition
├── vercel.json              # Vercel serverless build and routing configuration
├── api/
│   └── index.py             # Vercel serverless function entry point
├── requirements.txt         # Dependencies (Flask, SQLAlchemy, psycopg2, JWT)
├── .env.example             # Template for environment variables (placeholders)
├── .env                     # Local environment settings (git-ignored)
├── .gitignore               # Excludes venv, .env, *.db, cache, and logs
├── verify_api.py            # Automated test suite (CRUD, authorization, edge cases)
├── verify_live_server.py    # Real socket-level HTTP integration test script
├── postman_collection.json  # Complete Postman Collection ready to import
│
├── routes/
│   ├── __init__.py          # Routes package initializer
│   ├── auth.py              # Auth endpoints (/register, /login, /me)
│   └── tasks.py             # Task CRUD endpoints (/api/tasks, /api/tasks/<id>)
│
├── templates/
│   └── index.html           # Full-stack frontend UI dashboard & Postman helper
│
└── static/
    ├── css/
    │   └── style.css        # Responsive styling, modern badges, modal styles
    └── js/
        └── app.js           # Client-side state, API calls, and JWT token management
```

---

## 💻 Prerequisites

- **Operating System**: Windows 10/11 (or macOS / Linux)
- **Python**: Version 3.11+ (tested on Python 3.14)
- **PowerShell**: Windows PowerShell or PowerShell 7+
- **Postman**: Any modern version of Postman
- **GitHub Account**: For version control & connecting to Vercel
- **Vercel Account**: Free hobby account at [vercel.com](https://vercel.com)
- **Hosted PostgreSQL**: Free serverless PostgreSQL database from [neon.tech](https://neon.tech) or [supabase.com](https://supabase.com)

---

## 🚀 Windows PowerShell Setup & Run Instructions

Open **Windows PowerShell** and run:

```powershell
# 1. Navigate to the project directory
cd "C:\Users\Sojib\.gemini\antigravity\scratch\task-manager-api"

# 2. Create the virtual environment (if not already created)
python -m venv venv

# 3. Enable script execution and activate
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
.\venv\Scripts\Activate.ps1

# 4. Install all dependencies
pip install -r requirements.txt

# 5. Start the application locally
python app.py
```

Open your browser at `http://127.0.0.1:5000` to interact with the full dashboard.

---

## 🧪 Automated Testing & Verification

Run the automated unit and integration tests:
```powershell
.\venv\Scripts\python.exe verify_api.py
```
Expected output:
```text
Ran 3 tests in 1.600s
OK
```

Run the live network socket server test:
```powershell
.\venv\Scripts\python.exe verify_live_server.py
```
Expected output:
```text
ALL LIVE SERVER TESTS PASSED SUCCESSFULLY! The application is 100% verified.
```

---

## 📡 API Endpoint Reference

All task endpoints require an `Authorization` header: `Authorization: Bearer <token>`.

| Method | Endpoint | Description | Auth Required | Status Codes |
|---|---|---|---|---|
| `POST` | `/api/auth/register` | Register a new user | No | `201`, `400`, `409` |
| `POST` | `/api/auth/login` | Authenticate and obtain JWT | No | `200`, `400`, `401` |
| `GET` | `/api/auth/me` | Fetch authenticated user profile | Yes | `200`, `401`, `404` |
| `GET` | `/api/tasks` | List user's tasks (optional `?status=` & `?priority=`) | Yes | `200`, `400`, `401` |
| `GET` | `/api/tasks/<id>` | Retrieve single task by ID | Yes | `200`, `401`, `403`, `404` |
| `POST` | `/api/tasks` | Create a new task | Yes | `201`, `400`, `401` |
| `PUT` | `/api/tasks/<id>` | Full update of existing task | Yes | `200`, `400`, `401`, `403`, `404` |
| `PATCH` | `/api/tasks/<id>` | Partial update of task fields | Yes | `200`, `400`, `401`, `403`, `404` |
| `DELETE` | `/api/tasks/<id>` | Delete a task | Yes | `200`, `401`, `403`, `404` |
| `GET` | `/health` | Health check endpoint for uptime monitoring | No | `200` |

---

## 📮 Testing with Postman

1. Open Postman and click **Import**.
2. Select `postman_collection.json` from the project directory.
3. For local testing, keep the `base_url` variable as `http://127.0.0.1:5000`.
4. For deployed Vercel testing, update the `base_url` variable in Postman to `https://<your-project>.vercel.app`.
5. Execute the **Login User** request — it automatically extracts the `access_token` and saves it for all subsequent requests!

---

## ☁️ Vercel & PostgreSQL Deployment Guide

### Why PostgreSQL on Vercel?
Vercel is a **serverless** environment. Its filesystem is read-only (except `/tmp`), and serverless function instances are created and destroyed dynamically. A local SQLite `.db` file would be erased between requests. By using an external PostgreSQL database (such as Neon or Supabase), your data is persistently saved and accessible across all serverless invocations.

### Step 1: Create a Free Hosted PostgreSQL Database (e.g. Neon)
1. Go to [neon.tech](https://neon.tech) (or [supabase.com](https://supabase.com)) and sign up for a free account.
2. Create a new project (e.g., `task-manager-db`).
3. Copy your connection string (`DATABASE_URL`). It will look like:
   ```text
   postgresql://alex:AbCdEf123456@ep-cool-fog-123456.us-east-2.aws.neon.tech/neondb?sslmode=require
   ```

### Step 2: Push Your Code to GitHub
Open PowerShell in your project directory:

```powershell
# 1. Initialize git repository
git init

# 2. Add files
git add .

# 3. Create initial commit
git commit -m "Prepare full-stack Task Management API for Vercel deployment"

# 4. Link to your GitHub repository (replace with your repo URL)
git branch -M main
git remote add origin https://github.com/<YOUR_GITHUB_USERNAME>/<YOUR_REPOSITORY_NAME>.git
git push -u origin main
```

*(Note: Thanks to `.gitignore`, your local `.env`, database files, and virtual environment are never pushed to GitHub).*

### Step 3: Deploy on Vercel
1. Log in to [vercel.com](https://vercel.com) using your GitHub account.
2. Click **Add New...** &rarr; **Project**.
3. Select your GitHub repository and click **Import**.
4. In the **Environment Variables** section, add the following 3 variables:
   - `SECRET_KEY`: A strong random string (e.g. `your-production-secret-key-xyz`)
   - `JWT_SECRET_KEY`: A strong random string (e.g. `your-production-jwt-secret-key-xyz`)
   - `DATABASE_URL`: Your external PostgreSQL connection string from Neon or Supabase
5. Click **Deploy**.
6. Vercel will build the application using `@vercel/python`, execute `db.create_all()` non-destructively on first request to create your tables, and assign you a live URL:
   ```text
   https://<your-project>.vercel.app
   ```

### Step 4: Verify Your Deployed Application
1. Visit `https://<your-project>.vercel.app/health` &rarr; Returns `{"status": "healthy"}`.
2. Visit `https://<your-project>.vercel.app/` &rarr; Opens your Task Manager dashboard.
3. In Postman, set `base_url` to `https://<your-project>.vercel.app` and run your assessment tests!
#   a p i - t a s k - a s s i n g - m e n t  
 