# intersynth — Agent Guide

Multi-agent synthesis engine — collects findings from parallel review/research agents, deduplicates, writes verdicts, produces compact summaries. Keeps agent output out of the host context.

## Canonical References
1. 'PHILOSOPHY.md' — direction for ideation and planning decisions.
2. 'CLAUDE.md' — implementation details, architecture, testing, and release workflow.

## Execution Rules
- Keep changes small, testable, and reversible.
- Run validation commands from 'CLAUDE.md' before completion.
- Commit only intended files and push before handoff.
