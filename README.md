# Aftercard Skills

Reusable purchasing and payment guidance in the open Agent Skills format.

`npx skills add aftercard/skills --skill aftercard`

Connect the remote MCP at https://mcp.aftercard.ai/mcp through native settings, using OAuth or the explicitly selected external-agent credential. The skill alone does not connect an account or grant spending authority.

[Installation by platform](docs/install.md) · [Skill](skills/aftercard/SKILL.md) · [Releases](https://github.com/aftercard/skills/releases) · [License](LICENSE)

Hermes users can add this repository with `hermes skills tap add aftercard/skills` and install `hermes skills install aftercard/skills/skills/aftercard`. Browser users can upload the release ZIP where supported.

The same skill is bundled in thin native plugins. Business logic and authorization stay on the service; browser execution stays with the host. Tool names, wire contract labels and OAuth scopes use Aftercard.

Generated from the maintained Aftercard skill and current service registrations in [the source project](https://github.com/aftercard/aftercard). The maintained playbooks follow current Aftercard service contracts. See release.json for source and file hashes. Local package validation does not establish OAuth, live payment readiness or browser-host acceptance.
