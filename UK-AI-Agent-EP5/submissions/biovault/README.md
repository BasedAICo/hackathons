# BioVault

**Team:** BioVault  
**Members:** Don Aborah ([@pkaysantana](https://github.com/pkaysantana))

**Track:** BasedAI Enterprise Memory Governance at Scale · UK AI Agent EP5

> Educational prototype only — not production security or clinical decision support.

---

## One-line pitch

BioVault is a lineage-secured artifact memory layer for AI science agents.

---

## Problem

AI agents create Phase II memos, CRO handoffs, regulatory summaries, and scientific reports from sensitive sources such as SAR tables, toxicity reports, adverse-event memos, and CRO data. Those derived artifacts can leak source information unless permissions follow lineage.

When a source document is revoked for data integrity, every derived artifact that included it must quarantine automatically. External CROs must not read clinical memos they were never granted. Duplicating files into lab, CRO, and regulatory silos creates drift — revoke the canonical source and stale copies persist.

---

## Approach

BioVault is a **permission gate + demo dashboard** for shared scientific artifact memory. It is **not** an AI agent and **does not call any LLM** in the permission path.

| Mechanism | What it does |
|---|---|
| **Capability-bound artifact access** | Bearer token → `(principal, artifact, operation)` grant before any read |
| **Deterministic permission checks** | Pure SQL permission evaluation — 0 model tokens, sub-millisecond |
| **Lineage tracking** | Derived artifacts record parent sources; every read re-checks parent integrity |
| **Revocation propagation** | Revoke a source → BFS quarantine of all descendants |
| **Structured audit logs** | Every decision logged with `request_id`, principal, operation, reason, latency, provenance |

---

## Demo: BVK-14 kinase programme

Seed with **Seed / Reset Demo** in the UI or `POST /seed`.

```
public_target_paper ──┐
internal_sar_table  ──┼──► phase2_readiness_memo  (derived, confidential)
toxicity_report     ──┤
adverse_event_memo  ──┘
```

| Step | Principal | Action | Result |
|---|---|---|---|
| 1 | Nora Singh (Regulatory Lead) | Read Phase II Readiness Memo | **ALLOW** |
| 2 | Owen Brooks (External CRO) | Read Phase II Readiness Memo | **DENY** — no capability grant, no plaintext |
| 3 | — | Inspect lineage | Four parents → derived memo |
| 4 | Avery Chen (CEO) | Revoke Adverse Event Memo | Quarantine propagates to derived memos |
| 5 | — | Phase II memo quarantined | Amber badges on derived chain |
| 6 | CEO | Read Phase II memo again | **DENY** — `derived_from_revoked_source` |
| 7 | — | Audit log | `request_id`, principal, provenance, latency |

Full walkthrough: [docs/DEMO_SCRIPT.md](docs/DEMO_SCRIPT.md)

---

## Architecture

```
Agent / model runtime (optional open-weight model — after authorization only)
        │
        │  POST /query  { artifact_id, purpose }
        │  Authorization: Bearer <capability_token>
        ▼
FastAPI
        ├─ resolve_principal()     SHA-256(token) → principal_id
        ├─ evaluate_access()       deterministic SQL — 0 model tokens
        │   ├─ principal exists?
        │   ├─ artifact active/redacted?
        │   ├─ capability grant present?
        │   └─ all lineage parents active? (revocation check)
        ├─ log_audit()             request_id, decision, reason, latency_ms
        └─ decrypt + return        only if decision == allow
        ▼
SQLite (local demo persistence)
```

**Flow:** bearer capability token → resolve principal → evaluate_access → lineage/revocation check → audit write → decrypt/read only on allow → optional open-weight model after authorization.

Details: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)

---

## BasedAI requirement mapping

| BasedAI requirement | BioVault evidence |
|---|---|
| Deterministic retrieval-layer access | `evaluate_access()` — indexed SQL, no model in permission path |
| Audit logs | Every allow/deny with `request_id` and structured detail |
| P99 latency | `GET /metrics/permission-latency` — P99 under 200 ms in tests |
| Derived memory lineage | `lineage_edges` with source hashes; checked on every read |
| Source revocation propagation | BFS quarantine on revoke; `derived_from_revoked_source` deny |
| Open-weight compliance | `POST /query` gate — any model calls after authorization only |

---

## Open-weight model compliance

The **permission path is deterministic and model-free**. No closed-model runtime dependencies are required. Optional generation **after** authorization can use open-weight models (Qwen, Llama, Mistral, GLM, BGE, E5, Nomic, and similar).

```python
result = httpx.post(
    "/query",
    json={"artifact_id": "phase2_readiness_memo", "purpose": "board summary"},
    headers={"Authorization": f"Bearer {cro_token}"},
)
if result.json()["decision"] != "allow":
    # Do not call the model — surface denial to user
    ...
```

Nothing in this submission calls a model; `/query` is the integration hook.

---

## How to run

### Backend

```powershell
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements-dev.txt
uvicorn app.main:app --reload
```

API: `http://127.0.0.1:8000` · OpenAPI docs: `http://127.0.0.1:8000/docs`

### Frontend

```powershell
cd frontend
npm install
npm run dev
```

Open `http://localhost:5173` — BVK-14 demo loads automatically.

### Tests and build

```powershell
cd backend
python -m pytest -q

cd ..\frontend
npm run build
```

Copy `.env.example` to `.env` at the submission root for local CORS overrides (optional).

---

## Limitations

- **Prototype** — educational demo, not production security
- **Local SQLite** — single-process; replace with PostgreSQL for production
- **Simulated ACL/revocation events** — no external IAM integration
- **No production HSM/key management** — demo tokens returned once at seed time
- **No full Hirebase integration** — capability tokens issued at seed time
- **No LLM wired up** — permission path is complete; agent generation is optional stretch

---

## Demo video and live demo

| Asset | Link |
|---|---|
| **Live demo URL** | _TBD — add deployed Vercel/Render URL before submission deadline_ |
| **Demo video (≤3 min)** | _TBD — biotech walkthrough: regulatory allow → CRO deny → adverse-event revoke → quarantine → audit_ |
| **Screenshots** | _TBD — CRO deny panel, quarantine badge, audit row with `request_id`_ |

Planning notes: [docs/BEST_CASE.md](docs/BEST_CASE.md) · [docs/TODO.md](docs/TODO.md) · Deploy: [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md)

---

## License

MIT — BioVault team.
