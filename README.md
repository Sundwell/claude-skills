# claude-skills

Claude Code skills I use day to day. Each one lives in `plugins/<name>/skills/<name>/SKILL.md`.

## jira-task

Turns a couple of rough sentences into a Jira task description in one fixed schema and, on "ok", creates or updates the ticket through the Atlassian MCP. The schema is short on purpose - a plain-language summary, an optional Frontend list, an optional Backend list with endpoints, and Acceptance Criteria written through a tester's eyes.

The skill leans on `examples.md`, 13 real tickets in this schema (anonymized). Style does not transfer through descriptions, it transfers through examples, so swap that file for your own tickets once you have a dozen.

### Install as a plugin

```
/plugin marketplace add Sundwell/claude-skills
/plugin install jira-task@sundwell
```

Then in any project ask for a task description, or run `/jira-task:jira-task <rough description>`.

### Or copy the folder

```
git clone git@github.com:Sundwell/claude-skills.git
cp -r claude-skills/plugins/jira-task/skills/jira-task ~/.claude/skills/jira-task
```

Then run `/jira-task <rough description>`.

### Set up for your team

Open `SKILL.md`, fill in the "Team settings" table - Jira project key, backend repo to mirror endpoint naming from. Creating and updating tickets needs the [Atlassian MCP](https://www.atlassian.com/platform/remote-mcp-server) connected in Claude Code; each person authorizes with their own account.

### Requirements

- Claude Code
- Atlassian MCP for creating and updating tickets (drafting works without it)

## License

MIT
