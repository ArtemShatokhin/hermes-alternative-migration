Kortix is the open-source AI Operating System.

# FAQ: moving from Hermes to Kortix

Short answers to the questions that come up when a team moves from a personal agent to the open-source AI Operating System. Each answer says what is true today and where to read more.

The live version of this page is at [hermesalternative.com/faq](https://hermesalternative.com/faq).

## Is Kortix open source?

Yes. Kortix is the open-source AI Operating System: your agents, their skills, your company memory, and every connector in one git repo you own. You can read the source, fork it, and audit it, and self-hosting is free. The source is [Kortix on GitHub](https://github.com/kortix-ai/suna).

## Can I keep the skills my Hermes agent wrote for itself?

Yes. Hermes Agent creates skills from experience and stores them under `~/.hermes/skills`, and it is compatible with the agentskills.io open standard ([Hermes Agent repository](https://github.com/NousResearch/hermes-agent)). Because the format is an open standard, the skill files move into the Kortix project's `skills/` directory without conversion. From there every agent in the project can use them, and changes to a skill are reviewed like any other file.

## What happens to the memory my Hermes agent built up?

Sort it. Facts about the company go into `memory/` in the Kortix repo, where every agent reads them and where they are versioned with the rest of the project. Facts about one person, their tone and their preferences, stay personal. Hermes memory lives in `MEMORY.md` and `USER.md`; those entries are the raw material for the split. The [migration walkthrough](01-migration-walkthrough.md) covers this step.

## Do I have to stop using Hermes?

No. Hermes Agent is a persistent personal agent, and it remains a good fit for one operator on one machine. Teams move the company's skills, memory, automations, and shared work into Kortix and keep using whatever personal tools they like on the side. The migration is about where the company's state lives, and about how work that touches shared systems is reviewed.

## How is a Kortix session different from a local Hermes run?

A local run happens where you started it, with the host's permissions, and its changes apply in place. A Kortix session gets its own isolated cloud computer and its own branch. The agent commits on that branch and opens a change request toward `main`, and merge is default-deny for agents, so a person reads the diff before it lands. That review step is what makes it safe to let agents touch real systems.

## Which models can I use with Kortix?

Any model provider with your own keys, or the ChatGPT plan you already pay for. You can also sign in with your OpenCode Console account for OpenCode Zen and Go. Model choice is per agent, per session, or per message, so switching providers does not require a rebuild.

## Can I self-host Kortix?

Yes, and self-hosting is free. Kortix runs as one Docker Compose stack on your own box, in your VPC, or on your own on-prem network. Agent sessions run on a separate sandbox provider, which defaults to Platinum. The verified steps are in [Self-hosting](03-self-hosting.md) and at [kortix.com/docs/host](https://kortix.com/docs/host).

## What does Kortix cost?

Self-hosting is free. Kortix Cloud's Free plan is $0 with 200 credits per month and 1 project. The team plan is $40 per seat per month with 2,500 credits per month per seat, pooled, and seats for the people on your team. Bring your own model key and pay your provider directly. Current details are at [kortix.com/pricing](https://kortix.com/pricing).

## Does Kortix work in Slack and Microsoft Teams?

Yes. Slack and Microsoft Teams are live. Connect a channel, invite the bot, and mention it with a task: the message starts a session, the agent works on its own cloud computer with your connected tools, and it replies in the same thread. Email is experimental, per project.

## How does Kortix reach my company's tools?

Through connectors. Kortix covers 3,000+ apps in a click, plus MCP, OpenAPI, Postman, GraphQL and raw HTTP. Connector credentials are brokered server-side and never enter the machine. Each agent can be scoped to only the connectors its job needs, with each action set to Allow, Ask, or Block.

## Where do I start?

Run the [migration walkthrough](01-migration-walkthrough.md), read [Kortix vs Hermes](02-kortix-vs-hermes.md) for the side-by-side, or begin at [hermesalternative.com/migrate-from-hermes](https://hermesalternative.com/migrate-from-hermes).
