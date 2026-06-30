# BioVault

**Prevent AI agents from leaking sensitive company memory into the wrong answers.**

Hackathon MVP for the **BasedAI Enterprise Memory Governance at Scale** track · [UK AI Agent Hackathon EP.5](UK-AI-Agent-EP5/)

**Team:** BioVault · [@pkaysantana](https://github.com/pkaysantana) (Don Aborah)  
**Official submission PR:** [BasedAICo/hackathons#3](https://github.com/BasedAICo/hackathons/pull/3)

> Educational prototype only — not production security or clinical software.

---

## What this actually is

BioVault is a **permission gate + demo dashboard** for a shared artifact store. It is **not** an AI agent and **does not call any LLM** — open-weight or otherwise.

What you get in this repo:

| Component | What it does |
|---|---|
| **FastAPI backend** | SQLite store of users, encrypted artifacts, capability grants, lineage edges, audit log |
| **Permission engine** | Deterministic checks: does this bearer token have a grant on this artifact? Are all lineage sources still active? |
| **Revocation** | Revoking a source quarantines derived artifacts that depend on it (BFS through lineage) |
| **React dashboard** | Click-through demo of allow/deny/revoke with audit trail and lineage view |
| **`POST /query`** | Same permission check as a read, JSON shaped for an external caller — **nothing in this repo calls it with a model** |

There is no agent loop, no model runtime, and no integration with Notion/Confluence/etc. The problem framing is the *use case the gate is designed for*; the code implements the gate and a UI to prove it works.

---

## Default demo: SME payroll leakage

Seed with **Seed / Reset Demo** in the UI, or `POST /seed?scenario=sme`.

**Story:** Marketing should not see the Q3 Growth Margin Report because it was derived from payroll data. Finance can see it — until payroll is revoked.

```
campaign_cost_summary   ──┐
vendor_contracts        ──┼──► q3_growth_margin_report  (derived, confidential)
payroll_salary_register ──┘
```

| Step | Principal | Action | Result |
|---|---|---|---|
| 1 | Morgan Chen (Marketing) | Read Campaign Cost Summary | **ALLOW** |
| 2 | Morgan Chen (Marketing) | Read Q3 Growth Margin Report | **DENY** — no capability grant |
| 3 | Jordan Lee (Finance) | Read Q3 Growth Margin Report | **ALLOW** |
| 4 | Alex Kim (Owner) | Revoke Payroll Salary Register | Q3 report → **quarantined** |
| 5 | Jordan Lee (Finance) | Read Q3 report again | **DENY** — `derived_from_revoked_source` |

Every decision is written to the audit log with `request_id`, `principal_id`, `decision`, `reason`, and `latency_ms`.

**Secondary scenario:** `POST /seed?scenario=biotech` — pharma R&D with adverse-event revocation. Same engine, different seed data. Switch in the UI scenario picker.

Full walkthrough: [docs/DEMO_SCRIPT.md](docs/DEMO_SCRIPT.md)

---

## What's in the code

### API endpoints (`backend/app/main.py`)

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/health` | — | Liveness |
| POST | `/seed?scenario=sme\|biotech` | — | Reset DB, load demo data, return bearer tokens once |
| GET | `/users`, `/artifacts` | — | List principals / artifact metadata (no content) |
| GET | `/artifacts/{id}` | Bearer token | Read with permission check + audit |
| POST | `/artifacts/{id}/grant` | Bearer token | Delegate a capability (needs `grant` authority) |
| POST | `/artifacts/{id}/revoke` | Bearer token | Revoke source + quarantine descendants |
| POST | `/query` | Bearer token | Agent-shaped read — returns content only if allowed |
| POST | `/derive` | Bearer token | Create derived or redacted artifact with lineage |
| GET | `/lineage/{id}` | — | Parent/child graph for an artifact |
| GET | `/audit` | — | Audit log |
| GET | `/metrics/permission-latency` | — | p99 / p95 / mean from audit events |

Identity comes from the **bearer token only** — a `?user_id=` query param has no authority.

### Tests (`backend/tests/test_biovault.py`)

20 tests covering allow/deny matrix, grant authority, redaction rules, multi-level revocation, audit structure, SME payroll scenario, and permission latency.

```powershell
cd backend
python -m pytest -q
```

---

## How to run

**Prerequisites:** Python 3.11+, Node.js 18+

```powershell
# Terminal 1 — backend
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements-dev.txt
uvicorn app.main:app --reload
# → http://localhost:8000  (/docs for Swagger)

# Terminal 2 — frontend
cd frontend
npm install
npm run dev
# → http://localhost:5173
```

Click **Seed / Reset Demo**, pick a principal, click through the demo steps.

Optional env files: `backend/.env.example`, `frontend/.env.example`. Deploy notes: [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md).

---

## Hackathon track note (models)

BioVault's permission path is **pure Python + SQL — no model calls at all**. That satisfies the spirit of "no closed proprietary models in the loop" because there is no model in the loop.

`POST /query` is the hook where an **external** open-weight agent runtime would check access before using artifact content — but wiring up Qwen/Llama/Mistral/etc. is **not implemented in this repo**. The demo proves the gate; a model would sit outside it.

---

## Repo layout

```
backend/app/main.py    ← all server logic (~1100 lines)
backend/tests/         ← pytest suite
frontend/src/App.tsx   ← demo dashboard
docs/                  ← architecture, demo script, deployment
UK-AI-Agent-EP5/       ← hackathon submission copy for official PR
```

Architecture detail: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)

---

## Known demo limitations

- Fernet key is hardcoded in source — not production-safe
- `POST /seed` returns plaintext bearer tokens in the HTTP response
- SQLite — single-writer, data may not persist on serverless redeploy
- No token rotation or expiry enforcement

---

## License

MIT — BioVault team. See [LICENSE](LICENSE).
