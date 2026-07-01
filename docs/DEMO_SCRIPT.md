# BioVault Demo Script

> Audience: Hackathon judges · **BasedAI Enterprise Memory Governance at Scale**

**BioVault is AI science / biotech R&D memory governance.** The default demo is the BVK-14 kinase program: derived Phase II memos, external CRO denial, adverse-event revocation with lineage quarantine.

The SME payroll scenario is a **cross-industry proof** (same engine) — use only if a judge asks “does this generalise?”

---

## Setup (30 seconds)

1. Backend: `uvicorn app.main:app --reload` (port 8000)
2. Frontend: `npm run dev` (port 5173)
3. Open `http://localhost:5173` — **biotech auto-seeds on load**
4. If empty, click **↺ Seed / Reset Demo**
5. Point to **Flow Banner**: `Bearer token → resolve_principal() → evaluate_access() → log_audit() → Decrypt (allow only)` — no LLM in this chain

---

## Default scenario: BVK-14 pharma R&D

### The problem (15 sec)

> "An AI science agent synthesises a Phase II Readiness Memo from toxicity data, SAR tables, and an adverse-event memo. An external CRO must not access that derived memo. When the adverse-event source is revoked for data integrity, every derived clinical artifact that included it must quarantine — automatically, auditable, without routing access decisions through an LLM."

### Why NOT the alternatives?

Point to **Comparison Cards** (biotech copy):

- **Silo copies:** Stale adverse-event copies in partner folders — revoke the canonical source, copies persist
- **LLM filtering:** Token-heavy, non-deterministic, not a security boundary
- **BioVault:** One R&D store, capability per artifact, lineage propagation, 0 model tokens in permission path

---

## 2-minute demo script (biotech)

### [0:00–0:15] Intro

> "BioVault governs what AI science agents retrieve from shared R&D memory. I'll show you deterministic allow/deny on a derived Phase II memo — and what happens when we revoke an adverse-event source."

### [0:15–0:30] Step 1 — CEO opens Phase II Readiness Memo (ALLOW)

Click **Step 1**.

> "Avery Chen, CEO. Capability token resolved. Read grant on `phase2_readiness_memo`. ALLOW — content decrypted."

Green `allow` banner.

### [0:30–0:45] Step 2 — External CRO attempts same memo (DENY)

Click **Step 2**.

> "Owen Brooks, External CRO. Same artifact. No capability grant. DENY — `missing_capability_grant`. No clinical content returned."

Red `deny` banner. Empty content.

> **Key point:** Deterministic check on artifact ID — not LLM sensitivity filtering.

### [0:45–1:00] Step 3 — Regulatory Lead opens memo (ALLOW)

Click **Step 3**.

> "Nora Singh, Regulatory Lead — has read grant. ALLOW. Same pipeline, different token."

### [1:00–1:20] Step 4 — Revoke Adverse Event Memo

Click **Step 4**.

> "Data integrity issue on the adverse-event source. CEO revokes it. Watch quarantine cascade to derived artifacts — including the Phase II memo and any child derivations in the demo."

Amber `quarantined` badges. Status message with quarantined IDs.

### [1:20–1:35] Step 5 — CEO denied on Phase II memo

Click **Step 5**.

> "CEO — who was allowed a minute ago — is now DENIED. `derived_from_revoked_source`. Lineage sealed the derived memo when the source was revoked."

### [1:35–2:00] Step 6 — Audit + compliance matrix

Click **Step 6**. Scroll to **Audit Log**.

> "Every decision logged: `request_id`, principal, operation, decision, reason, latency_ms. P99 permission check is live — single-digit ms because it's indexed SQL, not a model."

Point to TESTED / LIVE badges on compliance matrix.

---

## 30-second fallback (biotech)

> "BioVault secures AI science memory in three ways: **One** — access is per artifact and token, not job title — CRO denied, Regulatory allowed. **Two** — lineage: revoke adverse-event data, Phase II memo quarantines automatically. **Three** — zero model tokens in the permission path — pure SQL, auditable, any agent calls POST /query before generation."

---

## Appendix: cross-industry SME demo (30 sec)

Switch to **Cross-industry (SME / payroll)** in the scenario switcher.

> "Same engine, enterprise memory parallel: Marketing denied a payroll-mixed Q3 margin report; revoke payroll → report quarantined. BasedAI workshop use case — BioVault's core is R&D governance above."

| Step | Who | Expected |
|---|---|---|
| Marketing | Campaign costs | ALLOW |
| Marketing | Q3 margin report | DENY |
| Finance | Q3 margin report | ALLOW → then DENY after payroll revoke |

---

## Common judge questions

**Q: How is this different from document ACLs?**  
A: ACLs are flat. BioVault checks lineage dynamically — revoke a source, all derived descendants quarantine via BFS.

**Q: LLM sensitivity filtering?**  
A: That puts a model in the security path — token-heavy, jailbreakable. BioVault's permission path is SQL only.

**Q: Bypass token check?**  
A: No `?user_id=` override. `resolve_principal()` on every protected route. Test: `test_user_id_query_param_is_not_authority`.

**Q: Open-weight model integration?**  
A: `POST /query` is the agent gate — call before generation. No model imported in backend today; integration is the hook, not yet wired in this MVP.

**Q: Performance?**  
A: Indexed lineage traversal. P99 under 200ms in tests (`test_permission_latency_p99_under_200ms`).

**Q: Does it work outside biotech?**  
A: Yes — switch to SME scenario. Same engine, different seed graph.
