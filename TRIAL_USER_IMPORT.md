# Render trial account import

The trial account list is stored in `tools/kedco-trial-users.json`. It contains account names, work emails, roles, and expected landing pages only; it contains no passwords. Commit that roster only to a KEDCO-approved private GitHub repository. If the repository is public, keep the roster outside Git and pass its path using `-ManifestPath`. `cloud data/` remains ignored by Git because it contains runtime user, session, audit, and operational data.

## Before importing

The Render service must already have an initialized administrator whose login works. This importer does not bypass authentication. If the Render administrator password is unavailable, reset it against the Render service's actual data store first; changing the local `cloud data/system/users.json` file does not change Render.

## Import accounts

From PowerShell in the project folder, run:

```powershell
.\tools\Import-KEDCO-TrialUsers.ps1 -ApiBase "https://YOUR-SERVICE.onrender.com" -ResetExistingPasswords
```

The script requests administrator credentials securely, then creates missing accounts, synchronizes the listed roles, and activates listed accounts. Accounts not in the 48-entry manifest are left unchanged. `-ResetExistingPasswords` generates a distinct random password for every account in the list, including existing accounts. The passwords are written to a CSV under your Documents folder, outside the repository. The CSV is also covered by `.gitignore`; do not commit or share it publicly. Give each password only to its account holder through a private channel.

Without `-ResetExistingPasswords`, existing account passwords are left unchanged; only newly created accounts receive generated passwords and appear in the CSV. The importer never uses the shared temporary password from the pasted register.

The app currently does not force a password change at first sign-in. The generated passwords remain valid until an administrator changes them. The register's landing page is informational; the app selects dashboards from account roles. The Data Analyst account is routed to the register's MIS landing page.

Render Free uses ephemeral local storage. Accounts or changes can be lost after a service restart or redeploy, so this flow is suitable for a disposable trial, not production records. Re-run the importer after a storage reset; use `-ResetExistingPasswords` only when you intend to issue a new credential sheet.