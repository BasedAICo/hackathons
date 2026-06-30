# Contributing to BioVault

BioVault is a hackathon MVP for the BasedAI Enterprise Memory Governance track. This guide covers local development and keeping the official submission folder in sync.

## Development setup

```powershell
# Backend
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements-dev.txt
uvicorn app.main:app --reload

# Frontend (separate terminal)
cd frontend
npm install
npm run dev
```

Run tests before pushing:

```powershell
cd backend
python -m pytest -q
```

## Project structure

| Path | Purpose |
|---|---|
| `backend/app/main.py` | FastAPI permission engine, seed data, audit |
| `frontend/src/App.tsx` | Demo dashboard UI |
| `docs/` | Architecture, demo script, deployment |
| `UK-AI-Agent-EP5/submissions/biovault/` | Official hackathon submission copy for [BasedAICo/hackathons PR #3](https://github.com/BasedAICo/hackathons/pull/3) |

## Hackathon submission workflow

The upstream PR into [BasedAICo/hackathons](https://github.com/BasedAICo/hackathons) must **only** change files under `UK-AI-Agent-EP5/submissions/biovault/`.

After editing code at the repo root, sync the submission folder before updating the upstream PR:

```powershell
$src = "."
$dst = "UK-AI-Agent-EP5/submissions/biovault"
robocopy $src $dst /E /XD .git node_modules .venv dist __pycache__ .pytest_cache .vercel UK-AI-Agent-EP5 /XF .env .env.local biovault.db README.md
# Keep the judge-facing README in the submission folder — do not overwrite it with root README
```

Then commit and push branch `submit/biovault` to update [PR #3](https://github.com/BasedAICo/hackathons/pull/3).

## Rules

- Never commit secrets — no `.env`, API keys, or wallet private keys. Use `.env.example` files.
- Keep the submission folder `README.md` judge-focused (what it does, demo, how to run, architecture, open-weight models).
- Run `pytest` before pushing.

## Questions

Open an issue on this repo or ask a BasedAI mentor at the event.
