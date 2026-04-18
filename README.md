# EasyBilling Integration Skill

A Claude Code skill that teaches AI coding agents how to integrate customer applications with the EasyBilling billing platform.

## What This Skill Does

When a Claude Code agent encounters an EasyBilling integration task, this skill provides:

- **Domain model** — entity relationships, lifecycle states, and key rules
- **Needs-assessment flowchart** — routes to the right workflow based on the customer's billing model
- **Workflow guides** — step-by-step API integration for subscription billing, usage-based billing, prepaid credits, and payment collection

## Installation

Copy the skill files into your Claude Code skills directory:

```bash
git clone git@github.com:adamzeng123/easybilling-integration-skill.git ~/.claude/skills/easybilling-integration
```

Or add as a submodule in your project:

```bash
git submodule add git@github.com:adamzeng123/easybilling-integration-skill.git .claude/skills/easybilling-integration
```

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Entry point — domain model summary, needs-assessment flowchart, common mistakes |
| `domain-model.md` | Full entity hierarchy, key rules, lifecycle states, ID formats |
| `auth-setup.md` | JWT authentication, token management, environment configuration |
| `subscription-billing.md` | Account creation, contract setup, pricing models, invoices |
| `usage-billing.md` | Usage event ingestion for metered billing |
| `prepaid-credits.md` | Bucket-based credit management |
| `payments.md` | PaymentHub: Stripe, Alipay, WeChat Pay, refunds |

## Supported Billing Models

- **Subscription** — flat-fee and tiered pricing with contract lifecycle management
- **Usage-based** — metered consumption via usage events
- **Prepaid credits** — resource-based or currency-based credit buckets
- **Payment collection** — Stripe, Alipay, and WeChat Pay via PaymentHub

## License

Proprietary. For authorized customers only.
