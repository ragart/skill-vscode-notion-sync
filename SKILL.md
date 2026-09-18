---
name: vscode-notion-sync
description: Regenerate and reconcile a VS Code profile and extension backup plus target-audit dashboards in Notion. Use for report generation, VS Code-to-Notion synchronization, extension/profile reconciliation, audit-target refreshes, or drift checks in a configured vscode-profiles project; do not use for unrelated VS Code or general Notion work.
---

# VS Code Notion Sync

Maintain a device-independent VS Code backup and an exception-only installation
audit. Treat locally generated JSON as synchronization input and Notion as the
backup/dashboard destination, not as the audit engine.

## Start

1. Find the project root containing the configured generator.
2. Resolve this skill's directory and read its adjacent `config.local.json`.
   If it is absent, stop and ask the user to create it from
   `config.example.json`; never guess database IDs.
3. Read the project’s `NOTION_SYNC.md` when present. It owns project-specific
   decisions and migration history.
4. For any Notion operation, read
   [references/schema.md](references/schema.md) and fetch the configured live
   schemas before assuming they match.
5. For reconciliation or mutations, also read
   [references/reconciliation.md](references/reconciliation.md).
6. If the Notion connector is unavailable, stop and ask the user to connect it.

Treat the local configuration as private operational metadata. Never copy its
IDs, paths, device labels, or generated inventories into this skill repository.

## Choose the requested mode

- **Reports only:** regenerate local reports and summarize changes. Do not write
  to Notion.
- **Backup sync:** reconcile Extensions, Profiles, and Extension Enablements.
- **Audit sync:** refresh Audit Targets and reconcile exception-only Audit
  Findings.
- **Full sync:** perform backup sync followed by audit sync and verification.

When the request simply says “run the sync,” use full sync.

## Generate

Read `generator.script`, `generator.deviceName`, and output paths from the
private configuration. Run the generator from the project root, passing the
configured stable device label. Do not replace it silently with a transient
hostname.

Use the configured inventory JSON for the backup model and audit JSON for
targets/findings. CSV and Markdown outputs are presentation reports, not stable
synchronization inputs.

## Preserve these invariants

- Extension `Identifier` is the catalog key. Never key by display name.
- Never delete an extension catalog row because it is absent or uninstalled.
- Do not replace a useful display name with a raw identifier.
- Audit observations must never add/remove backup extensions or profile
  assignments automatically.
- Keep successful target checks out of Audit Findings; store only exceptions.
- Preserve `Ignored` findings unless the user explicitly changes that choice.
- Preserve user-created views, filters, sorts, hidden-column choices, and
  property visibility.
- Obey configured icon policy; when icons are disabled, never add them.
- Do not restore archived or experimental databases unless explicitly asked.
- Routine syncs do not change schemas. Treat schema migration as a separate,
  explicitly requested operation.

## Verify and report

Verify stable-key uniqueness, expected counts, required current relations,
enablement states, target summaries, and open finding counts. Use direct page
fetches for critical changed records.

If Notion’s bulk-query quota prevents a complete comparison, do not guess or
claim full verification. Apply only changes addressable by stable known IDs,
use direct reads where safe, and disclose the exact verification limitation.

Report meaningful changes, retained historical/catalog records, target health,
and recoverable archival actions. Avoid narrating unchanged implementation
details or exposing private configuration values.
