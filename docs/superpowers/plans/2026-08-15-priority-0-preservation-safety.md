# Hermes Priority 0 Preservation and Safety Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a verified, read-only inventory and safety-policy foundation for Hermes without changing any legacy-agent asset.

**Architecture:** Hermes will hold a small set of focused Markdown and YAML control documents. Legacy systems remain in place and authoritative; Hermes records their locations, ownership, sensitivity, permissions, and migration state, then verifies that inventory work caused no legacy mutations.

**Tech Stack:** Markdown, YAML, PowerShell read-only verification commands, SHA-256 file hashing

**Spec:** `C:\Pepe Project\Agent\Hermes\docs\superpowers\specs\2026-08-15-priority-0-preservation-safety-design.md`

## Global Constraints

- `C:\Pepe Project\Agent\Hermes` is the only writable project scope for Priority 0.
- Do not modify, delete, rename, move, disable, or reconfigure Sage, Scout, Coders/Rex, Pixel, or Chimit.
- Do not read, copy, print, or document credential values.
- Do not create a local or cloud backup; record this as an owner-approved exception.
- Legacy systems remain authoritative until a later migration is copied, validated, and explicitly approved.
- Reversible in-scope documentation actions are allowed; difficult-to-reverse actions require explicit owner approval.
- Exclude `.git`, virtual environments, dependency directories, caches, and generated artifacts from file-level inventory.
- Version Hermes in the private GitHub repository `ramadhanpradhana-arch/hermes-shas`; never push credential-bearing or ignored runtime files.

---

## File Map

- `docs/inventory/asset-manifest.yaml`: authoritative index of the five legacy systems and their asset groups.
- `docs/inventory/audit-notes.md`: evidence, exclusions, access failures, ambiguities, and verification results.
- `docs/policies/permissions.md`: allowed actions and approval gates.
- `docs/policies/secrets.md`: rules for credentials and redaction.
- `docs/policies/preservation.md`: immutability rules for legacy systems during migration.
- `docs/recovery/rollback.md`: operational rollback to unchanged legacy runtimes and its limitations.
- `registry/projects.yaml`: initial project registry with source-of-truth status.
- `README.md`: links to the Priority 0 control documents and current phase status.
- `.gitignore`: excludes secrets, runtime state, dependencies, caches, and local editor files.

---

### Task 1: Capture a Read-Only Legacy Baseline

**Files:**
- Modify: `docs/inventory/audit-notes.md`

**Interfaces:**
- Consumes: the five legacy roots under `C:\Pepe Project\Agent`
- Produces: a timestamped baseline inventory and an in-memory SHA-256 comparison set used by Task 6

- [ ] **Step 1: Confirm the five expected roots without reading file contents**

Run:

```powershell
$legacyRoots = @(
  'C:\Pepe Project\Agent\Sage',
  'C:\Pepe Project\Agent\Scout',
  'C:\Pepe Project\Agent\Coders',
  'C:\Pepe Project\Agent\Pixel',
  'C:\Pepe Project\Agent\Marketing Specialist - Perhotelan\Chimit - CMO MGM'
)
$legacyRoots | ForEach-Object {
  [pscustomobject]@{ Path = $_; Exists = Test-Path -LiteralPath $_ -PathType Container }
} | Format-Table -AutoSize
```

Expected: all five rows show `Exists = True`. Any false row is recorded as `missing` and not silently replaced with an inferred path.

- [ ] **Step 2: Enumerate asset groups while excluding high-volume generated directories**

Run:

```powershell
$excludedNames = @('.git', 'venv', '.venv', 'node_modules', '__pycache__', '.cache')
foreach ($root in $legacyRoots) {
  Get-ChildItem -LiteralPath $root -Force -ErrorAction SilentlyContinue |
    Where-Object { $_.Name -notin $excludedNames } |
    Select-Object @{Name='Root';Expression={$root}}, Name, Mode, LastWriteTime
}
```

Expected: top-level asset groups are visible without enumerating dependency trees.

- [ ] **Step 3: Capture pre-implementation metadata and hashes in memory**

Run in the same PowerShell session that will execute Task 6:

```powershell
$legacyFilesBefore = foreach ($root in $legacyRoots) {
  Get-ChildItem -LiteralPath $root -File -Recurse -Force -ErrorAction SilentlyContinue |
    Where-Object {
      $_.FullName -notmatch '\\(\.git|venv|\.venv|node_modules|__pycache__|\.cache)\\'
    } |
    ForEach-Object {
      [pscustomobject]@{
        Path = $_.FullName
        Length = $_.Length
        LastWriteTimeUtc = $_.LastWriteTimeUtc
        Sha256 = (Get-FileHash -LiteralPath $_.FullName -Algorithm SHA256).Hash
      }
    }
}
```

Expected: `$legacyFilesBefore` contains no rows from excluded directories. Access failures are permitted but must be listed in audit notes.

- [ ] **Step 4: Create audit notes with exact decision and evidence headings**

Create `docs/inventory/audit-notes.md` with these headings:

```markdown
# Hermes Priority 0 Audit Notes

## Audit Metadata
- Started: <ISO-8601 timestamp with Asia/Jakarta offset>
- Workspace: `C:\Pepe Project\Agent\Hermes`
- Mode: read-only legacy inventory

## Owner Decisions
- Backup: waived by owner; no separate backup exists.
- Permission policy: reversible actions allowed; difficult-to-reverse actions require approval.
- Secret handling: existing Google Keep usage may continue temporarily; Hermes does not read it automatically.
- Transitional authority: legacy systems remain sources of truth until validated migration.

## Root Checks
| System | Expected path | Exists | Result |
|---|---|---:|---|

## Inventory Exclusions
- `.git`: repository internals
- `venv` and `.venv`: generated Python environments
- `node_modules`: generated dependencies
- `__pycache__` and `.cache`: generated caches

## Access Failures
| Path | Operation | Error | Impact |
|---|---|---|---|

## Ambiguities
| Subject | Evidence | Status | Owner decision needed |
|---|---|---|---|

## Duplicate Candidates
| Asset | Candidate locations | Action |
|---|---|---|

## Credential-Bearing Locations
Only minimum-scope, redacted locations may be recorded here. Never record secret names or values.

## Legacy Mutation Verification
Pending Task 6.
```

Replace only the audit timestamp and evidence-table rows with observed values. Do not place placeholders such as `<...>` in the finished file.

- [ ] **Step 5: Verify the audit note contains no accidental secret assignment**

Run:

```powershell
rg -n -i '(api[_-]?key|token|secret|password)\s*[:=]\s*[^<\s][^\s]*' 'C:\Pepe Project\Agent\Hermes\docs\inventory\audit-notes.md'
```

Expected: no matches containing values. Policy prose using these words without assignments is acceptable.

---

### Task 2: Write the Three Safety Policies

**Files:**
- Create: `docs/policies/permissions.md`
- Create: `docs/policies/secrets.md`
- Create: `docs/policies/preservation.md`

**Interfaces:**
- Consumes: owner decisions in the approved spec
- Produces: policy rules referenced by the manifest, registry, rollback procedure, and future Hermes phases

- [ ] **Step 1: Write the permission policy**

Create `docs/policies/permissions.md` with:

```markdown
# Hermes Permission Policy

## Default Rule
Hermes may perform reversible, in-scope actions. If reversibility or authority is unclear, Hermes stops and asks Pepe.

## Allowed Without Additional Approval
- Read files within the project scope.
- Create new Hermes documentation and drafts.
- Run non-mutating checks and report findings.
- Update Hermes status files when the underlying facts are verified.

## Explicit Approval Required
- Delete, overwrite, rename, or move existing files.
- Modify a legacy agent or runtime.
- Change a project's source of truth.
- Send messages or files to an external party.
- Enable a paid service or incur payment.
- Merge code or deploy to production.
- Retire a legacy agent.
- Expose, rotate, move, or revoke credentials.
- Perform any other action that is difficult to reverse.

## Scope Rule
Permission is evaluated per project and per action. Approval for one action does not grant standing approval for later actions.
```

- [ ] **Step 2: Write the secret policy**

Create `docs/policies/secrets.md` with:

```markdown
# Hermes Secret Policy

## Storage
- Do not store credential values in Hermes documentation, repository files, memory, skills, registries, manifests, or logs.
- Existing credentials may remain in Google Keep temporarily, but Hermes must not access Google Keep automatically.
- Runtime secrets must later use a local mechanism outside the repository.
- `.env.example` files may contain variable names only.

## Inventory
- Record only that a sensitive or credential-bearing asset exists.
- Use the minimum useful path scope.
- Never copy secret names or values into audit output.

## Logging and Errors
- Redact suspected credentials before persisting logs or reports.
- Stop inspecting content after identifying a likely credential-bearing file.
- Ask Pepe before exposing, moving, rotating, or revoking a credential.
```

- [ ] **Step 3: Write the preservation policy**

Create `docs/policies/preservation.md` with:

```markdown
# Hermes Legacy Preservation Policy

## Protected Systems
- Sage
- Scout
- Coders/Rex
- Pixel
- Chimit

## Priority 0 Rule
Inventory is read-only. Do not delete, rename, move, rewrite, disable, or reconfigure legacy assets or runtimes.

## Transitional Authority
Legacy systems remain authoritative until the corresponding Hermes capability is copied, validated, and explicitly approved as the new source of truth.

## Retirement Rule
`eligible_for_retirement` permits a proposal only. Retirement or deletion always requires explicit approval.

## Backup Exception
Pepe waived a separate backup for Priority 0. This is an accepted risk, not evidence that physical-drive recovery is available.
```

- [ ] **Step 4: Verify policy agreement**

Run:

```powershell
rg -n 'reversible|Explicit Approval Required|Google Keep|read-only|waived|explicit approval' 'C:\Pepe Project\Agent\Hermes\docs\policies'
```

Expected: every approved owner decision is represented and the three files do not contradict one another.

---

### Task 3: Build the Structured Legacy Asset Manifest

**Files:**
- Create: `docs/inventory/asset-manifest.yaml`
- Modify: `docs/inventory/audit-notes.md`

**Interfaces:**
- Consumes: Task 1 root evidence and Task 2 policies
- Produces: five stable system records used by future migration phases

- [ ] **Step 1: Create the manifest header and schema version**

Begin `docs/inventory/asset-manifest.yaml` with:

```yaml
schema_version: 1
generated_for: hermes-priority-0
inventory_mode: structured-incremental
backup_status: waived_by_owner
default_legacy_action: read
systems: []
```

- [ ] **Step 2: Add exactly five system records**

Add records with these stable IDs and verified absolute roots:

```yaml
- id: sage
  original_location: 'C:\Pepe Project\Agent\Sage'
- id: scout
  original_location: 'C:\Pepe Project\Agent\Scout'
- id: rex-coders
  original_location: 'C:\Pepe Project\Agent\Coders'
- id: pixel
  original_location: 'C:\Pepe Project\Agent\Pixel'
- id: chimit
  original_location: 'C:\Pepe Project\Agent\Marketing Specialist - Perhotelan\Chimit - CMO MGM'
```

Expand every record with all schema fields from the spec: `name`, `role`, `asset_categories`, `source_of_truth_for`, `sensitivity`, `runtime_status`, `migration_priority`, `migration_status`, `allowed_actions`, `approval_required`, `known_risks`, and `notes`. Use evidence from directory and documentation names. When evidence is insufficient, use `unknown` or an empty YAML list and record the ambiguity in `audit-notes.md`; do not guess.

- [ ] **Step 3: Apply the initial migration ordering**

Use these roadmap values:

- Sage: migration priority `2`, status `inventoried`.
- Rex/Coders: migration priority `3`, status `inventoried`.
- Scout: migration priority `4`, status `inventoried`.
- Pixel: migration priority `deferred`, status `inventoried`.
- Chimit: migration priority `deferred`, status `inventoried`.

- [ ] **Step 4: Apply allowed and approval-gated actions consistently**

Every record must include:

```yaml
allowed_actions:
  - read
approval_required:
  - modify
  - move
  - delete
  - disable_runtime
  - change_source_of_truth
  - retire
```

- [ ] **Step 5: Validate structure without installing dependencies**

Run:

```powershell
$manifest = Get-Content -Raw 'C:\Pepe Project\Agent\Hermes\docs\inventory\asset-manifest.yaml'
@('sage','scout','rex-coders','pixel','chimit') | ForEach-Object {
  if ($manifest -notmatch "(?m)^\s*- id: $([regex]::Escape($_))$") { throw "Missing system id: $_" }
}
$required = @('original_location','role','asset_categories','source_of_truth_for','sensitivity','runtime_status','migration_priority','migration_status','allowed_actions','approval_required','known_risks','notes')
foreach ($field in $required) {
  $count = ([regex]::Matches($manifest, "(?m)^\s+$([regex]::Escape($field)):")).Count
  if ($count -ne 5) { throw "Expected 5 occurrences of $field, found $count" }
}
```

Expected: command completes without throwing.

- [ ] **Step 6: Validate every root path**

Run:

```powershell
$legacyRoots | ForEach-Object {
  if (-not (Test-Path -LiteralPath $_ -PathType Container)) { throw "Missing legacy root: $_" }
}
```

Expected: command completes without throwing.

---

### Task 4: Create the Initial Project Registry

**Files:**
- Create: `registry/projects.yaml`
- Modify: `docs/inventory/audit-notes.md`

**Interfaces:**
- Consumes: project candidates and source material discovered in Task 1
- Produces: registry entries with explicit authority status for future Hermes routing

- [ ] **Step 1: Create the registry schema**

Start `registry/projects.yaml` with:

```yaml
schema_version: 1
projects: []
```

Every project record must contain:

```yaml
id:
name:
priority:
status:
objective:
workspace:
repository:
roadmap:
backlog:
definition_of_done:
source_of_truth:
allowed_actions:
approval_required:
current_task:
last_checkpoint:
```

- [ ] **Step 2: Discover project candidates from legacy registries and documentation**

Inspect filenames and structured registry documents under `Sage\Projects`, `Sage\Docs`, and `Coders\workspace-coders` without opening credential-bearing files. Start with the roadmap candidates Mabar, Threads, Mimi, and Lily. Add a project only when a real workspace or authoritative document is found.

- [ ] **Step 3: Record uncertainty explicitly**

For any candidate lacking sufficient evidence, set:

```yaml
source_of_truth: needs_owner_decision
status: audit_required
```

Record the evidence gap in `audit-notes.md`. Do not invent repository URLs, objectives, backlogs, or definitions of done.

- [ ] **Step 4: Verify all records have authority status**

Run:

```powershell
$registry = Get-Content -Raw 'C:\Pepe Project\Agent\Hermes\registry\projects.yaml'
$ids = ([regex]::Matches($registry, '(?m)^\s*- id:\s*([^\r\n]+)$')).Count
$sources = ([regex]::Matches($registry, '(?m)^\s+source_of_truth:\s*([^\r\n]+)$')).Count
if ($ids -ne $sources) { throw "Each project must have exactly one source_of_truth" }
```

Expected: project ID count equals source-of-truth count.

---

### Task 5: Write the Operational Rollback Procedure

**Files:**
- Create: `docs/recovery/rollback.md`

**Interfaces:**
- Consumes: manifest paths, preservation policy, and project registry authority
- Produces: a safe procedure for abandoning a failed Hermes migration and returning to unchanged legacy operation

- [ ] **Step 1: Write rollback triggers and prerequisites**

Create `docs/recovery/rollback.md` with these sections:

```markdown
# Hermes Migration Rollback

## Scope
This procedure restores operational authority to an unchanged legacy runtime after a Hermes migration failure. It cannot recover data after loss or corruption of the physical `C:` drive because no separate backup exists.

## Rollback Triggers
- Hermes output fails the capability's validation criteria.
- Project routing selects the wrong source of truth.
- A migrated workflow loses required context or permissions.
- Hermes cannot start or remain stable after restart.

## Preconditions
- The legacy runtime and assets still exist unchanged.
- The manifest identifies the original location.
- No destructive retirement action has been approved or performed.

## Procedure
1. Stop the affected Hermes workflow; do not delete its files.
2. Set the affected project or capability source of truth back to the recorded legacy system.
3. Resume the legacy runtime using its existing runbook; do not rewrite its configuration.
4. Verify one known-good legacy workflow.
5. Record the failure, rollback result, and next owner decision in the Hermes audit notes.

## Approval Boundary
Changing operational authority requires Pepe's explicit approval. Rollback does not authorize deletion of the failed Hermes implementation.

## Known Limitation
This procedure is not a physical-drive disaster-recovery plan. Priority 0 has an owner-approved backup exception.
```

- [ ] **Step 2: Add system-specific runbook references**

For each manifest system, add only verified runbook or architecture-document paths. If a runtime command is unknown, write `Runbook not yet identified` and add the gap to audit notes. Do not infer commands from filenames alone.

- [ ] **Step 3: Verify rollback does not claim backup recovery**

Run:

```powershell
rg -n -i 'physical.*drive|no separate backup|explicit approval|legacy runtime' 'C:\Pepe Project\Agent\Hermes\docs\recovery\rollback.md'
```

Expected: the limitation, approval gate, and unchanged legacy dependency are all explicit.

---

### Task 6: Verify No Legacy Mutation and Close the Audit

**Files:**
- Modify: `docs/inventory/audit-notes.md`
- Modify: `README.md`

**Interfaces:**
- Consumes: `$legacyFilesBefore` from Task 1 and all Priority 0 documents
- Produces: final verification evidence and a discoverable Priority 0 status from the Hermes README

- [ ] **Step 1: Capture the post-implementation legacy baseline**

Run in the same PowerShell session used for Task 1:

```powershell
$legacyFilesAfter = foreach ($root in $legacyRoots) {
  Get-ChildItem -LiteralPath $root -File -Recurse -Force -ErrorAction SilentlyContinue |
    Where-Object {
      $_.FullName -notmatch '\\(\.git|venv|\.venv|node_modules|__pycache__|\.cache)\\'
    } |
    ForEach-Object {
      [pscustomobject]@{
        Path = $_.FullName
        Length = $_.Length
        LastWriteTimeUtc = $_.LastWriteTimeUtc
        Sha256 = (Get-FileHash -LiteralPath $_.FullName -Algorithm SHA256).Hash
      }
    }
}
$legacyDiff = Compare-Object $legacyFilesBefore $legacyFilesAfter -Property Path,Length,LastWriteTimeUtc,Sha256
$legacyDiff | Format-Table -AutoSize
if ($legacyDiff) { throw 'Legacy mutation or inventory drift detected; stop and investigate.' }
```

Expected: no diff and no thrown error. If files changed externally during the audit, do not claim Hermes caused no mutation until timestamps and process ownership are investigated.

- [ ] **Step 2: Scan all Priority 0 documents for likely credential assignments**

Run:

```powershell
rg -n -i --glob '*.md' --glob '*.yaml' '(api[_-]?key|access[_-]?token|refresh[_-]?token|client[_-]?secret|password)\s*[:=]\s*["'']?[^<\s"''][^\s"'']+' 'C:\Pepe Project\Agent\Hermes'
```

Expected: no actual credential assignments. Review every match manually because policy examples may produce benign matches.

- [ ] **Step 3: Verify all required files exist**

Run:

```powershell
$requiredFiles = @(
  'C:\Pepe Project\Agent\Hermes\docs\inventory\asset-manifest.yaml',
  'C:\Pepe Project\Agent\Hermes\docs\inventory\audit-notes.md',
  'C:\Pepe Project\Agent\Hermes\docs\policies\permissions.md',
  'C:\Pepe Project\Agent\Hermes\docs\policies\secrets.md',
  'C:\Pepe Project\Agent\Hermes\docs\policies\preservation.md',
  'C:\Pepe Project\Agent\Hermes\docs\recovery\rollback.md',
  'C:\Pepe Project\Agent\Hermes\registry\projects.yaml'
)
$missing = $requiredFiles | Where-Object { -not (Test-Path -LiteralPath $_ -PathType Leaf) }
if ($missing) { throw "Missing Priority 0 files: $($missing -join ', ')" }
```

Expected: command completes without throwing.

- [ ] **Step 4: Close the audit notes with exact completion language**

Replace the pending mutation section with the observed hash-comparison result, timestamp, exclusions, and access limitations. If every check except backup passes, add exactly:

```text
Complete with owner-approved backup exception; physical-drive disaster recovery is unavailable.
```

Do not use this completion sentence if any other verification check fails.

- [ ] **Step 5: Add a Priority 0 control-document index to README**

Add a compact section to `README.md` linking to the manifest, audit notes, three policies, rollback procedure, project registry, approved spec, and this implementation plan. State the actual phase status: `planned`, `in progress`, or the exact completion sentence from Step 4.

- [ ] **Step 6: Perform the final document consistency scan**

Run:

```powershell
rg -n 'source.of.truth|needs_owner_decision|waived|read-only|explicit approval|physical-drive' 'C:\Pepe Project\Agent\Hermes\docs' 'C:\Pepe Project\Agent\Hermes\registry' 'C:\Pepe Project\Agent\Hermes\README.md'
```

Expected: terminology matches the approved spec, no file claims a backup exists, and no file authorizes automatic retirement or source-of-truth changes.

- [ ] **Step 7: Record the Git checkpoint**

Review `git status --short`, stage only Priority 0 Hermes files, verify ignored secrets remain untracked, commit with a terse description, and push to the private `ramadhanpradhana-arch/hermes-shas` repository. Report the branch, commit SHA, and verification result.

---

## Plan Self-Review Result

- Spec coverage: all approved structure, manifest, authority, permission, secret, preservation, rollback, error-handling, and verification requirements map to Tasks 1–6.
- Placeholder scan: finished artifacts prohibit unresolved placeholders; evidence-dependent values are populated during execution from observed state.
- Interface consistency: system IDs, migration statuses, paths, and policy terms are consistent across tasks.
- Scope: the plan implements Priority 0 only and does not install Hermes Core or migrate capabilities.
