Kortix is the open-source AI Operating System.

# Self-hosting Kortix, next to self-hosting Hermes

Kortix is the open-source AI Operating System, and self-hosting is free. The verified setup steps for running Kortix on your own box follow, alongside the Hermes Agent path for comparison. Both are open source and both can run on infrastructure you own. The difference is what the stack has to hold: Kortix runs a team's whole company as one project, so its self-hosted stack is larger than a single-agent deployment.

The live guide is at [hermesalternative.com/self-hosting-hermes-alternative](https://hermesalternative.com/self-hosting-hermes-alternative).

## Two shapes of self-hosting

Hermes Agent self-hosts as a single application. You install it on a host, it keeps everything in `~/.hermes` (or `/opt/data` in Docker), and you run one gateway process. Its Docker image is stateless, and the mounted data directory holds config, API keys, sessions, skills, and memories ([Hermes Docker setup](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/user-guide/docker.md)).

Kortix self-hosts as one Docker Compose stack: the frontend, the API, the LLM gateway, and the Supabase distribution. Agent sessions run on a separate sandbox provider, not on this stack; the default is Platinum, and Daytona remains available as a legacy fallback ([Kortix self-hosting docs](https://kortix.com/docs/host)). That split is the point: the control plane runs on your box, and each session boots its own isolated cloud computer elsewhere.

## Self-hosting at a glance

| Dimension | Kortix | Hermes Agent |
| --- | --- | --- |
| Stack | Docker Compose: frontend, API, LLM gateway, Supabase | One application plus a gateway process |
| Session compute | A separate sandbox provider (Platinum or the legacy Daytona fallback) | Local, Docker, SSH, or a cloud backend |
| Install | One-shot bootstrap or the Kortix CLI | Installer script or the Docker image |
| Dashboard | The app itself, on your domain | A web dashboard on port 9119 |
| Updates | Automatic, or pinned to an exact version | Re-run the installer, or pull a new image |
| Data to back up | `volumes/db/data`, `volumes/storage`, and `.env` | The `~/.hermes` data directory |

## Self-host Kortix: the verified steps

### Fast path on a bare Linux box

Kortix publishes a one-shot bootstrap script that installs Docker, installs the Kortix CLI, and starts the stack. The exact command lives on [kortix.com/docs/host](https://kortix.com/docs/host), because the script URL is versioned with each release. This path runs on Linux only. On another operating system, use the manual path.

### Manual path

1. **Install the CLI.**

   ```bash
   curl -fsSL https://kortix.com/install | bash
   ```

2. **Point DNS.** Create an `A` or `AAAA` record for your domain and for `api.<domain>`, both pointing at the box's IP.

3. **Open ports 80 and 443.** The bundled Caddy proxy uses them to issue a TLS certificate.

4. **Initialize the instance.**

   ```bash
   kortix self-host init --domain kortix.example.com
   ```

5. **Start the stack and watch it come up.**

   ```bash
   kortix self-host start
   kortix self-host status
   kortix self-host logs
   kortix self-host doctor
   ```

6. **Configure the sandbox provider.** This is an interactive prompt for the sandbox provider key, and optionally a managed-git token.

   ```bash
   kortix self-host configure
   ```

7. **Sign in and connect a model.** Sign up in the dashboard, then connect your own LLM key in the model picker. Self-hosted instances use your own key by default.

### Evaluation mode

To try Kortix with no domain, use a Cloudflare tunnel instead:

```bash
kortix self-host init --tunnel cloudflare
kortix self-host start
```

The tunnel URL changes on every restart, so this mode is for evaluation.

### Memory, updates, and backups

Each API container has a 640 MiB memory limit by default, which is right for an 8 GiB host. On a 16 GiB host you can raise it to 1 GiB when API traffic reaches the default limit:

```bash
kortix self-host env set KORTIX_API_MEMORY_LIMIT=1024m
docker stats --no-stream   # confirm the applied limit
```

Every instance updates itself automatically. To pin an exact version, use `kortix self-host update --tag <version>`, and turn the updater off with `--auto-update off`.

Kortix has no separate backup system. Each instance stores its data as two directories under `~/.config/kortix/self-host/<instance>/`: `volumes/db/data` (the Postgres database) and `volumes/storage` (file storage). The instance's `.env` file holds every secret and signing key it uses. Back up all three before a destructive command.

## Self-host Hermes: the Docker path

For comparison, Hermes's documented Docker flow is two commands. Create a data directory and run the setup wizard once, then run the gateway in the background:

```bash
mkdir -p ~/.hermes
docker run -it --rm -v ~/.hermes:/opt/data nousresearch/hermes-agent setup

docker run -d --name hermes --restart unless-stopped \
  -v ~/.hermes:/opt/data -p 8642:8642 \
  nousresearch/hermes-agent gateway run
```

Inside the image the gateway is supervised by s6-overlay, so it restarts if it crashes. A built-in dashboard runs on port 9119 when `HERMES_DASHBOARD=1` is set, and a non-loopback bind requires an auth provider. Command approval, DM pairing, and container isolation are the security controls in the single deployment ([Hermes docs](https://hermes-agent.nousresearch.com/docs)).

## Which self-hosted shape fits

If you are running one agent for one person, Hermes's single-application stack is smaller and quicker to stand up. If you are running agents for a company, Kortix's split stack is the one that holds a team: the control plane and the company repo on your box, an isolated cloud computer per session, and a change request before work lands on `main`. You can run it on Kortix Cloud, in your VPC, or on your own on-prem network.

Read the full comparison at [hermesalternative.com/kortix-vs-hermes](https://hermesalternative.com/kortix-vs-hermes), or start the migration with [Migrating from Hermes to Kortix](01-migration-walkthrough.md).
