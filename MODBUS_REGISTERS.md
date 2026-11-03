# Deye AE-FS2.0-2H2 — Modbus Register Reference

Community reverse-engineered **read-only** register descriptions for Home Assistant and other local Modbus clients.

> **Experimental:** Observed on one installation and compared with local scans / DeyeCloud readings (2026-11-03). This is **not** an official Deye specification and is not guaranteed to match other firmware revisions. The Home Assistant YAML has not been verified end-to-end on another installation.

## Reading and scaling

- Modbus function: **FC03 — Read Holding Registers**.
- Register addresses below are **zero-based** (the same convention as the local CSV scanner).
- Unless independently established otherwise, each value is treated as one **unsigned 16-bit** register.
- Engineering value = raw register value × scale. A scale of `1` means no conversion.
- Signedness and power-flow direction require further validation where noted.
- Do not write to these addresses; this reference covers reading only.

## Register details

| Address | Register / sensor | Description | Scale | Unit | Confidence |
|---:|---|---|---:|---|---|
| `372` | Bms Charge Voltage (`bms_charge_voltage`) | Charging voltage reported or requested by the battery management system (BMS). | `0.01` | V | High |
| `374` | Bms Charge Current Limit (`bms_charge_current_limit`) | Maximum charging current allowed by the BMS. | `1` | A | High |
| `375` | Bms Discharge Current Limit (`bms_discharge_current_limit`) | Maximum discharging current allowed by the BMS. | `1` | A | High |
| `376` | Bms Soc (`bms_soc`) | Battery state of charge as reported by the BMS. | `1` | % | High |
| `377` | Bms Voltage (`bms_voltage`) | Battery-pack voltage reported by the BMS. | `0.01` | V | High |
| `378` | Bms Current (`bms_current`) | Battery current reported by the BMS; direction/sign convention needs further validation. | `1` | A | Medium |
| `379` | Bms Temperature (`bms_temperature`) | Battery temperature reported by the BMS. | `0.1` | °C | High |
| `407` | Bms Nominal Capacity (`bms_nominal_capacity`) | Reported nominal battery capacity in ampere-hours; may reflect combined modules. | `0.1` | Ah | High |
| `408` | Bms Max Cell Voltage (`bms_max_cell_voltage`) | Highest individual cell voltage reported by the BMS. | `0.001` | V | High |
| `409` | Bms Min Cell Voltage (`bms_min_cell_voltage`) | Lowest individual cell voltage reported by the BMS. | `0.001` | V | High |
| `410` | Bms Max Cell Temperature (`bms_max_cell_temperature`) | Highest cell temperature reported by the BMS. | `0.1` | °C | High |
| `411` | Bms Min Cell Temperature (`bms_min_cell_temperature`) | Lowest cell temperature reported by the BMS. | `0.1` | °C | High |
| `412` | Bms Operating Batteries (`bms_operating_batteries`) | Count of batteries/modules reported as operating normally. | `1` | count | High |
| `413` | Bms Parallel Modules (`bms_parallel_modules`) | Count of battery modules reported as connected in parallel. | `1` | count | High |
| `1005` | Total Production Energy (`total_production_energy`) | Cumulative production energy counter; exact scope should be checked against firmware. | `0.1` | kWh | High |
| `1034` | Grid Voltage (`grid_voltage`) | Measured utility-grid AC voltage. | `0.1` | V | High |
| `1035` | Ac Frequency (`ac_frequency`) | Measured AC frequency. | `0.01` | Hz | High |
| `1036` | Ac Voltage (`ac_voltage`) | Measured AC voltage on the inverter AC side; relationship to grid voltage may vary by operating mode. | `0.1` | V | High |
| `1053` | Pv2 Voltage (`pv2_voltage`) | Voltage on the PV2 solar input. | `0.1` | V | High |
| `1067` | Battery Voltage (`battery_voltage`) | Battery voltage measured or reported by the inverter. | `0.01` | V | High |
| `1068` | Battery Current (`battery_current`) | Battery current measured or reported by the inverter; verify sign before inferring charge/discharge direction. | `0.01` | A | High |
| `1069` | Battery Soc (`battery_soc`) | Inverter-reported battery state of charge. | `1` | % | High |
| `1070` | Battery Temperature (`battery_temperature`) | Inverter-reported battery temperature. | `0.1` | °C | High |
| `1071` | Battery Power (`battery_power`) | Instantaneous battery power; positive/negative direction not independently guaranteed. | `1` | W | High |
| `1074` | Battery Total Discharge Energy (`battery_total_discharge_energy`) | Cumulative energy discharged from the battery. | `0.1` | kWh | High |
| `1076` | Battery Total Charge Energy (`battery_total_charge_energy`) | Cumulative energy charged into the battery. | `0.1` | kWh | High |
| `1080` | Grid Power (`grid_power`) | Instantaneous grid power; import/export sign convention should be verified. | `1` | W | High |
| `1084` | Home Load Power (`home_load_power`) | Instantaneous electrical power consumed by the home/load. | `1` | W | High |
| `1091` | Total Grid Purchase Energy (`total_grid_purchase_energy`) | Cumulative electrical energy purchased/imported from the grid. | `0.1` | kWh | High |
| `1095` | Home Load Daily Energy (`home_load_daily_energy`) | Energy consumed by the home/load during the current day. | `0.1` | kWh | High |
| `1096` | Home Load Total Energy (`home_load_total_energy`) | Cumulative electrical energy consumed by the home/load. | `0.1` | kWh | High |

## Not yet decoded / not included in public sensors

Registers `1028`, `1073`, `1130`, `1131`, `1132`, `1134`, and `1137` have not been assigned reliable meanings. The AC heatsink/radiator temperature register is also not yet identified. These are **not** safe to label as confirmed measurements.

## Important limitations

- **High confidence** means the mapping was consistent with the observations available during testing; it is not manufacturer certification. **Medium confidence** means interpretation still needs validation.
- Some values may be absent, stale, or encoded differently on other firmware versions or connection paths.
- Battery power, battery current, grid power, and BMS current should be checked against real operating states to determine sign and direction.
- The public Home Assistant configuration uses the native Modbus integration; it is not a Solarman-specific configuration.
- Connection settings (IP address, port, Modbus unit ID) depend on the user’s own local equipment and are deliberately not included here.

## Related files

- [`README.md`](README.md) — overview and Home Assistant setup.
- [`deye_ae_fs2_0_2h2_homeassistant_public.yaml`](deye_ae_fs2_0_2h2_homeassistant_public.yaml) — Home Assistant sensor definitions.

## Contributions

If you can confirm a register on the Deye AE-FS2.0-2H2, please share the register address, raw value, corresponding inverter/app reading, firmware version, and measurement conditions. **Remove IP addresses, logger serial numbers, credentials, and other private data before sharing.**
