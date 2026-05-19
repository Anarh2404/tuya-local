# Tuvio / Bulb A60 Local Notes

These notes preserve the useful, non-secret research from the old local
`/Users/eugene/repos/tuvio` scratch directory. The canonical device profiles
are now the tracked YAML files in:

```text
custom_components/tuya_local/devices/bulb_a60_rgbcw_lightbulb.yaml
custom_components/tuya_local/devices/tuvio_tuh05de_humidifier.yaml
custom_components/tuya_local/devices/tuvio_tuh07de_humidifier.yaml
```

Do not copy local Tuya keys, TinyTuya `devices.json`, cloud snapshots, raw
device exports, or local IP/MAC inventories into this repository.

## Discovery

The DP mappings for these OEM models were collected from Tuya Cloud
`thing model` / `thing shadow` endpoints. Standard TinyTuya mappings and the
usual `iot-03` specification endpoints were empty for these devices.

## Manual Install Without HACS Fork

Tuya Local loads device YAML from its integration package:

```text
<ha-config>/custom_components/tuya_local/devices/
```

For a quick manual check, copy the three YAML files listed above into that
directory and restart Home Assistant. Any HACS update of Tuya Local can replace
those files, so the fork install path remains preferred.

When re-adding devices manually in Tuya Local, use the protocol versions from
local discovery:

| Device | Protocol |
|---|---|
| Bulb A60 | `3.3` |
| Tuvio TUH05DE | `3.5` |
| Tuvio TUH07DE | `3.5` |

Use current DHCP/DNS data for local IPs. Do not rely on stale scratch notes.
Close the Tuvio/SmartLife mobile app during initial local pairing if local
connections are unreliable; some Tuya Wi-Fi modules appear to tolerate only a
small number of concurrent local connections.

## Tuvio TUH07DE DP Map

| DP | Code | Meaning |
|---|---|---|
| 1 | `switch` | power |
| 2 | `switch_spray` | auto/continuous humidity mode |
| 5 | `switch_led` | tank light |
| 13 | `humidity_set` | target humidity, 40-70%, step 5 |
| 14 | `humidity_current` | current humidity |
| 16 | `sleep` | sleep mode |
| 19 | `countdown_set` | timer, `cancel` / `1H`-`12H` |
| 22 | `fault` | fault bitmap |
| 23 | `level` | intensity, `level_1`-`level_3` |
| 25 | `anion` | named UV in Tuya model |
| 26 | `heat` | warm mist |
| 101 | `brightness_control` | tank light brightness, 1-100 |
| 102 | `fan_countdown` | remaining timer, hours |

## Tuvio TUH05DE DP Map

| DP | Code | Meaning |
|---|---|---|
| 1 | `switch` | power |
| 2 | `switch_spray` | auto/continuous humidity mode |
| 5 | `switch_led` | Tuya model text describes this as warm mist |
| 6 | `work_mode` | tank light color |
| 10 | `temp_current` | current temperature |
| 13 | `humidity_set` | target humidity, 40-80%, step 5 |
| 14 | `humidity_current` | current humidity |
| 16 | `sleep` | sleep mode |
| 19 | `countdown_set` | timer, `cancel` / `1h`-`12h` |
| 21 | `sterilization` | sterilization |
| 22 | `fault` | fault bitmap |
| 23 | `level` | intensity, `level_1`-`level_3`, `level4` |
| 25 | `anion` | ionization |

## Bulb A60 DP Map

All observed Bulb A60 devices used product id `p0k9kcgmgiv9kdah`.

| DP | Code | Meaning |
|---|---|---|
| 20 | `switch_led` | power |
| 21 | `work_mode` | mode: `white`, `colour`, `scene`, `music` |
| 22 | `bright_value_v2` | brightness, 10-1000 |
| 23 | `temp_value_v2` | color temperature, 0-1000 |
| 25 | `scene_data_v2` | scene |
| 26 | `countdown_1` | timer, seconds |
| 34 | `do_not_disturb` | do not disturb |

The current profile covers stable local white-light control: power,
brightness, color temperature, timer, do-not-disturb, and scenes. Full RGB for
this model appears to use `control_data` / DP 28 rather than the standard
`rgbhsv` DP, so it should be handled separately after validating baseline
control in Home Assistant.
