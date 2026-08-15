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
