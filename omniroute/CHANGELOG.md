# Changelog

## 3.8.50.0

- Upstream base bumped to `v3.8.50` (tag `6f5d4e00`)
- Dropped `01-opencode-quota-cache.patch` — superseded upstream by #11234 (dashboard snapshot bridge in opencode-go preflight, with extra guards)
- Dropped `02-pr-9353.patch` — PR #9353 (reset-window fix) merged upstream as `696435efa`
- Renumbered remaining patches: `01-reset-aware-monthly.patch` (was 03), `02-quota-refresh-apikey.patch` (was 04) — both still apply cleanly against v3.8.50 (verified with dry-run + real apply, no duplicates)

## 3.8.49.6

- opencode-go quota: hydrate the domain cache from persisted snapshots on cold start (before the first background refresh)

## 3.8.49.5

- Fix quota cache refresh dropping apikey connections (opencode-go) every 60s — the root cause that left opencode-go quota empty and routing falling back to the dead public endpoint

## 3.8.49.4

- opencode-go quota: read from the domain cache (dashboard cookie scraper) instead of the broken public endpoint
- reset-aware scoring: add monthly window with re-normalized weights

## 3.8.49.3

- Run as root (fix `/app/data` permission error with HA addon_config mount)
- Smaller image: dropped the duplicate chown layer

## 3.8.49.2

- Multi-stage build: patches now applied to source BEFORE compile (they take real effect)
- PR #9353 (reset-window fix) now actually included in the compiled bundle

## 3.8.49.1

- Applied PR #9353: fix reset-window strategy prioritization

## 3.8.48-2

- Removed ingress and nginx reverse proxy (interferes with SSE/WebSocket)
- Access dashboard directly at `http://homeassistant:20128`

## 3.8.48-1

- Fixed EACCES permissions error on `/app/data` with `USER root`
- Added red diamond logo

## Initial release

- OmniRoute 3.8.48 (non-web, lightweight ~460 MB)
- 250+ AI providers, unified API proxy
- Auto-generated secrets on first boot
