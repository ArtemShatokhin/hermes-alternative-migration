Kortix is the open-source AI Operating System.

# Migrating from Hermes to Kortix

Hermes Agent is a persistent personal agent. Kortix is an open-source AI Operating System for a team: your agents, their skills, your company memory, and every connector in one git repo you own, with each session on its own isolated cloud computer and work landing through a change request a person reviews.

This walkthrough moves a working Hermes setup to a Kortix project without losing the skills or the memory your agent built. It takes the state that lives under `~/.hermes` on one operator's machine and puts it where a team can use it, version it, and review changes to it.

## What Hermes keeps, and where it lives

Hermes stores everything for a deployment in one directory, `~/.hermes` (or `/opt/data` inside its Docker image): config, API keys, sessions, skills, and memories ([Hermes Docker setup](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/user-guide/docker.md)). The pieces you will move are:

- **Skills** the agent wrote and imported, under `~/.hermes/skills` ([Hermes Agent repository](https://github.com/NousResearch/hermes-agent)).
- **Durable memory** in `MEMORY.md` and `USER.md`.
- **Persona** in `SOUL.md`.
- **Workspace instructions** in `AGENTS.md`.
- **Automations** from the built-in cron scheduler.
- **Surfaces** wired to the gateway: Telegram, Discord, Slack, WhatsApp, Signal, email, and others, more than 20 platforms in all ([Hermes docs](https://hermes-agent.nousresearch.com/docs)).

The migration keeps the content and changes where it lives. Skills, persona, and memory become files in a repo the company owns. Cron jobs become triggers in `kortix.yaml`. Local and single-host runs become sessions on isolated cloud computers.

## Step 1: Inventory the state you want to keep

Before you create anything, write down what is worth carrying over. On the host that runs Hermes:

```bash
ls ~/.hermes/skills
ls ~/.hermes/skills/openclaw-imports
cat ~/.hermes/MEMORY.md
cat ~/.hermes/USER.md
cat ~/.hermes/SOUL.md
cat ~/.hermes/AGENTS.md
```

Sort each item into one of two buckets. Facts about the company (how a process works, where a system lives, who owns what) belong to the team and go into the repo. Facts about one person (their tone, their schedule, their preferences) stay personal. This split is the single most useful decision in the move, because company facts in a shared repo are what every agent reads.

## Step 2: Create the Kortix project

`kortix init` scaffolds a project: a git repo with `kortix.yaml` plus `agents/`, `skills/`, and `memory/`. That repo is the company, as files.

```bash
curl -fsSL https://kortix.com/install | bash
mkdir acme-company && cd acme-company
kortix init
git add -A && git commit -m "Scaffold the Kortix project"
```

`kortix.yaml` declares the machine image, the connectors and the triggers, and secret names and grants. Secret values never appear in the repo. `kortix ship` checks that the project compiles, asks for any missing secrets, pushes it up, and brings it live.

## Step 3: Move the skills into the repo

Hermes skills are compatible with the agentskills.io open standard, so the files move without conversion. Copy each skill you kept into `skills/`:

```bash
cp -R ~/.hermes/skills/<skill-name> acme-company/skills/
git add skills && git commit -m "Import skills from the personal Hermes agent"
```

In Kortix a skill is a file that describes how the company does a job, and every agent can use it. Put them in the repo so a teammate's agent gets the same procedure without re-learning it. Skills that encode a preference of one operator stay out.

## Step 4: Split memory into personal and company

Hermes memory is a closed learning loop that models one person across sessions, with `MEMORY.md` and `USER.md` at its center. In Kortix, company memory is files in `memory/` that accumulate and are read by every agent. Move the company facts:

```bash
mkdir -p acme-company/memory
# write the durable company facts into plain markdown files
git add memory && git commit -m "Seed company memory"
```

Keep the personal entries out of this repo. A useful rule while you migrate: if a fact would help a new teammate on day one, it is company memory. If it would only help you, it is not.

## Step 5: Turn the agent into a team agent

An agent in Kortix is `agents/<name>.md` (behavior) plus an `agents.<name>` block in `kortix.yaml` (governance). The markdown file says what the agent does. The block says what it may reach.

```yaml
# kortix.yaml
agents:
  support-triage:
    file: agents/support-triage.md
    connectors: all
```

Write one file per job. A Hermes Bot becomes a Kortix agent with its own markdown file. The first agent to build is usually the one you ran most often on the personal setup, because its skills and memory are already collected.

## Step 6: Rebuild the automations as triggers

Hermes runs natural-language cron jobs with delivery to any platform. Kortix has exactly two trigger types, `cron` and `webhook`, declared in `kortix.yaml`:

```yaml
# kortix.yaml
triggers:
  - slug: daily-digest
    type: cron
    cron: '0 0 9 * * 1-5'
    prompt: Summarize yesterday's support tickets and open a change request with the digest.
```

A trigger starts a session with nobody present. Move each recurring Hermes job into a trigger, and turn anything that should run on an event into a signed webhook instead of a schedule.

## Step 7: Reconnect the surfaces and connectors

Slack and Microsoft Teams are live in Kortix. Connect the project to a channel, invite the bot, and mention it with a task: the message starts a session, the agent works on its own cloud computer with your connected tools, and it replies in the same thread. Email is experimental, per project.

Connectors are the tools the agent can reach. Kortix covers 3,000+ apps in a click, plus MCP, OpenAPI, Postman, GraphQL and raw HTTP. Connector credentials are brokered server-side and never enter the machine. Give each agent only the connectors its job needs.

## Step 8: Replace local runs with sessions and change requests

This is the change that turns a personal assistant into company infrastructure. A Hermes run happens where you started it. A Kortix session gets its own isolated cloud computer and its own branch:

```bash
kortix sessions new --prompt "Reconcile last month's invoices and open a change request"
kortix cr ls   # review what the agent proposes
kortix chat    # talk to a session's agent from your terminal
```

The agent commits on its branch and opens a change request toward `main`. Merge is default-deny for agents, so a person reviews the diff before work lands. From here, every job you used to run by hand becomes a session whose output is reviewable, and the company improves one reviewed change at a time.

## Step 9: Add governance

Company work needs guardrails. Add them once the first agents run:

- **Permissions:** per-resource permissions for people and agents, with roles, groups, and an audit trail.
- **Approval gates:** set Allow, Ask, or Block per connector action, down to the arguments of each call. An Ask holds the call until a person approves it, then the agent resumes. Approval gates are off until you set them.
- **Secrets:** secrets are encrypted at rest with a key per project.

```yaml
# kortix.yaml
policy:
  default_mode: ask
```

## What goes where

| From the Hermes setup | In the Kortix project |
| --- | --- |
| `~/.hermes/skills/*` | `skills/` in the repo |
| `SOUL.md` (persona) | `agents/<name>.md` or a skill file |
| `MEMORY.md`, `USER.md` (company facts) | `memory/` in the repo |
| Personal preferences | stay with the person |
| Cron jobs | `triggers:` in `kortix.yaml` |
| Chat platforms | project channels (Slack, Microsoft Teams) |
| Tool access | connectors, scoped per agent |
| A local run or an in-place edit | a session on an isolated cloud computer, landed as a change request |

## Check the move

When the project ships, confirm four things before you retire the old setup:

1. `kortix ship` completes and the project is live.
2. The agent you use most answers from the new skills and company memory.
3. A trigger fires and opens a change request you can read.
4. A teammate starts a session and gets the same result without your machine.

Full command reference and self-hosting options are at [kortix.com/docs](https://kortix.com/docs). If you want the side-by-side first, read [Kortix vs Hermes](02-kortix-vs-hermes.md), or start from [hermesalternative.com/migrate-from-hermes](https://hermesalternative.com/migrate-from-hermes).
