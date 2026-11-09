# Aftercard

Connect Aftercard using remote Streamable HTTP MCP. A local plugin or skill is not required; the server supplies instructions during MCP initialization.

## Endpoint

https://mcp.aftercard.ai/mcp

Inspect the exact configured endpoint before changing anything. Reuse an existing matching Aftercard connection and valid authorization. Other endpoints are separate services, not aliases. If the selected connection name already identifies a different endpoint, report the conflict without overwriting, disconnecting, or removing it.

## Connect

- Codex: use an available native MCP configuration action. If the server is absent and the existing CLI is available and permitted, run `codex mcp add aftercard --url https://mcp.aftercard.ai/mcp`. When sign-in is needed, use native Authenticate or `codex mcp login aftercard`. For a host-reported auth failure such as failureReason=reauthenticationRequired, use Settings → MCP servers → the exact connection → Authenticate. Manual fallback for an absent server: Settings → MCP servers → Add server, choose Streamable HTTP. Missing tools alone do not establish that sign-in is required. App Server protocol methods are not callable model tools. Do not automate the host's own UI.
- Claude Code: if the server is absent, run `claude mcp add --transport http aftercard https://mcp.aftercard.ai/mcp`. When sign-in is needed, use `/mcp` in the current session, select the exact Aftercard server and follow its Authenticate action. After OAuth, discover tools in the same conversation first; use the native reload action only if the host reports activation is required.
- Other MCP clients: add the endpoint through the host's existing Streamable HTTP configuration and use its supported OAuth action. If direct remote MCP or OAuth is unavailable, report that host limitation; do not invent a connector or install a local proxy.

Use host-managed OAuth when authentication is needed. Enter email and OTP only in the secure browser window, never in chat. The host stores tokens; do not request an API key, copy tokens, reconstruct callbacks, or inspect credential files. Reuse a pending sign-in instead of opening another flow.

A canceled or denied action stops this attempt. Report cancellation, connection failure, or unavailable host support at the phase it occurred; do not bypass a restriction with another client, local server, or repeated sign-in.

## Verify in this conversation

After configuration or authentication, use deferred tool discovery in the current task to find `get_magicpay_capabilities`. Continue in the same task when it succeeds. Missing tools alone do not establish that sign-in is required; use the host's supported MCP refresh control and report the actual blocker if tools remain unavailable.

Call `get_magicpay_capabilities`, then the authenticated `get_magicpay_status`. Preserve these protocol tool names. Follow returned `setupState`, `outcome`, `nextAction`, and instructions. Configuration saved, a healthy server, and an OAuth window closing are separate facts; only a successful authenticated tool call verifies a usable connection. If sign-in completed but tools are unavailable, report exactly that.

After authenticated status succeeds, silently read `get_payment_balance` without an asset selector. Report only verified connection, balance and available payment capabilities. An unavailable balance is unknown, not zero. Do not create a top-up link, payment or test purchase during setup.

## Payment controls

Offer only rails whose returned capabilities are ready. Keep simulated activity distinct from real settlement and follow server instructions for blocked or deferred features. User approval and exact amount, currency, recipient and limits remain required by each operation.

Use the host's built-in browser for browser checkout. Preserve the exact approved operation, session and one-time card. Do not install a second browser controller or treat fill, submission, timeout or cancellation as settlement. Reconcile uncertain results under the same operation; never create a replacement payment to retry them.

## Optional reusable skill

For a compatible local agent, install the reusable workflow with `npx skills add aftercard/skills --skill aftercard`. This installs guidance, not another MCP connection. Reuse the exact authenticated connection configured above.
The current Skills CLI installer requires Node.js 22.20 or later. This requirement applies only to installation, not an already connected MCP workflow.

Browser skill uploads, native plugins and Hermes tap installation are described at https://github.com/aftercard/skills/blob/main/docs/install.md. A skill installation does not prove OAuth or browser-payment capability.
