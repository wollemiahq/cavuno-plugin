# Cavuno plugin

Cavuno helps associations, publications, communities and standalone
businesses launch, run and grow a job board with AI. This plugin lets Claude
work on your Cavuno board with you: post and import jobs, review jobs waiting
for approval, manage employers, set up pricing plans, draft job digests,
write and refresh blog posts, and report on traffic and search performance.

Claude asks before every change. Jobs and posts are created as drafts unless
you say otherwise, and each change is read back and reported with links.

## What it connects to

The plugin adds Cavuno's hosted connector,
`https://mcp.cavuno.com/mcp/connector`. You sign in with your Cavuno account
on first use (OAuth); Claude never sees your password, and you can
disconnect at any time from your Claude connector settings.

## What it sends

- Requests to the Cavuno API for the board you signed in to, made only when a
  task needs them: reading jobs, companies, posts, settings and analytics,
  and, after you confirm, creating or changing them.
- Text you paste into the conversation, such as a job description, when you
  ask Claude to save it to your board.
- Problem reports, when Claude hits something Cavuno could do better: a short
  description of the goal and what got in the way, with no credentials or
  personal data.

The plugin itself stores nothing. Your board's data stays in Cavuno, under
[Cavuno's privacy policy](https://cavuno.com/privacy-policy).

## Commands

| Command | What it does |
| --- | --- |
| `/cavuno:check` | Checks the connection and summarizes the board's health. |
| `/cavuno:post-job` | Posts one job, or a list of up to 100, as drafts. |
| `/cavuno:import-jobs` | Imports jobs from a careers page, or reviews backfill. |
| `/cavuno:blog` | Writes a blog post from outline to draft. |
| `/cavuno:refresh` | Finds posts losing search traffic and plans fixes. |
| `/cavuno:performance` | Reports traffic, job views, apply clicks and search. |
| `/cavuno:digest` | Drafts a jobs email digest or social posts. |
| `/cavuno:tidy` | Reviews pending, stale and duplicate jobs. |

In Claude chat you can also just ask, for example "post this job", "how did
the board do last month?" or "which blog posts should I update?".

## Skills

`cavuno-basics` holds the shared rules (read first, confirm, verify). The
task skills are `connect-and-check-board`, `launch-and-set-up-board`,
`post-jobs`, `source-jobs`, `manage-employers`, `set-up-monetization`,
`promote-jobs`, `plan-seo-content`, `write-blog-post`,
`refresh-and-prune-content`, `review-performance`,
`organize-taxonomy-and-fields`, `tidy-board` and
`migrate-from-another-platform`.

Skill and command names are stable from 1.0.0: a rename ships as a new name,
with the old one kept as a deprecated alias.

## What stays in the dashboard

Some work needs the Cavuno dashboard: bulk exports, resume downloads, CSV
imports, Stripe and tax setup, and payments. Claude tells you when a task
needs it.

## Install

- **Claude**: install Cavuno from the plugin directory, or run
  `/plugin marketplace add wollemiahq/cavuno-plugin` and install `cavuno`.
- **Codex, Cursor and Grok**: load this repository as a plugin. These
  clients use `https://mcp.cavuno.com/mcp`.

## Help

- Documentation: <https://cavuno.com/docs/mcp>
- Troubleshooting: <https://cavuno.com/docs/troubleshooting>
- Email: hi@cavuno.com
- Terms: <https://cavuno.com/terms-of-service>

## Evals

`evals/` holds cases for `claude plugin eval`, run against a mocked Cavuno
connector: `claude plugin eval . --runs 1`.
