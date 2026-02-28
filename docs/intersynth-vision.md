# intersynth — Vision and Philosophy

**Version:** 0.1.0
**Last updated:** 2026-02-28

## What intersynth Is

intersynth is a multi-agent synthesis engine. When N agents run in parallel — reviewing code, answering research questions, or analyzing documents — each produces verbose output that the host agent would otherwise need to read in full. intersynth interposes a synthesis step: it reads all agent outputs, validates structure, deduplicates findings, writes a structured verdict per agent, and returns a compact summary (max 15 lines) to the host. The full reports go to disk; the host context stays clean.

Three agents handle three distinct aggregation types: `synthesize-review` for code review runs (quality-gates, flux-drive), `synthesize-research` for parallel research queries, and `synthesize-documents` for collapsing clusters of compound/reflect/research docs into durable `docs/solutions/` entries. A shared library (`lib-verdict.sh`) provides structured verdict file I/O across all three.

## Why This Exists

Multi-agent parallelism is only useful if the host agent can act on the results without being buried in them. Reading 5-10 agent output files sequentially — each 100-300 lines — fills the context window, dilutes signal with duplication, and slows feedback loops. intersynth makes parallel agent output cheap to consume: the host reads a verdict summary, not a transcript.

## Design Principles

1. **Compact by contract.** Synthesis agents return at most 15 lines to the host. Full reports are always written to disk, never inlined. The host decides whether to read the detail.

2. **Disagreement is preserved, not resolved.** When agents conflict on severity, the highest rating wins and all positions are recorded (`severity_conflict`). When agents conflict on a fix, both recommendations are kept, keyed by agent name. The system surfaces disagreement; humans resolve it.

3. **Receipts over narratives.** Each agent produces a verdict JSON file: structured, timestamped, machine-readable. These are the handoff artifacts that downstream tools (beads, quality gates, intercore) consume. Verdicts are ephemeral — cleaned at sprint start, not committed to git.

4. **Haiku for structuring, not reasoning.** Synthesis is sorting, deduplicating, and formatting — not inference. Using the cheapest capable model keeps synthesis fast and focused on its actual job.

5. **Narrow contracts per aggregation type.** Review synthesis, research synthesis, and document synthesis have different input structures, different quality signals, and different output schemas. One agent per type keeps each contract clear and independently evolvable.

## Scope

**Does:**
- Aggregate and deduplicate findings from parallel review/research agents
- Write structured verdict JSON files to `.clavain/verdicts/`
- Produce human-readable synthesis reports to disk (`synthesis.md`, `findings.json`)
- Return compact summaries to the host agent
- Synthesize document clusters into `docs/solutions/` entries (2+ corroborating sources required)
- Auto-create beads from critical findings (opt-in via `INTERSYNTH_AUTO_BEAD=true`)

**Does not:**
- Run the review or research agents itself (that is Clavain and Interflux's job)
- Persist verdict state across sprints
- Make architectural or remediation decisions — it routes findings, not conclusions
- Pick a winner when agents give conflicting recommendations

## Direction

- Richer convergence tracking: the findings timeline feature (`peer-findings.jsonl`) enables detecting when agents influenced each other's conclusions — a stronger signal than independent agreement.
- The document synthesis pipeline (compound → reflect → synthesize-documents → `docs/solutions/`) is the newest capability; hardening its deduplication and provenance tracking is the near-term focus.
- As auto-bead creation matures from opt-in to default, intersynth becomes the bridge between multi-agent output and the task system — closing the loop without requiring human mediation.
