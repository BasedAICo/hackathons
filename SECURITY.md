# BioVault Security Policy

This is a **public repository**. Anything committed here is visible permanently. See [CONTRIBUTING.md](CONTRIBUTING.md) for development practices.

## Never commit secrets

Do **not** commit:

- API keys, access tokens, passwords
- `.env` files or live credentials
- Private TLS/SSH keys (`*.pem`, `*.key`, …)
- Crypto wallet private keys or seed phrases

BioVault does not require LLM API keys in the permission path. Demo capability tokens are minted at seed time via `POST /seed` — acceptable for hackathon demo only, not production.

### Use `.env.example` instead

```bash
# backend/.env.example  ✅ commit this
CORS_ALLOWED_ORIGINS=http://localhost:5173
DEMO_ALLOW_ALL_CORS=false
```

Keep real values in local `.env` files (gitignored).

## BioVault demo limitations

| Item | Demo behaviour | Production requirement |
|---|---|---|
| Fernet encryption key | Hardcoded in source | KMS / secrets manager |
| Capability tokens | Returned in `POST /seed` response | Out-of-band delivery |
| SQLite | Single-writer, ephemeral on serverless | PostgreSQL + connection pool |
| Token revocation | Not implemented | Rotation + expiry endpoints |

## I committed a secret — what now?

1. **Rotate / revoke immediately** — removing the commit does not un-leak it.
2. Remove from code and history.
3. For upstream repo leaks, use [BasedAICo security reporting](https://github.com/BasedAICo/hackathons/security/advisories/new).

## Reporting

Found a vulnerability in BioVault? Open a private security advisory on this repo or contact the team via [@pkaysantana](https://github.com/pkaysantana).
