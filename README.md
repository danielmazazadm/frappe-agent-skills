# Frappe Agent Skills

Skills for AI coding agents (Claude Code, Codex and others) that work on the
**Frappe** ecosystem: Frappe Framework, ERPNext, Frappe CRM, Webshop, Payments
and the rest.

Agents often write custom code for things Frappe already does, or work around
bugs that were fixed upstream. These skills make them check the platform first.

## Skills

| Skill | What it does |
|---|---|
| [`verify-native-first`](skills/verify-native-first/SKILL.md) | Before writing code, the agent checks the installed version, reads that version's source, the docs and GitHub (releases, issues, merged PRs), and picks the most native solution: configure, then customise, then the API, then an existing app, and custom code only as a last resort. |

## Install

**Claude Code** (plugin marketplace):
```
/plugin marketplace add danielmazazadm/frappe-agent-skills
/plugin install frappe-agent-skills@frappe-agent-skills
```

**Manual** (Claude Code, Codex or any agent that reads `SKILL.md`): copy the
skill folder into your skills directory:
```bash
git clone https://github.com/danielmazazadm/frappe-agent-skills
cp -r frappe-agent-skills/skills/verify-native-first ~/.claude/skills/    # Claude Code
cp -r frappe-agent-skills/skills/verify-native-first ~/.agents/skills/    # Codex / other agents
```

## Requirements

- [`gh`](https://cli.github.com/) (GitHub CLI) for the release and issue checks.
- Access to the bench or container where your Frappe site runs.

## Inspiration

Structure inspired by [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills).

## License

MIT
