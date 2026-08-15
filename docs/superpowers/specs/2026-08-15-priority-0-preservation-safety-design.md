# Hermes Priority 0 Preservation and Safety Design

**Status:** Approved design

**Date:** 2026-08-15

**Owner:** Pepe
**Workspace:** `C:\Pepe Project\Agent\Hermes`

## 1. Purpose

Priority 0 establishes a preservation and safety foundation before Hermes Core is installed or any legacy capability is migrated. It must make the assets of Sage, Scout, Rex/Coders, Pixel, and Chimit discoverable without modifying, deleting, disabling, or replacing their existing files and runtimes.

The selected approach is a structured, incremental manifest. Priority 0 records systems and important asset groups now; file-level detail is added when a capability enters its migration phase.

## 2. Scope

Priority 0 includes:

- an asset manifest for Sage, Scout, Rex/Coders, Pixel, and Chimit;
- audit notes for missing, inaccessible, ambiguous, sensitive, or duplicated assets;
- permission, secret-handling, and preservation policies;
- a rollback procedure for returning to an existing legacy runtime after a failed Hermes migration;
- an initial project registry with an explicit source of truth or `needs_owner_decision` status;
- verification that the inventory process did not alter legacy assets.

Priority 0 does not include:

- installing or running Hermes Core;
- copying capabilities into Hermes;
- changing the source of truth for any project;
- disabling or retiring a legacy agent;
- reading, copying, or documenting credential values;
- disaster recovery from physical drive loss.

## 3. Owner Decisions

The following decisions are authoritative for this design:

1. `C:\Pepe Project\Agent\Hermes` is the Hermes working folder.
2. The inventory will be created before any physical backup.
3. No separate local or cloud backup will be created for Priority 0.
4. The absence of a backup is an owner-approved risk and a waived exit criterion, not a successfully satisfied criterion.
5. Legacy systems remain the source of truth until their corresponding Hermes capability has been copied and validated.
6. Existing credentials may remain in Google Keep temporarily, but Hermes will not read Google Keep automatically.
7. Runtime credentials must not be stored in Hermes documentation, memory, skills, logs, or the project registry.
8. Reversible actions may run without approval; difficult-to-reverse actions require explicit owner approval.
9. Hermes source and control documents are versioned in the private GitHub repository `ramadhanpradhana-arch/hermes-shas`.

## 4. Directory Structure

Priority 0 will create the following focused documents:

```text
Hermes/
|-- README.md
|-- docs/
|   |-- inventory/
|   |   |-- asset-manifest.yaml
|   |   `-- audit-notes.md
|   |-- policies/
|   |   |-- permissions.md
|   |   |-- secrets.md
|   |   `-- preservation.md
|   |-- recovery/
|   |   `-- rollback.md
|   `-- superpowers/
|       `-- specs/
|           `-- 2026-08-15-priority-0-preservation-safety-design.md
`-- registry/
    `-- projects.yaml
```

Each file has one responsibility:

- `asset-manifest.yaml` indexes legacy systems and asset groups.
- `audit-notes.md` records evidence, unresolved findings, and access failures.
- `permissions.md` defines allowed and approval-gated actions.
- `secrets.md` defines credential handling without containing credentials.
- `preservation.md` protects legacy systems during migration.
- `rollback.md` describes returning to the legacy runtime after a Hermes migration failure.
- `projects.yaml` records project context and its current source of truth.
- `.gitignore` prevents common secret, runtime-state, dependency, cache, editor, and operating-system files from entering version control.

## 5. Asset Manifest Model

Every legacy system entry uses these fields:

```yaml
id: stable-kebab-case-identifier
name: human-readable system name
original_location: absolute Windows path
role: concise description of the system's responsibility
asset_categories:
  - category-name
source_of_truth_for:
  - responsibility-or-project
sensitivity: public | internal | sensitive | credential-bearing
runtime_status: active | inactive | unknown
migration_priority: 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | deferred
migration_status: inventoried | selected | copied | validated | hermes_source_of_truth | legacy_retained | eligible_for_retirement
allowed_actions:
  - read
approval_required:
  - action-name
known_risks:
  - concise-risk
notes: concise free-text context
```

Rules:

- `original_location` must be checked for existence.
- Credential values and secret names must not appear in the manifest.
- A credential-bearing location may be identified only at the minimum useful scope.
- Generated dependencies, caches, virtual environments, and repository internals are recorded as asset groups rather than enumerated file by file.
- Ambiguous ownership is recorded in audit notes and does not trigger an inferred source-of-truth decision.

## 6. Migration State Model

Migration proceeds through the following ordered states:

```text
inventoried
-> selected
-> copied
-> validated
-> hermes_source_of_truth
-> legacy_retained
-> eligible_for_retirement
```

State meanings:

- `inventoried`: the legacy asset and its location are known.
- `selected`: the asset has been approved for a specific migration phase.
- `copied`: a Hermes version exists, but no authority has changed.
- `validated`: the Hermes version has passed its defined checks.
- `hermes_source_of_truth`: the owner has approved Hermes as authoritative.
- `legacy_retained`: the legacy version remains available after the authority change.
- `eligible_for_retirement`: retirement may be proposed but still requires explicit approval.

Copying never changes the source of truth automatically. No legacy asset is deleted at any state in this model.

## 7. Transitional Sources of Truth

Until migration and validation are complete:

- Sage remains authoritative for its existing strategy, decisions, PRDs, and project context.
- Scout remains authoritative for its existing research, source lists, and intelligence vault.
- Coders/Rex remains authoritative for its existing engineering runtime, configuration, and workflows.
- Pixel remains authoritative for its design-generation concepts and documentation.
- Chimit remains authoritative for its hospitality knowledge and runtime.
- Hermes serves only as an index and policy layer during Priority 0.

Project-specific ambiguity must be represented as `needs_owner_decision` in `projects.yaml`.

## 8. Permission Policy

Hermes may perform reversible, in-scope actions without additional approval, including reading files, creating new Hermes documentation, generating drafts, running non-mutating checks, and reporting findings.

Explicit owner approval is required before Hermes:

- deletes or overwrites files;
- modifies legacy-agent assets or runtimes;
- changes a project's source of truth;
- sends messages or files to external parties;
- incurs payment or enables a paid service;
- merges code or deploys to production;
- retires a legacy agent;
- exposes, rotates, moves, or revokes credentials;
- performs another action that is difficult to reverse.

If reversibility or authority is unclear, Hermes must stop and request approval.

## 9. Secret Policy

- Credential values must not be stored in the Hermes repository, documentation, memory, skills, registry, manifest, or logs.
- Existing credentials in Google Keep may remain there temporarily, but Hermes must not connect to or read Google Keep automatically.
- Runtime secrets will later be supplied through a local, non-repository secret mechanism.
- A future `.env.example` may document variable names only; it must contain no real values.
- Logs and error reports must redact suspected tokens and credential values.
- Inventory may state that a sensitive asset exists but must not reproduce its contents.

## 10. Preservation and Rollback

Priority 0 is read-only with respect to Sage, Scout, Coders/Rex, Pixel, and Chimit. The process must not delete, rename, move, rewrite, disable, or reconfigure legacy assets.

Rollback means returning operational authority to the unchanged legacy runtime when a Hermes migration fails. It does not mean recovery after loss or corruption of the physical `C:` drive. Because the owner declined a separate backup, physical-drive disaster recovery is explicitly unavailable in this phase.

## 11. Error Handling

- Inaccessible file or folder: record the path, operation, and error in `audit-notes.md`; do not bypass access controls.
- Missing path: mark it `missing` and search documentation for references to its expected location.
- Ambiguous source of truth: set `needs_owner_decision`; do not infer ownership.
- Suspected credential: stop content inspection, record only a redacted sensitivity finding, and continue with other assets.
- Duplicate asset: record candidate locations; do not delete or consolidate.
- Generated or high-volume folder: inventory the folder as one asset group and record why file-level enumeration was excluded.
- Unexpected mutation: stop the audit, report the affected path, and do not continue until the change is understood.

## 12. Verification

Priority 0 verification must establish that:

1. Sage, Scout, Rex/Coders, Pixel, and Chimit each have a manifest entry.
2. Every manifest `original_location` has been checked and its result recorded.
3. No credential value appears in Hermes Priority 0 documents.
4. Every project registry entry has an explicit source of truth or `needs_owner_decision`.
5. Permission, secret, and preservation policies match the owner decisions in this specification.
6. No legacy file was modified by the inventory process.
7. Rollback instructions identify the unchanged legacy runtime and state their limitation without a backup.
8. Audit exclusions for caches, dependencies, virtual environments, and inaccessible files are explicit.

## 13. Completion Rule

Priority 0 is complete when all verification checks pass, except for the backup criterion explicitly waived by the owner. Completion must be reported as:

> Complete with owner-approved backup exception; physical-drive disaster recovery is unavailable.

This wording must not be shortened to imply that a recoverable backup exists.

## 14. Next Phase

After Priority 0 is implemented and verified, Priority 1 may begin designing and installing Hermes Core. No legacy capability migration begins during Priority 0.
