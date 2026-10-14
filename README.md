# 🛡️ FIN-SHIELD

**AI-powered financial advertisement intelligence platform.**

FIN-SHIELD helps investigators analyze suspicious financial advertisements: it extracts evidence from ad copy, produces explainable risk indicators, and surfaces potentially related advertisements as evidence-backed campaign clusters.

🔗 **Live demo:** [fin-shield-jet.vercel.app](https://fin-shield-jet.vercel.app)

> ⚠️ **Important:** Risk scores and relationships are **not proof of fraud** and are **not a probability of crime**. FIN-SHIELD is an investigation aid, not a verdict engine. All findings require human review.
>
> The seeded dataset is **synthetic demonstration data**. It does not describe real cases or real people.

---

## 📌 Problem

Suspicious financial advertisements appear across websites, social media, messaging platforms and other channels. Investigators often have to read each ad by hand, copy out phone numbers and URLs, and guess whether different ads are connected.

FIN-SHIELD is not a simple "fake ad" classifier. It combines ad analysis, entity extraction, explainable risk assessment, cross-advertisement matching, campaign discovery and an investigation dashboard in one workflow.

## 🎯 Core Workflow

```
Suspicious ad  →  Extracted evidence  →  Explainable risk assessment
               →  Related advertisements  →  Evidence-backed relationship graph
               →  Investigator review
```

## ✨ Features

- **Ad ingestion**: submit advertisement text, with an optional image and reference URL (the URL is inspected as a string only and is never fetched).
- **Entity extraction**: phone numbers, URLs, domains, UPI-like handles, emails, account-like strings and recurring phrase patterns.
- **Explainable risk scoring**: documented, deterministic rules with per-signal evidence. The score is a configured rules-based triage assessment, not a statistically calibrated probability.
- **Cross-ad matching**: links advertisements through shared normalized entities and conservative text similarity (TF-IDF cosine similarity).
- **Campaign discovery**: potentially coordinated campaigns are found as connected components of related ads.
- **Interactive relationship graph**: explore clusters and the evidence behind each link.
- **Investigation workflow**: persisted investigations, analyst notes, review status and alerts.
- **Optional OCR**: image submissions can be read with Tesseract. If it is not installed, the image is still stored, the limitation is reported, and text analysis keeps working.

## 🧰 Tech Stack

| Layer | Technology |
| --- | --- |
| Frontend | React 18, TypeScript, Vite, Tailwind CSS, React Router, Recharts, React Flow (`@xyflow/react`), Lucide icons |
| Backend | Python 3.12, FastAPI, Uvicorn, SQLAlchemy 2, Pydantic 2 |
| Database | SQLite by default; PostgreSQL via `DATABASE_URL` |
| Analysis | Deterministic rules engine, TF-IDF cosine similarity, optional Tesseract OCR (`pytesseract`, Pillow) |
| Testing | pytest (backend), TypeScript type-check and Vite build (frontend) |
| Deployment | Vercel (frontend), Render with Docker (backend) |

## 📁 Project Structure

```
Fin-Shield/
├── backend/              # FastAPI app, services, seed script, tests
│   ├── app/
│   │   └── services/     # extraction, risk scoring, similarity logic
│   ├── seed.py           # idempotent synthetic data seeder
│   └── requirements.txt
├── frontend/             # React + Vite investigation console
├── docs/                 # Additional documentation
├── Dockerfile            # Backend image for Render
├── render.yaml           # Render service configuration
├── vercel.json           # Root Vercel config (frontend deploys from frontend/)
├── PRODUCT_SPEC.md       # Product specification
├── DEVELOPMENT_ROADMAP.md
└── AGENTS.md             # Project conventions
```

## ✅ Requirements

- **Python 3.12** (Python 3.14 breaks `pydantic-core` and Pillow; on macOS use `python@3.12` from Homebrew)
- **Node.js 20+**
- No API keys are needed for the main demonstration
- *Optional:* Tesseract OCR for image text extraction

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/avi-2k07/FIn-Shield.git
cd FIn-Shield
```

### 2. Install dependencies

```bash
# Backend
cd backend
python3.12 -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# Frontend
cd ../frontend
npm install
```

### 3. Seed the database

```bash
cd backend
source .venv/bin/activate
python seed.py
```

The seeder is safe to rerun: rows with an existing `seed_key` are skipped. It loads roughly 100 synthetic advertisements.

### 4. Run the app

**Terminal 1: backend**

```bash
cd backend
source .venv/bin/activate
uvicorn app.main:app --reload --port 8000
```

**Terminal 2: frontend**

```bash
cd frontend
npm run dev
```

Open **http://127.0.0.1:5173**

## ⚙️ Environment Variables

Optional `backend/.env`:

```env
DATABASE_URL=sqlite:////absolute/path/to/data/finshield.db
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
SIMILARITY_THRESHOLD=0.72
```

Optional `frontend/.env`:

```env
VITE_API_BASE_URL=
```

Leave `VITE_API_BASE_URL` empty when using the Vite dev proxy. Set it to your backend URL when the frontend is deployed separately.

## 🧪 Tests

```bash
# Backend
cd backend
source .venv/bin/activate
pytest -q

# Frontend (type-check + production build)
cd frontend
npm run build
```

## ☁️ Deployment

- **Frontend:** deployed on Vercel as its own project with the root directory set to `frontend/`.
- **Backend:** deployed on Render using the root `Dockerfile` (Python 3.12 slim with Tesseract installed). Set these environment variables in the Render dashboard:
  - `DATABASE_URL`: the Render PostgreSQL connection string (never commit this value)
  - `CORS_ORIGINS`: your deployed frontend URL
  - `UPLOAD_DIR`: `/tmp/finshield_uploads`
  - `ENVIRONMENT`: `production`

## 🧭 Design Principles

- Use cautious wording: *potentially suspicious*, *risk indicator*, *potentially coordinated campaign*.
- Separate observable facts from risk inferences.
- Describe the rules engine honestly; it is not a trained model.
- Never fetch user-supplied URLs.
- Read dashboard statistics from the API rather than inventing them.
- Keep scoring logic in `app/services/*`, not scattered across routes.
- Label seeded data as synthetic everywhere it appears.

## 🗺️ Roadmap

Planned improvements that fit the existing structure:

- [ ] Replace the rules engine with a trained classifier behind `app/services/risk.py`
- [ ] Replace TF-IDF with embeddings behind `app/services/similarity.py`
- [ ] Optional, secured URL inspection separate from string parsing
- [ ] PostgreSQL as the default via `DATABASE_URL`
- [ ] Stronger OCR once Tesseract is installed locally
- [ ] Authentication and role-based review queues

## ⚖️ Disclaimer

FIN-SHIELD is a demonstration and research project. Its output is triage support for human investigators and must not be treated as evidence of wrongdoing on its own.

## 👤 Author

Built by **Avika Jain** ([@avi-2k07](https://github.com/avi-2k07)).
