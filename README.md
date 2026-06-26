# ⚡ SmartHire — Smart Job Portal with AI Resume Analyzer

> A **production-grade, full-stack job portal** with AI-powered resume matching.
> Built with **Spring Boot + MySQL + Python FastAPI + React**.

---

## 📋 Table of Contents

- [Architecture Overview](#architecture-overview)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Step-by-Step Setup](#step-by-step-setup)
  - [1. MySQL Database](#1-mysql-database)
  - [2. AI Microservice (Python FastAPI)](#2-ai-microservice-python-fastapi)
  - [3. Spring Boot Backend](#3-spring-boot-backend)
  - [4. React Frontend](#4-react-frontend)
- [Demo Accounts](#demo-accounts)
- [API Documentation](#api-documentation)
- [Features by Role](#features-by-role)
- [AI Module Details](#ai-module-details)
- [Database Schema](#database-schema)

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                   React Frontend (Port 5173)                     │
│  Login │ Signup │ Dashboard │ Jobs │ Resume Upload │ Admin Panel │
└──────────────────────────┬──────────────────────────────────────┘
                           │  HTTP / REST (Axios + JWT)
┌──────────────────────────▼──────────────────────────────────────┐
│              Spring Boot Backend (Port 8080)                     │
│  Auth │ Jobs │ Applications │ Resume │ Admin APIs                │
│              Spring Security + JWT (HS256)                       │
└──────────────┬────────────────────────┬────────────────────────┘
               │  JPA / Hibernate        │  HTTP (RestTemplate)
      ┌────────▼────────┐      ┌─────────▼──────────────────────┐
      │  MySQL Database  │      │  Python FastAPI AI Service      │
      │  (Port 3306)     │      │  (Port 8000)                    │
      └─────────────────┘      │  pdfplumber + spaCy + sklearn   │
                               └─────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Backend | Java 17, Spring Boot 3.2, Spring Security, JWT (JJWT) |
| Database | MySQL 8.x, Spring Data JPA, Hibernate |
| AI Service | Python 3.10+, FastAPI, pdfplumber, spaCy, scikit-learn |
| Frontend | React 18, Vite, React Router v6, Axios, Chart.js |
| Security | BCrypt passwords, HMAC-SHA256 JWT, Role-based access |

---

## 📁 Project Structure

```
AI Job Platform/
├── backend/                   # Spring Boot Maven project
│   ├── pom.xml
│   └── src/main/java/com/jobportal/
│       ├── config/            # Security, JWT, App beans
│       ├── controller/        # REST controllers (5)
│       ├── dto/               # Request/Response DTOs
│       ├── entity/            # JPA entities (5)
│       ├── enums/             # Role, ApplicationStatus
│       ├── exception/         # Global error handling
│       ├── repository/        # Spring Data repositories (5)
│       └── service/           # Business logic (6)
│
├── ai-service/                # Python FastAPI microservice
│   ├── main.py
│   ├── requirements.txt
│   └── services/
│       ├── pdf_parser.py      # pdfplumber PDF extraction
│       ├── nlp_analyzer.py    # spaCy NER + skill keywords
│       └── scorer.py          # TF-IDF + cosine similarity
│
├── frontend/                  # React + Vite SPA
│   ├── package.json
│   ├── vite.config.js
│   └── src/
│       ├── App.jsx            # Router + route guards
│       ├── context/           # AuthContext (JWT storage)
│       ├── services/          # Axios API layer
│       ├── components/        # Navbar
│       └── pages/             # 8 pages
│
└── database/
    ├── schema.sql             # DDL for all 5 tables
    └── seed_data.sql          # Sample users, jobs, applications
```

---

## ✅ Prerequisites

| Tool | Version | Check |
|---|---|---|
| Java JDK | 17+ | `java -version` |
| Maven | 3.8+ | `mvn -version` |
| MySQL | 8.x | `mysql --version` |
| Python | 3.10+ | `python --version` |
| Node.js | 18+ | `node -v` |
| npm | 9+ | `npm -v` |

---

## 🚀 Step-by-Step Setup

### 1. MySQL Database

```sql
-- Start MySQL and run these commands:
mysql -u root -p

-- Inside MySQL shell:
CREATE DATABASE job_portal_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
exit
```

```bash
# Load the schema and seed data
mysql -u root -p job_portal_db < database/schema.sql
mysql -u root -p job_portal_db < database/seed_data.sql
```

**Update credentials** in `backend/src/main/resources/application.properties`:
```properties
spring.datasource.username=root
spring.datasource.password=root
```

---

### 2. AI Microservice (Python FastAPI)

```bash
# Navigate to AI service directory
cd ai-service

# Create and activate a virtual environment
python -m venv venv

# Windows
venv\Scripts\activate

# macOS / Linux
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Download the spaCy English model (one-time)
python -m spacy download en_core_web_sm

# Start the AI service on port 8000
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

**Verify** the service is running:
```
http://localhost:8000/health      → {"status": "healthy"}
http://localhost:8000/docs        → Swagger UI with all endpoints
```

---

### 3. Spring Boot Backend

```bash
# Navigate to backend directory
cd backend

# Build and run (in a NEW terminal)
mvn spring-boot:run
```

The backend will:
- Auto-create/update the database tables (ddl-auto=update)
- Start on **port 8080**
- Create an `uploads/resumes/` directory for PDF storage

**Verify** the backend is running:
```
http://localhost:8080/api/jobs    → Returns list of jobs (public endpoint)
```

---

### 4. React Frontend

```bash
# Navigate to frontend directory
cd frontend

# Install dependencies
npm install

# Start the development server (in a NEW terminal)
npm run dev
```

Open your browser at: **http://localhost:5173**

> The Vite dev server automatically proxies `/api` requests to `http://localhost:8080` — no CORS issues.

---

## 👤 Demo Accounts

All passwords: **`password123`**

| Role | Email | Password |
|---|---|---|
| 🔴 Admin | admin@jobportal.com | password123 |
| 🔵 Recruiter | alice@techcorp.com | password123 |
| 🔵 Recruiter | bob@fintech.com | password123 |
| 🟢 Candidate | charlie@gmail.com | password123 |
| 🟢 Candidate | diana@gmail.com | password123 |
| 🟢 Candidate | eve@gmail.com | password123 |

> 💡 The Login page has **Quick Demo Login** buttons for all 3 roles.

---

## 📡 API Documentation

### Auth (`/api/auth`)

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/register` | Public | Register new user |
| POST | `/login` | Public | Login, returns JWT |
| GET | `/me` | JWT | Get current user |

### Jobs (`/api/jobs`)

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/` | Public | All open jobs |
| GET | `/search?keyword=` | Public | Search jobs |
| GET | `/{id}` | Public | Job details |
| GET | `/all` | ADMIN | All jobs incl. closed |
| POST | `/` | RECRUITER | Create job |
| PUT | `/{id}` | RECRUITER/ADMIN | Update job |
| DELETE | `/{id}` | RECRUITER/ADMIN | Delete job |

### Applications (`/api/applications`)

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/apply/{jobId}` | CANDIDATE | Apply (triggers AI) |
| GET | `/my-applications` | CANDIDATE | Own applications |
| GET | `/job/{jobId}` | RECRUITER | All applicants |
| PUT | `/{id}/status` | RECRUITER | Update status |

### Resumes (`/api/resumes`)

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/upload` | CANDIDATE | Upload PDF resume |
| GET | `/{candidateId}` | RECRUITER | View resume info |

### Admin (`/api/admin`)

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/users` | ADMIN | All users |
| PUT | `/users/{id}/block` | ADMIN | Toggle block |
| DELETE | `/users/{id}` | ADMIN | Delete user |
| GET | `/analytics` | ADMIN | System stats |

### AI Service (`http://localhost:8000`)

| Method | Endpoint | Description |
|---|---|---|
| GET | `/health` | Health check |
| POST | `/parse-pdf` | Extract text from PDF |
| POST | `/analyze` | Score resume vs JD |

---

## 🎯 Features by Role

### 👤 Candidate
- Register/login → auto JWT session
- Upload PDF resume → AI text extraction
- Browse and search all open jobs
- Apply with optional cover letter
- **Instant AI match score** on each application
- View matched skills, missing skills, suggestions

### 🏢 Recruiter
- Post, edit, close jobs with skill requirements
- View all applicants sorted by **AI score**
- See detailed AI analysis per candidate
- One-click Shortlist / Reject with status dropdown

### 🛠️ Admin
- View system analytics (users, jobs, applications)
- User management table with Block / Unblock / Delete
- Progress bars for application status distribution

---

## 🤖 AI Module Details

### Scoring Pipeline

```
PDF Upload → Text Extraction (pdfplumber) → Skill Detection (spaCy + keyword list)
         ↓
Job Description → Skill Detection → TF-IDF Vectorization
         ↓
Skill Overlap Score (60%) + Cosine Similarity (40%) = Final Score (0–100)
         ↓
Gap Analysis → Missing Skills → Improvement Suggestions
```

### Skill Coverage

The NLP analyzer recognizes 80+ tech skills across:
- **Languages**: Python, Java, JavaScript, TypeScript, Go, Kotlin, etc.
- **Frameworks**: Spring Boot, React, Node.js, Django, FastAPI, etc.
- **Databases**: MySQL, MongoDB, PostgreSQL, Redis, Elasticsearch, etc.
- **Cloud/DevOps**: AWS, Docker, Kubernetes, Terraform, Jenkins, etc.
- **Big Data**: Spark, Kafka, Airflow, Hadoop, etc.

---

## 🗄️ Database Schema

```sql
users           → id, name, email, password, role, is_active, created_at
jobs            → id, title, description, company, skills_required, recruiter_id (FK)
resumes         → id, candidate_id (FK UNIQUE), file_path, extracted_text
applications    → id, candidate_id (FK), job_id (FK), status, UNIQUE(candidate,job)
ai_scores       → id, application_id (FK UNIQUE), match_score, matched_skills, suggestions
```

---

## 🔐 Security Notes

- Passwords hashed with **BCrypt** (cost factor 10)
- JWT tokens signed with **HMAC-SHA256**
- Tokens expire after **24 hours**
- Endpoints protected by Spring Security role checks + `@PreAuthorize`
- File uploads validated for PDF content-type
- CORS restricted to `localhost:5173` and `localhost:3000`

---

## 📝 Interview Talking Points

1. **JWT Stateless Auth** — no server-side sessions; token verified on every request
2. **AI Microservice Architecture** — Python FastAPI decoupled from Java for flexibility
3. **TF-IDF + Skill Overlap** — dual scoring strategy for accuracy
4. **Spring Data JPA** — repositories with custom JPQL queries
5. **Role-Based Access Control** — enforced at both URL and method level
6. **OneToOne / OneToMany / ManyToOne** — all three JPA relationship types used
7. **Global Exception Handler** — consistent error responses across all controllers
8. **React Context API** — centralized auth state with localStorage persistence
9. **Vite Proxy** — dev-time CORS-free API communication
10. **Modular Layered Architecture** — Entity → Repository → Service → Controller

---

*Built with ❤️ as a production-level portfolio project — SmartHire AI Job Portal*
