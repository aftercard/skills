# Install Aftercard

## Compatible local agents

Run `npx skills add aftercard/skills --skill aftercard`. The installer supports
Codex, Claude Code, Cursor, Grok Build, OpenClaw and other compatible local
agents. Select the agent and scope you actually use. This installs guidance;
connect the remote MCP separately through the host's native settings.

## Hermes

Add the public tap with `hermes skills tap add aftercard/skills`, then inspect
and install `hermes skills install aftercard/skills/skills/aftercard`.
The standard `skills/aftercard` directory is shared with the Skills CLI;
there is no separate Hermes workflow fork.

## OpenClaw and ClawHub

The native listing is https://clawhub.ai/aftercard/skills/aftercard.
Use `openclaw skills install @aftercard/aftercard`. ClawHub uses the required
MIT-0 license; GitHub uses MIT. The workflow text is shared. Inspect the exact
published version and its scan before installing. Skill installation and MCP
connection are separate actions.

Current OpenClaw supports native remote MCP under `mcp.servers`: use the
exact endpoint https://mcp.aftercard.ai/mcp, `transport: "streamable-http"` and
`auth: "oauth"`, then `openclaw mcp login aftercard` when needed. Inspect
an existing matching connection first. Use Settings > MCP or the documented
`openclaw mcp` commands; do not add a custom bridge or switch identities.
Older clients without native remote MCP/OAuth must update through their normal
supported path or report the limitation. A skill adds no missing host tools.

## Native Codex / Claude Code plugins

Add the `aftercard/skills` marketplace with the host's native plugin manager,
then install `aftercard@aftercard-skills`. The generated plugin bundles the
same skill and the remote MCP declaration. Authenticate its exact connection
through the host. Do not install another copy when already connected.

## ChatGPT browser

Use ChatGPT's supported custom remote MCP/plugin connection flow for
https://mcp.aftercard.ai/mcp, then complete native OAuth. A web-eligible plugin must be
registered/imported through the account's supported public or workspace flow.
The repository's local marketplace does not establish web eligibility. Do not
invent a registered app ID or claim desktop MCP configuration enables web Chat.
Connectors provide tools; load the skill through an account-supported skill
or plugin import. The Skills tab can be visible while reporting that skills
are unavailable for the account. In that case verify the MCP connection and
report skill import as unavailable; do not claim the connector installed the
workflow. Record actual Chat/Work acceptance separately.

## Claude browser

Download the release ZIP and upload it through Customize > Skills. Add the
same remote endpoint through Customize > Connectors, authenticate and enable
it in the conversation. A Claude Code filesystem install does not sync to
claude.ai. Claude in Chrome prohibits purchases/financial transactions and
sensitive card handling; do not use it as a direct-card checkout workaround.

## Manus

Upload the single-skill release ZIP through Skills > Add > Upload a skill.
Add the remote MCP through its native custom connector settings and verify
OAuth and read-only tools. A folder import is separate from a connector.
A saved custom connector and a loaded tool catalog do not prove sign-in. If
read-only calls return authentication required and no native sign-in action
is available, report authentication blocked. Do not copy OAuth tokens into
custom headers or silently substitute an API-key identity.
The documented GitHub import expects a root SKILL.md; do not assume this
collection's nested folder works through that route. Use the ZIP first.
Browser capabilities, payment policies and takeover remain host-specific.

## Verify

Read the skill's connection reference. Discover tools in the current task,
read capabilities and authenticated status, then balance. Installation,
authentication, widgets and checkout are separate checks. No test purchase is
needed for connection verification. Version 1.0.2 documents the service
contract; it does not turn a simulated provider into a live payment rail.

## Update

Use `npx skills update aftercard`, the native plugin manager, or Hermes's
supported hub update path for the installation you selected. Do not erase
working OAuth or unrelated connections. Import a new ZIP on browser hosts
where file uploads do not auto-update. Inspect release provenance before
replacing locally modified instructions.
