# Contributing to Anthropic Sandbox Runtime

Thanks for helping improve `sandbox-runtime`, a TypeScript CLI and library for
running processes with filesystem, network, and OS-level sandbox restrictions.

This project is security-sensitive and still a beta research preview, so changes
should be small, explicit, and easy to review.

## Before You Start

- Read [README.md](./README.md) for the current CLI, library, and configuration
  behavior.
- Use Node.js 18 or newer, matching the `package.json` engine requirement.
- Install dependencies from the lockfile before making code changes:

```bash
npm ci
```

- Install Bun if you plan to run the test suite, because `npm test` delegates to
  `bun test`.
- Check platform-specific behavior before changing sandbox enforcement. macOS
  uses `sandbox-exec`/Seatbelt profiles, while Linux uses bubblewrap and
  vendored seccomp helpers.

## Project Layout

- `src/cli.ts` contains the `srt` command entrypoint.
- `src/index.ts` exports the public library API.
- `src/sandbox/` contains sandbox managers, platform implementations, proxy
  code, config schemas, and violation tracking.
- `src/utils/` contains shared platform, config, debug, ripgrep, and executable
  lookup helpers.
- `test/` contains CLI, config, proxy, platform, and sandbox behavior tests.
- `vendor/` and `vendor/seccomp-src/` contain vendored seccomp artifacts and
  build inputs.

## Making Changes

### CLI and Public API

- Keep CLI flags, config names, and exported types backward-compatible unless
  the PR explicitly documents a breaking change.
- Update README examples when CLI, settings, or public library behavior changes.
- Keep error messages actionable, especially for blocked filesystem or network
  operations.

### Sandbox Enforcement

- Treat filesystem, network, Unix socket, proxy, and seccomp changes as
  security-sensitive.
- Prefer deny-by-default behavior when adding new access paths.
- Keep macOS and Linux behavior aligned where the OS primitives allow it.
- Add or update focused tests for bypasses, path boundary handling, symlink
  behavior, proxy tunneling, config validation, and platform-specific fallbacks.

### Dependencies and Build Artifacts

- Do not hand-edit generated lockfile sections unless dependency metadata
  genuinely changed.
- Do not replace vendored seccomp artifacts without documenting how they were
  rebuilt and which platform behavior changed.
- Keep package metadata in `package.json` aligned with the files shipped to npm.

### Documentation

- Keep examples minimal and runnable.
- Document security-relevant assumptions, such as whether a path is denied,
  allowed, proxied, or platform-specific.
- For docs-only changes, avoid touching lockfiles, generated files, or source
  formatting.

## Validation

Run the smallest relevant checks before opening a PR. For documentation-only
changes:

```bash
git diff --check
```

For TypeScript changes:

```bash
npm run typecheck
npm run lint:check
npm run build
```

For behavior changes, run targeted tests first, then the full suite when
practical:

```bash
npm test
```

If a platform-specific check cannot run locally, say so in the PR and explain
which platform still needs verification.

## Pull Requests

Use focused PRs. Include:

- what changed
- why the change is needed
- which files or behavior areas were touched
- validation commands and results
- any platform-specific limits or follow-up needed

Good PR examples include:

- fixing one sandbox bypass or boundary condition
- adding a targeted regression test for a path, proxy, or config edge case
- clarifying one documented configuration behavior
- updating one dependency or vendored component with validation notes

Avoid broad refactors that mix CLI behavior, sandbox enforcement, formatting,
and documentation in one PR.
