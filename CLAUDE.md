# CLAUDE.md — claude-ops

## What this repo is

A fork of the public claude-ops Claude Code plugin ("Business Operating System": skills, agents,
hooks, daemons) at v3.10.12. It is not a web app and has no HTTP server. The repo root is a Claude
Code marketplace; the plugin itself lives one level down in `claude-ops/` (the `ops` plugin), with
`desktop-act/` as a companion plugin and `installer/` as a cross-CLI installer. `AGENTS.md` and
`claude-ops/CLAUDE.md` (which points at `skills/ops-rules/SKILL.md`) are the upstream's own notes.

**This repo is public.** No personal data in any commit: no secrets, no personal emails, no real
home-directory paths, no account IDs. `tests/test-no-secrets.sh` (the PII gate) fails on them.

## Requests that belong here

- Changes to the claude-ops plugin itself: its skills (`claude-ops/skills/`), agents, hooks,
  `bin/ops-*` scripts, daemons, telegram server, installer, docs.
- Reading how upstream solved something before lifting the idea into the estate (the estate has
  lifted from here before, e.g. the MCP fixer infra guard).

## Not here

- The estate's own guards, hooks and supervisors (fixer infra guard, cloudflared watchdog,
  services supervisor) — **ag-bin**. An idea lifted from claude-ops is built there, not patched in
  here.
- The command panel, its checks and the estate's own skills (`rur`) — **ag-command-center**.
- A multi-model "council" — the estate's is **ag-council**; **llm-council** is a separate fork.

## Install / build / test / sweep

All npm commands run from `claude-ops/` (the plugin directory), not the repo root.

- Install: `cd claude-ops && npm ci` (Node 18+, CI uses 20).
- Build: none. Type/syntax check: `cd claude-ops && npm run type-check`.
- Lint: `cd claude-ops && npm run lint` (Prettier `--check`; needs `npm ci` first).
- Test: `cd claude-ops && npm test` (PII/secret scan + shell and Node syntax checks, a few seconds).
- Sweep (what CI's test job runs): from the repo root, `bash claude-ops/tests/run-all.sh` — 50
  suites, about 4 minutes; measured 50 passed / 0 failed in a cloud checkout on 26/09/2026.
- CI also runs gitleaks (`.github/workflows/ci.yml`), which is not installed in a cloud session.
- Installer (only if it changed): `cd installer && npm install && npm test` (`node test/smoke.mjs`).

## Live-server-only checks

- None for the server: nothing here serves HTTP, so there is no health check and no `/healthz`.
- The launchd daemons, Keychain use and some hooks are macOS-only (`AGENTS.md`); they cannot be
  exercised in a Linux cloud session.
- Loading the plugin into a real Claude Code (`claude --plugin-dir ./claude-ops/claude-ops`, then
  `/reload-plugins`) needs an interactive Claude Code, not a cloud build.

## Shipping a change

1. Work on a branch, never on `main`.
2. Run install, `npm run type-check`, `npm run lint`, `npm test` and the sweep above.
3. Open a PR to `main` (the fork's only branch; upstream's PR template says `dev`, which does not
   exist in this fork). Its description's FIRST line may be exactly `BUILD AND SWEEP PASSED` **only
   if** those genuinely passed — the server applies such PRs automatically. Below it, list what was
   run and what could not run in the cloud (gitleaks, macOS daemons, a live plugin load). Otherwise
   do not use that phrase.
4. One app's change per PR. Keep the diff free of personal data — re-run `npm test` last.
5. Version and changelog bumps follow upstream's own process (`claude-ops/RELEASE.md`,
   `claude-ops/CHANGELOG.md`); do not hand-edit version strings.

## Server facts

- Folder on the server: not recorded in any repository; confirm with the inventory paste.
- Port: none (not a web service). Not listed in the estate's tool registry.
- Start/restart: none on the server that any repository records. It is a plugin, installed into a
  Claude Code with `/plugin install ops@ops-marketplace`.
