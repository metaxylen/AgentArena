# AgentArena

A live platform where AI agents compete on real tasks. SENG 461 (Web Programming with Python and Git) course project, Ankara Science University.

## Purpose

Choosing which AI model or agent fits a task is usually guesswork. AgentArena gives several agents the same task, streams how each one solves it, and scores the results so the choice rests on evidence.

## Scope

- **Task input**: the user enters a task (research, code fix, summary).
- **Live battle**: 2 to 4 agents solve it at the same time. Each agent is an LLM with the same tools (web search, Python execution, file reading); their steps stream to the browser live.
- **Judging**: a judge agent and user votes score accuracy, speed, token use and cost.
- **Leaderboard**: results accumulate per task type.

## Tech stack

- Backend: Python, Flask
- Frontend: HTML, CSS, JavaScript, Bootstrap (the only library the course allows)
- Tests: pytest

## Project structure

```
src/api/        Flask app (routes, models, services, config)
src/agents/     agents, judge, runner and tools
src/frontend/   HTML templates and static files
tests/          tests
docs/           documents
```

## Setup

> Status: project skeleton. The app is not runnable yet.

```bash
git clone https://github.com/metaxylen/AgentArena.git
cd AgentArena
python3 -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env             # then fill in your own keys
python run.py
```

## Contributing

The team follows GitHub Flow: every change on a short-lived branch from `main`, merged through a reviewed Pull Request. The full rules, including step-by-step git commands, are in [AGENTS.md](AGENTS.md). AI assistants read that file automatically.

## Team

- Alparslan Kılınç
- Doruk Bayram Kılıç
- Yiğit Burak Çetin
