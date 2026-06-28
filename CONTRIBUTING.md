# Contributing / Submitting a Project

Thanks for taking part! This repo hosts multiple hackathon events. The submission
process below is the same for every event — event-specific rules (theme, bounty,
deadline, judging) live in each event's `README.md`.

## Submission process (PR-per-team)

1. **Fork** this repository to your own GitHub account.
2. In your fork, find the event directory you're submitting to (e.g. `UK-AI-Agent-EP5/`).
3. **Copy** `<event>/submissions/_TEMPLATE/` to `<event>/submissions/<your-team-name>/`.
   - Use a lowercase, hyphenated team name, e.g. `team-rocket`.
4. Build your project inside that folder and fill in its `README.md`.
5. Commit and push to your fork, then open a **Pull Request** into `main` of this repo.
6. A BasedAI reviewer will be requested automatically. Address any feedback and we'll merge.

## Rules

- ✅ **Only touch your own team folder.** PRs that modify other teams' folders or the
  repo's shared files (root README, `.github/`, etc.) will be asked to change.
- ✅ **One folder per team**, named in lowercase-hyphenated form.
- ✅ Your folder's `README.md` must be filled in (see the template) — judges rely on it.
- ✅ Submit **before the event deadline** (see the event README). Late PRs may not be judged.
- 🚫 **Never commit secrets.** No API keys, no `.env` files, no wallet **private keys or
  seed phrases**. Push protection and CI secret scanning will block these — but the safest
  thing is to never add them. See [SECURITY.md](SECURITY.md).

## Local checklist before opening a PR

- [ ] My code is inside `<event>/submissions/<my-team>/` and nowhere else.
- [ ] I included a `.env.example` with placeholder values — **not** a real `.env`.
- [ ] I searched my diff for keys, tokens, mnemonics, and `0x…` private keys.
- [ ] My folder `README.md` explains what it does, how to run it, and includes a demo link.
- [ ] `git log` and the diff contain no secrets, even in earlier commits.

## Questions?

Open an issue using the **"Submission question"** template, or ask a BasedAI mentor at the
event.
