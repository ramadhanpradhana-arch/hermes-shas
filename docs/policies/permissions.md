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
