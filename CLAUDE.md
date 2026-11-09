# Claude Rules

## Principles
Don't assume: ask, with suggested options or let me type my reason (never guess why I built something). Minimum code. Touch only what you must. Define success criteria.

## Case Studies
Every case study opens with a START block (Situation, Task, Action, Result, Takeaway) and a "Jump to START" link in the header. Situation and motive come from me; the rest from what was built. Standard: `docs/conventions.md`.

## Context
- 70–80%: `/compact`. 80%+: `/clear` mandatory.
- Cost order, cheapest first: hooks, sub-agents, skills, MCP, CLAUDE.md (loaded every request, keep short).

## Files
Read `docs/conventions.md` before creating any file. No new top-level folder if an existing one fits. `/structure` prints the tree.

## Governance
- Follow `docs/git-workflow.md` for all commits, PRs, merges.
- Commands: `/kickoff` `/branch` `/changes` `/review` `/commit` `/push` `/compact` `/structure`.
- **Review gate:** before proposing ANY commit, run `/review` via the Skill tool and show its full output. Never call a change reviewed or "clean" unless it ran on these exact changes. Every commit proposal states `Review: <PASS|BLOCK>, run <just now>`. No review result shown = no commit, push or merge.
- After any edits, and before asking to commit, push, merge or deploy, run `/changes` then `/review` via the Skill tool on the current tree. Edits after a review need a new one. Don't wait to be asked.
- No `git add`, `commit` or `push` until the user has seen the diff (`/changes`) and approved. Leave changes unstaged.
- No secrets; empty placeholders only.
- If a requirement is ambiguous or has multiple valid paths, stop and ask.

---

## Project
appcomplish.click — cloud engineering portfolio for Nurudeen Adeniyi: AWS and DevOps projects as case studies with decisions, tradeoffs and diagrams.

Static HTML/CSS/JS, no build step or framework. Site files live in `src/` (deploy root). Hosted on S3 + CloudFront; GitHub Actions syncs `src/` to S3 on push to `main` (OIDC role, no stored keys). Real values live in the GitHub `production` environment; `.env` stays local and uncommitted. See `docs/aws-deploy-setup.md`.
