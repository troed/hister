# Development Environment

## Remotes

| Remote   | URL | Purpose |
|----------|-----|---------|
| `origin` | `https://github.com/troed/hister.git` | Your GitHub fork (PR target) |
| `forgejo` | `http://<server-host>:2997/<user>/hister.git` | Private Forgejo instance |
| `upstream` | `https://github.com/asciimoo/hister.git` | Original upstream repository |

## Key Branches

| Branch | Remote | Purpose |
|--------|--------|---------|
| `master` | `origin/master` | Your fork's main branch (updated from `upstream/master` regularly) |
| `master` | `upstream/master` | Latest upstream code |

## Untracked Files (Keep These)

These files are local-only and should NOT be committed:

- `.pre-commit-config.yaml` — Pre-commit hooks for local workflow
- `.test/user-field/` — Test environment for user field validation
- `AGENTS.md` — This file (development environment info)

## Pre-commit Hooks

`.pre-commit-config.yaml` runs:
- `npm run format` — Formats frontend code
- `.test/user-field/run.sh` — Validates user field configuration

## Upstream Sync

When `origin/master` falls behind `upstream/master`:
1. `git fetch upstream`
2. `git push origin upstream/master:master` (or merge)

## Development Commands

### Backend (Go)
```bash
golangci-lint run --fix ./...  # Format and lint
go test ./...                   # Run tests
```

### Frontend (SvelteKit + Tailwind)
```bash
npm run format                   # Format code
npm run format:check             # Check formatting
npm run build -w @hister/app     # Build the app
npm run build -w @hister/website # Build the website
```

### Full Build
```bash
./manage.sh build   # or: go generate ./... && go build
```

## Code Style

- **Go**: Follow `golangci-lint` v2 rules (config in `.golangci.toml`). Use `goimports` + `gofumpt` formatting.
- **Frontend**: Prettier with single quotes, trailing commas, 100 print width, 2-space tabs. Use Tailwind scale classes, not arbitrary values for standard utilities.
- Check `webui/components/src/lib/components/ui/` for existing components before creating new ones.

## Submitting Pull Requests

- Make sure all tests pass and linting is clean before submitting
- Write clear commit messages explaining the "why" behind your changes
- Keep pull requests focused on a single concern

## AI Policy

### Restrictions on Generative AI Usage

- **All AI usage in any form must be disclosed.** You must state the tool you used (e.g. Claude Code, Cursor, Amp) along with the extent that the work was AI-assisted.
- **The human-in-the-loop must fully understand all code.** If you use generative AI tools as an aid in developing code or documentation changes, ensure that you fully understand the proposed changes and can explain why they are the correct approach.
- **AI should never be the main author of the PR.** AI may be used as a tool to help with developing, but the human contribution to the code changes should always be reasonably larger than the part written by AI. For example, you should be the one that decides about the structure of the PR, not the LLM.
- **Issues and PR descriptions must be fully human-written.** Do not post output from Large Language Models or similar generative AI as comments on any of our discussion forums, as such comments tend to be formulaic and low content. If you're not a native English speaker, using AI for translating self-written issue texts to English is okay, but please keep the wording as close as possible to the original wording.
- **Bad AI drivers will be denounced.** People who produce bad contributions that are clearly AI (slop) will be blocked for all future contributions.
- **AI should never be used for "good first issues"** The purpose of "good first issues" is to provide a smooth on boarding experience for anyone who would like to be involved in contributing to a project and not to be a low hanging fruit for AI models.

### There are Humans Here

Every discussion, issue, and pull request is read and reviewed by humans. It is a boundary point at which people interact with each other and the work done. It is rude and disrespectful to approach this boundary with low-effort, unqualified work, since it puts the burden of validation on the maintainer.

It takes a lot of maintainer time and energy to review AI-generated contributions! Sending the output of an LLM to open source project maintainers extracts work from them in the form of design and code review, so we call this kind of contribution an "extractive contribution".

The _golden rule_ is that a contribution should be worth more to the project than the time it takes to review it, which is usually not the case if large parts of your PR were written by LLMs.

## Public repo hygiene

- The Forgejo repo is public. Never commit internal hostnames, LAN IPs,
  usernames, home paths, or credentials. Tracked files use placeholders
  (`<server-host>`, `<user>`, `~/...`); the `go.mod` module path ends in a `.local` suffix — it is a module identifier, not a hostname (excluded from the scan).

- Automated checks: git hooks in `~/.githooks` (installed via global
  `core.hooksPath`) block commits and pushes that match the hygiene pattern
  (LAN IPs, `.local` hostnames, `/home/<user>` paths). Legitimate matches
  (test fixtures, vendored code) belong in `.git/hygiene-excludes`.
- Manual scan of the tracked tree (what the pre-push hook checks):
  `source ~/.githooks/hygiene-lib.sh && hygiene_scan_tree HEAD`
