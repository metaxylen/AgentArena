# AGENTS.md

Rules for every AI agent (Claude Code, Codex, Cursor, Copilot, ChatGPT, Gemini) and every human working in this repo. The course rules come from the SENG 461 slides (Git_3.pdf, "Git and GitHub Best Practices for a Multi-Developer Web Project"). The project grade looks at the GitHub history itself: commit dates, commit messages, branches and PRs. Keep that history clean.

The teammate you are helping may not know git. You run the git commands for them, explain each one in one plain sentence (in their language, usually Turkish) before you run it, and follow the workflow below exactly.

## Project

AgentArena: a live platform where AI agents compete on the same task. Each agent is an LLM with tools (web search, Python execution, file reading); a judge agent and user votes score accuracy, speed and cost; results feed a leaderboard.

## Course rules (hard constraints)

1. The project solves a problem using AI.
2. Backend is **Flask** (the course allows Flask or FastAPI; this team chose Flask).
3. Frontend is **pure HTML, CSS and JavaScript. Bootstrap is the only library allowed.** No React, Vue, Svelte, jQuery, Tailwind, Chart.js, Three.js, npm frontend packages or build tools. Draw charts and animations with plain JS (Canvas, SVG, CSS).
4. Python libraries on the backend are fine; add each one to `requirements.txt` in the same PR that starts using it.

## Layout and ownership

```
src/api/        Flask app: routes/, models/, services/, config.py
src/agents/     agents, judge, runner and tools/
src/frontend/   templates/ (Jinja HTML) and static/ (css, js, img)
tests/          pytest tests
docs/           project documents
.github/        CODEOWNERS, PR and issue templates
```

`.github/CODEOWNERS` lists who owns each area; GitHub auto-requests their review. Work inside your own area. Keep files small and split by feature (one route file, one JS file, one CSS file per page) so two people rarely edit the same file.

**Shared files** (`src/api/__init__.py`, `src/api/config.py`, `src/frontend/templates/base.html`, `src/frontend/static/css/main.css`, `src/frontend/static/js/main.js`, `requirements.txt`, `run.py`): before editing one, tell the human to announce it in the team chat, and keep the change in its own small PR.

## Workflow: GitHub Flow

- `main` is always working and deployable.
- Every change happens on a **short-lived branch created from `main`** and merges back **only through a Pull Request** with at least 1 approval.
- One branch holds one small, focused piece of work, linked to one GitHub issue.

### Branch age

| Branch age | Status |
|---|---|
| under 1 to 2 days | ideal |
| 3 to 5 days | fine |
| 1 to 2 weeks | warning: split the work or merge what is ready |
| more than 2 weeks | problem: high conflict risk, stale code |

### Step 0: once per computer

```bash
git config user.name "<teammate's full name>"
git config user.email "<email of the teammate's GitHub account>"
gh auth status          # GitHub CLI login; if missing: gh auth login
```

Every commit must carry the teammate's own name and GitHub email, because each person's contribution is graded from the history. Commits stay authored by the human: no AI co-author or "Generated with" lines in commit messages or PR descriptions.

### Step 1: start a task

1. The task has an issue on GitHub. If not, create one: `gh issue create --title "feat: <short goal>" --body "<details>"`. Note its number `N`.
2. Get the latest `main` and branch from it:

```bash
git checkout main
git pull origin main
git checkout -b feature/N-short-name
```

Branch prefixes: `feature/` new feature, `fix/` bug fix, `docs/` documents, `refactor/` restructure without behaviour change, `test/` tests, `chore/` config and tooling. Use lowercase words joined by `-`.

### Step 2: work and commit small

Commit after each logical change, not at the end of the day:

```bash
git status
git add <the files of this change>
git commit -m "type(scope): short summary"
```

Commit message format (Conventional Commits, as in the slides: `feat(ui): add search bar component`):

- `type`: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `style`
- `scope`: the area, e.g. `ui`, `api`, `agents`, `judge`, `db`
- summary: imperative, lowercase, under ~70 characters, says what changed

Each commit is **atomic** (one logical change) and **works**: run the app or tests before committing. Check `git status` and `git diff --staged` before every commit so only the intended files go in.

### Step 3: stay in sync with main (at least daily)

```bash
git fetch origin
git rebase origin/main      # or: git merge origin/main
```

Rebasing rewrites only your own branch, which is safe while nobody else works on it. If someone else also pushes to your branch, use `git merge origin/main` instead.

### Step 4: push and open a PR

```bash
python -m pytest            # tests pass before every push
git push -u origin feature/N-short-name
```

After a rebase, a branch that was already pushed needs `git push --force-with-lease`, and only on your own branch.

Open the PR:

```bash
gh pr create --base main --fill
```

Then edit the description to complete `.github/pull_request_template.md`: what, why, how to test, screenshots for UI changes, and `Closes #N` so the issue closes on merge. Keep the PR under ~400 changed lines; split bigger work into several PRs.

### Step 5: review and merge

- The reviewer checks logic, tests, security and readability, and reviews promptly (same or next day) with constructive comments.
- The author fixes comments with new commits on the same branch and pushes.
- Before merging, the branch must be up to date with `main` (Step 3). Merge on GitHub with the "Create a merge commit" button, or `gh pr merge --merge`.
- After merging, the branch is deleted (the repo does this automatically). Locally:

```bash
git checkout main
git pull origin main
git branch -d feature/N-short-name
```

## Merge conflicts

Conflicts are normal; keep them small by syncing daily and keeping branches short. When git reports a conflict:

1. Run `git status` to list the conflicted files.
2. In each file, find the markers: `<<<<<<< HEAD` (your side), `=======`, `>>>>>>>` (the incoming side). Decide which lines to keep, possibly combining both, and delete every marker line. VS Code's merge editor shows this clearly.
3. If the conflict touches a teammate's code and the right result is unclear, stop and ask the human to check with that teammate before choosing.
4. Run the tests, then `git add <file>` and finish with `git rebase --continue` (during a rebase) or `git commit` (during a merge).
5. To give up and return to the state before: `git rebase --abort` or `git merge --abort`.

## Things to avoid

| Don't | Why |
|---|---|
| commit directly to `main` | bypasses review (main is protected) |
| `git push --force` on `main` or a shared branch | overwrites teammates' work |
| huge "update everything" commits | hard to review and revert |
| commit build output, `venv/`, `__pycache__/` or `node_modules/` | bloats the repo and causes conflicts |
| commit secrets (API keys, `.env`, passwords) | security breach risk |
| leave PRs open for weeks | guarantees conflicts and stale code |
| commit broken, non-working code to a shared branch | breaks everyone's `main` |

Destructive commands (`git reset --hard`, `git clean -fd`, `git branch -D`, `git push --force`, `git checkout -- .`) lose work. Before running one, explain what will be lost and get the human's explicit yes.

## Secrets

Real keys live only in `.env` (gitignored); `.env.example` lists the variable names with empty values. If a secret is ever committed or pushed, **rotate it immediately**: revoke the key in the provider's dashboard and create a new one. Deleting the commit is not enough.

## Releases

- Versions follow semantic versioning: `vMAJOR.MINOR.PATCH` (e.g. `v1.4.2`).
- Tag a release on `main`: `git tag -a v1.4.2 -m "Release 1.4.2"` then `git push origin v1.4.2`.
- Publish it as a GitHub Release with a changelog (new features, bug fixes, breaking changes): `gh release create v1.4.2 --title "v1.4.2" --notes-file <notes>`, and add the same entry to `CHANGELOG.md`.
