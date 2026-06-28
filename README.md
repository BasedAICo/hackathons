# BasedAI Hackathons

This is the public home for hackathon tracks **sponsored by [BasedAI](https://basedai.co)**.
Each event lives in its own directory, and participants submit their projects here via Pull Request.

> ⚠️ **This is a public repository.** Never commit secrets — API keys, `.env` files, and
> especially **crypto wallet private keys or seed phrases**. See [SECURITY.md](SECURITY.md).

## Events

| Event | When | Status | Submit here |
| ----- | ---- | ------ | ----------- |
| [UK AI Agent Hackathon EP.5](UK-AI-Agent-EP5/) | 28 Jun – 4 Jul 2026 · Imperial College London | 🟢 Open | [`UK-AI-Agent-EP5/`](UK-AI-Agent-EP5/) |

## How submissions work

We use a **PR-per-team** model:

1. **Fork** this repository.
2. Copy the event's `submissions/_TEMPLATE/` folder to `submissions/<your-team-name>/`.
3. Add your project there and open a **Pull Request** into `main`.

Your PR should only touch files inside *your own* team folder. Full instructions are in
[CONTRIBUTING.md](CONTRIBUTING.md), and event-specific rules (bounty, deadline, judging)
are in each event's README.

## Repository layout

```
.
├── README.md             ← you are here
├── CONTRIBUTING.md       ← general submission process (all events)
├── SECURITY.md           ← what not to commit + how to report a leak
├── LICENSE
├── .gitignore            ← repo-wide ignore rules (secrets, build output, …)
├── .gitleaks.toml        ← secret-scanning rules (incl. crypto keys)
├── .github/              ← CI, CODEOWNERS, PR/issue templates
└── <EVENT-NAME>/         ← one directory per hackathon
    ├── README.md         ← event rules, bounty, deadline, judging
    └── submissions/
        ├── _TEMPLATE/    ← copy this to start your submission
        └── <team-name>/  ← your project
```

## License

Repository scaffolding is licensed under [MIT](LICENSE). **Each submission remains the
property of its authors** — teams may include their own `LICENSE` file inside their folder.
