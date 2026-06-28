# Security Policy

This is a **public repository**. Anything committed here — including in deleted files or
earlier commits in your history — is visible to everyone, forever. Treat every commit as
permanent and public.

## Never commit secrets

Do **not** commit any of the following, even temporarily:

- API keys, access tokens, OAuth client secrets, passwords
- `.env` files or any file containing live credentials
- Private TLS/SSH keys (`*.pem`, `*.key`, `id_rsa`, …)
- **Crypto wallet private keys, keystores, or BIP-39 seed / recovery phrases**

Several event sponsors are web3 / crypto projects (e.g. Bittensor, BasedAI, Solana, Kaspa).
A leaked wallet key or seed phrase can drain real funds **within seconds** of being pushed.
Use a fresh, empty, throwaway wallet for hackathon work if you need on-chain access.

### Use `.env.example` instead

Commit a `.env.example` with placeholder values and document the variables. Keep the real
`.env` local — it's already in [`.gitignore`](.gitignore).

```bash
# .env.example  ✅ commit this
ANTHROPIC_API_KEY=your-key-here
WALLET_PRIVATE_KEY=do-not-put-a-real-key-here
```

## Layers of protection (and their limits)

This repo enables:

- **GitHub Secret Scanning + Push Protection** — blocks many known key formats *at push time*.
- **gitleaks in CI** — scans every Pull Request, including crypto-key patterns
  (see [`.gitleaks.toml`](.gitleaks.toml)).

These are a safety net, **not** a guarantee. Novel or obfuscated secrets can slip through.
You are responsible for keeping secrets out of your commits.

## I committed a secret — what now?

A secret pushed to a public repo must be treated as **compromised immediately**.

1. **Rotate / revoke it first.** Delete the API key, or **move all funds out of the wallet
   and abandon it.** Do this before anything else — removing the commit does not un-leak it.
2. Then remove it from the code and history (`git rm`, rewrite history, or open a fresh PR).
3. If it appears in this repo, contact a maintainer (below) so we can help purge it.

## Reporting a vulnerability or an exposed secret

Found a leaked secret in someone's submission, or a security issue with the repo itself?
Please **do not open a public issue.** Use GitHub's
[private vulnerability reporting](https://docs.github.com/en/code-security/security-advisories/guidance-on-reporting-and-writing-information-about-vulnerabilities/privately-reporting-a-security-vulnerability)
("Report a vulnerability" under the Security tab) or email **security@basedai.co**.

<!-- TODO(maintainer): confirm the security contact address above. -->
