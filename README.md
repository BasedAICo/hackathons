# BioVault

**Capability-secured artifact memory for AI science agents.**

Deterministic, lineage-aware, LLM-free permission enforcement for shared R&D memory — pharma/biotech first.

Hackathon MVP · **BasedAI Enterprise Memory Governance at Scale** · [UK AI Agent Hackathon EP.5](UK-AI-Agent-EP5/)

**Team:** BioVault · [@pkaysantana](https://github.com/pkaysantana) (Don Aborah)  
**Official submission PR:** [BasedAICo/hackathons#3](https://github.com/BasedAICo/hackathons/pull/3)

> Educational prototype only — not production security or clinical decision support.

---

## What this is

BioVault is a **permission gate + demo dashboard** for shared scientific artifact memory. It is **not** an AI agent and **does not call any LLM**.

| Component | What it does |
|---|---|
| **FastAPI backend** | SQLite store of users, encrypted artifacts, capability grants, lineage edges, audit log |
| **Permission engine** | Deterministic checks: bearer token → grant on artifact → lineage sources all active? |
| **Revocation** | Revoking a source (e.g. adverse-event memo) quarantines derived artifacts via BFS lineage |
| **React dashboard** | Click-through biotech demo: CRO deny, revoke, quarantine cascade, audit trail |
| **`POST /query`** | Agent-shaped read endpoint — **nothing in this repo calls it with a model yet** |

---

## Default demo: BVK-14 pharma R&D

Seed with **Seed / Reset Demo** (loads biotech by default), or `POST /seed?scenario=biotech`.

**Story:** An AI science program (kinase BVK-14) derives a **Phase II Readiness Memo** from source documents including an **Adverse Event Memo**. External CRO must not access the derived memo. When the adverse-event source is revoked for data integrity, the Phase II memo quarantines automatically.

```
public_target_paper ──┐
internal_sar_table  ──┼──► phase2_readiness_memo  (derived, confidential)
toxicity_report     ──┤
adverse_event_memo  ──┘
```

| Step | Principal | Action | Result |
|---|---|---|---|
| 1 | Avery Chen (CEO) | Read Phase II Readiness Memo | **ALLOW** |
| 2 | Owen Brooks (External CRO) | Read Phase II Readiness Memo | **DENY** — no capability grant |
| 3 | Nora Singh (Regulatory Lead) | Read Phase II Readiness Memo | **ALLOW** |
| 4 | CEO | Revoke Adverse Event Memo | Quarantine propagates to derived memos |
| 5 | CEO | Read Phase II memo again | **DENY** — `derived_from_revoked_source` |

Every decision logged with `request_id`, `principal_id`, `decision`, `reason`, `latency_ms`.

Full walkthrough: [docs/DEMO_SCRIPT.md](docs/DEMO_SCRIPT.md)

---

## Cross-industry demo (optional)

`POST /seed?scenario=sme` or switch to **Cross-industry (SME / payroll)** in the UI — same engine, BasedAI workshop parallel (payroll → margin report leakage). Use if a judge asks “does this generalise?”

---

## What's in the code

### API (`backend/app/main.py`)

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/health` | — | Liveness |
| POST | `/seed?scenario=biotech\|sme` | — | Reset DB; **biotech default**; returns bearer tokens once |
| GET | `/artifacts/{id}` | Bearer | Read with permission check + audit |
| POST | `/query` | Bearer | Agent gate — content only if allowed |
| POST | `/artifacts/{id}/revoke` | Bearer | Revoke source + quarantine descendants |
| GET | `/lineage/{id}`, `/audit`, `/metrics/permission-latency` | — | Lineage, audit, latency |

### Tests

```powershell
cd backend
python -m pytest -q   # 20 passed — core engine (biotech seed) + SME scenario tests
```

---

## How to run

```powershell
# Backend
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements-dev.txt
uvicorn app.main:app --reload

# Frontend
cd frontend
npm install
npm run dev
```

Open `http://localhost:5173` → biotech demo loads automatically.

Deploy: [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md) · Planning: [docs/TODO.md](docs/TODO.md) · [docs/BEST_CASE.md](docs/BEST_CASE.md)

---

## Models

No LLM in the permission path. `POST /query` is where an external open-weight agent would check access before generation — not wired up in this MVP.

---

## License

MIT — BioVault team. See [LICENSE](LICENSE).
