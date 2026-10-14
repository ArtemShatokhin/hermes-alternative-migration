# Kortix: the open-source AI Operating System

Kortix is the open-source AI Operating System. Your agents, their skills, your company memory, and every connector live in one git repo you own, and each session runs on its own isolated cloud computer. Work lands through a change request a person reviews.

This repo is the migration guide for a team that runs [Hermes Agent](https://github.com/NousResearch/hermes-agent) and wants a company-grade system instead of one personal agent. It covers moving your skills, memory, persona, and automations out of a single operator's machine and into a shared repo, then replacing ad-hoc local runs with sessions on isolated cloud computers whose output lands through a change request.

## Who this is for

You are comfortable running Hermes today: you have `~/.hermes` on a server, a gateway pointed at Telegram or Slack, skills the agent wrote for itself, and a `MEMORY.md` that has learned how you work. Now the work is leaving your hands. Someone else on the team needs the same skills without re-teaching them, a second project needs the memory your agent accumulated, and anything that changes the company's systems should be reviewable before it lands.

That is the shift this repo is about. A personal agent optimizes for one operator and one machine. A company needs shared state in a place the whole team owns, an isolated machine per task, and a review step before work changes the shared branch.

## The migration in one screen

The full walkthrough is in [docs/01-migration-walkthrough.md](docs/01-migration-walkthrough.md). The shape of it:

1. **Inventory what to keep.** List the skills under `~/.hermes/skills`, the persona in `SOUL.md`, the durable facts in `MEMORY.md` and `USER.md`, the workspace instructions in `AGENTS.md`, and every cron job you rely on.
2. **Create the Kortix project.** `kortix init` turns a directory into a project: a git repo with `kortix.yaml` plus `agents/`, `skills/`, and `memory/`. That repo is the company, as files.
3. **Move the skills into the repo.** Hermes skills are compatible with the agentskills.io open standard, so the files move as they are. In Kortix each skill becomes a file every agent can use, reviewed like code.
4. **Split memory into personal and company.** Facts about the company go into `memory/` in the repo, where every agent reads them. Facts about one person stay with that person.
5. **Turn the agent into a team agent.** An agent is `agents/<name>.md` plus an `agents.<name>` block in `kortix.yaml`. The file says what it does; the block says what it may touch.
6. **Rebuild the automations as triggers.** A cron schedule or a signed webhook starts a session with nobody present. Triggers are declared in `kortix.yaml`.
7. **Reconnect the surfaces.** Slack and Microsoft Teams connect to the project, so a mention starts a session and the answer comes back in the thread.
8. **Replace local runs with sessions and change requests.** Every session gets its own cloud computer and branch. The agent commits there and opens a change request; a person reviews the diff and merges it to `main`.

## Why a team outgrows one personal agent

Hermes Agent does its own job well. It is a persistent personal agent: it lives on your server, learns across sessions, writes its own skills, and reaches you on more than 20 chat platforms through one gateway. All of its state (config, keys, sessions, skills, and memories) sits in one directory, `~/.hermes`, on the machine that runs it ([Hermes Docker setup](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/user-guide/docker.md)).

The friction appears when more than one person and more than one job are involved:

- **Shared state.** One operator's `~/.hermes` directory is not a repo a team can clone, diff, or roll back. Kortix keeps the agents, skills, memory, connectors, and triggers as files in one git repo the company owns.
- **Isolation per task.** A local or single-host run shares the machine and its permissions. Kortix gives every session its own isolated cloud computer and its own branch, so thousands can run in parallel without crossover.
- **A review gate.** Changes apply where the agent runs. Kortix routes session work through a change request to `main`, default-deny for agents, so a person reads the diff before it lands.
- **Governance.** Company work needs per-resource permissions for people and agents, roles, groups, an audit trail, approval gates, and SAML 2.0 single sign-on and SCIM 2.0. Hermes ships command approval, DM pairing, and container isolation for a single deployment ([Hermes docs](https://hermes-agent.nousresearch.com/docs)).

Kortix is the open-source AI Operating System: your agents, skills, company memory, and connectors in one git repo you own, on real cloud computers, with work landing through a change request a human approves.

## Documents

- [Migrating from Hermes to Kortix](docs/01-migration-walkthrough.md): the step-by-step move, from `~/.hermes` to a shared project, with the mapping for every kind of state.
- [Kortix vs Hermes](docs/02-kortix-vs-hermes.md): a comparison built row by row from Hermes's own repository and docs.
- [Self-hosting](docs/03-self-hosting.md): verified setup steps for running Kortix yourself, next to Hermes's Docker path.
- [FAQ](docs/04-faq.md): short answers on skills, memory, models, channels, and cost.

## Kortix on GitHub

Kortix is open source: read it, fork it, audit it. [Kortix on GitHub](https://github.com/kortix-ai/suna) is the source, with 20,000+ GitHub stars (checked October 2026). Install with three commands:

```bash
curl -fsSL https://kortix.com/install | bash
kortix init
kortix ship
```

## Learn more

- The full migration path: [hermesalternative.com/migrate-from-hermes](https://hermesalternative.com/migrate-from-hermes)
- How Kortix compares with Hermes side by side: [hermesalternative.com/kortix-vs-hermes](https://hermesalternative.com/kortix-vs-hermes)
- Other options when a personal agent is not enough: [hermesalternative.com/hermes-alternatives](https://hermesalternative.com/hermes-alternatives)
- When the move is really about a team: [hermesalternative.com/hermes-for-teams](https://hermesalternative.com/hermes-for-teams)
- What happens to the memory your agent built: [hermesalternative.com/hermes-agent-memory](https://hermesalternative.com/hermes-agent-memory)
