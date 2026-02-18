# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Next.js 16 + React 19 + TypeScript + Tailwind CSS v4 application that serves as a platform for AI-powered GitHub automation via Claude Code Action. The web app itself is a default `create-next-app` scaffold; the primary value is in the `.github/workflows/` configuration.

## Commands

- `npm run dev` — Start dev server (Turbopack)
- `npm run build` — Production build
- `npm run lint` — ESLint (flat config, `eslint.config.mjs`)
- `npm start` — Serve production build

No test framework is configured.

## Architecture

### GitHub Workflows (`.github/workflows/`)

**`claude.yml`** — The main automation. Triggers when `@claude` is mentioned in issues, PR comments, or reviews. It:
- Extracts an effort level (`Low`/`Medium`/`High`) from `**Effort:** <level>` in the issue/comment body (defaults to `high`)
- Runs Claude Code Action with `--model opus`
- Restricts tools to `Bash(git:*)`, `Bash(gh pr:*)`, `Bash(gh issue:*)`
- System prompt mandates creating a branch, committing, pushing, and opening a PR targeting `develop` via `gh pr create`

**`claude-code-review.yml`** — Automated PR review using the `code-review` plugin. Currently **disabled** (`if: false`). Remove that line to re-enable.

**`jira-pr-sync.yml`** — Reverse sync from GitHub back to Jira. Triggers on PR `opened` targeting `develop`. Extracts the Jira key (from linked issue title/body, PR title, or branch name), transitions the ticket to a review status, and adds a comment with the PR link. Requires secrets `JIRA_EMAIL` and `JIRA_API_TOKEN`, and variables `JIRA_BASE_URL` and `JIRA_REVIEW_STATUS`.

### Branching Strategy

- `main` — production branch
- `develop` — integration branch; all PRs target `develop`
- Claude-created branches follow `claude/issue-<number>-...` naming

### Next.js App (`app/`)

Standard App Router layout with Geist fonts, Tailwind v4 via `@tailwindcss/postcss`, and path alias `@/*` mapped to project root.
