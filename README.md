# CMC Crypto Skill Hub Guide

This repository contains the agent-facing skill guide for using CMC Crypto Skill Hub through MCP.

The guide teaches an agent how to:

- discover CMC Crypto Skill Hub capabilities with `find_skill`;
- validate parameters against each returned `input_schema`;
- execute selected capabilities with `execute_skill`;
- parse wrapped `raw_output` responses;
- handle blocked, partial, stale, and failed tool results;
- render concise Markdown research summaries for crypto market questions.

## Repository Layout

The installable skill is in the `cmc-crypto-skill-hub/` subdirectory:

```text
cmc-crypto-skill-hub/
  SKILL.md
  reference/
    template-overview.md
    template-macro.md
    template-scanner.md
    template-comparison.md
    template-attribution.md
```

The directory name matches the `name` field in `SKILL.md`.

## Compatibility

This guide is compatible with Codex and Claude Code skill layouts when installed as the `cmc-crypto-skill-hub` skill directory.

| Host | Install path |
|---|---|
| Codex | `${CODEX_HOME:-$HOME/.codex}/skills/cmc-crypto-skill-hub/` |
| Claude Code | `$HOME/.claude/skills/cmc-crypto-skill-hub/` |

Install the contents of this directory into the skill folder:

```text
cmc-crypto-skill-hub/
```

The installed folder must remain:

```text
cmc-crypto-skill-hub
```

Do not install the repository root as the skill. The repository root contains project documentation and is not the skill directory.

`SKILL.md` is the entry point; the `reference/` directory holds the output report templates the skill loads on demand (overview, macro, scanner, comparison, attribution). Both hosts load the skill from `SKILL.md`; configure the `crypto-skill-hub` MCP server separately in the host so the `find_skill` and `execute_skill` tools are available.

## Codex Skill Installer

Use the GitHub directory URL:

```text
$skill-installer install https://github.com/XavenY/cmc-crypto-skill-hub-guide/tree/main/cmc-crypto-skill-hub
```

Equivalent explicit installer arguments:

```bash
python install-skill-from-github.py \
  --repo XavenY/cmc-crypto-skill-hub-guide \
  --path cmc-crypto-skill-hub
```

## Send To Agent

Copy the prompt below into Codex, Claude Code, or another local coding agent:

```text
Install the CMC Crypto Skill Hub guide from https://github.com/XavenY/cmc-crypto-skill-hub-guide/tree/main/cmc-crypto-skill-hub.

Use the `cmc-crypto-skill-hub/` directory as the skill source.

Steps:

1. Clone the repository into a temporary directory.
2. Verify that `cmc-crypto-skill-hub/SKILL.md` exists and that its frontmatter name is `cmc-crypto-skill-hub`.
3. Install `cmc-crypto-skill-hub/` as folder `cmc-crypto-skill-hub`.
4. If you are running as Codex, install to `${CODEX_HOME:-$HOME/.codex}/skills/cmc-crypto-skill-hub/`.
5. If you are running as Claude Code, install to `$HOME/.claude/skills/cmc-crypto-skill-hub/`.
6. If both `$HOME/.codex` and `$HOME/.claude` exist, install to both locations.
7. Copy only the contents of `cmc-crypto-skill-hub/`, preserving the `reference/` subdirectory.
8. If an existing `cmc-crypto-skill-hub` folder is present, move it to a timestamped backup before installing.
9. Do not install the repository root as the skill.
10. After installation, report the installed path or paths and remind me to restart the host agent so it can load the guide.

Use shell commands where appropriate. Stop and explain the error if the repository cannot be cloned or the expected skill directory is missing.
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
cp -R "$tmp_dir/cmc-crypto-skill-hub/." "$install_dir/"
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
cp -R "$tmp_dir/cmc-crypto-skill-hub/." "$install_dir/"
```
