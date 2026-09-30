# Changelog

## [Unreleased]

## [1.3.4.1] - 2026-09-30

### Changed

- Merge upstream v1.3.3: named-profile startup compatibility. The add-on now sets
  `gateway.standalone: true` on add-on-managed named profiles before any gateway starts.
- Merge upstream v1.3.4: honour the Hermes checkout's `.python-version`, validate the
  runtime before recording a successful install, and rebuild an incompatible virtual
  environment with rollback instead of forcing Python 3.11 on every install.
- Keep the fork's entrypoint contract: `/entrypoint-local.sh` still applies
  container-fixes before `exec /run.sh`, so the `/run.sh` patches are unaffected.
- Preserve the fork identity (slug, `image`, `url`, description) over the upstream
  `config.yaml` block.

### Verified

- The opt-in Hermes Desktop remote backend on container port 9119 still ships in this
  release; the fork publishes pre-built images for it and changes none of that contract.
- `/run.sh` patch compatibility: the fork's `fix_run_sh` applies all six patches to the
  upstream v1.3.4 `run.sh`, idempotently, and `bash -n` stays clean.
- Local regression suite: 139 tests, 1 failure; the failure is an upstream defect that
  also fails on a pristine v1.3.4 checkout (see README).

## 1.3.2.1

- Merge upstream v1.3.2: gateway supervision runtime health fixes + `backup_exclude` to shrink Home Assistant backups

## [1.3.4] - 2026-09-26

### Fixed

- Use the selected Hermes checkout's `.python-version` instead of forcing Python 3.11. Older revisions without that file retain the Python 3.11 default.
- Rebuild incompatible or broken virtual environments even when the installation marker matches. Validate CLI/config imports before recording a successful install, and restore the previous environment if a rebuild fails.
- Preserve healthy compatible environments and additional packages during ordinary source updates. An interpreter migration reinstalls the project's dependencies; manually added packages may need reinstalling for the new Python version.

### Verified

- Local regression suite: 138 passed, 2 skipped.
- Real Home Assistant Supervisor candidate test: Python 3.11 to 3.14 migration, both standalone gateway APIs, per-profile nginx routing and unauthenticated-request rejection. Exact candidate identity and scope are recorded in [`docs/verification/v1.3.4-ha-smoke.json`](../docs/verification/v1.3.4-ha-smoke.json).
- Native single-profile `hermes gateway restart` remains unresolved in #35; it did not restart or reload the isolated custom-path profile during this test.

## [1.3.3] - 2026-09-26

### Fixed

- Restore named-profile startup compatibility with current Hermes by setting `gateway.standalone: true` on add-on-managed named profiles before any gateway starts.
- Leave older Hermes revisions, the default profile, and legacy or custom flat profile homes untouched by capability-detecting standalone support instead of relying on version strings.

### Verified

- `PYTHONDONTWRITEBYTECODE=1 python -B -m unittest discover -s tests -q` with Python 3.11.15 - 129 tests OK, 2 skipped.
- Focused coverage verifies unsupported Hermes revisions, default and flat homes, missing/false/true named-profile values, write failure propagation, and configuration-before-start ordering under macOS Bash 3.2.
- Shell syntax for the touched scripts, Python syntax, YAML parsing, and `git diff --check` passed.

## [1.3.2] - 2026-08-27

### Changed

- Reduce backup size by excluding only regenerated shared venv, project/dashboard dependencies, profile LSP runtimes, and `.cache`/`.npm` caches. The first start or first LSP use after a restore can be slower and network-dependent; canonical state, user-managed tools, and browser profiles/login state remain backed up.

### Fixed

- Reduce Linux gateway-supervisor process-tree snapshots from 20 per second to one every two seconds while preserving 50 ms exit and signal responsiveness, immediate cleanup scans, and the existing non-Linux containment behavior.
- Publish add-on launcher processes with the recognized `hermes-gateway` command identity so Hermes Dashboard liveness correctly reports s6-supervised gateways as running.
- Preserve Linux virtualenv discovery while publishing that identity by starting a venv-local `hermes-gateway` interpreter symlink directly; this prevents startup from losing installed packages such as `hermes_cli`.
- Keep the per-slot supervisor out of Hermes Gateway process discovery by running it through ordinary venv Python and deriving the recognizable `hermes-gateway` alias only for the final child; status and update flows no longer treat the supervisor as an extra manual gateway.
- Keep older pinned Hermes revisions startable when they predate `--external-supervisor`: the add-on launcher feature-detects the installed Gateway parser and removes only the unsupported Python argument, while modern revisions retain the external supervisor handback marker.

## 1.3.1.2

- Fix entrypoint: read `apply_fixes` with jq (bashio not available in this addon)

## 1.3.1.1

- Added `apply_fixes` config option (default true) to toggle container-fixes at startup
- Made repo and images public

## 1.3.1

- Fork from WolframRavenwolf/hermes-ha-addon v1.3.1
- Added entrypoint wrapper: applies container-fixes before /run.sh starts
- Pre-built images via CI (no compilation at install time)
- Image published to ghcr.io/grunjol/addon-hermes-agent
