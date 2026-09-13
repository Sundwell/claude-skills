# claude-skills

Claude Code skills I use day to day. Each one lives in `plugins/<name>/skills/<name>/SKILL.md`.

## jira-task

Turns a couple of rough sentences into a Jira task description in one fixed schema and, on "ok", creates or updates the ticket through the Atlassian MCP. The schema is short on purpose - a plain-language summary, an optional Frontend list, an optional Backend list with endpoints, and Acceptance Criteria written through a tester's eyes.

The skill leans on `examples.md`, 13 real tickets in this schema (anonymized). Style does not transfer through descriptions, it transfers through examples, so swap that file for your own tickets once you have a dozen.

### Install

`SKILL.md` is the open [Agent Skills](https://agentskills.io) format, so the same folder works in Claude Code, Codex, Cursor, Gemini CLI, GitHub Copilot, OpenCode, Zed, Windsurf and most other coding agents. Pick whichever route fits your tool.

**Any agent, one command.** The `skills` CLI detects the agents on your machine and symlinks the skill into each one's skills folder.

```
npx skills add Sundwell/claude-skills -g
```

Update later with `npx skills update -g`.

**Claude Code** as a plugin, with updates through `/plugin`:

```
/plugin marketplace add Sundwell/claude-skills
/plugin install jira-task@sundwell
```

Then ask for a task description in plain words, or run `/jira-task:jira-task <rough description>`.

**Gemini CLI**

```
gemini skills install https://github.com/Sundwell/claude-skills.git --path plugins/jira-task/skills/jira-task
```

**By hand.** Clone and copy the skill folder into the place your tool reads. Most tools also read `~/.agents/skills/`, so that one folder covers several of them at once.

```
git clone https://github.com/Sundwell/claude-skills.git
cp -r claude-skills/plugins/jira-task/skills/jira-task ~/.agents/skills/jira-task
```

| Tool | Personal folder | In a repo | Invoke |
|---|---|---|---|
| Claude Code | `~/.claude/skills/` | `.claude/skills/` | `/jira-task` |
| Codex | `~/.agents/skills/` | `.agents/skills/` | `$jira-task` |
| Cursor | `~/.cursor/skills/` or `~/.agents/skills/` | `.cursor/skills/` or `.agents/skills/` | `/jira-task` |
| GitHub Copilot, VS Code | `~/.copilot/skills/` or `~/.agents/skills/` | `.github/skills/` or `.agents/skills/` | `/jira-task` |
| Gemini CLI | `~/.gemini/skills/` or `~/.agents/skills/` | `.gemini/skills/` or `.agents/skills/` | picked up by description |
| OpenCode | `~/.config/opencode/skills/` or `~/.claude/skills/` | `.opencode/skills/` or `.claude/skills/` | picked up by description |

Cursor, Copilot, VS Code and OpenCode also read `~/.claude/skills/`, so an existing Claude Code setup usually needs nothing extra.

### Set up for your team

Open `SKILL.md`, fill in the "Team settings" table - Jira project key, backend repo to mirror endpoint naming from. Creating and updating tickets needs the [Atlassian MCP](https://www.atlassian.com/platform/remote-mcp-server) connected in your agent (Claude Code, Codex, Cursor and the others all support MCP servers); each person authorizes with their own Atlassian account. Without it the skill still drafts the description, you paste it into Jira yourself.

## License

MIT
