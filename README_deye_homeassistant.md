# Deye AE-FS2.0-2H2 — Home Assistant Modbus sensors

Community reverse-engineered **read-only** register configuration for the native Home Assistant Modbus integration. This is an experimental public adaptation of locally observed FC03 holding registers, **not yet verified end-to-end in Home Assistant**. Some values and sign conventions may need adjustment for other firmware versions.

## Setup
1. Copy the `modbus:` block from `deye_ae_fs2_0_2h2_homeassistant_public.yaml` into `configuration.yaml`. If `modbus:` already exists, merge the list entry instead of adding a second top-level `modbus:` key.
2. Add `deye_modbus_host: 192.168.x.x` to your private `secrets.yaml` (replace with your local Modbus TCP endpoint). **Do not commit secrets.yaml.**
3. Check the Modbus TCP port (default 502), unit/slave ID (default 1), and device accessibility. These settings vary by adapter/logger and are not supplied in the public file.
4. Run Home Assistant configuration validation, then restart Home Assistant. Check sensor values against your inverter before relying on them.

The file targets **native Home Assistant Modbus**, not the custom Solarman integration. It does not configure a Solarman logger, serial number, or alternate protocol. If your existing working integration is Solarman, do not replace it with this file without confirming Modbus TCP access.

## Caveats
- Address numbering is zero-based. All registers are FC03 holding registers.
- BMS current and power signs are tentative; compare actual charge/discharge and import/export readings.
- `int16` is used for signed power/current; check your firmware's representation.
- No register writes or inverter configuration controls are included.
- All addresses/scales are derived from field observations, not official manufacturer documentation.
