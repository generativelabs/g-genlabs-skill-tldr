# tldr

A Claude Code skill that turns long replies into short grouped bullets without dropping any facts. Type `/tldr` after a long answer and get the same information in a third of the space. Turn it `on` and every reply comes back that way.

## Install

```bash
git clone https://github.com/generativelabs/g-genlabs-skill-tldr ~/.claude/skills/tldr
```

Start a new Claude Code session and `/tldr` is available. To install it for one project only, clone it into that project's `.claude/skills/tldr` instead.

## Example

You ask why a deploy failed and get this back:

> I looked into the failed deploy. The build itself passed, but the deploy step failed because the `DATABASE_URL` environment variable isn't set in the production environment. It's set in preview, which is why the preview deploys have been working fine. I also noticed that the `STRIPE_WEBHOOK_SECRET` is set in production but still has the test-mode value, so even once the database issue is fixed, webhooks will fail signature checks. To fix this, you'll need to add `DATABASE_URL` to production and swap the webhook secret for the live one from the Stripe dashboard. I haven't changed anything yet. Once both are updated, a redeploy should go through. I didn't check whether any other variables differ between preview and production.

`/tldr`:

> **Deploy failure**
> - Build passed; deploy failed on missing `DATABASE_URL` in production (set in preview only)
> - `STRIPE_WEBHOOK_SECRET` in production is the test-mode value; webhooks will fail signature checks
>
> **To fix (nothing changed yet)**
> - Add `DATABASE_URL` to production
> - Replace the webhook secret with the live one from the Stripe dashboard
> - Redeploy
>
> **Not checked**
> - Other preview vs. production variable differences

`/tldr super`:

> **Deploy failure**
> - Deploy failed: `DATABASE_URL` missing in prod, preview only
> - Prod `STRIPE_WEBHOOK_SECRET` is test-mode; webhooks fail
>
> **To fix (nothing changed yet)**
> - Add `DATABASE_URL` to prod
> - Swap in live webhook secret
> - Redeploy
>
> **Not checked**
> - Other preview/prod var diffs

## Use

```text
/tldr            condense the previous reply, once
/tldr <what>     condense what the instruction points at, once
/tldr on         condense the previous reply, then keep every reply in TL;DR form
/tldr super      same as on, with every bullet cut to its tightest form
/tldr off        back to normal replies
/tldr status     say which mode is on
/tldr help
```

The instruction can point at an earlier message or a part of one:

```text
/tldr your reply about the migration
/tldr just the decisions
/tldr only the numbers
```

While a mode is on, every reply ends with a line naming it (`TL;DR mode on` or `TL;DR super mode on`). Code blocks, commands, and error text are never condensed. Mode lasts for the session; a new session starts with it off.

## How it differs from summarizing

A summary picks what matters and drops the rest. `/tldr` keeps every fact, number, path, decision, and open item, and drops only the connecting prose. If a third of the length would lose information, it keeps the information.

## Update or remove

```bash
git -C ~/.claude/skills/tldr pull     # update
rm -rf ~/.claude/skills/tldr          # remove
```

## About

Made by [Generative Labs](https://www.generativelabs.com/?utm_source=github&utm_medium=referral&utm_campaign=skill_tldr). We help teams take AI-built software to production. Issues and pull requests are welcome.

MIT licensed.
