# BioVault

**Team:** BioVault  
**Members:** [@pkaysantana](https://github.com/pkaysantana) (Don Aborah)  
**Event/Track:** UK-AI-Agent-EP5 — BasedAI track (Enterprise Memory Governance at Scale)

## What it does

BioVault governs **AI science / biotech R&D memory**: shared artifacts (toxicity reports, adverse-event memos, SAR tables) from which AI agents derive clinical memos. Role-based access cannot answer: *should an external CRO's agent retrieve a Phase II readiness memo derived from a source we just revoked for data integrity?*

BioVault implements a **permission gate**:

- **Capability** per `(principal, artifact, operation)` — not role alone
- **Lineage** on every derived artifact
- **Revocation** quarantines all derived descendants (BFS)
- **Audit** on every allow/deny with `request_id`

No LLM in the permission path. FastAPI + React demo UI. `POST /query` is the hook for external agents.

**Default demo (BVK-14):** CEO reads Phase II memo → CRO **denied** → Regulatory allowed → CEO revokes adverse-event memo → Phase II memo **quarantined**.

**Cross-industry:** SME payroll scenario via UI switcher or `POST /seed?scenario=sme` — same engine.

## Demo

- 🎥 Video: _not yet recorded_ (record biotech walkthrough)
- 🌐 Live demo: _not yet deployed_ — [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md)
- 🖼️ Screenshots: CRO deny, quarantine badge, audit log — from biotech flow

## How to run it

```powershell
cd backend && pip install -r requirements-dev.txt && uvicorn app.main:app --reload
cd frontend && npm install && npm run dev
```

Open `http://localhost:5173` — biotech seeds on load. `python -m pytest -q` in backend.

## How it works

```
Caller (UI or POST /query) + Bearer token
        ▼
FastAPI — resolve_principal → evaluate_access → log_audit → decrypt if allow
        ▼
SQLite — artifacts, grants, lineage_edges, audit_events
```

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) · [docs/DEMO_SCRIPT.md](docs/DEMO_SCRIPT.md)

## Tech & sponsor APIs used

- **Models / LLMs:** None in permission path. Optional future agent via `POST /query`.
- **Frameworks:** FastAPI, React + Vite
- **BasedAI track:** Enterprise memory governance — artifact capabilities, lineage, audit
- **Other:** SQLite, cryptography (Fernet), pytest

## What's next

- Open-weight science agent calling `POST /query` before summarisation
- Live Vercel deploy + demo video
- PostgreSQL, KMS for production

## License

MIT — BioVault team. Hackathon MVP only.

---

> 🔒 Never commit secrets. See [../../../SECURITY.md](../../../SECURITY.md).
