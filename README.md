# intersynth

Multi-agent synthesis engine for Claude Code.

## What This Does

When interflux dispatches 5 review agents in parallel, each one writes its findings to a file. Without synthesis, the host agent has to read all 5 files — flooding its context with thousands of tokens of agent output. intersynth sits between the parallel agents and the host, reading their output files, deduplicating findings, writing structured verdicts, and returning a compact 10-line summary.

Two specialized synthesis agents handle the different contracts: `synthesize-review` for code reviews and flux-drive output, `synthesize-research` for flux-research findings. They're separate because review verdicts have different structure than research findings, and one generic synthesizer would do neither well.

## Installation

First, add the [interagency marketplace](https://github.com/mistakeknot/interagency-marketplace) (one-time setup):

```bash
/plugin marketplace add mistakeknot/interagency-marketplace
```

Then install the plugin:

```bash
/plugin install intersynth
```

## Architecture

```
agents/
  synthesize-review.md      Review output synthesis
  synthesize-research.md    Research output synthesis
hooks/
  lib-verdict.sh            Verdict file utilities (write, read, parse, count, clean)
```

Uses Haiku by default — synthesis is structuring and deduplication, not deep reasoning.

`lib-verdict.sh` provides the canonical verdict file format: `verdict_write`, `verdict_read`, `verdict_parse_all`, `verdict_count_by_status`, `verdict_get_attention`, `verdict_clean`, `verdict_total_tokens`. Clavain keeps a backward-compat copy.

## Design Decisions

- Verdict files are ephemeral — cleaned at sprint start, never committed to git
- The 10-line summary constraint is intentional: if synthesis can't compress findings into 10 lines, the agents produced too much noise
- Two agents, not one, because the review and research synthesis contracts are fundamentally different
