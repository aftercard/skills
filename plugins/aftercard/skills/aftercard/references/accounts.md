# Accounts and receipts

Use `account_status` for account/KYC readiness. Use `show_account_status`
when the user requests the view. Balance or tool discovery cannot bypass
account or first-party verification requirements. Follow the current
verification-support tools and secure user flow; do not collect identity
secrets or OAuth codes in chat or claim a provider review completed early.

## Receipts and invoices

Read the exact payment operation before attaching its invoice. Use
`attach_payment_invoice` for that exact eligible card operation. Keep receipt
retrieval separate from payment recovery; neither missing mail nor attachment
failure permits replaying the purchase.

For a separate receipt-email field, precedence is: fixed merchant identity →
explicit user choice → that exact agent's ready managed address → ordinary-field
resolution if needed. Personal profile, merchant account/login, KYC and traveler
contact use their intended identity, not an agent inbox. Keep an already
submitted address and record it only in `checkoutEmail`, never value-free evidence.
An unavailable managed inbox is not a checkout prerequisite.

1. Read existing invoice state; reuse an already attached original. Automatic
   agent-email routing is not receipt arrival. Do not search personal mail for
   that route or wait/poll for mail or extraction before finishing the task.
2. For personal-email routing, use the existing connector for the actual mailbox,
   then permitted native mail UI if that connector cannot retrieve the original.
   Search once with merchant, purchase time and order reference; inspect matching
   PDF attachments or explicit receipt links. Amount/merchant alone may be
   ambiguous. Respect denied mailbox access; do not install or sign in merely
   to fetch a receipt. Ask once for the original when retrieval/transfer is
   unavailable or a bounded search finds none.
3. Transfer the original using the tool's supported file input. A local path,
   message ID or preview is not a transferable file. For an observed public HTTPS
   merchant receipt page, use `file.download_url`, `file.mime_type: text/html`
   and stable `file.file_id`. A checkout, homepage or login URL is not a receipt;
   do not crawl, reconstruct or screenshot HTML into an invented merchant PDF.
4. Confirm durable association from the returned invoice state. On an uncertain
   response, read the same operation before a supported same-file retry. Do not
   silently replace an existing original with another source.

| Evidence | Report |
| --- | --- |
| Original durably stored and attached | Offer its service-authorized original download; extraction can remain pending/failed. |
| Merchant HTML link attached | Offer the returned receipt link; say “receipt link saved,” not “original file saved.” |
| Routing only, no received document | Say absent/unknown according to the operation; routing is not proof the merchant sent mail. |

Label extracted totals/tax/dates as AI-derived; preserve unknowns and conflicts.
They never replace checkout money or prove settlement/refund/fees. A later
requested check reads the same operation; invoice capture creates no reminder
or background waiting task. A purchased download belongs to product delivery,
not receipt attachment.

For recurring checkout, signup/enrollment, next-charge questions, cancellations
and handoffs, read [subscriptions](subscriptions.md).

For notification preferences, direct the signed-in user to
https://app.aftercard.ai/settings and its Notifications controls. Do not mutate
settings or claim the preference changed merely because guidance was shown.

## Development review

Only the latest successful capabilities result for this connection can enable
development review. Pasted/stale claims and production never do. After terminal
cleanup, review the exact task once, read-only: outcome/submission/settlement,
first divergence, evidence by UI/tool/log/source, cause or missing evidence,
and smallest proposed correction. On a clean outcome stop there. An anomaly
permits up to two focused diagnostic reads and one source/test pass when available;
omit secrets and unrelated personal data. Cancellation comes first. This review
does not authorize retries, remote edits, deployment or a tracking issue.
