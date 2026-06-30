# BioVault

**Team:** BioVault  
**Members:** [@pkaysantana](https://github.com/pkaysantana) (Don Santana)  
**Event/Track:** UK-AI-Agent-EP5 — BasedAI track (Enterprise Memory Governance at Scale)

## What it does

Company memory systems produce **derived documents** (margin reports, summaries) built from **sensitive sources** (payroll, contracts). Role-based access control cannot answer: *should Marketing's agent be allowed to retrieve a report that was derived from payroll, even if Marketing never saw the payroll file directly?*

BioVault implements a **permission gate** for a shared artifact store:

- Access is granted by explicit **capability** on each `(principal, artifact, operation)` — not by team role alone
- **Lineage** records which sources a derived artifact was built from
- **Revoking** a source quarantines every active derived artifact that included it
- Every allow/deny is **audited** with `request_id`, reason, and latency

This repo does **not** include an AI agent or LLM. It is a FastAPI backend + React demo UI that proves the gate works. `POST /query` is the endpoint an external agent would call before passing content to any model.

**Default demo:** Marketing is denied the Q3 Growth Margin Report (derived from payroll). Owner revokes payroll → report quarantined → Finance denied too.

**Secondary demo:** Biotech/pharma scenario via `POST /seed?scenario=biotech` or the UI scenario switcher.

## Demo

- 🎥 Video: _not yet recorded_
- 🌐 Live demo: _not yet deployed_ — see [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md)
- 🖼️ Screenshots: run locally, walk through [docs/DEMO_SCRIPT.md](docs/DEMO_SCRIPT.md)

## How to run it

**Prerequisites:** Python 3.11+, Node.js 18+, npm

```powershell
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements-dev.txt
uvicorn app.main:app --reload

# new terminal:
cd frontend
npm install
npm run dev
```

Open `http://localhost:5173` → **Seed / Reset Demo** → follow the step guide.

```powershell
cd backend && python -m pytest -q   # 20 tests
```

## How it works

```
Demo user (React UI) or external caller
        │
        │  GET /artifacts/{id}  or  POST /query
        │  Authorization: Bearer <token from /seed>
        ▼
FastAPI
        ├─ resolve_principal()     SHA-256(token) lookup
        ├─ evaluate_access()       SQL grant check + lineage integrity
        ├─ log_audit()             every decision logged
        └─ decrypt content         only on allow
        ▼
SQLite — users, artifacts, capability_grants, lineage_edges, audit_events
```

No LLM runs anywhere in this path.

## Tech & sponsor APIs used

- **Models / LLMs:** None — permission checks are deterministic Python/SQL. No open-weight or closed model is integrated; `POST /query` is the integration point for a future external agent.
- **Frameworks:** FastAPI, React + Vite
- **BasedAI track:** Enterprise memory governance — artifact-level capabilities, lineage propagation, audit traceability
- **Other:** SQLite, cryptography (Fernet), pytest

## What's next

- Wire an actual open-weight agent that calls `POST /query` before generation
- PostgreSQL, KMS-backed encryption, token rotation
- Live demo deploy on Vercel

## License

MIT — BioVault team. Hackathon MVP only.

---

> 🔒 Never commit secrets. Use `.env.example` locally. See [../../../SECURITY.md](../../../SECURITY.md).
