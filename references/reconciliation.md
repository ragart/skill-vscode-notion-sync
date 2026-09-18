# Reconciliation procedure

Read this reference before any Notion mutation.

## Preflight

1. Load and validate the private configuration.
2. Regenerate the reports and parse both configured machine-readable files.
3. Fetch all configured production schemas and confirm stable-key properties.
4. Query existing rows once per data source where possible. Avoid repeated
   exploratory queries because Notion may enforce a small shared quota.
5. Build complete desired/current maps before writing.
6. Preserve unrelated properties and all view configuration.

## Backup reconciliation

### Profiles

- Upsert desired profiles by exact name.
- Create missing profile pages before enablement facts.
- Remove obsolete profiles only after dependent facts are reconciled.
- Do not infer renames; addition plus removal is the safe default.

### Extensions

- Upsert desired records by lowercase identifier.
- Preserve every current catalog row absent from the desired set.
- Update a display name only when the generated name differs from the raw
  identifier.
- Detect duplicate identifiers and stop before writing ambiguous records.

### Enablements

For each desired `extensionId + profile` key:

- reuse an existing fact when possible, including a previously removed fact;
- set both relations and duplicated text keys;
- set State, Assignment Scope, Source, Current status, and Last Observed.

For a current fact absent from desired state:

- set Status to Removed;
- clear Extension and Profile relations;
- retain identifier/profile text and historical dates.

Clearing relations keeps removed facts from contaminating rollups.

## Audit reconciliation

### Targets

- Upsert by exact target name.
- Refresh metadata, summary counts, health, and Last Checked.
- Obey `policies.allowIcons`.
- If a previously known target is not scanned, prefer marking it Unknown over
  deleting it unless the user requests removal.

### Findings

For each desired finding key:

- create it as Open if missing;
- preserve First Observed;
- refresh Last Observed and relations;
- reopen a Resolved record if the discrepancy returns;
- preserve Ignored status while the finding remains present.

For existing Open findings absent from the new audit, set Status to Resolved.
Do not create rows for successful checks and do not delete resolved history.

Follow the audit policy emitted by the configured generator. Intentional
target-specific exceptions should become an explicit applicability/ignore
policy, not ad-hoc catalog deletion.

## Writes and connector details

- Use configured data-source IDs for queries and page creation.
- Relation values are arrays of page URLs or IDs.
- Dates use `date:<Property>:start` and
  `date:<Property>:is_datetime`.
- Apply narrow property updates; do not replace page content.
- Never change schemas, views, visibility, filters, or sorts during routine
  sync.
- Never print private configuration values unless the user explicitly requests
  them in an appropriate private context.

## Query-limit fallback

If bulk querying becomes unavailable:

1. Stop issuing further bulk queries.
2. Use known stable page IDs and direct fetches only for narrowly identified
   changes.
3. Do not create records when uniqueness cannot be established.
4. Refresh known target summaries only when their IDs are unambiguous.
5. State which tables were not fully compared in the final report.

Do not treat unchanged local counts as proof that all remote keys match.

## Verification

Check, in proportion to available connector access:

- profile names exactly match desired state;
- catalog identifiers are unique and desired identifiers exist;
- no catalog row was deleted for being absent;
- current enablement keys exactly match desired keys;
- changed enablement states are confirmed by direct fetch;
- target summaries match the audit JSON;
- finding keys are unique, required relations exist, and open counts match;
- icon policy is satisfied.
