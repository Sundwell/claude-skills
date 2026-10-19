---
name: jira-task
description: Writes or restructures a Jira task description in a fixed schema (summary, Open Questions, Frontend, Backend, Acceptance Criteria), using real past tickets as the style reference, and creates or updates the ticket via the Atlassian MCP on command. Invoke when asked to describe, draft, write up, restructure, create or update a task, ticket, or issue in Jira.
---

# Jira task

Turns a couple of rough sentences into a task description in a fixed schema, shows the Markdown, and after edits or "ok" creates the ticket in Jira. Can also restructure an existing ticket into the schema.

## Team settings

Fill these in once for your team. Everything below refers to them.

| Setting | Value |
|---|---|
| Jira project key | `<PROJECT>` (for example `PROJ`) |
| Atlassian site | `<your-site>.atlassian.net` |
| Default issue type | Task (Bug if something is being fixed, Story if large) |
| Ticket language | English |
| Backend repo to mirror endpoint naming from | `<owner>/<backend repo>`, routes in `<file with routes>` |
| New list and search endpoints | `GET` with query params, not `POST /<entity>/filter` with a body (drop this row if your team prefers the other way) |
| Style reference | `examples.md` next to this file - replace with 10-15 of your own tickets once you have them in this schema |

## The schema

```
<link to the Slack thread or wherever the request came from, if any>

<one or two paragraphs - what and why, in plain language>

**Open Questions:**      (only if something is not decided yet)
1. what is still open, one line each

**Frontend:**            (only if there is frontend work)
1. what to do, short and in frontend terms

**Backend:**             (only if there is backend work)
1. endpoint and what changes in it, with `code` for paths and fields
   1. request body example, and a response example only if the frontend needs it

**Acceptance Criteria:**
1. what must work and how - through a tester's eyes, no tech
```

AC is **business output only**. "Button appears when status is Processing", not "endpoint returns 200". Tech lives in Frontend and Backend.

Open Questions stand right after the description and before every other section. A detail nobody has decided yet, such as who gets access or a limit, is written as `TBD` in the item it belongs to and listed once in Open Questions.

## How to work

**1. Look at the examples.** They are the style reference; describing style in words does not work. Read `examples.md` - 13 real descriptions in this schema (anonymized). Pick the 3-4 closest by topic and lean on them.

**2. Write to the pattern.** Descriptions are in English. Reuse the phrasing from the examples, the numbering, the `code` for endpoints.

**3. Invent nothing - with one clarified exception.**

Requirements, Slack links, numbers, field names the user mentioned, and every AC item come only from what the user gave. When a whole section has no material, **ask**, do not fill it with something plausible. An empty section beats an invented one. A single detail nobody has decided (who gets access, a limit) is neither a reason to ask nor to guess by analogy, it becomes `TBD` and goes to Open Questions.

Where the facts come from, in this order:
- what the user typed, plus a Slack thread pasted as extra context - the primary source. The user usually copies the thread in by hand; treat it as given facts, and put its link at the top of the description if one was given. The top link is the Slack thread, never an epic or a parallel or blocking ticket from another project
- Jira via MCP - search the project for tickets on the same feature or page and reuse their terminology, field names and already existing endpoints; if a parent or related ticket is mentioned, read it and its comments. Do not dig through epics or parallel tickets in other spaces, and never link them in the description
- the backend repo from Team settings - its neighbouring routes only, see below. If it is not cloned locally, read it through `gh` rather than guessing

The exception is **endpoints**. The ticket is usually written before the backend exists, so proposing an endpoint is expected, not inventing. Look at the neighbouring routes in the backend repo (locally or through `gh api repos/<owner>/<repo>/contents/<file with routes>`) for two things only. One is how the path, the payload and the response usually look, so the proposal takes similar naming and shape. The other is whether an endpoint the task needs is already there, since some can be ready for various reasons. Then the item names it as existing ("Use existing `GET /...`" or "Update `GET /...`") instead of "New endpoint". If the repo cannot be reached either way, mirror the closest existing endpoint the user or the context mentions, and say that is what you did.

Nothing else from the backend goes into the description. Do not read pull requests or other services, do not check what a backend has or lacks, and do not explain how a field is derived. The ticket is a business task, not a backend audit. Anything worth knowing that you notice on the way goes to the user in chat.

The ticket says what the frontend needs from the backend and stops there. Where the backend takes the data from, whether another service already exposes it, and how it stores it are the backend's business. Never write that as an item, a hint, a `TBD` or an Open Question. A field the frontend needs is simply listed in the response example. Open Questions hold business questions only, such as roles or what an action does.

New list and search endpoints follow the shape in Team settings, even when their neighbours use the other one.

**4. Backend - keep it short.** Every item in as few words as possible. Per endpoint:
- approximate request body
- response only if the frontend needs it, and minimal - just what the frontend uses; a big object shows the fields that matter and `// ...` for the rest
- pagination is one line, "Add pagination"; sorting and filtering name their fields, with the logic only when it is not obvious
- every request or response example is an object in curly braces inside a `json` code block, with `// new` on added fields. This holds for form data too, with a "(form data)" note in the item text instead of a bare `key: value` list
- no status codes, no descriptions of empty responses
- no items about other services, how a field is computed, or links to pull requests

**5. Frontend - what matters and what does not.**
- confirmation modals are important behaviour, describe them
- toasts almost never
- feedback after a click never
- when the frontend already exists (a prototype, an earlier partial version), the section opens with one ultra short line "Already done in ... - ..." and then numbers only what is left

**6. Acceptance Criteria - only what someone asked for.** No obvious consequences, no "and the button is disabled while loading" unless it was requested. One item is fine. Frontend and Backend sections are optional too: no work for the frontend - no section, do not write "Frontend: no changes".

**7. Show the Markdown and wait.** The user edits or says "ok". Do not create or update the ticket on your own.

**8. On "ok" - create via the Atlassian MCP.** Use the project and site from Team settings. If several Atlassian servers are connected, pick the one for that site. Get cloudId from `getAccessibleAtlassianResources`; each person has their own account and permissions. Issue type from the user's words, defaulting per Team settings. Pass the description as markdown. After creating, give the key and link.

## Updating an existing ticket

When the raw text is already a ticket, or the user asks to restructure one:

1. Read the description **and the comments** via MCP - decisions often live in comments.
2. Restructure into the schema **without losing the author's facts or phrasing**. Move sentences into the right sections; do not rewrite what is already said.
3. Add Frontend, Backend and AC where the source has the material for them; ask where it does not.
4. Do not touch the title unless asked.
5. Before replacing, **say what drops out** - anything from the original that did not fit the schema, so the user can decide. Only then update via `editJiraIssue`.

Never create a duplicate for something that is already a ticket.

## What not to do

Do not translate the schema into another language; tickets are in English. Do not bloat - descriptions are short, gist in two paragraphs plus lists. Do not add sections the schema does not have. Do not write AC in technical language. Do not describe toasts, loaders, or post-click feedback. Do not link epics or parallel tickets, and do not audit the backend.
