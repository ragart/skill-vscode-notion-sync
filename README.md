# VS Code Notion Sync

A focused Agent Skill for regenerating VS Code profile reports and safely
reconciling a Notion backup and exception-only extension audit dashboard.

## Use cases

- Regenerate VS Code profile, enablement, and target-audit reports.
- Synchronize extension and profile backup records with Notion.
- Refresh audit-target health and reconcile missing or extra extensions.
- Diagnose drift while preserving historical catalog and finding records.

## Installation

Keep one canonical checkout and expose it through the standard Agent Skill
directory:

```bash
ln -s /absolute/path/to/vscode-notion-sync \
  ~/.agents/skills/vscode-notion-sync
```

Restart or refresh Agent Skill discovery after first installation.

Copy `config.example.json` to `config.local.json` beside `SKILL.md` and fill in
its Notion IDs and project-specific settings. The filename is ignored by Git;
do not force-add or otherwise publish that private configuration.

The configured project must provide the report generator and the Notion
connector must have access to the selected databases.

## Usage

Invoke it explicitly:

```text
$vscode-notion-sync
```

Or ask naturally:

```text
Run the VS Code–Notion sync.
Regenerate my VS Code profile reports.
Audit my VS Code targets and update Notion.
```

A natural-language request can activate the Agent Skill automatically.
A plain “run the sync” request performs both backup and audit reconciliation.
Report-only requests do not mutate Notion.

## Repository structure

- `SKILL.md`: agent-facing routing, safeguards, and high-level workflow.
- `agents/openai.yaml`: Agent Skill display metadata and default invocation
  prompt.
- `config.example.json`: non-sensitive configuration template.
- `config.local.json`: ignored, deployment-specific configuration created by
  the user; never committed.
- `references/schema.md`: logical schema and stable-key contract.
- `references/reconciliation.md`: mutation order, lifecycle rules, fallbacks,
  and verification requirements.
- `.gitignore`: excludes private configuration and common secret files.
- `LICENSE`: MIT license.

## Development

Keep project-specific identifiers and paths out of this repository. Validate
changes with:

```bash
python3 /path/to/skill-creator/scripts/quick_validate.py \
  /absolute/path/to/vscode-notion-sync
```

Run the configured project generator and inspect both JSON outputs after
modifying report or audit behavior. Do not test against production Notion
unless the user has authorized a sync or migration.

Before publishing, scan the complete working tree and staged diff for
credentials, private paths, hostnames, personal identifiers, Notion IDs, and
generated inventories.

## Privacy

The workflow may read local VS Code profile databases and extension
inventories. Synchronization can send extension identifiers, profile names,
device labels, inventory paths, enabled states, and audit findings to the
configured Notion workspace. It is not intended to collect source code or
credentials.

Private `config.local.json` stays beside the installed skill but outside Git.
Generated reports remain in the configured project. Neither belongs in the
published repository.

## License

Licensed under the MIT License. See [LICENSE](LICENSE).
