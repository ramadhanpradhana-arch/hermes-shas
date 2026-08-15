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
- The asset manifest identifies the original location.
- No destructive retirement action has been approved or performed.

## Procedure

1. Stop the affected Hermes workflow; do not delete its files.
2. Ask Pepe for approval to restore operational authority to the recorded legacy system.
3. Update the affected project or capability source of truth only after approval.
4. Resume the legacy runtime using its verified existing runbook; do not rewrite its configuration.
5. Verify one known-good legacy workflow.
6. Record the failure, rollback result, and next owner decision in the Hermes audit notes.

## System References

| System | Original location | Verified reference |
|---|---|---|
| Sage | `C:\Pepe Project\Agent\Sage` | `Sage\Docs\MONITORING.md` and `Sage\Docs\05_orchestrator.md` |
| Scout | `C:\Pepe Project\Agent\Scout` | `Scout\Docs\00_README.md` and `Scout\Docs\07_roadmap.md` |
| Rex/Coders | `C:\Pepe Project\Agent\Coders` | `Coders\Docs\02_runbook.md` and `Coders\Docs\04_status.md` |
| Pixel | `C:\Pepe Project\Agent\Pixel` | No runtime runbook identified; documentation-only reference |
| Chimit | `C:\Pepe Project\Agent\Marketing Specialist - Perhotelan\Chimit - CMO MGM` | Operational scripts exist; authoritative runbook not yet identified |

## Approval Boundary

Changing operational authority requires Pepe's explicit approval. Rollback does not authorize deletion of the failed Hermes implementation.

## Known Limitation

This procedure is not a physical-drive disaster-recovery plan. Priority 0 has an owner-approved backup exception.
