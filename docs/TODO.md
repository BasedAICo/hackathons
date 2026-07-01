# BioVault — Pre–Demo Day TODO

**Today → submission deadline:** 3 Jul 2026 (end of day)  
**Demo Day:** 4 Jul 2026  
**Official PR:** [BasedAICo/hackathons#3](https://github.com/BasedAICo/hackathons/pull/3)

---

## What BioVault actually is (don’t lose this)

BioVault is **governance for AI science memory** — a capability-secured artifact store for pharma/biotech R&D where AI agents derive clinical memos from source documents, and **revoking a compromised source quarantines every derived artifact downstream**.

That is the project you built first (kinase program BVK-14, Phase II readiness memo, adverse-event revocation, CRO denied). The SME payroll scenario is a **later cross-industry illustration** that the same engine works outside biotech — it is **not** what BioVault is.

**Demo Day story = biotech. SME = optional 30-second “same engine, different industry” if a judge asks.**

---

## 🔴 Restore identity (do this before polish)

- [x] **Default scenario → biotech** — `App.tsx` loads `biotech` on mount; backend `/seed` default is `biotech`
- [x] **README order** — lead with pharma R&D problem; SME in cross-industry section
- [x] **DEMO_SCRIPT.md** — biotech is the 2-minute script; SME is appendix
- [x] **ARCHITECTURE.md** — biotech listed first
- [x] **Submission README** — judge-facing pitch is AI science / clinical derivation
- [x] **UI hero copy** — biotech default; SME switcher labelled cross-industry

Until these are done, every other doc will keep feeling wrong.

---

## 🔴 Must do (submission breaks without these)

### Hackathon admin

- [ ] Confirm [PR #3](https://github.com/BasedAICo/hackathons/pull/3) is open and only touches `UK-AI-Agent-EP5/submissions/biovault/`
- [ ] Submission `README.md` leads with **biotech identity** + accurate team handle
- [ ] Final push to `submit/biovault` before **3 Jul EOD**
- [ ] Smoke test from clean clone → seed **biotech** → demo steps 1–6 ([DEMO_SCRIPT.md](DEMO_SCRIPT.md))

### Demo that judges can actually see

- [ ] **Live deploy** — [docs/DEPLOYMENT.md](DEPLOYMENT.md)
- [ ] After deploy: seed **biotech**, verify CRO deny → revoke adverse event → Phase II memo quarantined
- [ ] Live URL in submission README

### Demo Day presentation

- [ ] **2-minute demo rehearsed** — biotech path:
  1. CEO opens Phase II Readiness Memo → **ALLOW**
  2. External CRO attempts same memo → **DENY**
  3. Regulatory Lead opens memo → **ALLOW**
  4. CEO revokes Adverse Event Memo → quarantine cascades
  5. CEO opens Phase II memo again → **DENY** (`derived_from_revoked_source`)
  6. Audit log + compliance matrix
- [ ] **Demo video** (≤3 min) — **biotech walkthrough**, not payroll
- [ ] Screenshots from biotech flow (CRO deny, quarantine badge, audit row)

---

## 🟠 High impact (strongly recommended)

### Close the “where’s the agent?” gap — biotech framing

- [ ] Minimal **agent script** calling `POST /query` as an **AI science agent** (External CRO token)
- [ ] Script tries to retrieve Phase II Readiness Memo → **denied** → agent refuses to summarise (no clinical leak)
- [ ] Document which open-weight model (if any) — only **after** `/query` allow

| Option | Effort | Notes |
|---|---|---|
| **A. CLI agent** (httpx, CRO token) | ~2 hrs | On-brand, easy to demo |
| **B. “Science agent preview” in UI** | ~4–6 hrs | Shows `/query` in dashboard |
| **C. Ollama + scripted clinical question** | ~1–2 days | Strongest, only if Tier 1 done |

- [ ] Pick **one** — biotech scenario only for the agent demo

### Pitch materials

- [ ] One-liner memorised: *“When adverse-event data is revoked for integrity, BioVault quarantines every AI-derived clinical memo that included it — deterministically, audited, no LLM in the permission path.”*
- [ ] 30-sec **SME backup** rehearsed (only if judge asks “does this generalise?”)
- [ ] Name consistency across READMEs (Santana / Aborah)

### Tests & stability

- [ ] `python -m pytest -q` — 20 passed
- [ ] `npm run build` — clean
- [ ] Re-seed on deployed URL after cold start

---

## 🟡 Nice to have

- [ ] Repo About: “BioVault — lineage-aware governance for AI science memory”
- [ ] Architecture diagram centred on **clinical derivation** flow
- [ ] Lineage panel screenshot showing adverse_event_memo → phase_ii_readiness_memo chain

---

## ✅ Already done

- [x] Permission engine (capabilities, lineage, revoke propagation, audit)
- [x] Biotech seed data (BVK-14, Phase II memo, adverse event, CRO/CEO/regulatory principals)
- [x] Biotech 6-step demo flow in UI
- [x] SME scenario as additional seed (engine proof — keep, don’t lead with it)
- [x] 20 pytest tests (core + biotech + SME)
- [x] Submission folder + PR #3
- [x] Deploy configs

---

## Suggested schedule

### 30 Jun – 1 Jul — restore identity + deploy

| Task | Est. |
|---|---|
| Flip default to biotech (code + README + DEMO_SCRIPT) | 2–3 hrs |
| Vercel deploy + biotech smoke test | 1–2 hrs |
| Record **biotech** demo video | 1 hr |
| CRO agent script (option A) | 2 hrs |

### 2 Jul — freeze

| Task | Est. |
|---|---|
| Rehearse biotech demo ×3 on live URL | 1 hr |
| Sync submission folder → push `submit/biovault` | 30 min |
| Biotech screenshots for README | 30 min |

### 3 Jul — submit

| Task | Est. |
|---|---|
| PR #3 final check | 30 min |
| Submission README = biotech-first | 15 min |

### 4 Jul — Demo Day

| Task | |
|---|---|
| Live biotech demo, ≤2 min | |
| Local backup running biotech seed | |
| SME answer ready if asked “other industries?” | |

---

## Identity checklist (quick audit)

Before you ship anything, ask:

| Question | Should be |
|---|---|
| What industry is the hero? | Pharma / AI science R&D |
| What artifact gets quarantined? | Phase II Readiness Memo |
| What source gets revoked? | Adverse Event Memo |
| Who is denied access? | External CRO (not Marketing) |
| What is SME? | Optional cross-industry proof, not the brand |
| What track is this? | BasedAI Enterprise Memory Governance — via **scientific artifact lineage** |

---

## Blockers

| Decision | Pick by |
|---|---|
| Confirm biotech as default everywhere | **Today** |
| Agent demo: CLI vs UI vs Ollama | 1 Jul |
| Live URL vs video-only fallback | 1 Jul |

---

*Last updated: 30 Jun 2026*
