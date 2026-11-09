# Saved details and secure forms

Use the current connected Secrets schemas. Discover value-free metadata with
`get_secret_footprint` before filling a payment or non-payment form, without
requiring a checkout session. Discover current templates and field keys with
`list_secrets`, `includeTemplates: true`, even when no item exists.
Follow the footprint's bounded-page/excerpt instructions; widen a narrow literal
search before concluding that a matching person, account or field is absent.

Match entity/person, semantic role, field meaning and script/transliteration,
not just a similar label. Me owns the user's Personal profile, Passport and
Address; Passenger and Traveler describe use roles. Preserve an existing
custom title. Prefer a clear task-compatible match and its current reuse
policy. Ask once by name for material ambiguity or consent, not for values
that discovery already establishes are available. Chat context does not skip
discovery. For transfers, resolve address, network and asset together.

## Named transfer recipients

For “send USDT on TRON to Sam”, discover matching person/organization metadata
and bind the intended entity before selecting its wallet fields. Two people
named Sam require a name/context clarification; a TRON label alone does not
choose the person. Materialize the selected address, network and asset together
for that entity through the current task/request. Descriptions and tags help
selection but are not destination values. If the saved tuple conflicts with
the requested network or asset, clarify the conflict; do not silently convert
or substitute it. Use the exact authorized tuple with `run_crypto_transfer`.
A complete tuple explicitly supplied for this transfer needs no Secrets lookup.

### National and international identity documents

Before requesting a passport number, inspect the merchant's rendered document
type and accepted options, then match the intended person's saved document
metadata. A route, nationality or generic “Passport” label does not establish
which document is required.

| Merchant document type | Secrets template | Number fields |
| --- | --- | --- |
| International/travel passport; Russian “Заграничный паспорт” | `identity.passport` | `templateFieldKey: "passport_number"` |
| National ID or internal/domestic passport; Russian “Паспорт РФ” or “Внутренний паспорт” | `identity.national_id` | `templateFieldKey: "document_number"`; separate `series` when applicable |

Use a suitable saved document that the merchant accepts, selecting its matching
document-type option and re-observing the form when needed. Keep its number,
series, issuer and issue/expiry dates tied to that same document. An incomplete
international passport does not rule out a usable national/internal document;
check the accepted types before collecting a missing number. If the type remains
materially ambiguous, clarify that type once before collecting or filling.

## Select saved fields and map the form

Materialize exact selected secret revisions and field IDs through
`materialize_secrets` or the current `resolve_browser_form_values`
workflow. Metadata CRUD is value-free; request/status reads do not release
protected data. Secrets consent authorizes only that use, not payment or
submission. Retain selected entities, secret revisions and the typed collection
through a multi-field/batch continuation; replay the unchanged resolver as
directed rather than dropping previously resolved fields or starting siblings.

Reuse suitable ready fields, including Title from Personal profile. Incomplete
items are valid; a missing Passport does not make saved profile fields missing.
Use task-provided details where no usable saved match exists. Collection uses
the returned secure form/editor; optional template extras stay in its UI.

| Resolver input | Meaning |
| --- | --- |
| `candidateAssemblies.selections` | Exact saved secret revisions and requested field IDs. Keep partial matches. |
| `targetBindings` | Map each saved source to its requested `fields[].fieldKey`, never a browser snapshot ref. |
| `entityBindings` | Bind only groups used by saved selections in that assembly. |
| `collectionGroups` | One template per group; map every requested field once, including saved fields. The service derives missing inputs. Missing-only groups identify their person here. |
| `collectionGroups.target` | Proposed Save/update target; it does not select saved values. |

New operations use `collectionMappingScope: form/v1`; replays keep their
original mapping and identity. Only a new form with no usable selected saved
fields uses an empty assembly. A service-issued different-details decision keeps
the original assembly/body; its continuation below chooses fresh collection.
Custom groups cover only
requirements no current template fits; missing values or invalid mappings are
not reasons to replace typed collection.

For a named person without an owned entity, propose that explicit name with
`collectionGroups.newEntity: {type: "person", displayName}` on their groups,
without `entityId` or an update target. Use once creates no entity/secret; only
reviewed Save persists the destination. Keep each person's roles bound separately;
never infer passport sex from Title, nationality from residence, or a person
from an secret name. For research needing age, `get_secret_profile_context` with
the intended entity and travel-date `asOfDate` can supply age without a birth date.

### Partial-saved travel example

Synthetic `resolve_browser_form_values` input: Iris has saved Title and passport
number; Me supplies the booking phone. Only Iris's passport expiry and the
contact postcode are missing. All IDs/revisions come from the same footprint in
a real call; template keys/versions and phone options come from current discovery.

```json
{
  "taskId": "10000000-0000-4000-8000-000000000001",
  "clientRequestId": "selected-travel-form-1",
  "footprintRevision": "fp-example-1",
  "pageUrl": "https://flight-fixture.example.test/traveler",
  "purpose": "Prepare selected traveler and booking contact",
  "groups": [{"groupRef":"traveler-profile"},{"groupRef":"traveler-passport"},{"groupRef":"contact"},{"groupRef":"contact-address"}],
  "fields": [
    {"fieldKey":"traveler_title","label":"Iris: Title","inputType":"select","groupRef":"traveler-profile"},
    {"fieldKey":"passport_number","label":"Iris: Passport number","inputType":"password","groupRef":"traveler-passport"},
    {"fieldKey":"passport_expiry","label":"Iris: Passport expiry","inputType":"date","groupRef":"traveler-passport"},
    {"fieldKey":"contact_phone","label":"Booking contact phone","inputType":"tel","groupRef":"contact","phoneTargets":[
      {"targetRef":"phone_national","label":"Phone number","projection":"national"},
      {"targetRef":"phone_country","label":"Telephone country","projection":"country","options":[{"country":"US","value":"UnitedStates"},{"country":"SG","value":"Singapore"}]}
    ]},
    {"fieldKey":"contact_postcode","label":"Contact postcode","inputType":"text","groupRef":"contact-address"}
  ],
  "collectionMappingScope": "form/v1",
  "collectionGroups": [
    {"groupRef":"traveler-profile","templateKey":"profile.person","templateVersion":1,"entityId":"20000000-0000-4000-8000-000000000002","fields":[{"requestFieldKey":"traveler_title","templateFieldKey":"title"}]},
    {"groupRef":"traveler-passport","templateKey":"identity.passport","templateVersion":1,"entityId":"20000000-0000-4000-8000-000000000002","fields":[{"requestFieldKey":"passport_number","templateFieldKey":"passport_number"},{"requestFieldKey":"passport_expiry","templateFieldKey":"expiry_date"}]},
    {"groupRef":"contact","templateKey":"profile.person","templateVersion":1,"entityId":"20000000-0000-4000-8000-000000000001","fields":[{"requestFieldKey":"contact_phone","templateFieldKey":"phone"}]},
    {"groupRef":"contact-address","templateKey":"address.postal","templateVersion":1,"entityId":"20000000-0000-4000-8000-000000000001","fields":[{"requestFieldKey":"contact_postcode","templateFieldKey":"postal_code"}]}
  ],
  "candidateAssemblies": [{
    "selections": [
      {"secretId":"30000000-0000-4000-8000-000000000001","expectedRevision":"iris-profile-1","groupRef":"traveler-profile","requestedFields":[{"fieldId":"40000000-0000-4000-8000-000000000001","fieldKey":"title"}]},
      {"secretId":"30000000-0000-4000-8000-000000000002","expectedRevision":"iris-passport-1","groupRef":"traveler-passport","requestedFields":[{"fieldId":"40000000-0000-4000-8000-000000000002","fieldKey":"passport_number"}]},
      {"secretId":"30000000-0000-4000-8000-000000000003","expectedRevision":"me-profile-1","groupRef":"contact","requestedFields":[{"fieldId":"40000000-0000-4000-8000-000000000003","fieldKey":"phone"}]}
    ],
    "entityBindings": [
      {"groupRef":"traveler-profile","entityId":"20000000-0000-4000-8000-000000000002"},
      {"groupRef":"traveler-passport","entityId":"20000000-0000-4000-8000-000000000002"},
      {"groupRef":"contact","entityId":"20000000-0000-4000-8000-000000000001"}
    ],
    "targetBindings": [
      {"targetRef":"traveler_title","secretId":"30000000-0000-4000-8000-000000000001","fieldId":"40000000-0000-4000-8000-000000000001","groupRef":"traveler-profile"},
      {"targetRef":"passport_number","secretId":"30000000-0000-4000-8000-000000000002","fieldId":"40000000-0000-4000-8000-000000000002","groupRef":"traveler-passport"},
      {"targetRef":"contact_phone","secretId":"30000000-0000-4000-8000-000000000003","fieldId":"40000000-0000-4000-8000-000000000003","groupRef":"contact"}
    ]
  }]
}
```

Making these items only Save targets with an empty assembly would recollect
usable fields. Keep the selections while collecting the two missing fields.

### Mapping corrections and phone controls

For `memory_collection_mapping_invalid`, inspect the returned `diagnostic`
against the current templates. Locations are zero-based: `groupIndex` selects
`collectionGroups`, `mappingIndex` selects that group's `fields`, and
`fieldIndex` selects the top-level requested `fields`. Use distinct groups for
passenger and booking-contact roles, retaining the same intended entity and
suitable saved selections. Each group uses one template; its field keys must
come from that template.

When validation rejected a new mapping before any request was created, correct
the identified mapping and resolve again with the same session/clientRequestId.
Recheck the complete mapping before retrying; waiting or resending unchanged
input cannot repair it. Continue through the returned consent/collection or
`ready` result. If a request or approved batch already exists, preserve its
original fingerprint and follow its owning continuation instead. Report a
remaining blocker only after a supported correction cannot resolve it; a
mapping error alone is not a reason to abandon the selected checkout.

For one logical telephone input, request one canonical phone field. Separate
rendered controls use `phoneTargets` on that field with exact targetRef, label
and projection: full, national, calling_code or country. A country select needs
its rendered ISO-country to option-value mapping. Telephone country is not the
person's profile country. The containing ready field supplies `phoneParts`
(fullPhone, countryCode, phone, optional country) alongside projected phone targets.
Fill only current rendered controls. A target marked `mappingRequired` deliberately
has no projected value: use that field's `phoneParts` and complete current options.
If country is absent and the mapping remains ambiguous, ask only the missing fact;
a calling code such as +1 does not establish country. Correct the browser mapping
without recollection or a new approval.

## Present and continue the request

Show each saved secret name, its intended person and only requested field labels.
Separate reused fields from missing inputs: “Iris: Passport number saved;
passport expiry needed” is useful; listing Passport twice without fields is not.
Use value-free labels and the existing request view/control. Do not add another
question beside an already visible request.

| Result or decision | Next action |
| --- | --- |
| `ready` | Map and fill the complete authorized batch; optional collected extras are not task output. |
| `kind: request_required` | Present its secure request; after fulfillment replay the original resolver and key, including its original footprint revision after Save. |
| Declared Provide different details | Submit its exact stored `selectedChoiceId` with `choose_candidate`; replay the original resolver. The service switches to fresh collection; do not rewrite its input or deny the request to clear it. |
| `saveOutcome.status: saved` | Confirm the returned item/field labels without values. |
| `saveOutcome.status: not_saved` | Current-run use only. Use once creates no Secrets records. |
| Failed or unknown Save | Inspect the same request; keep its draft/identity. Do not claim Saved, create a second save, or silently substitute Use once. |

Approval and collection can be separate service-issued stages of the same form.
Keep one active decision per stage; a status question does not create another.

## Explicit Save and updates

For an explicit request to save or update supplied ordinary or protected values,
call `save_secret` directly, preserving the user’s values and any supplied
description. Supplying checkout details alone is not a request to save them. Save the
requested partial item without blocking on optional fields or a description.
An explicit “do not save” takes precedence over inferred reuse or a default;
use authorized details only for this task. Select the
person/organization from context or create the appropriate new entity; use Me
for self or unusable identity hints. Direct Save uses `newEntity.name`; the
form collection contract uses `collectionGroups.newEntity.displayName`. Do not
interchange these fields. Use Personal profile or Organization
profile for default profile titles and retain custom titles. Do not invent
placeholder values, require a collection session or ask for the same values
again. Retry the same client request identity and unchanged input after an
ambiguous result; a new key is for a genuinely distinct Save intent. Payment
and Save have independent outcomes: if the purchase succeeded but Save is
unconfirmed, recover only the same Save with unchanged input/key. Keep the
completed purchase and report Save separately; never purchase again to save details.

For updates, omitted fields preserve values; `null` clears a named field and
strings stay byte-exact. Never read a stored secret merely to preserve it.
Confirm persistence from the returned receipt. For an updated existing secret,
check value-free `get_secret` availability: an archived secret stays archived
until an explicit restore. A failed availability read does not undo a successful
Save. For missing standalone Save input, use the existing secure Secrets editor
rather than a placeholder, task collection or clipboard detour.

Clipboard input exposes plaintext: require a field-specific choice and allow
direct input. Never fetch clipboard contents speculatively. If direct Save
is unavailable, report the blocked save; preserve installation and sign-in.
For updates/deletions, identify the exact owned secret and expected revision
through the current metadata APIs. `correct_secret_field` follows its own
service-issued correction decision; do not invent a different-details choice.

Use authorized values through the host's documented targeted input only for
the intended form/task. Public recipient fields may also be used for the
exact requested transfer. For explicitly requested protected recall, show a
value only when the approved purpose explicitly authorizes revealing it in
this chat; form-use consent does not grant that permission. Otherwise do not
expose values in chat, files, logs, unrelated
forms, screenshots exported to others or external messages. A complete batch
must resume its same request/resolver after the recorded decision, including
when another channel supplied the winning answer. On denial, do not release
the denied values; continue only the parts that remain authorized.

An expired fulfilled release is not a denial. Refresh the footprint, verify
the same active form and resolve again with a new clientRequestId as the
service directs. Current policy decides whether another review is needed.
Preserve the task, merchant and payment operation; do not recollect every
field, replay an expired artifact or use a new identity to bypass a denial.
