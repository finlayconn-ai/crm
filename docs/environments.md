# CRM environments

This guide covers the finlayconn-ai fork. The repository documents an inherited deployment design. Confirm the fork's provider settings before using a hosted environment.

| Setting | Development | PR preview | Production |
|---|---|---|---|
| App | App on localhost port 3000, API on port 3001. | Use the deployment URL for the PR commit. | The documented deployment uses separate Vercel app, API, and agent projects. Confirm the fork's domains. |
| Database | Local Postgres, Prisma migrations, and synthetic seed data. | The inherited setup describes previews sharing production. Verify isolation before testing writes. | The API build applies migrations on the Vercel Production target. The documented production branch is `release`. |
| Auth | Better Auth serves provider callbacks on the API origin. | Verify the callback and allowed sign-in list for each preview. | Register the production API callback and set the cookie domain. |
| Integrations | Use dedicated test mailboxes. Enable external connections only for tests that need them. | Check mailbox recipients, agent schedules, and external connections before testing writes. | Review connected accounts in Settings. Confirm owners and schedules with the operator. |
| CI | Prepare a separate test database with `bun run db:test`. Run `bun run test`. | CI requests `ubuntu-24.04`. The fork's VPS runner and browser test command need verification. | Verify the deployed release commit and run a read-only smoke test. |

## Start and stop

Follow [local setup](setup.md) and [environment variables](environment.md). Confirm the database host before applying migrations. The integration suite uses a separate `TEST_DATABASE_URL` ending in `_test`.

Start local Postgres with `docker compose up -d`. Run `bun install`, `bun run db:deploy`, `bun run db:seed`, and `bun run dev`. Stop the dev process and use `docker compose stop` to retain local data.

## Portfolio alignment

- Assign a named Boat environment and private Tailscale Serve URL when provisioning development.
- Provision an isolated Postgres database per PR before enabling write-capable preview tests. Apply the branch migrations and seed synthetic records.
- Record the VPS runner label, preview cleanup owner, error tracking, and database recovery procedure after verification.
- Use Stripe sandbox keys and MailerSend test inboxes if the fork adds billing or transactional email. CRM mailbox sync uses its own connected accounts.

See [release procedures](../CONTRIBUTING.md#releases).

## Errors and product analytics

Sentry is the portfolio default for error tracking. The fork has no verified Sentry project or error delivery. When enabling it, tag preview and production separately and attach the deployed commit. Enable local capture only for a deliberate test.

PostHog is optional for product analytics. This fork inherits upstream telemetry with a fixed destination. Set `CRM_TELEMETRY_DISABLED=1` during development and preview. Confirm the destination and intended events before enabling production analytics. [Telemetry behavior](telemetry.md)

Start with the free plans. The fork’s actual provider plans, billing settings, and alert owner remain unverified.
