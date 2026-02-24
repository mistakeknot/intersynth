# Demarch Plugin Ecosystem Audit

**Date:** 2026-02-22
**Scope:** All 33 plugins in `/home/mk/projects/Demarch/interverse/*/`
**Auditor:** intersynth automated analysis

---

## Executive Summary

Audited 33 plugins across three data sources: local `plugin.json` manifests, the marketplace registry, and the installed plugin cache. Found **3 version mismatches**, **14 undeclared hooks.json files**, **7 plugins with zero declared features** (but features exist on disk), **1 hardcoded API key**, and **106 stale cache directories** consuming unnecessary disk space.

---

## 1. Version Mismatches (plugin.json vs marketplace.json vs cache)

### Three-Way Comparison

| Plugin | plugin.json | marketplace | cache latest | Issue |
|--------|-------------|-------------|--------------|-------|
| **interflux** | 0.2.20 | 0.2.19 | 0.2.19 | plugin.json bumped but marketplace NOT updated |
| **interkasten** | 0.4.3 | 0.4.4 | 0.4.4 | marketplace ahead of plugin.json (reverse mismatch) |
| **interlock** | 0.2.2 | 0.2.1 | 0.2.2 | plugin.json bumped, cache updated, but marketplace NOT updated |

### Plugins Missing from Marketplace

| Plugin | plugin.json version | Notes |
|--------|---------------------|-------|
| interchart | 0.1.0 | Not published to marketplace |
| interlearn | 0.1.0 | Not published to marketplace |

### In Marketplace but Not in Interverse Directory

| Plugin | marketplace version | Notes |
|--------|---------------------|-------|
| clavain | 0.6.59 | Lives at `/home/mk/projects/Demarch/os/clavain/`, not in interverse |

### All Other Plugins: Versions Match

The remaining 28 plugins have consistent versions across all three sources.

---

## 2. Feature Declaration Matrix

### Full Matrix

| Plugin | Ver | Skills | Cmds | Agents | Hooks | MCP | Total |
|--------|-----|--------|------|--------|-------|-----|-------|
| interchart | 0.1.0 | 1 | 0 | 0 | 0 | 0 | 1 |
| intercheck | 0.2.0 | 1 | 0 | 0 | 0 | 0 | 1 |
| intercraft | 0.1.0 | 1 | 1 | 1 | 0 | 0 | 3 |
| interdev | 0.2.0 | 5 | 0 | 0 | 0 | 0 | 5 |
| interdoc | 5.1.1 | 1 | 1 | 0 | 0 | 0 | 2 |
| interfluence | 0.2.5 | 1 | 1 | 1 | 0 | 1 | 4 |
| interflux | 0.2.20 | 2 | 4 | 17 | 0 | 2 | 25 |
| interform | 0.1.0 | 1 | 0 | 0 | 0 | 0 | 1 |
| interject | 0.1.6 | 1 | 0 | 0 | 0 | 1 | 2 |
| interkasten | 0.4.3 | 3 | 2 | 0 | 0 | 1 | 6 |
| interlearn | 0.1.0 | 1 | 0 | 0 | 0 | 0 | 1 |
| interleave | 0.1.1 | 1 | 0 | 0 | 0 | 0 | 1 |
| interlens | 2.2.4 | 0 | 0 | 0 | 0 | 1 | 1 |
| **interline** | 0.2.6 | **0** | **0** | **0** | **0** | **0** | **0** |
| interlock | 0.2.2 | 0 | 0 | 0 | 0 | 1 | 1 |
| intermap | 0.1.3 | 1 | 0 | 0 | 0 | 1 | 2 |
| intermem | 0.2.2 | 1 | 0 | 0 | 0 | 0 | 1 |
| intermux | 0.1.2 | 1 | 0 | 0 | 0 | 1 | 2 |
| internext | 0.1.2 | 1 | 0 | 0 | 0 | 0 | 1 |
| **interpath** | 0.2.2 | **0** | **0** | **0** | **0** | **0** | **0** |
| interpeer | 0.1.0 | 1 | 0 | 0 | 0 | 0 | 1 |
| **interphase** | 0.3.3 | **0** | **0** | **0** | **0** | **0** | **0** |
| interpub | 0.1.3 | 0 | 1 | 0 | 0 | 0 | 1 |
| **intersearch** | 0.1.1 | **0** | **0** | **0** | **0** | **0** | **0** |
| interserve | 0.1.4 | 0 | 0 | 0 | 0 | 1 | 1 |
| interslack | 0.1.0 | 1 | 0 | 0 | 0 | 0 | 1 |
| interstat | 0.2.5 | 1 | 0 | 0 | 0 | 0 | 1 |
| intersynth | 0.1.4 | 0 | 0 | 2 | 1 | 0 | 3 |
| intertest | 0.1.1 | 3 | 0 | 0 | 0 | 0 | 3 |
| **interwatch** | 0.1.2 | **0** | **0** | **0** | **0** | **0** | **0** |
| tldr-swinton | 0.7.14 | 4 | 6 | 0 | 1 | 1 | 12 |
| **tool-time** | 0.3.5 | **0** | **0** | **0** | **0** | **0** | **0** |
| **tuivision** | 0.1.4 | **0** | **0** | **0** | **0** | **0** | **0** |

**Bold** = zero declared features.

---

## 3. Undeclared Features (Exist on Disk but Not in plugin.json)

### 3a. Undeclared hooks.json Files (14 plugins)

These plugins have `hooks/hooks.json` on disk with valid structure, but do NOT declare `"hooks"` in their `plugin.json`. This means Claude Code will **never load these hooks**.

| Plugin | hooks.json Location | Hook Events | Hook Count |
|--------|---------------------|-------------|------------|
| intercheck | hooks/hooks.json | PostToolUse | 3 hook commands |
| interfluence | hooks/hooks.json | PostToolUse | 1 hook command |
| interflux | hooks/hooks.json | SessionStart | 1 hook command |
| interject | hooks/hooks.json | SessionStart | 1 hook command |
| interkasten | hooks/hooks.json | SessionStart, Stop | 2 hook commands |
| interlearn | hooks/hooks.json | SessionEnd | 1 hook command |
| interline | hooks/hooks.json | SessionStart | 1 hook command |
| interlock | hooks/hooks.json | SessionStart, PreToolUse, Stop | 3 hook commands |
| intermem | hooks/hooks.json | SessionStart | 1 hook command |
| intermux | hooks/hooks.json | SessionStart | 1 hook command |
| interserve | hooks/hooks.json | PreToolUse | 1 hook command |
| interstat | hooks/hooks.json | PostToolUse, SessionEnd | 2 hook commands |
| tool-time | hooks/hooks.json | PreToolUse, PostToolUse, SessionStart, SessionEnd | 4 hook commands |

**Only 2 plugins correctly declare hooks:** `intersynth` (hooks/hooks.json) and `tldr-swinton` (.claude-plugin/hooks/hooks.json).

### 3b. Undeclared Skills (7 plugins)

| Plugin | Skill Path on Disk | Status |
|--------|--------------------|--------|
| interlock | skills/coordination-protocol/SKILL.md | Not in declared skills list |
| interlock | skills/conflict-recovery/SKILL.md | Not in declared skills list |
| interpath | skills/artifact-gen/SKILL.md | plugin.json declares NO skills |
| interphase | skills/beads-workflow/SKILL.md | plugin.json declares NO skills |
| interwatch | skills/doc-watch/SKILL.md | plugin.json declares NO skills |
| tool-time | skills/tool-time/SKILL.md | plugin.json declares NO skills |
| tool-time | skills/tool-time-codex/SKILL.md | plugin.json declares NO skills |
| tuivision | skills/tui-test/SKILL.md | plugin.json declares NO skills |

### 3c. Undeclared Commands (3 plugins)

| Plugin | Command Path on Disk | Status |
|--------|--------------------|--------|
| interline | commands/statusline-setup.md | plugin.json declares NO commands |
| interpath | commands/{changelog,prd,propagate,roadmap,status,vision}.md (6 files) | plugin.json declares NO commands |
| interwatch | commands/{refresh,status,watch}.md (3 files) | plugin.json declares NO commands |

---

## 4. Hooks Validation

### Properly Declared and Valid

| Plugin | hooks.json Path | Events | Status |
|--------|-----------------|--------|--------|
| intersynth | ./hooks/hooks.json | SessionStart | Valid, 1 hook command |
| tldr-swinton | ./.claude-plugin/hooks/hooks.json | Setup, PreToolUse, PostToolUse | Valid, 3 hook groups |

### All hooks.json Files: Structure Valid

All 15 hooks.json files found on disk have a proper `"hooks"` top-level key with correctly structured event handlers. None have the `{}` empty-object problem. All referenced hook scripts exist on disk.

---

## 5. Declared Files That Don't Exist on Disk

**No issues found.** Every file referenced in every plugin.json (skills, commands, agents, hooks, MCP server binaries) exists on disk.

---

## 6. Security Issues

### Hardcoded API Key

**interject** (`plugin.json`) contains a hardcoded Exa API key in the `mcpServers.interject.env` block:

```json
"env": {
  "EXA_API_KEY": "eba9629f-75e9-467c-8912-a86b3ea8d678"
}
```

This should be changed to `"${EXA_API_KEY}"` like interflux does, referencing an environment variable instead.

---

## 7. Cache Bloat

### Summary

- **138 total** version directories in the plugin cache
- **106 stale** (not the latest version) = **76.8%** of cache directories are obsolete
- **32 current** (latest version for each plugin)

### Top Offenders

| Plugin | Stale Versions | Latest |
|--------|---------------|--------|
| clavain | 25 | 0.6.59 |
| interflux | 14 | 0.2.19 |
| interkasten | 7 | 0.4.4 |
| interfluence | 6 | 0.2.5 |
| interline | 5 | 0.2.6 |
| tldr-swinton | 5 | 0.7.14 |
| tool-time | 5 | 0.3.5 |

---

## 8. Severity-Ranked Action Items

### Critical (functionality broken)

1. **14 plugins have undeclared hooks** -- hooks exist on disk but Claude Code never loads them because plugin.json lacks the `"hooks"` reference. This is the same bug intersynth had before it was fixed. Affected: intercheck, interfluence, interflux, interject, interkasten, interlearn, interline, interlock, intermem, intermux, interserve, interstat, tool-time.

2. **7 plugins declare zero features** but have skills/commands on disk -- interline (1 command, 1 hooks.json), interpath (1 skill, 6 commands), interphase (1 skill), interwatch (1 skill, 3 commands), tool-time (2 skills, 1 hooks.json), tuivision (1 skill). These plugins install but do nothing. intersearch is a library, so zero features is intentional.

### High (version drift)

3. **interflux** v0.2.20 in plugin.json, v0.2.19 in marketplace -- marketplace needs bump.
4. **interlock** v0.2.2 in plugin.json, v0.2.1 in marketplace -- marketplace needs bump.
5. **interkasten** v0.4.4 in marketplace, v0.4.3 in plugin.json -- plugin.json needs bump (reverse mismatch).

### Medium (security)

6. **interject** has a hardcoded EXA_API_KEY in plugin.json -- should use `${EXA_API_KEY}` env var reference.

### Low (hygiene)

7. **106 stale cache directories** -- can be cleaned with `rm -rf` of old version dirs.
8. **interchart** and **interlearn** not published to marketplace -- intentional or forgotten?
9. **interlock** has 2 undeclared skills on disk (coordination-protocol, conflict-recovery) beyond the MCP server.

---

## 9. Recommended Fix Order

```
# 1. Add hooks declarations to 14 plugins (highest impact)
#    For each: add "hooks": "./hooks/hooks.json" to plugin.json

# 2. Add skills/commands to zero-feature plugins
#    interpath: add skills + commands
#    interphase: add skills  
#    interwatch: add skills + commands
#    interline: add commands
#    tool-time: add skills
#    tuivision: add skills

# 3. Fix version mismatches
#    interflux: bump marketplace to 0.2.20
#    interlock: bump marketplace to 0.2.2
#    interkasten: bump plugin.json to 0.4.4

# 4. Fix interject hardcoded API key
#    Change "eba9629f-..." to "${EXA_API_KEY}"

# 5. Clean stale cache
#    For each plugin in cache, remove all but latest version dir
```

---

## Appendix: Full Plugin Inventory

33 plugins audited across the interverse directory:

```
interchart      intercheck      intercraft      interdev        interdoc
interfluence    interflux       interform       interject       interkasten
interlearn      interleave      interlens       interline       interlock
intermap        intermem        intermux        internext       interpath
interpeer       interphase      interpub        intersearch     interserve
interslack      interstat       intersynth      intertest       interwatch
tldr-swinton    tool-time       tuivision
```

Plus `clavain` in the marketplace (lives at `/home/mk/projects/Demarch/os/clavain/`).
