# Unipod-Interns

A platform where the interns at the University of Lagos AI-UniPod access learning materials and training instructions[cite: 2].

## System Architecture

This project uses a decoupled 3-tier architecture to bypass server limits on large media files and eliminate cloud egress fees:

*   **Frontend (Presentation Layer):** Vite + React (TypeScript) deployed on Vercel. This handles the UI, routing, and media players as a static bundle with zero compute overhead.
*   **Backend (Application Layer):** FastAPI (Python) deployed on Render. Acts as the stateless API gateway, handling metadata requests and generating secure upload tickets.
*   **Database (Data Layer):** PostgreSQL on Render. Stores the system state, module groupings, and resource metadata.
*   **Storage (Media Layer):** Cloudflare R2. Handles 1GB+ video uploads via Presigned URLs directly from the client browser, streaming back to interns via edge caching nodes.

## Prerequisites

*   **Node.js v18+** (For Vite/React frontend)
*   **Python 3.10+** (For FastAPI backend)
*   **Docker & Docker Compose** (For local database orchestration)
*   **Cloudflare Account** (For R2 Bucket and CDN configuration)

## Local Development Setup

### 1. Database (Docker)
Start the local PostgreSQL database using Docker Compose from the root directory:
```bash
docker-compose up -d
```

### 2. Backend (FastAPI)
Navigate to the backend directory, activate your virtual environment, and start the Uvicorn server:
```bash
cd backend
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
alembic upgrade head      # Run database migrations
uvicorn app.main:app --reload --port 8000
```
*API Documentation will be available at: `http://localhost:8000/docs`*

### 3. Frontend (Vite)
Open a new terminal window, navigate to the frontend directory, install dependencies, and start the development server:
```bash
cd frontend
npm install
npm run dev
```
*Client UI will be available at: `http://localhost:5173`*

## Environment Variables

Create a `.env` file in the `backend/` directory and configure the following keys:

```env
# Database Configuration
DATABASE_URL=postgresql://user:password@localhost:5432/unipod_db

# Cloudflare R2 Configuration
CLOUDFLARE_R2_ENDPOINT_URL=https://<ACCOUNT_ID>.r2.cloudflarestorage.com
CLOUDFLARE_ACCESS_KEY_ID=your_access_key
CLOUDFLARE_SECRET_ACCESS_KEY=your_secret_key
R2_BUCKET_NAME=unipod-media

# Security
ADMIN_SECRET_KEY=your_secure_random_string
```

## Deployment

*   **Frontend:** Connect the `frontend/` directory to Vercel. Vercel will automatically detect the Vite build settings.
*   **Backend:** Connect the `backend/` directory to a Render Web Service, using Docker as the runtime environment.
*   **Database:** Provision a managed PostgreSQL instance on Render and supply the internal connection string to your backend environment variables.
