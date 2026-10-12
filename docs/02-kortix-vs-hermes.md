Kortix is the open-source AI Operating System.

# Kortix vs Hermes: one personal agent or a company operating system

Hermes Agent and Kortix are both open source and both run agents on your own infrastructure. They aim at different units of work. Hermes Agent is a persistent personal agent that learns one operator across sessions. Kortix is the open-source AI Operating System: your agents, skills, company memory, and connectors in one git repo you own, with each session on its own isolated cloud computer and work landing through a change request a person reviews.

Every row in the table comes from Hermes Agent's own repository and documentation ([Hermes Agent on GitHub](https://github.com/NousResearch/hermes-agent), [Hermes docs](https://hermes-agent.nousresearch.com/docs)) and from Kortix's own sources. The live comparison is at [hermesalternative.com/kortix-vs-hermes](https://hermesalternative.com/kortix-vs-hermes).

## The unit each one is built around

Hermes Agent is a deployment per operator. Its own description calls it a persistent personal agent: you install it on a server, give it your messaging accounts, and it learns your projects and writes its own skills. Its state (config, keys, sessions, skills, and memories) sits in one directory, `~/.hermes`, on the host that runs it ([Hermes Docker setup](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/user-guide/docker.md)). Hermes Bot Mode adds specialist bots that work together in group chats, still on one gateway and one operator's state.

Kortix is a project for a team, and the project is a git repo. The agents, the skills they share, the memory, the connector config, and the triggers are all text in that repo. Because the repo is versioned, a teammate clones the same setup, a change to an agent is a diff, and any part of it rolls back. Each session the project starts gets its own isolated cloud computer and its own branch.

## How they compare

| Dimension | Kortix | Hermes Agent |
| --- | --- | --- |
| Open source | Yes | Yes, MIT licensed |
| Built for | A company and a team, on one shared config | One operator's persistent personal agent |
| Where agents run | Its own isolated cloud computer per session, on its own branch | Local, Docker, SSH, a cloud VM or serverless backend |
| Where the config lives | Files in a git repo the company owns | Files under `~/.hermes` on the host that runs it |
| How work lands | A change request a person reviews before it merges | Changes apply in the environment the agent runs in |
| Shared knowledge | Company memory and skills in the repo, used by every agent | Memory and skills that learn one operator across sessions |
| Surfaces | Web, Slack, Microsoft Teams, email, mobile, CLI and API | CLI plus 20+ chat platforms from one gateway |
| Access control | Per-resource permissions, roles, groups and an audit trail | Command approval, DM pairing and container isolation |

The two "Where agents run" cells are both true and they mean different things. Hermes runs the agent on a backend you choose: a local machine, a Docker container, an SSH host, or a cloud VM, with Daytona and Modal offering serverless persistence. Kortix boots a fresh isolated cloud computer for every session, names each branch after the session, and discards the machine when the session ends. Only what the agent commits survives.

## What Hermes Agent does well

Hermes Agent is strong where it counts:

- **A real learning loop.** The agent curates memory, creates skills from experience, improves them during use, and searches past sessions. It is compatible with the agentskills.io open standard.
- **Reach.** One gateway connects CLI, Telegram, Discord, Slack, WhatsApp, Signal, email, and more than 20 platforms, with cross-platform conversation continuity.
- **Flexibility in where it runs.** Seven terminal backends, from local to Docker to serverless, so the same agent runs on a $5 VPS or a GPU cluster.
- **Free and open.** It is MIT licensed, self-hosting carries no platform fee, and an optional Nous Portal plan starts at $20 per month for model and tool credits ([Hermes homepage](https://hermes-agent.nousresearch.com)).

If one person is the whole operation, and the agent's memory belongs only to that person, Hermes is a complete answer. That is exactly the case it is designed for.

## What changes when a team owns the work

Four things stop holding once more than one person and more than one job are involved, and each one maps to a capability in Kortix:

**Shared state instead of one directory.** A `~/.hermes` directory on one host is not a repo a team can clone, diff, or roll back. Kortix keeps the agents, skills, memory, connectors, and triggers as files in one git repo the company owns and versions like code.

**Isolation per task instead of one machine.** A local or single-host run shares the host and its permissions. Kortix gives every session its own isolated cloud computer and its own branch, so thousands of agents run in parallel on one config with no crossover.

**A review gate instead of a change in place.** Session work reaches `main` through a change request, and merge is default-deny for agents. A person reads the diff before it lands, which is what makes it safe to let agents touch real systems.

**Governance instead of a single deployment.** Company work needs per-resource permissions for people and agents, roles, groups, and an audit trail, plus SAML 2.0 single sign-on and SCIM 2.0. Approval gates are off until you set them, then each connector action is Allow, Ask, or Block, down to the arguments of each call.

## Cost shape

Hermes Agent is free to self-host, and a Nous Portal plan adds model and tool credits from $20 per month. Kortix is free to self-host. Kortix Cloud is $0 with 200 credits per month and 1 project, or $40 per seat per month with 2,500 pooled credits per seat and seats for the people on your team ([Kortix pricing](https://kortix.com/pricing)). Both let you bring your own model keys.

## Which one to pick

Pick Hermes Agent when the agent belongs to one person, runs on one host, and learns one operator. Pick Kortix when the AI work belongs to a company: when skills have to be shared, memory has to be team property, every task needs its own machine, and changes to shared systems need a review before they land. That is the case Kortix is built for, and it is the recommendation here.

The move itself is covered step by step in [Migrating from Hermes to Kortix](01-migration-walkthrough.md). To run Kortix yourself first, see [Self-hosting](03-self-hosting.md). For a short list of reader questions, see the [FAQ](04-faq.md), or start at [hermesalternative.com/kortix-vs-hermes](https://hermesalternative.com/kortix-vs-hermes).
