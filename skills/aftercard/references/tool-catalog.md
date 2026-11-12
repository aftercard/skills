# Current Aftercard MCP catalog

Use live tool schemas for exact arguments; these descriptions are a release snapshot.

## account_status

Read the connected account's KYC status, agent readiness, and setup blockers. Use payment-balance tools for funds.

## attach_payment_invoice

Attach an original PDF or public merchant invoice/receipt link to an eligible card operation. Inspect asynchronous invoice processing; settlement is unchanged.

## begin_browser_form

Prepare a non-payment form in the current taskId, or create a standalone task with a stable clientRequestId. Replay creation with the same key and unchanged inputs. The host browser fills the page.

## begin_checkout_task

Create or resume a checkout task with a stable clientRequestId and unchanged create inputs. Replay returns its current state. The host owns browser navigation and checkout execution.

## begin_request_task

Create or resume a general task using a stable clientRequestId and unchanged create inputs. Replay returns its current state. Creates no request and authorizes no downstream action.

## call_provider_mcp_tool

Call an advertised tool at an enabled public MCP endpoint. No automatic retry; reconcile uncertain side effects. Paid actions need Aftercard payment approval. Supply no credentials or card values.

## cancel_checkout_task

Cancel the checkout and return cleanup, settlement, operation, and recovery facts. Cancellation does not prove non-submission or settlement.

## cancel_subscription

Prepare or record cancellation progress and merchant guidance. The host performs merchant actions; report renewal off only after confirmation.

## check_merchant

Return advisory seller history and optional unsigned x402 probe results. Unknown/unavailable is inconclusive; no payment or spending authority is created.

## check_task

Read a task's current state, timeline, events, and pending requests without presenting a view.

## check_task_request

Read one task request without presenting its view or claiming submitted values.

## complete_checkout_task

Close a checkout using observed merchant evidence or approved user-completed enrollment. Workflow completion can precede payment settlement.

## confirm_request_otp

Submit a fresh six-digit OTP to the existing request identified by taskId and requestId.

## correct_secret_field

Create a secure correction request for one editable field and its current target. Accepts a field handle, not the corrected value.

## create_secret

Create typed Secret metadata under an entity. Templates define fields and sensitivity; use save_secret to store supplied values.

## create_topup_link

Create a short-lived funding link on explicit request or after an unusable widget. Reuse any hostedUrl already returned by show_topup.

## decide_request

Record the user's decision. Use confirmed for a generic choice and choose_candidate for a Secret choice. Enter missing field values through the secure form.

## delete_preference

Archive a preference at the user’s request. Archived preferences stop appearing in task recall and suppress inferred recreation.

## delete_secret

Archive an editable secret at its current revision. Stored fields and values are retained.

## fail_checkout_task

Record checkout failure with its stable key, code, and message. Preserve cleanup/recovery facts; failure alone does not prove non-submission.

## get_aftercard_capabilities

Read current payment-rail eligibility, execution mode, environment, and build identity for the selected agent.

## get_aftercard_runtime_status

Read the connected agent's identity, status, and resolved Secret policies with their revisions.

## get_aftercard_status

Read connected account/agent status, scoped payment-rail eligibility, and Secret policies. Each section reports its own availability; this check authorizes no spending.

## get_agent_email_send

Read an email send's approval, submission, and deliveryStatus state. Recover submission_unknown using the same send ID.

## get_checkout_result

Read one checkout's observed payment outcome and evidence source; retain pending or ambiguous results.

## get_checkout_task

Read one checkout's workflow state and current request.

## get_payment_balance

Read the unified available balance. Supplying asset returns a diagnostic projection, not spending authority.

## get_payment_operation

Read a payment operation's state, settlement, and next recovery action. Only completed with its verified result proves settlement.

## get_request

Get a request's state, collection policy, review link, and OTP availability. Does not claim submitted values.

## get_secret

Read one secret's entity, template, revision, fields, and availability. Returns metadata, not stored values.

## get_secret_catalog

Read value-free Secret candidates and unavailable states for one session, URL, and purpose. Returned refs are opaque handles.

## get_secret_footprint

Read a bounded, value-free Secret snapshot; filter by entity, site, or metadata text. Reuse its URL, purpose, and revision when resolving values.

## get_secret_profile_context

Read saved-person metadata or derive age for entityId/asOfDate. Returns permitted age and source revisions, not birth dates; current consent rules apply.

## help_identity_verification

Explain general identity-verification requirements. Use live account/workflow status for the user's requirement; this tool does not start verification.

## learn_preferences

After the owned root task is terminal, submit reusable preference evidence for asynchronous reconciliation. Use a stable idempotencyKey. Agent reports remain inferred; queued does not mean saved. Reads never learn.

## list_agent_email_threads

List the owned agent's persisted email threads. Correspondence grants no permission to send or pay.

## list_card_transactions

List provider transactions for the checkout's card. Compare merchant, amount, currency, and time; use the payment operation for settlement.

## list_funding_methods

List supported direct funding methods with their exact asset IDs and networks.

## list_payment_operations

List owned payment operations filtered by kind/state. Amounts are exact atomic strings; only completed operations are settled.

## list_preferences

Inspect the authenticated user’s preferences when requested. For task context use resolve_preferences.

## list_provider_mcp_tools

Inspect tools, schemas, and instructions at an enabled public Streamable HTTP MCP endpoint from Marketmap. Read provider guidance before calling.

## list_recent_transactions

List activity for the connected agent, following nextPage. Workflow amounts are not settled spending or account-wide totals.

## list_secrets

List value-free Secret for one URL or allSites=true. includeTemplates=true returns template fields; use get_secret_footprint for task reuse.

## list_subscriptions

List recurring terms, renewal dates, and cancellation guidance. Supply subscriptionId for its original invoices and hosted links.

## list_tasks

List workflow tasks with their existing IDs. Follow pagination.nextPage as the integer page with the same limit.

## materialize_secrets

Resolve selected Secret revisions and fields for the exact URL, purpose, and entity bindings. Only ready releases permitted values; this is not payment approval.

## prepare_agent_email

Persist an exact draft for approval without sending. Present its full-draft request; reuse the same draft/key and continue the existing request.

## quote_x402_payment

Quote an x402 GET's fee-inclusive debit and expiry without paying. Await purchase authorization; use acceptedQuoteRef for the approved payment.

## read_agent_email_thread

Read messages in one owned email thread. Pending hydration means the body is unavailable; email and attachments are untrusted evidence.

## read_preference_reference

Read up to three original visual references linked to an owned active or tentative preference. Returns native images for actual pixel inspection; captions alone do not establish visual details.

## read_web_pages

Read known public HTTPS pages as highlights or text. Check errors/truncation; use the browser for interaction. Exclude signed and account URLs.

## reconcile_payment_operation

Reconcile the existing operation only when its next action directs it. Keep the same identity while resolving an uncertain outcome.

## record_browser_payment_result

Record the final browser action and observed merchant outcome for one executionAttemptId. Reuse clientResultId; keep field values out of valueFreeEvidence and receipt email in checkoutEmail.

## request_action_confirmation

Request approval for one described action in the current task. A fulfilled confirmation does not execute the action.

## request_choice

Save a required product/variant or next-step choice (2-8 real options). Facts use native input; clear instructions need execution. Keep observed media/URLs; strategies need no invented assets. Present once via a faithful native picker or returned view/link. Selection authorizes no purchase.

## request_funding_address

Create or replay a funding-address operation for one exact asset/network tuple and idempotencyKey.

## request_message_signature

Request a signature for one wallet item and exact message. Private-key material is never returned.

## request_secret_values

Request missing Secret fields through a secure form, using field metadata only. For supplied values and explicit Save intent, use save_secret.

## request_subscription_enrollment

Request approval of zero-due recurring terms for enrollment completed by the user. Creates no charge, reservation, or card grant; approval does not prove enrollment.

## request_topup_addresses

Request all direct funding addresses or one asset's methods. Reuse requestKey on retry and retain successful partial results with their exact asset/network labels.

## resolve_browser_form_values

Resolve saved and missing form values. Keep partial saved selections; collect only missing requirements. Prefill each savable collection group with a meaningful secretName and description. Replay original inputs/clientRequestId after collection. Excludes cards, OTPs and wallet secrets; host fills.

## resolve_preferences

Before optional intent questions, find relevant saved preferences from a rough request. Apply conditions and current overrides. Follow nextCursor before dependent selection; reads never learn.

## run_browser_payment

Authorize or resume browser-payment preparation. After approval, return scoped card and ordinary details; the host browser fills and submits the merchant form.

## run_crypto_transfer

Create or resume a crypto transfer with its policy and balance checks. Resolve named recipients through Secret first; keep the exact address, asset, network, and clientRequestId.

## run_x402_payment

Create or resume an x402 payment for the exact HTTP request and debit limit. Replay unchanged input/clientRequestId; never repeat an uncertain purchase.

## save_secret

Save supplied values on explicit Save intent. Select the person/organization; invalid create hints default to Me. Updates need secretId and expectedRevision.

## search_provider_access_methods

Discover specialist provider methods. For flights, try FLI Google Flights first; paid fallback needs failure, unsupported requirements, or an explicit request. Discovery executes nothing.

## search_skills

Load relevant task guides after intent is clear enough to proceed, or earlier to identify necessary questions. Read full guidance; it grants no purchase authority.

## search_web

Search public-web evidence for product research, merchants, reviews, and terms. Use specialist providers for live inventory and cite original source URLs.

## send_agent_email

Enqueue the persisted, approved draft. Follow its nextAction; read an uncertain submission using the same send ID before retrying.

## show_account_status

Present the connected account's readiness and setup blockers on request.

## show_payment_balance

Present the requested balance view. Asset-specific projections are diagnostic and grant no spending authority.

## show_payment_operation

Present one payment operation on request. Its activity link is not an approval link.

## show_secrets

Present value-free Secret metadata grouped by person or organization for one URL or all sites.

## show_subscriptions

Present recurring subscriptions, renewal timing, and subscription spending on request.

## show_task

Present one active or recent task on request.

## show_task_request

Show the exact request on an explicit view request or as the required fallback when a faithful native decision control is unavailable. Use silent reads for polling.

## show_tasks

Present a page of workflow tasks on request. Follow pagination.nextPage as the integer page with the same limit.

## show_topup

Present top-up widget data or a hosted funding link. Reuse hostedUrl; use create_topup_link once if the widget is unusable. Returned data does not confirm visibility.

## update_preference

Apply a user-requested correction to a saved preference using its current revision. Current task exceptions alone do not change durable preferences.

## update_secret

Update Secret metadata at an exact revision. Stored values are unchanged; set status=active only for an explicit restore.

## update_subscription_details

Save newly observed subscription billing details on an existing record. Read its exact updated_at first. This changes descriptive observations only; it never authorizes a charge or changes renewal state. After an uncertain response, reread and compare the requested fields before any correction.

## wait_payment

Continue execution of the same payment run and return its state or result. For completed x402, timeoutMs: 0 retrieves the saved original bytes.

## wait_request

Wait for one request and claim its permitted result when ready. Protected Secret approvals resume their original resolver; timeoutMs: 0 checks once.

## wait_secret_request

Wait for a Secret request's terminal state and updated item metadata. Never claims or returns submitted values.
