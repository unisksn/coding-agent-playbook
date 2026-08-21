# Repository Instructions

## Public Repository Safety

This is a public repository. Treat every committed file and the entire Git history as publicly accessible.

Before committing or pushing changes:

- Review the staged diff for secrets, credentials, and non-public information.
- Never commit API keys, access tokens, passwords, private keys, authentication cookies, `.env` values, or other credentials.
- Never commit confidential company, customer, infrastructure, or internal-only information.
- Use clearly fake placeholders in examples instead of real credentials or sensitive values.
- Do not copy sensitive values discovered in local files, environment variables, logs, or tool output into repository files.

If potentially sensitive information is found:

- Stop before committing or pushing it.
- Remove or replace it with a safe placeholder.
- If you are unsure whether information is safe to publish, ask before including it.

If a secret may already have been committed:

- Do not assume that deleting it in a later commit removes the exposure.
- Report the potential exposure immediately.
- Recommend revoking or rotating the affected credential before considering Git history cleanup.
