# Changelog

## 0.3.0

- CI: fix `publicize-ghcr` job skipped on `workflow_dispatch` (was push-only)
- CI: `publicize-ghcr` script now checks package visibility and prints UI link (GitHub REST API has no PATCH visibility endpoint)
- CI: `GH_PACKAGES_PAT` only needs `read:packages` for the visibility check

## 0.2.9

- CI: `sync-store` checkouts use `persist-credentials: false` so pushes to `ha-addons` use the deploy key
- CI: `publicize-ghcr-packages` workflow documents PAT-based GHCR visibility for HA pulls

## 0.2.8

- Add `publish-release.sh` to commit and push `main` (Builder publishes on push)
- README: clarify add-on store install step title

## 0.2.7

- Publish container images to `ghcr.io/evalexio/{arch}-hapiyoai`; source repo is now `evalexio/hapiyoai` (private)

## 0.2.2

- Sync initial camera switch state from PiyoAI on startup and install
- HA switch entities now reflect actual enabled/disabled state instead of always starting ON
- Graceful fallback to all-ON if PiyoAI is unreachable at startup

## 0.2.0

- Per-camera enable/disable detection via MQTT command topics from PiyoAI
- Creates `switch.hapiyoai_<slug>` entities in HA when `commandListenerTopicPrefix` is available
- Subscribes to four state topics per camera: `state/enabled`, `state/resumed` (ON), `state/disabled`, `state/paused` (OFF)
- Publishes to `cmd/enable` and `cmd/disable` topics when toggled from the dashboard
- Dashboard shows ON/OFF toggle button per camera when command control is available
- `GET /api/config` now includes `commandControlAvailable`; `GET /api/cameras` includes `detectionEnabled`

## 0.1.9

- Persist detection logs to disk as daily JSONL files (`/data/detections/YYYY-MM-DD.jsonl`)
- Detections survive addon restarts — no more empty log after update
- Configurable retention period (default 7 days) with automatic daily purge
- New "Log retention (days)" setting in Setup page
- Remove in-memory detection deque

## 0.1.8

- Mobile-responsive sidebar: collapses to icon-only (56px) on viewports below 768px
- Camera grid adapts to narrow screens (single-column on phones)

## 0.1.7

- Fix dashboard camera cards to use fixed-height image area so cards stay uniform
- Add camera filter dropdown and prev/next pagination to Detection Log
- Add paginated backend API for detections (`camera_id`, `offset`, `limit` query params)
- Add mock preview pages and screenshots to README

## 0.1.6

- Fix broken logo in sidebar (move icon.png to Vite public/ so it survives build)

## 0.1.5

- Fix sensor incorrectly turning to detected on empty inference (objects: [])

## 0.1.4

- Show HAPiyoAI logo next to app name in sidebar
- Add Detection Log page (last 100 MQTT detection events, auto-refreshes every 3s, pause button)

## 0.1.3

- Suppress noisy httpx HTTP request logs

## 0.1.2

- Rename app to HAPiyoAI throughout the UI
- Add `panel_title: "HAPiyoAI"` so HA sidebar shows correct label
- Add `ingress_stream: true` for correct HA ingress behaviour

## 0.1.1

- Fix blank white page in HA ingress (relative asset paths)
- Fix API calls failing through HA ingress (relative fetch paths)
- Add hapiyoai icon and logo

## 0.1.0

- Initial release
- Discover cameras from PiyoAI via `api/ha/info`
- Configure per-camera MQTT topics via `api/ha/setup-mqtt`
- Dashboard showing last received image per camera
- `binary_sensor` entity per camera (detected / idle)
- Configurable idle timeout (global + per-camera override)
- Reconfigure flow with cleanup for removed cameras
