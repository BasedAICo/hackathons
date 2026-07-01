# BioVault — Best Case by Demo Day

**Horizon:** now → **4 Jul 2026**  
**Identity:** AI science / biotech R&D memory governance — **not** a payroll SaaS

BioVault was built for **pharma AI agents**: derived clinical memos, adverse-event sources, external CRO access boundaries, and revocation that propagates through scientific lineage. The SME payroll demo proves the engine generalises — it should never be the headline.

---

## One sentence (what you say on stage)

> “BioVault governs what AI science agents can retrieve from shared R&D memory — when we revoke an adverse-event source for data integrity, every derived Phase II memo is quarantined automatically, the external CRO is denied, and every decision is audited with zero LLM calls in the permission path.”

---

## Best-case Demo Day (2 minutes) — biotech

What judges **see**:

1. Live URL opens — dashboard loads **AI Science · pharma R&D** scenario (not payroll)
2. **Seed / Reset Demo** — BVK-14 kinase program artifacts appear
3. **Avery Chen (CEO)** opens **Phase II Readiness Memo** → **ALLOW** — derived from four source documents, content shown
4. **Owen Brooks (External CRO)** attempts same memo → **DENY** — `missing_capability_grant`, no clinical content leaked
5. **Nora Singh (Regulatory Lead)** opens memo → **ALLOW** — same artifact, different capability token
6. Lineage view: adverse_event_memo, biomarker_analysis, … → phase_ii_readiness_memo
7. **CEO revokes Adverse Event Memo** (data integrity) → quarantine cascades to derived memos
8. **CEO** opens Phase II memo again → **DENY** — `derived_from_revoked_source`
9. Audit log: structured `request_id`, principal, latency_ms on every step
10. **Optional:** science agent script asks “Summarise Phase II readiness for the board” as CRO → `/query` deny → no synthesis from protected clinical data

**Under 2 minutes. Deterministic. On-brand for BioVault.**

If a judge asks *“Does this work outside biotech?”* — switch to SME for 30 seconds, then back: *“Same engine. Different seed data.”*

---

## What BioVault is (and isn’t)

| BioVault **is** | BioVault **is not** |
|---|---|
| Governance gate for **scientific artifact memory** | A payroll / HR product |
| Lineage-aware revocation for **derived clinical memos** | RBAC with a React skin |
| Capability tokens for **CEO / CRO / Regulatory** roles | LLM-based sensitivity filtering |
| Audit trail for **regulated R&D contexts** | A full autonomous agent (yet) |
| LLM-free permission path | Open-weight model integrated (yet — optional stretch) |

---

## Best-case product shape

```
┌──────────────────────────────────────────────────────────────┐
│  React dashboard                                              │
│  · DEFAULT: BVK-14 biotech demo (6 steps)                    │
│  · lineage · audit · compliance matrix                       │
│  · optional: SME switcher ("cross-industry proof")           │
└────────────────────────────┬─────────────────────────────────┘
                             │ POST /query
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  BioVault gate (FastAPI)                                      │
│  · capability per (principal, artifact, operation)           │
│  · lineage integrity on every read                           │
│  · revoke source → BFS quarantine descendants                │
│  · 0 LLM calls in permission path                            │
└────────────────────────────┬─────────────────────────────────┘
                             │ plaintext only if allow
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  AI science agent (OPTIONAL stretch)                          │
│  · e.g. Ollama — only after BioVault allow                   │
│  · CRO agent demo: deny on Phase II memo = no clinical leak  │
└──────────────────────────────────────────────────────────────┘
```

---

## Feature tiers

### Tier 1 — Must ship (credible + on-brand)

| Feature | Status | Demo Day role |
|---|---|---|
| Biotech seed (BVK-14, Phase II memo, adverse event) | ✅ Built | **Primary story** |
| CRO deny / regulatory allow / CEO revoke cascade | ✅ Built | Core proof |
| Lineage + audit | ✅ Built | Regulated R&D trust |
| **Identity restored in docs + default UI** | ❌ TODO | Stops the project feeling lost |
| Live deploy + biotech video | ❌ TODO | Judges can verify |
| PR #3 + honest submission README | ✅ Mostly | First impression |

### Tier 2 — Best case (strong pitch)

| Feature | Why |
|---|---|
| **Biotech-default UI + README** | Coherent identity |
| **CRO agent script** (`POST /query` deny) | “Autonomous agent” without faking it |
| Rehearsed 2-min biotech script | Stage confidence |
| 3 biotech screenshots in README | Visual proof |
| 30-sec SME answer prepared | Generalisation question |

### Tier 3 — Stretch

| Feature | Why |
|---|---|
| “Ask the science agent” UI panel | Shows agent + gate together |
| Ollama summarisation **after** allow | Open-weight compliance with real model |
| Multi-level quarantine visible in UI (exec brief → Phase II) | Already partially in step 4 |

### Not the best case (wrong direction)

- Leading every doc with payroll / Marketing / Q3 margins
- Calling biotech “secondary” anywhere user-facing
- Building SME-specific features instead of biotech polish
- Claiming LLM integration that isn’t wired up

---

## Best-case judge narrative

### Problem (15 sec) — biotech

AI science agents derive Phase II readiness memos from adverse-event reports, biomarker analyses, and trial data. When a source document is revoked for data integrity, **derived memos built from it must not remain accessible** — especially to external CROs. Folder copies and role labels don’t track derivation.

### Approach (15 sec)

BioVault: one canonical R&D artifact store, explicit capabilities per principal and artifact, lineage on every derivation, deterministic revoke propagation, full audit. **No LLM decides access.**

### Proof (60 sec)

Live biotech demo — steps above.

### Why BasedAI track (15 sec)

Enterprise memory governance at scale — in AI science, “memory” is derived clinical knowledge. BioVault enforces **who may surface which derived artifact** before any open-weight model sees it.

### Generalisation (15 sec — only if asked)

“The SME payroll demo uses the identical engine — revoke payroll, quarantine the margin report. Same governance primitive, different industry seed.”

---

## Best-case README (submission)

Judges read this first. It should open with:

1. **BioVault** — governance for AI science / biotech R&D memory
2. Problem: derived clinical artifacts + revocation + external collaborator boundaries
3. Demo: biotech steps + live URL + video
4. How to run + architecture
5. Models: none in permission path; optional agent names open-weight model if built
6. *Then* a short “cross-industry” note on SME — not the lead

---

## Best-case minimal agent (biotech)

**`scripts/demo_agent_cro.py`**

```text
1. Load External CRO bearer token (from POST /seed?scenario=biotech)
2. Question: "Summarise Phase II readiness for the investor deck"
3. Target artifact: phase_ii_readiness_memo
4. POST /query → deny, missing_capability_grant
5. Print: "BioVault denied access to Phase II Readiness Memo. Cannot generate summary."
6. No clinical content printed — ever
```

This is the agent story **for BioVault**, not a marketing chatbot asking about payroll.

---

## Day-by-day best case

| Day | Outcome |
|---|---|
| **30 Jun** | Identity restored (biotech default, README/DEMO_SCRIPT reorder) |
| **1 Jul** | Live deploy; biotech video; CRO agent script |
| **2 Jul** | Three biotech rehearsals; screenshots; submission sync |
| **3 Jul** | PR frozen; submission README is biotech-first |
| **4 Jul** | Stage demo: BVK-14 → CRO deny → revoke → quarantine → audit |

---

## What success looks like

**Minimum:** Biotech-first docs + live biotech demo + video + PR → submission that matches what you built.

**Best case:** Judges remember *“clinical lineage quarantine when adverse events are revoked”* — and optionally *“same engine works for enterprise memory too.”*

**Failure mode to avoid:** Judges think you built a payroll tool with a biotech easter egg.

---

## After Demo Day (vision — still biotech-centred)

- FHIR / ELN connectors for real R&D systems
- Policy: “CRO may never read artifacts with adverse-event lineage”
- Integration with open-weight science agents (literature review, trial design)
- PostgreSQL + KMS for production pharma IT

SME enterprise memory remains a **vertical** — not the product name.

---

*Action list: [TODO.md](TODO.md)*
