# Browser checkout and merchant accounts

The current host owns navigation, live-page understanding, screenshots,
ordinary/protected input, challenges, submission and observation. Aftercard
supplies financial authority and authorized values; it does not operate a
second browser. Use only the host's documented APIs and actual permissions.
If browser access or required payment input is unavailable or prohibited,
report that limitation and preserve the operation. A skill cannot override it.

After a payment-method or form-revealing transition, discard stale targets
and inspect the rendered page again. Hidden DOM/accessibility presence is not
visible availability. Use screenshots and normal visual reasoning throughout,
including after filling. Handle frames, intermediate controls, field errors
and challenges through normal host recovery while keeping the same checkout.
Do not replace a payment to repair a form or install another controller.

Verify merchant, item, amount, currency, total and billing roles against the
current task and approved operation. Only the unchanged approved checkout
and its exact final action are authorized. A material change requires the
owning service's new decision/approval path; never reuse the earlier approval
for a different merchant or amount. Respect explicit user “do not buy”.

Authorized one-time card and Secrets values may be visible to the model and
host. Use them only through host-supported targeted input for the approved
tab and task, including documented per-key input or a host REPL wrapper when
required. This does not provide transcript isolation. Do not quote values,
save them in helper scripts/files/logs or exported evidence, inject credentials
with arbitrary page-evaluation code, or send them to another site/message.

## Prepare the exact payment

For a USD checkout, use the exact merchant money unchanged as both `amount`
and `maximumDebit`. For a non-USD checkout, keep the observed merchant amount
and currency in `amount` and prepare one fixed USD `maximumDebit` before the
first `run_browser_payment` call:

1. Fetch the direct pair
   `https://api.frankfurter.dev/v2/rate/{MERCHANT_CURRENCY}/USD` using the
   observed uppercase ISO currency. Require successful JSON with matching
   `base`, `quote: "USD"` and a positive `rate`. Use that forward rate, not a
   search snippet, broad table, inverted pair or estimated rate.
2. Treat the rate as USD per merchant-currency unit. Apply the existing 5% FX
   allowance and round upward once, at the final USD cent:
   `usdCents = ceil(merchantMajorAmount × rate × 1.05 × 100)`.
3. Pass `maximumDebit: { quantity: String(usdCents), scale: 2, currency: "USD" }`.
   Keep the local `amount` unchanged; atomic quantities in different currencies
   are not comparable. Use exact decimal/integer arithmetic for the calculation.
4. Show the merchant money, pair/date/rate, 5% allowance and resulting USD
   ceiling. A failed fetch, invalid pair or failed calculation stops preparation
   before a run is created.

The first call binds this ceiling. Keep the same `maximumDebit`, merchant
amount/currency, `clientRequestId` and the payment run’s returned `taskId` through approval, ordinary-field
resolution, waiting and recovery. Do not refetch or reprice an existing run.
Use the composed run directly; it owns balance, approval and card preparation.
Follow its [card-source guidance](payments.md#choose-a-checkout-card) for personal
saved cards, one-time entry and merchant confirmation without issuer settlement.

Use `browserExecution.card.billingAddress` for the card's billing address in
preference to a personal address from Secrets. Adapt its supplied street, city,
postal code and country to the current rendered controls. If a required
component remains missing, discover saved details and use
`resolve_browser_form_values`, or the run's returned secure ordinary-field
continuation, for only the missing requirements. Keep the same payment run;
missing billing details belong in that secure flow, not ordinary chat/native
input. Do not invent components or save the card address as a personal address.

Carry ordinary contact details already supplied or returned by this purchase's
ready Secrets batch into `run_browser_payment` without another collection:

| Input | Use for |
| --- | --- |
| `ordinaryValues` | Known current-run ordinary values, with matching role and value-free `fieldRef`; phone uses the full logical number. |
| `ordinaryFields` | Unresolved saved references with exact item/field revisions. |
| `ordinaryFieldRoles` | Requirements still genuinely missing. |

Keep protected/card values out of these ordinary inputs. The checkout's session
is distinct from the general form/task session; reuse its exact returned ID.
For a processor-hosted checkout, `merchantDomain` is the checkout host;
`sellerDomain`/`sellerName` identify the observed actual seller, not the processor.

For travel, retain included cabin baggage and decline optional checked bags,
insurance, paid seats, priority, meals, assistance and donations unless requested
or covered by an applicable preference. Summarize once with the final total.
Ask only for mandatory unresolved choices or material changes. Follow child
seating rules, using free adjacent assignment where available; do not silently
accept separation or a mandatory added charge.

## Recover, submit and observe

For a failed field action, reobserve the visible enabled control and its current
frame; use supported targeted typing/per-key input if fill fails. Replace only
incorrect partial input. Verify ordinary values normally; verify payment fields
by presence and merchant validation without reading credentials back.

Before final dispatch, a stale tab handle does not invalidate the Browser or
payment: reacquire the exact live tab, replay the same run/key if needed, and
refill only missing allowed fields. Reopen the approved checkout only if the tab
is gone and dispatch definitely has not occurred. Navigating to the current URL
is not a safe reload of a POST-backed checkout; recover through its retained
merchant flow. If bounded recovery fails, hand off that checkout.

| Observed state | Record and continue |
| --- | --- |
| Payment-method selector, next step or field error | Ordinary interaction in this run; not payment dispatch. |
| Total changed before the final action | Record `not_clicked`, `not_submitted`, `nonSubmissionReason: checkout_total_changed`; follow [cleanup](payments.md#observe-and-recover-the-same-operation) before a fresh exact approval. Stabilize the total first; ask again only for changed authority/budget. |
| Final payment action activated once | Record `clicked` with the separately observed submission and merchant outcome. Keep that isolated final action distinct from filling/navigation. |
| Timeout/interruption overlaps final activation | Record `click_uncertain`, `submission_unknown`; observe/reconcile the same attempt. No refresh, refill, navigation or another Pay to recover. |
| Receipt, merchant error or processing deadline | Update the merchant outcome/evidence on the original run/attempt and `clientResultId`; preserve the original click result and submission state. A later error does not prove non-submission. |

After the final action, inspect fresh rendered state for receipt, error or human
challenge; a spinner is intermediate. Use interruptible read-only observation
within the host's limit, normally up to 60 seconds before recording pending.
Hosted clients may supply their own processing loop. Read the exact card's
transactions when useful: a processor name may differ from the seller and an
empty/incomplete page does not prove no charge. Report confirmed merchant
purchase promptly, with provider processing/settlement stated separately.

For example, after a timeout recorded uncertain dispatch, a later paid receipt
updates only merchant evidence. Reuse the actual original IDs/key:

```json
{
  "taskId": "10000000-0000-4000-8000-000000000001",
  "executionAttemptId": "20000000-0000-4000-8000-000000000001",
  "clientResultId": "original-browser-result-1",
  "result": "click_uncertain",
  "submissionState": "submission_unknown",
  "merchantOutcome": "success",
  "valueFreeEvidence": {"observation": "Paid booking receipt observed after the original timeout"}
}
```

A completed composed run already closes its workflow; do not call a second
closer. Finish the requested delivery through the merchant's observed download
or success link, including its original product page when needed. Present the
purchased file through supported host output; otherwise report paid and
retrieval blocked separately. A purchased PDF is the product, not an invoice.

## Login, signup and forms

Inspect the current page first. If already signed in to the intended account,
continue without another login or Secrets release. Ask which account only when
ownership is ambiguous. Otherwise inspect URL-scoped saved `login.website`
fields before asking whether an account exists. Use [Secrets](secrets.md) to discover and release
exact fields, or `resolve_browser_form_values` to collect missing values
securely. Reuse the saved account. Signup is a separate user-intended action
when needed, not permission to accept unrelated terms or create identities.
Recommend a dedicated new merchant account when none is usable or the user
wants separation; preserve the existing account by default.

If several suitable saved logins remain, use the existing Secrets candidate
chooser for those accounts. Do not select the first match or add a separate
account-existence question or generic choice beside that secure chooser.

Website login email/username belongs to the site's `login.website` secret,
not Personal profile. Do not bind a profile email as an existing account login
or combine its email with another login's password. If the login identifier is
missing, collect it in the typed Website login form; the authenticated form may
prefill profile email as an editable suggestion. Confirmation there establishes
the login identity; the agent must not copy profile values into the login.

When collecting a login, follow [secret naming](secrets.md#name-secrets-for-their-purpose):
prefill `collectionGroups[].secretName` with a service-specific name such as
“Omni gym login” and `description` with its intended reuse. These are editable
metadata, separate from the login email and password.

If the merchant explicitly rejects credentials, keep the same browser/cart/task
and follow [secure login correction](secrets.md#website-login-correction) first:
update the existing Website login in the secure form, then try its new revision.
Do not ask the person to sign in manually just because saved credentials were
rejected. A challenge, outage or denied release is not an invalid-password result.

Collect only the method currently shown: an email-only/passwordless page does
not need both username and password. Scope saved login discovery to the actual
credential destination after redirects; `www.example.com` and
`login.example.com` can differ. Do not invent aliases or move credentials to a
different site to obtain a match. Verify sign-in on the resulting page, then
resume the same selected item and cart.

### Routine signup questions

Complete low-impact administrative questions without interrupting the user.
For “How did you hear about us?”, referral-source surveys and similar marketing
fields, skip optional inputs; when required, prefer “Other”, “Prefer not to say”
or a general answer supported by the current task, such as online search when
that is how the service was found. If no neutral answer exists, choose the
closest non-specific offered option. Do not invent a named referrer, referral
code or personal history, or collect these answers as reusable Secrets.

Decline optional marketing, newsletters, upsells and paid extras unless requested
or covered by an applicable preference. Ask only when the unresolved answer
materially affects price, eligibility, service, legal consent or an actual
personal fact needed for the task. A required marketing-survey field alone is
not a reason to create a choice request or stop signup.

For ordinary passenger, contact, address and other form fields, do the work in
the host browser using the details already supplied or authorized. If fields
are blank, invalid or an attempted Continue leaves the page unchanged, inspect
the current controls and validation, correct or refill what is needed, and
try the non-payment step again. Recover saved details through the same Secrets
resolver when earlier values are absent from the current context; follow its
exact release/consent recovery rather than asking the person to fill the whole
form. A stalled page or lost field contents alone is not a human-only challenge.

### Human browser handoff and continuation

For SSO, OTP, passkey, QR or another observed step requiring a human, or when
the user explicitly chooses manual sign-in, use one supported browser handoff
and retain the same live browser, cart and task. Explain the specific step and
that **Hand off to agent** inside the browser returns control so you can continue.
The handoff exposes **Open browser** only; do not add “I’ll sign in”, Done,
Continue, a free-text answer or a separate choice between browser access and
signing in manually. They are the same action. Keep the host's durable input
wait active until browser control returns. An intent answer or opened link is
not a completed handoff. If the link is unavailable or expired, use supported
handoff recovery rather than asking for a substitute completion answer.

When the host returns browser control, resume the original task in that browser
immediately. In the Aftercard hosted relay, **Hand off to agent** (or the earlier
**Done**) resolves the existing input and automatically resumes the paused
agent; no extra “go on” is needed. Other hosts own their documented resume path.
Inspect fresh rendered state first, including the current tab and validation.
The handback proves that the person returned control, not that login, form
submission or payment succeeded.

Continue the remaining authorized work from what you observe. If an ordinary
form is still incomplete, recover the authorized details and correct/refill it
yourself before retrying the next non-payment step. If another human-only step
remains, ask only for that specific blocker through the same supported input
flow. Finish the turn when the task is fulfilled or a real durable wait exists;
a status report about the unchanged page does not complete the task.

Preserve the original task, checkout, requests, payment run, approval and
submission evidence. After interruption, recover the actual current page and
operation. Do not reopen or reconstruct a checkout merely because control
changed hands, or repeat a possibly submitted purchase. If the original browser
is unavailable, follow the same-operation recovery guidance above. A handback
adds no payment authority.

Non-payment form preparation does not authorize submitting it. Resolve saved
details and missing fields through the same Secrets workflow. Saved card,
default-card changes, recurring authorization and the purchase are distinct
actions. A one-time checkout card is not a permanent merchant-locked card;
claim merchant restrictions or approval enforcement for later charges only
when verified provider support establishes both. Never make a test charge or
silently delete a saved card or transfer subscriptions.

Receipt-email precedence and original-document handling follow
[accounts and receipts](accounts.md#receipts-and-invoices).
