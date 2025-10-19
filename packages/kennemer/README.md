# Kennemer Package Conventions

Room dashboards and automations rely on a thin shim layer so we can ship UI/logic today and swap in real hardware later without touching Lovelace.

## Entity Naming

| Room          | Lights / Groups                | Blind / Cover            | CO₂ Sensor (future)             | Occupancy Sensor (future)               |
|---------------|--------------------------------|--------------------------|---------------------------------|-----------------------------------------|
| Entrance Hall | `light.entrance_hall_main`     | `cover.entrance_hall`    | `sensor.entrance_hall_co2_actual` | `binary_sensor.entrance_hall_occupancy_actual` |
| Reception     | `light.reception_main`         | `cover.reception_blind`  | `sensor.reception_co2_actual`   | `binary_sensor.reception_occupancy_actual`      |
| Hall GF       | `light.hall_gf_main`           | `cover.hall_gf_blind`    | `sensor.hall_gf_co2_actual`     | `binary_sensor.hall_gf_occupancy_actual`        |
| Library       | `light.library_main`           | `cover.library_blind`    | `sensor.library_co2_actual`     | `binary_sensor.library_occupancy_actual`        |

*Keep these IDs consistent. When hardware arrives, rename or group the real entities to match and everything lights up instantly.*

## Helper Shims

- `input_number.*` and `input_boolean.presence_*` stay in place even after commissioning. Automations should copy real sensor values into these helpers so the UI always has a valid fallback.
- Template sensors in `template.yaml` already prefer the real (`*_actual`) entities and fall back to helpers if the hardware is offline.

## Automation Skeletons

Disabled automations at the bottom of `automations.yaml` map each future occupancy sensor into the matching helper boolean. Enable and update the `entity_id` once the real sensor is available.

```
- id: presence_sync_entrance_hall
  enabled: false
  trigger:
    - platform: state
      entity_id: binary_sensor.entrance_hall_occupancy_actual
```

Follow the same pattern for CO₂ → helper input numbers if you need temporary overrides.

## HACS / Lovelace Resources

Ensure every wall tablet has these resources installed and loaded (Configuration → Dashboards → Resources):

- `custom:mushroom-*` (`lovelace-mushroom`)
- `custom:button-card`

Add more cards here as you introduce them so new displays can be staged quickly.

## Commissioning Checklist

1. Install / pair the device; rename the entity to the convention above.
2. Enable the matching automation (or edit the template) so the helper updates from the real sensor.
3. Run `python -m homeassistant --config /config --script check_config`.
4. Reload Home Assistant or the specific integration.
5. Verify the wall dashboard reflects the new live data.

Document extra wiring details or vendor-specific notes in this file as installations expand (e.g., KNX group addresses, Modbus IDs, calibration offsets).
