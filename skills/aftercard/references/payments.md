# Payments, funding and recovery

Use the connected tools' current schemas and owned next actions. Preserve
amount, currency, asset, network, recipient, merchant, total debit and expiry.
Do not infer account readiness or spend authority from available balance.

## Balance and history

`get_payment_balance` returns wallet availability; activity is different.
`list_recent_transactions` is paginated agent-scoped workflow activity. Follow
the returned page/cursor unchanged with consistent limits. Even fully paginated
workflow activity can never establish settled spending or remaining allowance.
Purchases, compute, reserves, confirmed fees, refunds and other agents need
their owning operation/accounting reads. State period, timezone and scope;
without those records, report only the visible agent-scoped activity subtotal.
Use the provided money representation and formatter without floating-point
reconstruction. Unknown amounts or currencies remain unknown.

## Start the requested rail

- Crypto transfer: resolve exact recipient/address, network, asset and amount.
  For an unresolved named recipient, follow the [Secrets recipient recipe](secrets.md#named-transfer-recipients)
  before asking for an address. Materialize the address, network and asset
  together. A complete explicitly supplied tuple needs no Secrets lookup;
  Secrets release is not payment approval.
  Use `run_crypto_transfer` with the user's authorized debit and current schema.
- x402: a price-only request uses `quote_x402_payment`, presents the
  fee-inclusive debit/maximum and expiry, and stops. For an authorized purchase,
  obtain the exact request from current official provider documentation and
  use `run_x402_payment`. Do not pay as a probe or treat a registry description
  or historical token-label error as authority to change the request.
  Validate provider-specific identifiers and units before binding paid input:
  a flight API's airport/location ID is not a city name, and a service duration
  such as five minutes is not its 300-second payment-authorization window.
  Preserve the user's airport scope; do not silently select one airport from a
  multi-airport city. A bound request stays unchanged even when its input is wrong.
  Report an unsupported method as a limitation of that exact endpoint, not proof
  the whole provider is broken. Missing payer allowance/liquidity does not
  authorize a gas top-up, wallet switch or a separate approval transaction.
  After explicit acceptance of a quote, preserve its `acceptedQuoteRef`,
  original clientRequestId and exact request. The execution maximum debit may
  be tightened to the accepted fee-inclusive debit, but must remain at least
  the quoted debit and no greater than the preview maximum. Expired or changed
  terms require a fresh preview and acceptance before execution.
- Browser: `begin_checkout_task` binds the exact intended checkout; it
  neither opens nor controls the merchant page. Follow [browser execution](browser.md)
  for preparation, saved/current-run ordinary values, filling and observation.

Continue each payment run with `wait_payment` using its exact returned `taskId`,
cursor and next action. Request, task and operation statuses describe their own
records; one record’s status does not establish another’s outcome. Approval and policy checks are owned by that run.
Do not ask for a redundant chat confirmation before or after Aftercard's
approval, repeat an approval, or replace it with a generic action request.
The host's own permissions and required confirmations still apply.

## Choose a checkout card

An owned issued Aftercard and a personal card saved as `payment.saved_card` in
Secrets are separate sources. A saved-card count never proves issuance or
Aftercard balance. Follow the current `cardGuidance` message and actions from
status/capabilities or the browser run. Issuance is the recommended option when
available; the user may use a saved card or add their own instead. Report pending,
unavailable or unknown issuance honestly. Do not initiate issuance from guidance.

Pass `cardChoice` only for an explicit choice. When the run returns
`state: "input_required"`, present its existing `cardInputRequest` through the secure
request form, then continue that same `taskId` with `wait_payment`. The service
selects exact saved revisions, collects only missing fields and binds the source
before payment approval. Card entry defaults to **Use once**; **Save this card**
is an explicit choice. Retrying cannot repeat Save or switch the bound source.
Keep PAN and CVV in the protected form and scoped browser output, never chat or
generic Secrets materialization.

For `fundingSource: external_card`, the personal card funds the merchant directly.
The purchase does not require or debit Aftercard wallet principal; normal spend
limits and payment approval still apply. A visible order confirmation can complete
the merchant workflow while issuer settlement remains **unverified**. Report that
distinction. Include this exact value-free evidence when recording a confirmed
order on the original submitted attempt. Put this object inside `valueFreeEvidence`:

```json
{"merchantConfirmation":{"kind":"order_confirmation","executionAttemptId":"<original attempt UUID>","merchantDomain":"<exact checkout host>","amount":{"quantity":"1250","scale":2,"currency":"USD"},"orderReference":"<visible order ID>","visible":true}}
```

Use the observed amount, currency and order reference. Missing or mismatched
evidence remains unresolved; a success flag alone is insufficient. An uncertain
click keeps `submission_unknown` even if an order later appears; follow the same
attempt's recovery and never submit again to obtain better evidence.
For a pending personal-card result, continue observing that checkout. Record a
later confirmation on the same attempt and `clientResultId`, preserving the
original `clicked` and `submitted` facts, then use `wait_payment` to recover the
same operation. Personal cards have no Aftercard issuer-settlement notification.

## Funding

Start with the tool for the requested funding intent; balance and method
discovery are not prerequisites for these composed actions.

| Request | Action |
| --- | --- |
| Generic “top up”, or show/open the top-up view | `show_topup` once; follow its returned view/link. |
| A funding link | `create_topup_link` once; return its fresh hosted URL. |
| Available direct funding methods | `list_funding_methods`. |
| Every direct funding address | `request_topup_addresses` without `asset`, with one stable `requestKey`. |
| Direct addresses for one asset | `request_topup_addresses` with that exact `asset` and one stable `requestKey`. |

For a hosted-chat how-to-top-up support question, follow the host's funding
handoff: return live direct addresses and a hosted link using the two composed
tools above. This support question needs no balance or setup detour.

When `show_topup` returns a hosted URL, present it without minting another.
If it returns widget data but the view cannot render or is reported missing,
use `create_topup_link` once. Returned widget data does not prove visibility.
Present successful address methods with their exact asset/network labels and
name unavailable methods separately. Preserve successes on a partial result;
retry the same aggregate request with the same `requestKey`. An unsupported
asset does not authorize another network or a hosted-link substitution for an
address-only request. Use lower-level `request_funding_address` only when an
authoritative flow already supplied the exact asset namespace, ID and network;
keep that tuple and its `idempotencyKey` unchanged on recovery.

Use balance and exact funding-operation reads when needed to verify funds.
A displayed address, opened link or changed balance does not prove a particular
payment or deposit settled.

When the owning run reports required funding, retain its existing `fundingGate`
and any returned approval/request. State the verified shortfall. Reuse only an
actually returned request/link and an explicitly supplied deadline; the gate’s
amounts/status alone contain neither. If the result reports a missing/failed
approval handoff, follow that blocker and reason without manufacturing a link,
deadline or replacement approval. Use `show_topup` only when the owning next
action directs it or the user requests standalone funding. A pending manual
approval still needs its recorded decision; a deposit does not approve it.
After timeout or expiry, read that same run/request; elapsed time never permits
replacement.

| Observed condition | Continuation |
| --- | --- |
| Customer balance shortfall | Follow the gate’s exact amounts and the returned continuation. Reuse a request/link/deadline only when actually supplied. Preserve partial funding and any pending approval. |
| Card-pool capacity or payment service unavailable | Report the service blocker. A funded customer wallet does not repair service capacity; do not ask the user to top up for it. |
| Funding settled and the service returns a resumable operation | Verify the same owner, recipient/merchant, money, unexpired approval and absence of prior submission; resume only that returned operation as directed. Funding alone grants no replay or new spending authority. |
| `foregroundAction: await_settlement_notification` | End foreground polling and report the submitted/pending outcome once. Retain the same operation for the service notification or a later requested status read. |
| Dispatch or merchant submission uncertain | Recover/reconcile the same operation; do not resume its submission or create a replacement. |

## Observe and recover the same operation

Use `get_payment_operation` for the exact payment and
`reconcile_payment_operation` only as directed for that operation. Do not
repeat a possibly dispatched transfer, purchase or merchant submission after
a timeout, missing output or unclear result. Preserve all operation, session,
request, approval, run and idempotency identities and unchanged retry input.

For browser results, inspect the live page and use
`record_browser_payment_result` on the original attempt as its current schema
requires. `list_card_transactions` can provide evidence for that exact card;
one incomplete page cannot establish that a charge is absent. Merchant paid
confirmation, provider processing and durable settlement must be reported as
separate observations. Verified purchased output belongs to the user; deliver
it through the [result procedure](#deliver-the-purchased-result).

Follow current financial and cleanup state. The legacy `freshStartAllowed`
summary alone is not task-wide permission. A separately requested additional
purchase must explicitly authorize possible duplicate charges, use fresh
identities and satisfy normal balance/approval limits. A service result that
disallows fallback cannot be bypassed by another route or client.

An `invalid_payment_run_failure` with `durableState: unknown` establishes no
payment outcome. Keep the same run/client request identity and follow its
next action to repair the existing connection or check that payment's status.
A malformed or lost response is neither a balance shortfall nor permission for
a replacement.

User cancellation preempts further payment actions. Use
`cancel_checkout_task` for the exact workflow and follow cleanup and
same-operation reconciliation. Cancellation is not proof of refund, release
or settlement. Report preserved recovery state honestly and continue normal
browsing or independently authorized work when available. Use
`fail_checkout_task` only for an open workflow when its native terminal
failure/cleanup contract directs it; preserve existing terminal outcomes.

To describe an old payment as safely released for replacement, read its current
closed workflow and require `cleanupDisposition: released_pre_submit` or
`released_after_failure`, `settlementStatus: not_started` or `failed`, and
`nextAction: none`. Failure/expiry alone proves no release. These facts do not
authorize new spending or release another operation's hold. Other cleanup
variants follow their actual service continuation; do not infer a usable branch
from a legacy description or schema mismatch.

The explicit `released_platform_liability` disposition is different: when the
workflow is canceled, the operation is `written_off` (reported as
`CARD_CANCELLATION_WRITTEN_OFF`) and `nextAction: none`, the customer's hold was
released and Aftercard assumed the unresolved cost. Issuer settlement can remain
pending. Report those facts, never a verified decline or pre-submit release.
Keep the old attempt closed. Before a fresh purchase, verify merchant failure
and no booking, then follow the service's allowed continuation with fresh exact
approval, budget and funding checks; this release does not reset spending limits.

For x402 `upto`, the approval maximum is a ceiling; the verified
`amounts.customerDebit.actual` is the charge. Zero actual debit plus confirmed
reserve release means no charge. Financial settlement and product fulfillment
can differ: settled money with missing/unverified output needs same-operation
support/retrieval, never another purchase. A seller continuation needs its
documented exact endpoint, method and complete inputs; do not probe an invented
order/invoice route or construct private signatures.

## Deliver the purchased result

Present the integrity-verified original before an optional summary:

| Returned output | Presentation |
| --- | --- |
| `deliverable.json` / `.text` | Meaningful seller JSON or bounded text. |
| Image / binary attachment | The host's image/file output; never dump Base64 or invent a public URL. |
| Purchased license, eSIM QR/LPA or API credential | Deliver privately to its owner: this is the purchased product even if fields are called `credentials`, `key` or `token`. |
| Explicit seller bearer/continuation capability | Keep private; use only its supported continuation to retrieve the product. Show the final deliverable, not that capability. |

Retain media type, length, digest and expiry with the result; report an integrity
or ownership mismatch rather than reconstructing output. For a completed x402
run, `wait_payment({taskId, timeoutMs: 0})` retrieves the same original without
paying. An embedded `aftercard` resource URI identifies returned bytes; it is not
a browser URL or a separate resource-read endpoint. Keep already retained output
available even if a continuation expires. Receipt attachment follows
[accounts](accounts.md#receipts-and-invoices) on the same payment operation.
