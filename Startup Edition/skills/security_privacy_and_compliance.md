# Skill: Security, Secrets & Data Privacy (Startup Edition)

> **Load When:** Handling API keys/auth, configuring dev environments, or working with user data.
> **Precedence:** Safety (`AGENT_RULES.md` §1) is never overridable.

---

## Hard Security Rules
1. **Zero Secrets in Code or Git:** Store all API keys, DB credentials, and JWT secrets in `.env` (gitignored). Commit only `.env.example` with `<YOUR_API_KEY>` placeholders.
2. **Mute Outbound Comms in Dev:** Ensure email, SMS, push notifications, webhooks, and payment gateways (Stripe, etc.) use test keys or are disabled (`0` / `false`) locally.
3. **Never Run Destructive SQL on Shared/Prod DBs:** Verify table/column names in models/`SCHEMA_INDEX.md` first, and deliver any shared/prod data changes as transaction-wrapped `.sql` scripts for manual review.
4. **No Real User PII in Docs/Tests:** Use synthetic test emails, names, and IDs in fixtures, `MASTER_MEMORIES.md`, and wiki pages.
5. **Protect Existing Files:** Never delete files you didn't create without asking; move superseded verification files to `./Verification/archive/`.
