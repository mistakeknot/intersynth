# Plugin Validation Report: intersynth

**Date:** 2026-02-18
**Validator:** claude-opus-4-6 plugin-validator agent
**Plugin path:** `/home/mk/.claude/plugins/cache/interagency-marketplace/intersynth/0.1.1/`

---

## Plugin: intersynth
Location: `/home/mk/.claude/plugins/cache/interagency-marketplace/intersynth/0.1.1/` (symlink to `0.1.0/`)

## Summary

**CONDITIONAL PASS** — The plugin structure is sound, agents are well-defined, and the verdict library is solid. However, there are two critical issues: (1) the cached version (0.1.0) is 4 commits behind the source repo (0.1.1), meaning the symlink `0.1.1 -> 0.1.0` is misleading — the cache contains stale code; and (2) both agents are missing the required `color` frontmatter field.

---

## Critical Issues (2)

### 1. Cache/source version mismatch — stale cached code
- **Files:** `/home/mk/.claude/plugins/cache/interagency-marketplace/intersynth/0.1.1` (symlink to `0.1.0`)
- **Issue:** The symlink `0.1.1 -> 0.1.0` implies that version 0.1.1 is installed, but the actual cached code is from commit `8ca8c97` (the initial sync). The source repo at `/root/projects/Interverse/plugins/intersynth/` is at commit `86768e9` (4 commits ahead), with version 0.1.1 in its `plugin.json`. Key differences:
  - `synthesize-review.md`: cached version is 4,318 bytes (143 lines), source is 5,632 bytes (159 lines) — the source has 5 explicit dedup rules added in commit `ff642f8` that are missing from the cache
  - Source has `README.md` and `PHILOSOPHY.md` that are absent from cache (minor, not functional)
  - Source `plugin.json` says version `0.1.1`, cached says `0.1.0`
- **Fix:** Republish the plugin or manually update the cache. The symlink trick masked a real version gap. Run `/interpub:release` or reinstall from marketplace.

### 2. Agents missing `color` frontmatter field
- **Files:**
  - `agents/synthesize-review.md` — has `name`, `description`, `model` but no `color`
  - `agents/synthesize-research.md` — has `name`, `description`, `model` but no `color`
- **Issue:** Claude Code agent definitions should include a `color` field in frontmatter (one of: blue, cyan, green, yellow, magenta, red). Without it, the agent may use a default or unpredictable color in the UI.
- **Fix:** Add `color: cyan` (or another appropriate color) to both agent frontmatter blocks.

---

## Warnings (4)

### 1. Empty hooks.json
- **File:** `hooks/hooks.json`
- **Content:** `{}` (empty object)
- **Issue:** The manifest declares `"hooks": "./hooks/hooks.json"` but the file contains no hook definitions. This is technically valid (empty hooks don't cause errors) but the manifest reference is misleading. The `lib-verdict.sh` file in the hooks directory is a library, not a hook — it is sourced by agents, not triggered by Claude Code hook events.
- **Recommendation:** Either remove the `"hooks"` field from `plugin.json` (since there are no hooks), or add a comment in CLAUDE.md clarifying that `hooks/` is used for the verdict library, not for Claude Code hooks.

### 2. Plugin manifest version mismatch with source
- **File:** `.claude-plugin/plugin.json`
- **Content:** `"version": "0.1.0"`
- **Issue:** The cached plugin.json says 0.1.0, but the marketplace listing and symlink say 0.1.1. The source repo plugin.json already says 0.1.1. This is a consequence of the stale cache (Critical Issue #1).
- **Recommendation:** Resolve by republishing.

### 3. No `commands/` or `skills/` directories
- **Issue:** The plugin has only agents and a verdict library. While this is valid for a synthesis-focused plugin, adding a `/synthesize` slash command could improve discoverability. The CLAUDE.md already documents the usage pattern, but a command would make it more accessible.
- **Recommendation:** Consider adding a `commands/synthesize.md` command that dispatches to the appropriate agent based on context.

### 4. `.orphaned_at` marker file present
- **File:** `.orphaned_at` (contains timestamp `1771470958724`)
- **Issue:** This file indicates the cached plugin was marked as orphaned (no longer referenced by any installed plugin entry). This may mean the plugin was uninstalled or the marketplace entry was updated without clearing the old cache.
- **Recommendation:** Verify the plugin is still installed via `/plugin list`. If it should be active, reinstall it to clear the orphan marker.

---

## Component Validation

### Manifest (`.claude-plugin/plugin.json`)

| Field | Value | Status |
|-------|-------|--------|
| `name` | `intersynth` | PASS — kebab-case, no spaces |
| `version` | `0.1.0` | WARNING — stale, source is 0.1.1 |
| `description` | 186 chars | PASS — clear, descriptive |
| `author.name` | `MK` | PASS |
| `author.email` | `mistakeknot@vibeguider.org` | PASS |
| `repository` | `https://github.com/mistakeknot/intersynth` | PASS |
| `license` | `MIT` | PASS |
| `keywords` | 5 keywords | PASS |
| `agents` | 2 paths | PASS — both files exist |
| `hooks` | `./hooks/hooks.json` | WARNING — file exists but is empty |
| `mcpServers` | not present | N/A |

JSON syntax: **Valid** (confirmed with `json.load()`)

### Agents (2 found, 2 partially valid)

#### `agents/synthesize-review.md`
- Frontmatter: `name`, `description`, `model` present
- `name`: `synthesize-review` — PASS (lowercase, hyphens)
- `description`: 185 chars, clear — PASS
- `model`: `haiku` — PASS (valid model)
- `color`: **MISSING** — FAIL
- System prompt: 137 lines, substantial — PASS
- `<example>` blocks: not present (description does not include examples, which is acceptable for synthesis agents that receive structured input)
- Content quality: Excellent — well-structured input contract, 8 execution steps, clear output format, return value constraint

#### `agents/synthesize-research.md`
- Frontmatter: `name`, `description`, `model` present
- `name`: `synthesize-research` — PASS (lowercase, hyphens)
- `description`: 197 chars, clear — PASS
- `model`: `haiku` — PASS (valid model)
- `color`: **MISSING** — FAIL
- System prompt: 106 lines, substantial — PASS
- Content quality: Excellent — parallel structure to review agent, clear source ranking hierarchy, compact return value

### Skills (0 found)
No `skills/` directory — acceptable for this plugin type.

### Hooks (`hooks/hooks.json`)
- JSON syntax: **Valid**
- Content: Empty object `{}`
- No hook definitions registered
- **Result:** Technically valid but misleading manifest reference

### Verdict Library (`hooks/lib-verdict.sh`)
- Bash syntax: **Valid** (confirmed with `bash -n`)
- 129 lines, 8 functions
- Uses `set -euo pipefail` — good practice
- Uses `jq` for JSON operations — good practice
- Atomic writes via temp file + `mv` — good practice
- Functions: `verdict_init`, `verdict_write`, `verdict_read`, `verdict_parse_all`, `verdict_count_by_status`, `verdict_get_attention`, `verdict_clean`, `verdict_total_tokens`
- **Result:** Well-implemented, production-quality shell library

### MCP Servers
- None configured — appropriate for this plugin (agents-only pattern)

---

## File Organization

| Item | Status |
|------|--------|
| `CLAUDE.md` | PASS — comprehensive, includes overview, agents, usage, design decisions |
| `README.md` | ABSENT from cache (present in source) |
| `.gitignore` | PASS — covers ephemeral verdict/review data |
| `LICENSE` | ABSENT — not critical for internal plugin |
| No `.DS_Store` or `node_modules` | PASS |

---

## Security Checks

| Check | Status |
|-------|--------|
| No hardcoded credentials | PASS |
| No secrets in examples | PASS |
| lib-verdict.sh uses safe patterns | PASS — `set -euo pipefail`, quoted variables |
| No HTTP (non-HTTPS) URLs | PASS — only HTTPS repo URL |
| Agent prompts don't expose sensitive info | PASS |

---

## Positive Findings

1. **Well-designed agent contracts** — Both agents have clear input contracts, step-by-step execution instructions, and strict return value constraints (10-15 line summaries). This is exactly the right pattern for context-efficient synthesis.

2. **Excellent verdict library** — `lib-verdict.sh` is production-quality: atomic writes, proper error handling, `set -euo pipefail`, clean API surface with 8 focused functions.

3. **Good separation of concerns** — Two agents (review vs research) rather than one generic, reflecting genuinely different input/output contracts.

4. **Context efficiency by design** — The plugin's core value proposition (keeping agent output out of host context) is well-executed through the compact return value constraints.

5. **Clean manifest** — All fields properly typed, no unknown fields, proper kebab-case naming.

6. **Comprehensive CLAUDE.md** — Quick commands for validation, design decisions documented, usage pattern clearly explained.

---

## Recommendations

1. **[HIGH] Republish to sync cache with source.** The cached version is missing the 5 explicit dedup rules from commit `ff642f8`. This is functional regression — the cached `synthesize-review` agent has a simpler dedup section than what was developed. Use `/interpub:release 0.1.2` or reinstall from marketplace.

2. **[HIGH] Add `color` frontmatter to both agents.** Add `color: cyan` (or preferred color) to `synthesize-review.md` and `synthesize-research.md` frontmatter.

3. **[MEDIUM] Clean up hooks reference.** Either remove `"hooks": "./hooks/hooks.json"` from `plugin.json` (since there are no actual hooks), or rename the directory to `lib/` to avoid confusion. The verdict library is not a Claude Code hook — it's a bash library sourced by agents.

4. **[MEDIUM] Investigate `.orphaned_at` marker.** Verify plugin installation status and clear the orphan marker if the plugin should be active.

5. **[LOW] Add `README.md` to published package.** The source has a good README but it was not included in the cached/published version.

---

## Overall Assessment

**CONDITIONAL PASS** — The plugin is well-architected and its components are individually sound. The two critical issues (stale cache with missing dedup rules, missing agent color fields) should be addressed before relying on this plugin in production workflows. The stale cache issue is particularly important because users running `intersynth:synthesize-review` from the cached version will get the simpler dedup logic, not the improved 5-rule version from the source.
