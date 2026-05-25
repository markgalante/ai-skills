# ai-skills

Personal [agent skills](https://agentskills.io) for my daily workflow — reusable instruction sets that extend AI coding agents with specific behaviors and workflows.

General-purpose and not tied to any codebase. Works with [Cursor](https://cursor.com), [Claude Code](https://code.claude.com), and [50+ other agents](https://github.com/vercel-labs/skills#supported-agents) via [npx skills](https://github.com/vercel-labs/skills).

## Install

```bash
# List available skills
npx skills add markgalante/ai-skills --list

# Install a specific skill globally
npx skills add markgalante/ai-skills --skill debugging -g

# Install all skills
npx skills add markgalante/ai-skills --all -y
```

Use `-a <agent>` to install to a specific tool — replace `<agent>` with your agent's name, e.g. `cursor`, `claude-code`, or `codex`. See the [supported agents list](https://github.com/vercel-labs/skills#supported-agents) for all values.

## Skills

| Skill | Description |
| --- | --- |
| [`debugging`](./debugging/SKILL.md) | Collaborative debugging partner — one step at a time, teaching *why* a fix works |

## License

Shared as-is. Install, adapt, and fork freely.
