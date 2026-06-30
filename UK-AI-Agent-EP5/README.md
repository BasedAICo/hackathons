# BioVault — UK AI Agent Hackathon EP.5

**BasedAI track:** Enterprise Memory Governance at Scale  
**Team:** BioVault · [@pkaysantana](https://github.com/pkaysantana)  
**Dates:** 28 June – 4 July 2026 · Imperial College London

---

## Submission

BioVault is submitted for the BasedAI track of UK AI Agent Hackathon EP.5.

| Item | Link |
|---|---|
| **Project code** | [../backend](../backend) · [../frontend](../frontend) (repo root) |
| **Official submission folder** | [`submissions/biovault/`](submissions/biovault/) |
| **Judge README** | [`submissions/biovault/README.md`](submissions/biovault/README.md) |
| **Upstream PR** | [BasedAICo/hackathons#3](https://github.com/BasedAICo/hackathons/pull/3) |

## What BioVault does

Deterministic, LLM-free capability enforcement for shared artifact memory. Prevents AI agents from leaking payroll-sensitive lineage into marketing answers — with propagating revocation when a source document is revoked.

**Default demo:** Marketing is denied the Q3 Growth Margin Report (payroll-mixed derived artifact). Owner revokes Payroll Salary Register → Q3 report quarantined → Finance denied. Zero model tokens in the permission path.

## Key dates

| Date | Milestone |
|---|---|
| **3 Jul (end of day)** | Submission deadline |
| **4 Jul** | Demo Day & finals |

## Judging criteria (BasedAI track)

| Criterion | Weight |
|---|---|
| Autonomy & technical execution | 30% |
| Innovation / originality | 25% |
| Use of BasedAI / sponsor tech | 20% |
| Real-world usefulness & impact | 15% |
| Demo & presentation | 10% |

## Quick start

```powershell
cd backend && pip install -r requirements-dev.txt && uvicorn app.main:app --reload
# new terminal:
cd frontend && npm install && npm run dev
```

Open `http://localhost:5173`, click **Seed / Reset Demo**, walk through [docs/DEMO_SCRIPT.md](../docs/DEMO_SCRIPT.md).

## Open-weight compliance

No closed or proprietary models in the permission path. BioVault is model-agnostic — any open-weight agent runtime integrates via `POST /query`.
