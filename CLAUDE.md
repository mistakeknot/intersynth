# intersynth

Multi-agent synthesis engine. Collects findings from parallel review/research agents, deduplicates, writes structured verdicts, produces compact summaries. Keeps agent output out of the host context.

## Overview

3 agents, 0 commands, 0 skills, 0 hooks. Companion plugin for Clavain and Interflux.

## Agents

- `intersynth:synthesize-review` — synthesis for code reviews (quality-gates, review, flux-drive)
- `intersynth:synthesize-research` — synthesis for research queries (flux-research)
- `intersynth:synthesize-documents` — synthesis for document clusters (compound, reflect, research → docs/solutions/)

## Verdict Library

`hooks/lib-verdict.sh` provides structured verdict file utilities. Source it from any process:

```bash
source "${INTERSYNTH_ROOT}/hooks/lib-verdict.sh"
verdict_init
verdict_write "agent-name" "verdict" "CLEAN" "haiku" "No issues found"
```

Functions: `verdict_write`, `verdict_read`, `verdict_parse_all`, `verdict_count_by_status`, `verdict_get_attention`, `verdict_clean`, `verdict_total_tokens`.

## Usage Pattern

Instead of the host agent reading N agent output files (flooding context):

```
# Old pattern (floods host context):
# For each agent: Read {OUTPUT_DIR}/{agent}.md → parse findings → deduplicate

# New pattern (host context stays clean):
Task(intersynth:synthesize-review):
  OUTPUT_DIR={dir}
  VERDICT_LIB={path to lib-verdict.sh}
  MODE=quality-gates
  CONTEXT="Reviewing 5 files across Go and TypeScript"
→ Returns: "PASS" or "FAIL: 2 P1 findings" (~10 lines)
→ Writes: {OUTPUT_DIR}/synthesis.md (full report for user)
→ Writes: .clavain/verdicts/{agent}.json (structured verdicts)
```

## Quick Commands

```bash
python3 -c "import json; json.load(open('.claude-plugin/plugin.json'))"  # Manifest check
ls agents/*.md | wc -l  # Should be 3
bash -n hooks/lib-verdict.sh  # Syntax check
```

## Design Decisions (Do Not Re-Ask)

- Two separate agents (review vs research) rather than one generic, because their input/output contracts differ
- Model: haiku by default (synthesis is structuring, not reasoning)
- Verdict files are ephemeral — cleaned at sprint start, not committed
- lib-verdict.sh is the canonical home; clavain keeps a backward-compat copy
