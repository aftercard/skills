# Subscriptions

Read this for recurring-payment recognition, signup/enrollment terms, saved
subscriptions, next-charge questions, selected cancellations and reminder replies.
Use the host's [browser and saved merchant login flow](browser.md) and the
existing [payment approval/recovery flow](payments.md). Current tool schemas and
owned structured results govern exact arguments and continuation.

## Recognize the selected obligation

Before choosing checkout type, inspect the actual final selected offer and its
billing terms. During a user-requested billing review, inspect the merchant's
account/billing page and relevant receipts or correspondence available within
that task. Decide whether this is a new recurring obligation, one-time purchase,
payment of an existing invoice, or merely an option to save a card. A merchant
name, Stripe URL, save-card checkbox or repeated amount alone is insufficient.
This commercial interpretation belongs to the agent. The backend stores the
interpretation and validates types, approvals, limits and identity; it does not
classify subscriptions from merchant lists, URL rules or transaction patterns.

Supply `subscriptionDetails` through checkout creation and result reporting:
service/plan identity in `planLabel`, full `recurringAmount` and
`recurringCurrency`, `intervalUnit` and `intervalCount` (legacy `interval` remains
supported), `recurringAmountKind` (`fixed`, `variable`, `unknown`), `trialEndAt`,
`introductoryPeriod`, `nextRenewalAt`, `autoRenews`, known `expiresAt`,
`cancellationUrl`, `cancellationHint`, and optional `serviceLogoUrl` and
`estimatedRenewalUsd`. Use `billingTerms` for material qualifiers not represented
by those fields: usage charges, taxes, deferred fees or cancellation charges.
Keep descriptive extractor prose out of those material terms.

For paid recurring signup, use `begin_checkout_task` with `type: "subscription"`.
Then use `run_browser_payment` with `paymentType: "subscription"` and the full
`subscriptionDetails`; omitting `paymentType` defaults the run to one-time.
Aftercard’s approval
binds both the money due now and recurring terms together; do not split it into
a generic action approval plus a second payment approval. Follow the same run
when terms change or approval is pending.

Respect the requested billing cadence when comparing offers: an annual plan
with a monthly price equivalent is not a monthly-billed option.
Save the full period charge: $240 annually means a $240 next charge; $20/month
is only a secondary equivalent. Quarterly means month/count 3. A paid
introductory price is not a free trial. Variable/usage pricing stays variable;
unknown money/date stays unknown, while an observed zero remains zero. Prefer
the merchant's stated next date. Otherwise reasonably calculate an expected date
from observed terms and explain uncertainty. Calendar advancement is only a
fallback for an already described schedule; explicit agent corrections win.

Recognition or saving details does not authorize signup or change approved money
or recurrence. If the selected offer changes before submission, preserve the
original operation and use its existing approval-update flow for material terms.
Plan-label copy is cosmetic; a newly disclosed fee is material.

For supported user-completed zero-due enrollment, use
`request_subscription_enrollment`, then `complete_checkout_task` with its
observed enrollment outcome. Preserve its approved terms, checkout and request.
The user supplies their card and submits directly at the merchant
(`executionOwner: user`); a merchant card prompt does not switch this route to
a payment run. If the amount due becomes positive, reassess the changed terms
and obtain the appropriate authority before charging. Never invent a verification
charge. Enrollment is not payment settlement.

## Read and correct saved details

Use live `list_subscriptions`, or `show_subscriptions` when a view is requested.
Pass a selected `subscriptionId` for current details, cancellation history and
original invoice evidence. Describe only saved owned records; this registry does
not discover all of the user's subscriptions. Plain listing remains read-only.
Show the full billing-period charge, renewal-on/off state and expected or unknown
next date. A known-paid subtotal covers only its stated payments.

After a requested later billing review, call `update_subscription_details` on
the same record with the exact `updated_at` string from the latest read as
`expectedUpdatedAt`. Supply only observed corrections: `serviceName`, nullable
`serviceUrl`, nullable `planLabel`, nullable `billingTerms`, and a strict
`subscriptionDetails` patch. Omitted fields are preserved; null clears nullable
values. A fixed amount needs currency and an interval count needs its unit.
Unknown/variable price clears fixed and monthly summaries. Changed money clears
a stale USD reminder estimate unless a replacement is supplied.

Use the existing cancellation tool for renewal state, progress and evidence;
the details action excludes `autoRenews`, status, cancellation progress, evidence
and mail references. Correcting an expiry cannot enable renewal. Later observed
`billing_terms` and plan/price describe the current record; they never rewrite
the original approval snapshot/hash, source identities or paid history. Do not
replay checkout completion, create another signup or invent a settled payment to
refresh metadata. On a stale version or uncertain update response, reread and
compare the requested fields. Preserve a newer correction instead of resubmitting
an old patch blindly.

The signed-in hosted agent can manage selected saved subscriptions across the
user's agents through the subscription-only owner adapter. Its owner comes from
the authenticated connection; never supply an owner or create a user-wide MCP
key. External MCP credentials keep their existing agent scope. Related history,
invoice and linked correspondence reads use the subscription's owning agent.
Generic email authority does not expand. When the host cannot send through that
owning mailbox, return the support draft and concrete next step.

## Cancel selected subscriptions and resume handoffs

For each selected record, read current details/history, then call
`cancel_subscription` for its current instruction. Use the host browser and
saved merchant login flow, perform the user's requested cancellation, report
the observed outcome through that tool, and reread the record. No extra approval
is needed for an unambiguous requested cancellation with unchanged terms.
Preserve remaining paid access when offered. Verify an explicit user condition
such as full refundability before acting.

If several saved subscriptions match, use the existing choice/input request for
only the missing distinction. Keep independent results for every selection;
one failure cannot turn another into success. A reminder reply such as “cancel
it” refers to its owned subscription context when present; otherwise resolve
the missing target through the existing choice/input flow. Stopping the agent's
task is different from canceling merchant renewal.

Preparation is not success. Report `cancelled` or `already_cancelled` only after
observing renewal off. For login/MFA/CAPTCHA, a fee, loss of remaining access or
a merchant failure, persist `needs_user` or `unsuccessful` as appropriate with
the safe destination, observed state and concrete next action. Keep ordinary
credentials on the protected path. An uncertain or failed cancellation remains
unresolved: inspect current merchant state before another potentially duplicate
click. Preserve the exact selected subscription and existing cancellation effort
through handoff, support-mail replies and continuation; an acknowledgement does
not prove cancellation. Read linked correspondence using the existing supported
mail flow, and keep [email authorization](requests-and-email.md) intact.
