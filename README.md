# CMC Crypto Skill Hub Guide

This repository contains the agent-facing guide for using CMC Crypto Skill Hub through MCP.

The guide teaches an agent how to:

- discover CMC Crypto Skill Hub capabilities with `find_skill`;
- validate parameters against each returned `input_schema`;
- execute selected capabilities with `execute_skill`;
- parse wrapped `raw_output` responses;
- handle blocked, partial, stale, and failed tool results;
- render concise Markdown research summaries for crypto market questions.

## Compatibility

This guide is compatible with both Codex and Claude Code skill layouts.

| Host | Install path |
|---|---|
| Codex | `${CODEX_HOME:-$HOME/.codex}/skills/cmc-crypto-skill-hub/` |
| Claude Code | `$HOME/.claude/skills/cmc-crypto-skill-hub/` |

Install only these files into the skill folder:

```text
SKILL.md
agents/openai.yaml
```

Do not rename the skill folder to the repository name. The installed folder should be:

```text
cmc-crypto-skill-hub
```

## Send To Agent

Copy the prompt below into Codex, Claude Code, or another local coding agent:

```text
Install the CMC Crypto Skill Hub guide from https://github.com/XavenY/cmc-crypto-skill-hub-guide.

Use the repository as an agent-facing skill guide, not as an application repo.

Steps:

1. Clone the repository into a temporary directory.
2. Verify that `SKILL.md` exists at the repository root and that its frontmatter name is `cmc-crypto-skill-hub`.
3. Install the guide as folder `cmc-crypto-skill-hub`, not as `cmc-crypto-skill-hub-guide`.
4. If you are running as Codex, install to `${CODEX_HOME:-$HOME/.codex}/skills/cmc-crypto-skill-hub/`.
5. If you are running as Claude Code, install to `$HOME/.claude/skills/cmc-crypto-skill-hub/`.
6. If both `$HOME/.codex` and `$HOME/.claude` exist, install to both locations.
7. Copy only `SKILL.md` and `agents/openai.yaml`, preserving the `agents/` subdirectory.
8. If an existing `cmc-crypto-skill-hub` folder is present, move it to a timestamped backup before installing.
9. Do not change the content of `SKILL.md`.
10. After installation, report the installed path or paths and remind me to restart the host agent so it can load the guide.

Use shell commands where appropriate. Stop and explain the error if the repository cannot be cloned or the expected files are missing.
```

## Manual Install

For Codex:

```bash
tmp_dir="$(mktemp -d)"
git clone --depth 1 https://github.com/XavenY/cmc-crypto-skill-hub-guide "$tmp_dir"
install_dir="${CODEX_HOME:-$HOME/.codex}/skills/cmc-crypto-skill-hub"
if [ -e "$install_dir" ]; then
  mv "$install_dir" "${install_dir}.bak.$(date +%Y%m%d%H%M%S)"
fi
mkdir -p "$install_dir"
cp "$tmp_dir/SKILL.md" "$install_dir/SKILL.md"
mkdir -p "$install_dir/agents"
cp "$tmp_dir/agents/openai.yaml" "$install_dir/agents/openai.yaml"
```

For Claude Code:

```bash
tmp_dir="$(mktemp -d)"
git clone --depth 1 https://github.com/XavenY/cmc-crypto-skill-hub-guide "$tmp_dir"
install_dir="$HOME/.claude/skills/cmc-crypto-skill-hub"
if [ -e "$install_dir" ]; then
  mv "$install_dir" "${install_dir}.bak.$(date +%Y%m%d%H%M%S)"
fi
mkdir -p "$install_dir"
cp "$tmp_dir/SKILL.md" "$install_dir/SKILL.md"
mkdir -p "$install_dir/agents"
cp "$tmp_dir/agents/openai.yaml" "$install_dir/agents/openai.yaml"
```
