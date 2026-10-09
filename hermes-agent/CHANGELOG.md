# Changelog

## [Unreleased]

## [1.3.7.1] - 2026-10-09

### Changed

- Merge upstream v1.3.5: dashboards start through `/usr/local/lib/hermes-dashboard-launcher.py`
  in exactly their assigned profile home, the gateway launcher keeps a real, discoverable
  command across a package-manager interpreter replacement, the Desktop backend survives that
  replacement, and authenticated detailed-health traffic is forwarded on ingress and on the
  enabled direct HTTP/HTTPS listeners.
- Merge upstream v1.3.6: keep the Hermes source root importable when the gateway launcher
  re-enters itself after an interpreter replacement, which fixes the 1.3.5 gateway restart loop
  (`ModuleNotFoundError: No module named 'hermes_cli'`).
- Merge upstream v1.3.7: dashboard gateway Start/Stop/Restart answers HTTP 409 with Home
  Assistant lifecycle guidance instead of dispatching native s6 actions.
- Keep the fork's entrypoint contract: `/entrypoint-local.sh` still applies container-fixes
  before `exec /run.sh`, and the fork identity (slug, `image`, `url`, description) still wins
  over the upstream `config.yaml` block.
- Adopt upstream's version-aware changelog test and extend it for the fork's fourth component
  (`1.3.7.1`), so the published version can never drift from its changelog section again.

### Verified

- Local regression suite: 158 tests, 1 failure, 1 skipped. The failure is the pinned upstream
  defect (`test_gateway_supervisor_rejects_changed_parent_before_launch`: asserts `parent
  changed`, while the logger reports `parent-death contract failed (errno N)`); it fails
  identically on a pristine upstream v1.3.7 checkout, so the fork's CI rule allows exactly it.
- `/run.sh` patch compatibility (container-fixes `fix_run_sh`): the dashboard-start patches 5a
  and 5g are **retired** by this rebase — upstream replaced the `python -c … start_server` body
  they patched with the real launcher script. Patches 5b, 5c, 5d, 5e, 5f, 5h and 5i still apply.
- Container and Home Assistant Supervisor restart/update verification of this release is still
  pending (see the handoff README).


All notable changes to the Hermes Agent Home Assistant add-on are documented here.

The format follows the spirit of [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Versions match the add-on `version` in `config.yaml`.

## [1.3.7] - 2026-10-05

### Fixed
- Reject dashboard gateway Start/Stop/Restart requests with HTTP 409 and Home Assistant add-on lifecycle guidance before upstream Hermes can spawn native service-manager actions. The add-on's fixed gateway slots remain owned by its `run.sh` supervisor; this does not add individual slot controls.

### Verified
- Local focused RED/GREEN tests, pinned Hermes `439334127f` route-handler smoke, and full isolated unittest discovery. Container and Home Assistant Supervisor behavior have not been tested.

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

## [1.3.6] - 2026-10-04

### Fixed

- Keep the Hermes source root importable when the gateway launcher re-enters itself after a PM interpreter replacement. PM relaunches with `-I -c "sys.path.insert(0, <root>)..."`; the 1.3.5 identity re-exec (`<store-python> -I <launcher>`) dropped that insert, and `-I` ignores `PYTHONPATH`, so every supervised gateway start failed with `ModuleNotFoundError: No module named 'hermes_cli'` (gateway slot restart loop, API port down). The root is now carried in a private, consumed environment variable and restored before bootstrap.

### Verified

- New process-boundary regression test with a managed interpreter that does not have the source on its own path (the real PM store layout): fails on 1.3.5 with the production `ModuleNotFoundError`, passes with the fix.
- Full suite with Python 3.14.7: 150 run; the same 2 failures and 1 import error (`yaml` missing) occur identically on unpatched `main` in the test container.
- Live HA OS add-on (1.3.5, Hermes `1298c8e`): with the captured environment of a failing supervised start, the shipped launcher fails with the same traceback; the patched launcher completes `gateway status`.
- Container and Home Assistant Supervisor restart/update verification has not been performed for this candidate.

## [1.3.5] - 2026-10-04

### Fixed

- Preserve the real Hermes dependency-root helper while bypassing interactive sticky-profile selection for add-on-managed gateways.
- Run package-manager bootstrap before consuming the add-on's API/profile handoffs, and restore a real, discoverable gateway command after an interpreter replacement.
- Start dashboards through a real script with their exact assigned profile home, including primary, named, legacy flat and custom layouts.
- Keep the Desktop backend's script path and command arguments stable across interpreter replacement; read its Basic-auth password from stdin only after that replacement.
- Forward authenticated detailed-health requests to the selected profile API on ingress and enabled direct HTTP/HTTPS listeners, preserving existing web authentication and public liveness behavior.

### Verified

- Local automated regression checks and real process replacements with isolated synthetic bootstrap/CLI fixtures; no model or provider requests.
- Real task-local nginx requests over HTTP and HTTPS for two profiles: shared-key authentication, wrong/missing credentials, public health, existing web authentication and disabled direct API routes.
- Container and Home Assistant Supervisor restart/update verification has not been performed for this candidate.

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

### Security

- Require enabled API credentials to contain at least 16 printable ASCII characters after trimming; reject Hermes' known placeholders, single-quote and backslash characters, dotenv interpolation syntax, controls, and non-ASCII input.
- Serialize enabled credentials as single-quoted literals that round-trip identically through Bash and python-dotenv; keep `API_SERVER_KEY` empty when the API is disabled.
- Keep rejected values out of startup errors, reject line breaks before dotenv serialization, and reject malformed or multiline `env_vars` and `profile_env_vars` records.

### Verified

- `PYTHONDONTWRITEBYTECODE=1 /usr/bin/python3 -B -m unittest discover -s tests -q` - 108 tests OK, 2 skipped.
- Shell syntax for every shipped `hermes_agent/*.sh` file, YAML parsing, and `git diff --check` passed.
- Differential comparison with Hermes' authoritative `has_usable_secret(..., min_length=16)` predicate matched all 30 credential cases in the supported single-line matrix; 2 CR/LF serialization-hardening cases were separately rejected.
- Complex safe credentials round-tripped byte-for-byte through both Bash and Hermes' production python-dotenv parser.

## [1.3.0] - 2026-07-19

### Added

- Add an opt-in Hermes Desktop remote backend on container port 9119 using the official `hermes serve` contract and the existing access password.
- Keep the Desktop port unmapped and the feature disabled by default; Home Assistant's Network settings choose the external host port.

### Security

- Require `access_password` whenever the Desktop backend is enabled.
- Warn that authenticated Desktop access provides full agent control, carries the accepted opt-in risk, and belongs only on a trusted LAN/VPN/Tailscale path rather than the public internet.

### Verified

- `PYTHONDONTWRITEBYTECODE=1 /usr/bin/python3 -B -m unittest discover -s tests -v` - 66 tests OK, 1 skipped.
- Shell syntax, Python AST, YAML parsing, public-repository hygiene, and `git diff --check` passed.
- An isolated Home Assistant test repository built and installed the add-on without changing the stopped production installation.
- The live remote backend enforced authentication, exposed the `default` and `worker` profiles, minted single-use WebSocket tickets, and rejected ticket reuse.
- The codesign-verified Hermes Desktop app connected through its real cookie-authenticated remote path and reached the expected provider onboarding for the credential-free test profile.
- Independent code reviews completed with no blocking findings.

## [1.2.1] - 2026-06-18

### Fixed

- Keep Dashboard Chat WebSockets open behind Home Assistant Ingress by enabling `ingress_stream` and rewriting proxied dashboard API `Origin` headers to the dashboard backend host.
- Fix direct-port Dashboard Chat WebSocket authentication when browsers send Basic auth plus the SPA `?token=` query parameter.

### Verified

- `git diff --check` - OK.
- `bash -n hermes_agent/nginx-render.sh hermes_agent/run.sh` - OK.
- `python3 -m py_compile hermes_agent/dashboard-patches.py` - OK.
- `uv run --with pytest python -m pytest -q tests/test_dashboard_ingress_patches.py` - 21 passed.
- `uv run --with pytest python -m pytest -q` - 53 passed, 1 skipped.
- Live Home Assistant local DEV add-on lifecycle smoke - `/dashboard/api/pty` streamed binary TUI frames, `/dashboard/api/ws` emitted `gateway.ready`, and `/dashboard/api/events` stayed open through both HA Ingress and direct HTTPS access.

## [1.2.0] - 2026-06-15

### Added

- Add `profiles_base` so bare multi-profile names can default to upstream-style `.hermes/profiles/<name>` storage.

### Changed

- Use one shared Hermes Agent clone and virtualenv for all profiles instead of installing Hermes separately for every profile.
- Preserve existing flat profile directories such as `/config/amy` during upgrades when the new `profiles_base` target does not exist yet.

### Fixed

- Fix the shared-install dashboard patch helper call so startup uses the shared `SRC_DIR` instead of removed per-profile variables.
- Correct Home Assistant option text and storage documentation for the shared install and `profiles_base` behavior.
- Fix the README OpenAI-compatible API authentication example formatting.

### Verified

- `git diff --check` - OK.
- `PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover -s tests -q` - 53 tests OK, 1 skipped.
- `PYTHONDONTWRITEBYTECODE=1 <test-interpreter> -m pytest -q` - 52 passed, 1 skipped.
- `bash -n hermes_agent/run.sh hermes_agent/profile-init.sh hermes_agent/nginx-render.sh` - OK.
- YAML parse checks for `repository.yaml`, `hermes_agent/build.yaml`, `hermes_agent/config.yaml`, and `hermes_agent/translations/en.yaml` - OK.
- Live Home Assistant local DEV add-on smoke - two profiles started, `nginx -t` passed, one shared Hermes clone/venv was reused, restart reused the shared install marker, primary and secondary Dashboard/Terminal/API routes returned HTTP 200, HA Ingress Dashboard WebSockets returned `HTTP/1.1 101 Switching Protocols` for `/api/pty`, `/api/ws`, and `/api/events` on both root and `/profile/amy`, and legacy flat profile preservation passed.

## [1.1.2] - 2026-06-15

### Added

- Add this Home Assistant add-on changelog so users can see what changed before updating.

### Changed

- Clarify persistent storage documentation: container `/config` maps to the add-on's private `addon_config` storage, not the normal Home Assistant Core `/config` folder.
- Document where to find the same storage from HAOS/Samba via the `addon_configs` share.
- Correct the install-marker name shown in the storage tree for multi-profile aware versions.

### Fixed

- Fix the dashboard Chat tab behind Home Assistant Ingress by preserving WebSocket upgrades through the nginx `/dashboard/api/` proxy.
- Forward `Upgrade` and mapped `Connection` headers for dashboard API routes while keeping normal REST calls on the same path sane.
- Add regression coverage for primary and multi-profile dashboard API routes across Ingress, direct HTTP, and direct HTTPS render modes.

### Verified

- `git diff --check` - OK.
- `python3 -m pytest -q tests/test_dashboard_ingress_patches.py` - 18 passed.
- `python3 -m pytest -q` - 43 passed, 1 skipped.
- `bash -n hermes_agent/nginx-render.sh hermes_agent/run.sh` - OK.
- `python3 -m py_compile hermes_agent/dashboard-patches.py` - OK.
- Live Home Assistant local DEV add-on smoke - add-on started, `nginx -t` passed, dashboard returned HTTP 200 through Ingress, and `/dashboard/api/pty`, `/dashboard/api/ws`, and `/dashboard/api/events` returned `HTTP/1.1 101 Switching Protocols`.

## [1.1.1] - 2026-06-04

### Fixed

- Fix dashboard startup regression by executing the installed dashboard patch helper directly instead of relying on a non-guaranteed `python` alias.
- Use the helper's `#!/usr/bin/env python3` shebang, matching the Python executable installed in the add-on image.
- Add a regression assertion so startup does not reintroduce the bare `python` dependency.

### Verified

- `python3 -m unittest discover -s tests` - 43 tests OK, 1 skipped.
- `python3 -m py_compile hermes_agent/dashboard-patches.py tests/test_dashboard_ingress_patches.py tests/test_multi_profile.py` - OK.
- `bash -n hermes_agent/run.sh hermes_agent/profile-init.sh hermes_agent/nginx-render.sh` - OK.

## [1.1.0] - 2026-06-01

### Added

- Add multi-profile mode via `profiles`; the first profile keeps the existing root URLs and additional profiles are exposed under `/profile/<name>/...`.
- Add Home Assistant-compatible `profile_env_vars` for per-profile `.env` overrides.
- Render nginx routing per profile for dashboard, terminal, and API access.
- Add profile initialization support and per-profile tmux/session naming.
- Add regression tests for multi-profile behavior.

### Changed

- Keep Hermes Gateway in the foreground under Home Assistant s6 so Supervisor tracks the add-on lifecycle correctly.
- Update README and translations for multi-profile support.

### Notes

- Includes the v1.0.8 gateway foreground fix that was tagged but not published as a GitHub Release.
- Thanks to @imcvampire for the multi-profile work.

### Verified

- `python3 -m unittest discover -s tests -q` - 43 tests OK, 1 skipped.
- `git diff --check` - OK.
- `python3 -m py_compile hermes_agent/dashboard-patches.py tests/test_dashboard_ingress_patches.py tests/test_multi_profile.py` - OK.
- `bash -n hermes_agent/run.sh hermes_agent/nginx-render.sh hermes_agent/profile-init.sh` - OK.

## [1.0.8] - 2026-05-28

### Fixed

- Keep Hermes Gateway in the foreground under Home Assistant s6 supervision.
- Ensure Supervisor can correctly track the add-on lifecycle instead of losing sight of a backgrounded gateway process.
- Add a regression test for the foreground gateway behavior.

### Notes

- Tagged as v1.0.8 and later included in the v1.1.0 GitHub Release notes.

## [1.0.7] - 2026-05-17

### Fixed

- Fix dashboard asset loading behind Home Assistant Ingress for modern Hermes dashboard builds.
- Add an add-on-controlled `import.meta.url` fallback for dashboard base-path detection so API, plugin, router, and asset paths keep working when Home Assistant Ingress uses a long random prefix.
- Force Vite to emit relative dashboard asset URLs with `base: "./"` for modern and legacy dashboard sources.
- Rebuild stale dashboard bundles when `index.html` still contains absolute `/assets/...` references.
- Keep legacy dashboard compatibility patches and partial-patch repair behavior.
- Avoid polling `/v1/health` when the API server is disabled, preventing misleading nginx `connect() failed` log noise.

### Changed

- Update the landing page so `/v1/health` is shown as optional API server health, not Gateway health.

### Notes

- Thanks to @imcvampire for the logs and browser Network screenshot that exposed the remaining asset-path failure.

### Verified

- `python3 -m pytest -q` - 13 passed.
- `git diff --check` - OK.
- `python3 -m py_compile hermes_agent/dashboard-patches.py` - OK.
- `bash -n hermes_agent/run.sh` - OK.
- Independent code review passed with no blocking findings.

## [1.0.6] - 2026-05-16

### Fixed

- Fix Home Assistant add-on startup failure introduced in v1.0.5 when direct HTTP/HTTPS ports are enabled.
- Move `map_hash_bucket_size 128;` into the main nginx template before the first `map` block.
- Remove the late duplicate directive from the direct-ports include.
- Add a regression test that locks the directive ordering.

### Verified

- Verified on a real Home Assistant add-on installation with a fresh `/config` runtime.
- Upgrade/start path reached `nginx reloaded` and `All services started`.
- Ingress landing page, dashboard, dashboard deep links, dashboard assets, dashboard API, and `/v1/health` returned 200.

### Notes

- Thanks to @imcvampire for reporting the post-v1.0.5 dashboard startup problem.

## [1.0.5] - 2026-05-16

### Fixed

- Fix Home Assistant add-on startup failures caused by fragile dashboard source patching after upstream Hermes dashboard changes.
- Move dashboard source rewriting into a tested helper script instead of multi-expression `sed` calls.
- Skip source patches for modern Hermes dashboards that already support proxy prefixes via `X-Forwarded-Prefix`.
- Repair partially patched v1.0.4 dashboard sources from failed previous starts.
- Keep legacy fallback patches for older root-path-only Hermes dashboard sources.
- Forward the dashboard prefix from Home Assistant Ingress or custom reverse proxies to Hermes.
- Add regression tests for modern upstream, legacy fallback, and the `sed` delimiter failure class.

### Notes

- Thanks to @imcvampire for pinpointing the `sed` delimiter crash in PR #4.

## [1.0.4] - 2026-04-24

### Fixed

- Pass a loopback `Host` header to the upstream dashboard when proxying through nginx.
- Improve dashboard reverse-proxy compatibility after the v1.0.3 dashboard hardening work.

### Notes

- Tagged as v1.0.4 but not published as a separate GitHub Release.

## [1.0.3] - 2026-04-20

### Fixed

- Make the dashboard work fully behind Home Assistant Ingress, direct `/dashboard/` mounts, and custom reverse proxies.
- Replace the previous `BASE = "."` workaround with runtime base-path computation from `import.meta.url`.
- Patch `api.ts`, `usePlugins.ts`, and `vite.config.ts` idempotently in-container so API calls, plugin assets, router paths, and built HTML assets stay correctly prefixed.
- Fix the landing-page Gateway indicator by switching the probe to the public `/v1/health` endpoint instead of authenticated `/v1/models`.
- Protect direct-port `/dashboard/api/*` routes with the dashboard session token while keeping `/dashboard/api/status` public for health/status use.

### Added

- Add a README Security Model section covering Home Assistant Ingress, direct-port dashboard auth, and `/v1/*` API Bearer auth.

## [1.0.2] - 2026-04-20

### Fixed

- Restore dashboard compatibility with Hermes Agent v0.10.0 after upstream added a global auth middleware requiring an ephemeral Bearer token on `/api/` endpoints.
- Read the dashboard token from the HTML on startup and inject it server-side through nginx so Basic Auth and Home Assistant Ingress do not strip or consume it.

## [1.0.1] - 2026-04-20

### Added

- Add Web Dashboard support as a new tab alongside Hermes and Terminal through the Home Assistant sidebar.
- Add direct HTTP/HTTPS dashboard exposure through the `enable_dashboard` toggle.
- Expose dashboard pages for status, sessions, analytics, logs, cron, skills, config, and API keys.

### Fixed

- Fix Supervisor loop behavior by tracking the gateway PID correctly and always restarting the gateway, preventing container death on manual gateway restart.

## [1.0.0] - 2026-03-27

### Added

- Initial stable Home Assistant add-on packaging for Hermes Agent.
- Persistent AI agent with SQLite FTS5 long-term memory, self-improving skills, and multi-platform messaging.
- OpenAI-compatible API under `/v1/` for frontends such as Open WebUI and SillyTavern.
- Persistent web terminals backed by tmux through the Home Assistant sidebar.
- HTTP and HTTPS direct LAN access with auto-generated TLS certificates and optional Basic Auth.
- Full persistence for source code, venv, Homebrew, npm, Go, and agent data across add-on updates.
- Editable install so the agent can read and modify its own source code.
- Plugin architecture for custom tools, commands, and hooks.
- Container toolchain with Go 1.26, Node.js 22, Python 3.11, Chromium, GitHub CLI, Homebrew, uv, and common development tools.
- Support for `amd64` and `aarch64`.
