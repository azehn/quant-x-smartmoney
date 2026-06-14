# Project Documentation

This directory records durable project knowledge for humans, Codex, Claude Code,
and future maintainers.

The goal is to preserve decisions and execution plans without requiring future
agents to reconstruct the full chat history.

## Directory Structure

- `plans/`: step-by-step implementation plans and roadmaps.
- `adr/`: architecture decision records. Use these for decisions that affect the
  shape of the system over time.
- `ai-sessions/`: concise summaries of AI-assisted planning or implementation
  sessions.
- `worklog/`: implementation notes for completed or in-progress work.

## How Future Agents Should Use These Docs

1. Read README.md, AGENTS.md, and CLAUDE.md when applicable.
2. Read the latest roadmap in `docs/plans/`.
3. Read relevant ADRs in `docs/adr/` before making architecture changes.
4. Add a short worklog entry when completing a meaningful milestone.
5. Add or update an ADR when making a decision that changes architecture,
   deployment, authentication, storage, or product direction.

## Documentation Rules

- Keep docs factual and actionable.
- Record decisions, tradeoffs, and validation results.
- Do not paste long raw chat transcripts.
- Prefer high-signal summaries that future agents can execute.
- Keep code behavior changes in commits; keep reasoning and plans here.
