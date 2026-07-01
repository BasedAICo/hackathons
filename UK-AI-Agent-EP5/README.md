# BioVault — UK AI Agent Hackathon EP.5

**BasedAI track:** Enterprise Memory Governance at Scale  
**Team:** BioVault · [@pkaysantana](https://github.com/pkaysantana)  
**Dates:** 28 June – 4 July 2026 · Imperial College London

---

## Submission

| Item | Link |
|---|---|
| **Project code** | [../backend](../backend) · [../frontend](../frontend) |
| **Submission folder** | [`submissions/biovault/`](submissions/biovault/) |
| **Judge README** | [`submissions/biovault/README.md`](submissions/biovault/README.md) |
| **Upstream PR** | [BasedAICo/hackathons#3](https://github.com/BasedAICo/hackathons/pull/3) |

## What BioVault does

**Governance for AI science memory** — capability-secured artifact store for pharma R&D. Derived Phase II memos, adverse-event revocation, external CRO boundaries, full audit. LLM-free permission path.

**Demo Day story:** BVK-14 biotech scenario (6 steps). SME payroll available as cross-industry proof.

## Quick start

```powershell
cd backend && pip install -r requirements-dev.txt && uvicorn app.main:app --reload
cd frontend && npm install && npm run dev
```

Open `http://localhost:5173` — [docs/DEMO_SCRIPT.md](../docs/DEMO_SCRIPT.md)

## Models

No LLM integrated. Permission checks are Python/SQL only. `POST /query` for future external agents.
