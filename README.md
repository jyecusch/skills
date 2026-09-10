# skills

A collection of agent skills I use with Claude Code and other coding agents. Shared publicly in case they're useful to you.

## Install

```bash
npx skills@latest add jyecusch/skills
```

This works with Claude Code, Cursor, Codex, and other agents supported by the [skills CLI](https://github.com/vercel-labs/skills). To see what's available before installing:

```bash
npx skills@latest add jyecusch/skills --list
```

Or install a specific skill:

```bash
npx skills@latest add jyecusch/skills --skill code-critique
```

## Skills

| Category | Description |
| --- | --- |
| [code-review](./skills/code-review/) | Opinionated code review and refactoring skills — naming, nesting, abstraction, state, API design, and an orchestrating critique skill. |

## License

MIT
