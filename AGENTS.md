# Repository Guidelines

## Project Structure & Module Organization
The root holds `configuration.yaml` as the primary entry point and `secrets.yaml` for sensitive values. Reusable domain bundles live under `packages/kennemer/`, where `helpers_*.yaml` define demo input helpers, `template.yaml` turns them into faux entities, and domain files (`covers.yaml`, `scripts.yaml`, `scenes.yaml`, `automations.yaml`) keep logic isolated. The Mushroom dashboard for the Shelly Wall Display lives at `dashboard.yaml` beside this folder. UI themes stay in `themes/`, and Home Assistant runtime artifacts (`home-assistant.log*`, `home-assistant_v2.db*`) should never be committed. Add new integrations under their own subfolder with descriptive filenames.

## Build, Test, and Development Commands
This repo syncs straight to the Odroid host running Home Assistant; any merged change goes live on the next reload. Validate changes on a workstation first with the official container image:
```
docker run --rm -v "$(pwd)":/config ghcr.io/home-assistant/home-assistant:stable \
  python -m homeassistant --config /config --script check_config
```
Run it before every PR to catch syntax or schema issues. When working directly on the Odroid, execute `hass --config . --script check_config` and only reload/restart once it passes to avoid downtime.

## Coding Style & Naming Conventions
Write YAML with two-space indentation and lowercase keys. Group related entities by domain (`light.living_room`) and use descriptive object IDs (e.g., `automation.kitchen_motion_notify`). Keep comments brief and prefer anchors over duplication when sharing values. If you introduce scripts or templates, add a header comment describing their trigger or purpose.

## Dashboard & UI Guidelines
Design for the small Shelly Wall Display: light mode only, cohesive room groupings, and zero more-info popups. Lean on Mushroom or approved HACS cards to keep typography consistent, minimize borders, and balance spacing. Each room should render as a single visual card containing scene selects, temperature readouts, ±0.5 °C buttons, blinds, and power metrics. Reuse existing helpers and template entities—rearrange layout, but do not remove functionality.

## Testing Guidelines
 Treat `check_config` as the minimum gate; configurations that touch automations should also be exercised against the Odroid instance (dev or maintenance window) to confirm entity names and dashboard behavior. Validate dashboard changes on the Shelly display to ensure typography, spacing, and button hit areas still work. When adding template sensors or scripts, create a temporary debug trace (e.g., `logger:` rules) and remove it once validated. Use the `test_` prefix for any helper blueprints or temporary validation assets and clean them off the Odroid promptly.

## Commit & Pull Request Guidelines
Follow an imperative, ≤72 character subject line (e.g., `Add hallway motion smoothing`). Include a focused body when behavioral context matters (triggers, dependencies, breaking changes). For PRs, provide: the motivation, a summary of impacted domains or entities, validation evidence (copy of `check_config` output or screenshots), and links to any related issues or forums. Keep unrelated changes out of scope and mention required secrets or hardware implications explicitly, plus any expected Odroid reboots or manual interventions.

## Security & Configuration Tips
Never commit credentials—extend `secrets.yaml` instead and document new keys in the PR. Assume the repository is public: redact device serials, Odroid access details, and exact geo-coordinates. If a configuration needs environment-specific toggles, expose them through helpers or `input_boolean` entities rather than editing core files per installation. Verify any Odroid-specific device paths (e.g., `/dev/ttyUSB*`) before merging.
