# Changelog

## 1.3.2.1

- Merge upstream v1.3.2: gateway supervision runtime health fixes + `backup_exclude` to shrink Home Assistant backups

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
