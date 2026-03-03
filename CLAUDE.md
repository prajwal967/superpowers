# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Superpowers is a skills library and workflow framework for AI coding agents (Claude Code, Cursor, Codex, OpenCode). It provides composable "skills" — reference guides for proven development techniques, patterns, and tools. Skills are primarily Markdown documents with YAML frontmatter, distributed as a plugin/git repository.

## Testing

```bash
# Run all fast tests
./tests/claude-code/run-skill-tests.sh

# Run integration tests (10-30 minutes, uses Claude CLI in headless mode)
./tests/claude-code/run-skill-tests.sh --integration

# Run a specific test
./tests/claude-code/run-skill-tests.sh --test test-subagent-driven-development.sh

# Verbose output
./tests/claude-code/run-skill-tests.sh --verbose
```

Tests are bash scripts in `tests/claude-code/` that use `test-helpers.sh` and invoke `claude -p` for headless skill verification.

## Architecture

### Plugin System

The project is a multi-platform plugin:
- `.claude-plugin/plugin.json` — Claude Code plugin manifest (version, metadata)
- `.cursor-plugin/plugin.json` — Cursor IDE plugin config
- `.codex/` — Codex integration
- `.opencode/` — OpenCode integration

### Hook System

`hooks/hooks.json` defines a `SessionStart` hook that runs `hooks/session-start` synchronously on every session startup/resume/clear/compact. This injects the `skills/using-superpowers/SKILL.md` content as initial context. `hooks/run-hook.cmd` is a polyglot bash/cmd wrapper for cross-platform support.

### Skills (`skills/`)

Each skill is a directory containing a required `SKILL.md` with YAML frontmatter (`name` and `description` fields only, max 1024 chars total). Optional supporting files for heavy reference (100+ lines) or reusable tools.

14 skills organized in a flat namespace: `brainstorming`, `dispatching-parallel-agents`, `executing-plans`, `finishing-a-development-branch`, `receiving-code-review`, `requesting-code-review`, `subagent-driven-development`, `systematic-debugging`, `test-driven-development`, `using-git-worktrees`, `using-superpowers`, `verification-before-completion`, `writing-plans`, `writing-skills`.

### Skill Discovery (`lib/skills-core.js`)

JavaScript ES module providing skill infrastructure:
- `extractFrontmatter()` — Parses YAML frontmatter from SKILL.md files
- `findSkillsInDir()` — Recursively discovers skills (max depth 3)
- `resolveSkillPath()` — Resolves skill names with personal-over-superpowers shadowing (personal skills in `~/.claude/skills` override superpowers skills; use `superpowers:` prefix to force superpowers version)
- `stripFrontmatter()` — Returns content without frontmatter
- `checkForUpdates()` — Git fetch with 3s timeout to check for upstream updates

### Commands (`commands/`)

Slash commands mapping to skills: `/brainstorm`, `/write-plan`, `/execute-plan`.

### Agents (`agents/`)

Reusable agent configurations (e.g., `code-reviewer.md`).

## Skill Authoring Conventions

- Skill names: lowercase letters, numbers, hyphens only
- Descriptions must start with "Use when..." and describe triggering conditions only (never summarize the skill's workflow — this causes agents to shortcut and skip the full content)
- Skills follow TDD: write failing test (baseline without skill) → write minimal skill → refactor to close loopholes
- Token efficiency matters: getting-started skills <150 words, frequently-loaded <200 words, others <500 words
- Use `superpowers:skill-name` cross-references, never `@` links (which force-load and burn context)
- Flowcharts only for non-obvious decision points; use Graphviz dot format per `graphviz-conventions.dot`
