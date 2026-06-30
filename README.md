# BioVault

**Prevent AI agents from leaking company memory — like payroll — into marketing answers.**

Deterministic, lineage-aware, LLM-free capability enforcement for shared artifact memory.

Built for the **BasedAI Enterprise Memory Governance at Scale** track · [UK AI Agent Hackathon EP.5](UK-AI-Agent-EP5/) · Imperial College London, June–July 2026.

**Team:** BioVault · [@pkaysantana](https://github.com/pkaysantana) (Don Santana)  
**Official submission PR:** [BasedAICo/hackathons#3](https://github.com/BasedAICo/hackathons/pull/3)

> **Classification:** Educational prototype / hackathon MVP. Not validated scientific logic, clinical decision support, or a regulated medical device.

---

## One-line pitch

> BioVault enforces artifact-level capability checks so a marketing AI agent asking for "Q3 cost data" is denied the payroll-mixed margin report — and revoking the payroll source automatically quarantines every derived artifact that included it, without touching a model.

---

## The problem (BasedAI workshop use case)

AI agents in company memory systems — Notion, Confluence, internal wikis — create derived artifacts: margin reports, cost summaries, staffing analyses — built from sensitive source documents: payroll registers, vendor contracts, compensation tables.

Standard access control answers the question "can this user read files?" BioVault answers a harder question: **"can this agent surface this derived artifact when one of its source documents contains payroll data that Marketing should never see?"**

Existing approaches fail here:

| Failure mode | Why it fails |
|---|---|
| **Duplicate files into team silos** | Marketing gets a copy of shared files. Changes don't sync. A payroll file copied into a "shared" folder becomes invisible to governance. You cannot revoke a copy you don't track. Silo drift makes revocation unenforceable. |
| **LLM-based sensitivity filtering** | Route every retrieval through a model to classify sensitivity. Token-heavy, latency compounds, models can be confused or prompted into surfacing revoked context. Not a security boundary. |
| **Role-based access control (RBAC)** | Grants access by role ("Marketing can read internal files"). No concept of artifact lineage. Revoking a source does not automatically close access to derived outputs. |

BioVault addresses this with a capability-per-artifact model, transitive lineage tracking, and propagating revocation — all enforced before any model sees any content. **Zero model tokens in the permission path.**

---

## Default demo: SME / company-memory payroll leakage

The default seed scenario:

- **One global company memory store** — no team silos, no copies
- **Marketing** has read access to campaign costs and vendor contracts
- **Marketing does NOT** have access to the Q3 Growth Margin Report — a governed derived artifact synthesised from campaign costs, vendor contracts, and **payroll data**
- **Finance** can read the Q3 report (they have a capability grant and access to payroll)
- **Owner revokes Payroll Salary Register** → Q3 Growth Margin Report is automatically quarantined through lineage
- **Finance is now denied** the Q3 report — `derived_from_revoked_source`
- Every decision is audited with `request_id`, `principal`, `decision`, `reason`, and `latency_ms`

### SME artifacts and lineage

```
campaign_cost_summary   ──┐
vendor_contracts        ──┼──► q3_growth_margin_report  (confidential, derived)
payroll_salary_register ──┘

Revoking payroll_salary_register quarantines q3_growth_margin_report.
```

### Seeding

```
POST /seed?scenario=sme       # default
POST /seed?scenario=biotech   # AI-science pharma R&D (secondary scenario)
```

---

## Secondary demo: AI science / biotech

Switch via the **scenario switcher** in the UI, or seed with `POST /seed?scenario=biotech`. Models a pharmaceutical R&D program where revoking an adverse-event source quarantines derived Phase II memos through the same permission engine.

---

## Architecture

```
Agent / model runtime (any open-weight model)
        │
        │  POST /query  { artifact_id, purpose }
        │  Authorization: Bearer <capability_token>
        ▼
FastAPI  (:8000)
        ├─ resolve_principal()    SHA-256(token) → principal_id
        ├─ evaluate_access()      deterministic SQL/Python  ← 0 model tokens
        ├─ log_audit()            request_id, decision, reason, latency_ms
        └─ decrypt + return       only if decision == allow
        ▼
SQLite  (users, artifacts, capability_grants, lineage_edges, audit_events)
```

Full architecture, compliance matrix, and API reference: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) · Judge demo: [docs/DEMO_SCRIPT.md](docs/DEMO_SCRIPT.md) · Deploy: [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md)

---

## Setup

### Backend

```powershell
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements-dev.txt
uvicorn app.main:app --reload
```

Runs at `http://localhost:8000`. Interactive docs at `/docs`.

### Frontend

```powershell
cd frontend
npm install
npm run dev
```

Runs at `http://localhost:5173`. Override backend URL with `VITE_API_BASE_URL` (see `frontend/.env.example`).

### Tests

```powershell
cd backend
python -m pytest -q
# expected: 20 passed
```

---

## Open-weight model compatibility

BioVault makes **zero model calls** in its permission path. Compatible with any open-weight runtime — Qwen, Llama 3, Mistral, Phi, GLM, etc. The model is outside the permission boundary; agents call `POST /query` before passing content to generation.

---

## Repository layout

```
.
├── README.md              ← you are here
├── backend/               ← FastAPI permission engine
├── frontend/              ← React demo dashboard
├── docs/                  ← architecture, demo script, deployment
├── vercel.json            ← unified Vercel deploy (frontend + backend)
├── UK-AI-Agent-EP5/       ← hackathon event metadata + official submission copy
│   └── submissions/biovault/
├── CONTRIBUTING.md        ← development guide
└── SECURITY.md            ← secrets policy
```

---

## License

MIT — BioVault team. See [LICENSE](LICENSE).
