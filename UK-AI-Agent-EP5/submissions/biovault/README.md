# BioVault

**Team:** BioVault  
**Members:** [@pkaysantana](https://github.com/pkaysantana) (Don Santana)  
**Event/Track:** UK-AI-Agent-EP5 — BasedAI track (Enterprise Memory Governance at Scale)

## What it does

AI agents in company memory systems — Notion, Confluence, internal wikis — create **derived artifacts** (margin reports, cost summaries) from **sensitive sources** (payroll registers, vendor contracts). Standard RBAC asks "can this user read files?" BioVault asks a harder question: **can this agent surface a derived artifact when one of its sources contains payroll data that Marketing should never see?**

BioVault is a deterministic, **LLM-free** capability enforcement layer between agents and a shared artifact store. Every access decision runs through pure SQL/Python — **zero model tokens in the permission path**. When a source document is revoked, quarantine propagates through lineage to all derived descendants automatically.

**Default demo (SME / payroll leakage):** A marketing agent asks for Q3 cost data. Marketing can read campaign costs but is **denied** the Q3 Growth Margin Report — a derived artifact that includes payroll lineage. When the owner revokes the Payroll Salary Register, the Q3 report is quarantined and Finance is denied too. Every decision is audited with `request_id`, principal, decision, reason, and latency.

A secondary **biotech / AI science** scenario (pharma R&D, adverse-event revocation) uses the same permission engine — switch via the UI scenario picker or `POST /seed?scenario=biotech`.

## Demo

- 🎥 Video: _Add before Demo Day_
- 🌐 Live demo: Deploy via Vercel Services — see [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md) (`vercel.json` at repo root). After deploy, click **Seed / Reset Demo** once.
- 🖼️ Screenshots: Run locally and capture the SME payroll-leakage flow (Steps 1–8 in [docs/DEMO_SCRIPT.md](docs/DEMO_SCRIPT.md))
- 📦 Standalone repo: [github.com/pkaysantana/BioVault](https://github.com/pkaysantana/BioVault)

## How to run it

**Prerequisites:** Python 3.11+, Node.js 18+, npm

```powershell
# 1. Backend
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements-dev.txt
uvicorn app.main:app --reload
# → http://localhost:8000  (API docs at /docs)

# 2. Frontend (new terminal)
cd frontend
npm install
npm run dev
# → http://localhost:5173

# 3. Configure (optional — defaults work for local dev)
#    backend/.env.example  →  backend/.env
#    frontend/.env.example →  frontend/.env.local

# 4. Seed demo data
#    Click "Seed / Reset Demo" in the UI, or:
#    curl -X POST http://localhost:8000/seed?scenario=sme

# 5. Tests
cd backend
python -m pytest -q
# expected: 20 passed
```

Unified production deploy (frontend + backend on one URL): see [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md).

## How it works

```
Agent / open-weight model runtime
        │
        │  POST /query  { artifact_id, purpose }
        │  Authorization: Bearer <capability_token>
        ▼
FastAPI permission gate
        ├─ resolve_principal()   SHA-256(token) → principal_id
        ├─ evaluate_access()     deterministic SQL — 0 model tokens
        │   ├─ capability grant on (principal, artifact, operation)?
        │   └─ all lineage sources active? (no revoked/quarantined parents)
        ├─ log_audit()           request_id, decision, reason, latency_ms
        └─ decrypt + return      only if decision == allow
        ▼
SQLite (users, artifacts, capability_grants, lineage_edges, audit_events)
```

**Key design choices:**

| Decision | Rationale |
|---|---|
| Capability per `(principal, artifact, operation)` | Minimise blast radius — no wildcard grants |
| Lineage-aware revocation (BFS quarantine) | Revoking payroll quarantines every derived artifact that included it |
| `POST /query` as single agent gate | Model never decides access — enforcement is outside the model boundary |
| No LLM in permission path | Auditable, reproducible, sub-millisecond; compatible with any open-weight runtime |

Full architecture, BasedAI compliance matrix, and API reference: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).  
2-minute judge demo script: [docs/DEMO_SCRIPT.md](docs/DEMO_SCRIPT.md).

## Tech & sponsor APIs used

- **Models / LLMs:** None in the permission path. BioVault is model-agnostic — any open-weight agent runtime (Qwen, Llama 3, Mistral, Phi, etc.) integrates via `POST /query` before passing content to generation. **Open-weight compliance:** zero closed/proprietary model calls in enforcement.
- **Frameworks:** FastAPI (backend), React + Vite (frontend dashboard)
- **BasedAI / sponsor tech used:** Built for the BasedAI Enterprise Memory Governance at Scale track — artifact-level capability enforcement, lineage propagation, and audit traceability aligned with workshop requirements
- **Other notable libraries:** SQLite, cryptography (Fernet), pytest

## What's next

- PostgreSQL + connection pooling for production concurrency
- KMS-backed encryption (replace hardcoded Fernet key)
- Token rotation and expiry endpoints
- Webhook/streaming audit export for enterprise SIEM integration
- Benchmark lineage propagation at 100+ node depth

## License

MIT — see submission authors. Educational prototype / hackathon MVP; not validated clinical decision support or a regulated medical device.

---

> 🔒 **Reminder:** never commit secrets. Provide a `.env.example` with placeholders, keep
> your real `.env` local, and never add wallet private keys or seed phrases. See
> [../../../SECURITY.md](../../../SECURITY.md).
