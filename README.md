# 🔐 IT offboarding automation (n8n template)

Remove a leaver's access the same day, every time, with a record of what was done. This n8n workflow takes an offboarding request, **suspends the user in JumpCloud and Google Workspace**, and posts a summary to Slack, including the steps that still need a person.

![n8n](https://img.shields.io/badge/n8n-EA4B71?style=flat&logo=n8n&logoColor=white) ![JumpCloud](https://img.shields.io/badge/JumpCloud-1B2A3A?style=flat) ![Google Workspace](https://img.shields.io/badge/Google_Workspace-4285F4?style=flat&logo=google&logoColor=white) ![Slack](https://img.shields.io/badge/Slack-4A154B?style=flat&logo=slack&logoColor=white) ![License](https://img.shields.io/badge/license-MIT-green?style=flat)

> ⚠️ **Starts in DRY RUN mode.** It only reports what it *would* do until you set `DRY_RUN: false` in the Config node. Test with a dummy account first.

## How it works

```
Webhook POST /offboard  { "email": "leaver@company.com", "ticketKey": "HELP-123" }
   → Config (DRY_RUN, Slack channel)
   → JumpCloud: find the user by email
   → Live run?
        yes → JumpCloud: suspend user → Google Workspace: suspend user
        no  → skip changes
   → Build summary (done + still-manual steps)
   → Slack: post summary
```

It **suspends** accounts rather than deleting them, so a mistake is reversible. Delete later, as a separate step, once your retention period has passed.

## Setup

1. **Import** `workflow.json` in n8n (*Workflows → Import from file*).
2. **JumpCloud:** create an API key and add it as the environment variable `JUMPCLOUD_API_KEY` in your n8n instance.
3. **Google Workspace:** create a *Google OAuth2 API* credential with the scope `https://www.googleapis.com/auth/admin.directory.user`, using an admin account. Select it on the Google node.
4. **Slack:** a bot token with `chat:write`. Select it on the Slack node and invite the bot to your offboarding channel.
5. Call the webhook with a **test user** while `DRY_RUN` is still `true`, and check the Slack summary.
6. Set `DRY_RUN: false` when you're confident.

Trigger it from anywhere: a Jira automation rule, an HR system, a form or a schedule on the last working day.

## Ideas to extend

- Remove group memberships (removes SSO app access) before suspending.
- Unbind devices, wipe enrolled phones, deactivate Slack (Enterprise SCIM) and Jira.
- Post a daily reminder with **Laptop collected** / **Tools removed** buttons until someone confirms.
- Write the summary back to the Jira ticket as an audit comment.

---

Built by [Ahmed Mohamed](https://github.com/AhmedMustafa-tech), IT Operations & Automation Engineer. Want this for your stack? [Let's talk on LinkedIn](https://www.linkedin.com/in/ahmed-mohamed-9411311a8/).
