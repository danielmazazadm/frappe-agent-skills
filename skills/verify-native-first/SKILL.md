---
name: verify-native-first
description: Before building, changing or fixing anything on Frappe Framework, ERPNext, Frappe CRM, Helpdesk, Webshop, Payments or any other Frappe app (and on other open-source platforms such as Chatwoot) — and whenever you are unsure whether a feature already exists or whether a bug belongs to the product — check the installed version, read that version's source code, the official docs and GitHub (releases, changelogs, issues and merged PRs). Prevents writing custom workarounds for what the platform already does, and patching around bugs that upstream already fixed. Also use when planning a version upgrade. Do not use for code that does not touch these products.
---

# Verify the native feature first

**Use what the platform already ships before writing your own code.** Docs lag
behind releases; the source code of the installed version and GitHub are the
truth. Never conclude "this doesn't exist" or "this is broken" without evidence.

## Know which product owns what

Mixing roles is the most common cause of unnecessary custom code.

- **Frappe Framework** — the base: REST API, roles and permissions, Custom
  Fields, Property Setters, Webhooks, Notifications, Server/Client Scripts,
  Print Formats, Report Builder, the scheduler and, since v16, the Automation
  engine (event, scheduled and wait triggers; actions such as Set Field Value,
  Create Document, Send Notification, Assign to User, Call Webhook, Run Script).
- **Frappe CRM** — the customer relationship: leads, deals, contacts, notes,
  tasks, Kanban, SLAs, assignment rules. It does **not** record sales or
  invoices. It hands off to ERPNext through **ERPNext CRM Settings**: create
  the Customer when a deal is won, "Create Quotation" from a deal. This works
  with ERPNext on the same site or on a different site via API key.
- **ERPNext** — commercial operations: items, prices, stock, quotations,
  orders, invoices, payments.
- **Webshop / Payments** — the online store and payment gateways on top of ERPNext.
- Other apps (Helpdesk, HRMS, Insights, LMS…) — check https://frappegems.com/best-frappe-apps before building a module yourself.

## Steps, in order

1. **Installed version**: check where the code will run, not what you remember.
   ```bash
   bench version                                    # inside the bench / container
   docker exec <backend-container> bench version    # docker deployments
   ```

2. **Source of that version**: search before you write.
   ```bash
   cd apps/<app> && grep -rn "<keyword>" --include=*.py | head
   ```
   Look at:
   - `@frappe.whitelist()` functions: they are already callable at `/api/method/<dotted.path>`;
   - `hooks.py`;
   - settings DocTypes such as `FCRM Settings`, `ERPNext CRM Settings` or `Selling Settings`;
   - `frappe/automation_engine/`.

3. **Official docs**: they may be behind, so confirm against step 2.
   - Framework: https://docs.frappe.io/framework/user/en/introduction
   - REST API: https://docs.frappe.io/framework/user/en/api/rest
   - ERPNext: https://docs.frappe.io/erpnext/introduction
   - Frappe CRM: https://docs.frappe.io/crm/introduction

4. **GitHub: what changed and what was already fixed.**
   ```bash
   gh release list -R frappe/crm -L 5             # also frappe/frappe, frappe/erpnext, frappe/webshop, frappe/payments
   gh release view <tag> -R frappe/crm            # fixes and features of a release
   gh api repos/frappe/crm/compare/<installed>...<newer> --jq '.commits[].commit.message' | head -50
   gh search issues --repo frappe/crm "<error or feature>" --limit 10
   gh search prs --repo frappe/frappe --merged "<error or feature>" --limit 10
   ```
   If the bug is fixed in a newer release, **upgrade**: staging first, then
   production. Do not patch around it.

5. **Decision ladder**: stop at the first rung that works.
   1. It already exists and only needs **enabling or configuring**: a setting, role, assignment rule or automation.
   2. **Native customisation** covers it: Custom Field, Print Format, Notification, Webhook, Automation or Report Builder.
   3. **Native API calls** from outside cover it: `/api/resource/<DocType>` or `/api/method/<whitelisted function>`.
   4. An **existing app** already does it.
   5. Only then write custom code. Keep it minimal, keep it in your own app (never in core), and state why rungs 1–4 did not fit.

## Other platforms

The same steps apply to any self-hosted open-source product. For example, with
**Chatwoot**:
- **Version:** the image tag.
- **Source:** `config/routes.rb` for the API, `app/models/automation_rule.rb`, `app/services/`.
- **Docs:** https://www.chatwoot.com/hc/user-guide/en and https://developers.chatwoot.com/api-reference/introduction.
- **GitHub:** `gh release list -R chatwoot/chatwoot`.

Built-in options to rule out first: automation rules, macros, canned responses,
custom attributes, agent bots and webhooks.

## What to report

Before proposing code, state briefly:
- the installed version;
- what the source and GitHub show, with a file path or a PR/release link;
- which rung of the ladder the solution landed on.

Without that evidence, do not claim something "doesn't exist" or "is broken". Confirm against real data (logs, records) before calling something a bug.
