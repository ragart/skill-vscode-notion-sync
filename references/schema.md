# Schema contract

Read this reference for any Notion-backed operation. Database identifiers come
only from `config.local.json` beside the installed skill. Fetch each configured
data source before writing because users may rename properties or customize
views.

## Configuration

The private configuration maps logical database roles to Notion page and data
source IDs:

- `extensions`
- `profiles`
- `enablements`
- `auditTargets`
- `auditFindings`

Never embed those values in skill files, generated examples, logs intended for
publication, or final answers.

## Backup databases

### Extensions

- Stable key: `Identifier`
- Required logical properties:
  - `Name` — title
  - `Identifier` — text
  - `Enablements` — technical reciprocal relation
  - `Profiles` — rollup
  - `Enablement` — rollup

Catalog rows are permanent. The current desired set comes from the configured
inventory JSON; absent identifiers remain historical catalog entries.

### Profiles

- Stable key: exact `Name`
- Required logical properties:
  - `Name` — title
  - `Enablements` — technical reciprocal relation
  - `Extensions` — rollup

Use `conventions.defaultProfileName` for the Notion-facing default profile
label.

### Extension Enablements

- Stable current key: `Extension Identifier` + `Profile Name`
- Required logical properties:
  - `Name`
  - `Extension`, `Extension Identifier`
  - `Profile`, `Profile Name`
  - `State`: Enabled, Disabled, Unknown
  - `Assignment Scope`: All profiles, Profile
  - `Status`: Current, Removed
  - `Source`
  - `Last Observed`

All-profile assignments may be expanded across effective profiles so
per-profile disabled state remains recoverable.

## Audit databases

### Audit Targets

- Stable key: exact `Name`
- Required logical properties:
  - `Name`, `Kind`, `Device`, `Authority`, `Inventory Source`
  - `Health`, `Expected`, `Observed`, `Missing`, `Extra`
  - `Last Checked`

Device is metadata; a separate Devices database is not required.

### Audit Findings

- Stable key/title:
  `<target> | <extension identifier> | <finding type>`
- Required logical properties:
  - `Target`, `Target Name`
  - `Extension`, `Extension Identifier`
  - `Finding`: Missing, Extra, Version drift
  - `Status`: Open, Resolved, Ignored
  - `Expected Version`, `Observed Version`
  - `First Observed`, `Last Observed`

Prefer one-way audit relations so backup dashboards do not gain technical
audit columns.
