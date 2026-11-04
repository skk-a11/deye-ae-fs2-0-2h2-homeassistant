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

# Deye AE-FS2.0-2H2 — Modbus Register Map, Monitoring and Configuration

## Overview

This repository contains a reverse-engineered register map and configuration reference for the **Deye AE-FS2.0-2H2** energy storage inverter.

The documentation is based on real device measurements, local Modbus communication, DeyeCloud configuration exports, captured read/write command structures, and comparisons between cloud-reported values and locally retrieved register data.

The goal of this project is to help users develop **fully local monitoring and configuration solutions**, without depending on DeyeCloud or an active internet connection.

The Excel workbook contains six worksheets covering configuration parameters, command structures, real-time monitoring, write operations, and selectable menu values.

## 1. Parameters

The **Parameters** worksheet contains the inverter configuration catalog extracted from DeyeCloud.

It includes:

- 178 exported configuration entries across 18 groups.
- 56 identified register addresses.
- Decimal and hexadecimal register addresses.
- Parameter names and configuration groups.
- Allowed value ranges and engineering units.
- Raw register values and display scaling factors.
- Comparisons between DeyeCloud values and locally captured data.
- Verification status and associated read commands.

The documented parameters cover battery configuration, charging and discharging limits, operating modes, energy management, export limitation, scheduled operation, inverter functions, and other device settings.

## 2. Read Commands

The **Read Commands** worksheet documents six DeyeCloud configuration read-command groups:

- Battery Settings
- Storage Inverter Work Mode 1
- Inverter System Settings
- Storage Inverter Work Mode 2
- Function Settings
- Smart Load Settings

Each entry includes the command group identifier, operation type, hexadecimal command content, and number of physical addresses.

These are DeyeCloud command structures and should not be confused with standard Modbus RTU frames.

## 3. Information

The **Information** worksheet provides an overview of the documented device, data sources, register coverage, and verification methodology.

The documentation combines DeyeCloud exports with locally captured register measurements to identify and validate parameter mappings.

## 4. Real-time Monitoring

The **Real-time Monitoring** worksheet contains 32 documented measurement entries, including register addresses, raw values, signed 16-bit interpretations, scaling factors, engineering units, and confidence levels.

The monitoring data covers:

- Battery Management System (BMS) information.
- Battery state of charge, voltage, and current.
- BMS charging and discharging current limits.
- Photovoltaic generation parameters.
- Grid power and energy flow.
- Household load and inverter operating measurements.

The register descriptions identify the available evidence and indicate where further validation is required.

Local monitoring uses Modbus holding-register reads (Function Code 03).

## 5. Write Format

The **Write Format** worksheet documents a captured DeyeCloud configuration write operation for the Battery Settings group.

It includes:

- Read and write command identifiers.
- Cloud operation types.
- Command structure and field layout.
- A recorded successful cloud operation.
- Acknowledgment structure.
- Relevant register/value encoding information.

**Important:** A successful DeyeCloud write operation does not automatically prove that the same command can be executed through a local Modbus connection.

The cloud protocol and local Modbus write behavior must be distinguished and validated separately.

## 6. DeyeCloud Menus

The **DeyeCloud Menus** worksheet documents selectable configuration options extracted from the device-specific DeyeCloud configuration rules.

It includes 156 data rows covering available menu choices and their numeric values.

For each option, the worksheet provides:

- Configuration group and parameter name.
- Hexadecimal command or field code.
- Candidate decimal Modbus register address.
- Input type.
- Numeric option value.
- English description.
- Original option label.
- DeyeCloud translation identifier.
- Additional notes where applicable.

Examples include:

| Setting | Code | Available values |
|---|---|---|
| Battery Type | `0x012D` | 0 = Lead-acid, 1 = Lithium, 2 = No battery |
| System Work Mode | `0x014E` | 0 = Green Power, 2 = Full Charge, 3 = Customized |
| Energy Management | `0x0149` | 0 = Battery First, 1 = Load First |
| Limit Control | `0x014A` | 0 = Grid Priority, 1 = Zero Export to Load, 2 = Zero Export to CT, 3 = Wireless CT |
| Grid Charging | `0x0148` | 0 = Disabled, 1 = Enabled |
| Solar Sell | `0x014D` | 0 = Disabled, 1 = Enabled |
| USB Switch | `0x0189` | 0 = Disabled, 1 = Enabled |

The worksheet also includes options for battery activation, inverter functions, scheduled charging, synchronization modes, and AC coupling.

Some field codes refer to individual bits within a register rather than independent register addresses.

## Local Communication

Local communication has been established using the Solarman V5 protocol and Modbus register access.

The documented approach supports reading inverter information over the local network without relying on DeyeCloud.

One useful mapping identified during testing is:

- DeyeCloud parameter code: `0x018A`
- Decimal register address: `394`
- Parameter: USB No-Load Keep Time

This correspondence supports the relationship between DeyeCloud parameter codes and local register addresses. However, individual mappings and write permissions should be verified before use.

## Verification and Reliability

This documentation distinguishes between several evidence levels:

**Locally verified:** Register values successfully retrieved from the device and compared against known operating measurements.

**Cloud-verified:** Parameter values, menu choices, or command responses confirmed through DeyeCloud.

**Partially verified:** Register interpretations supported by available evidence but requiring additional testing.

**Not yet verified:** Candidate register mappings or local write operations that have not been demonstrated on the physical inverter.

The workbook includes verification notes and confidence indicators where applicable.

## Project Goals

This research is intended to support the development of:

- A standalone local inverter monitoring dashboard.
- Local configuration and control interfaces.
- Home Assistant integrations.
- Real-time energy monitoring.
- Scheduled battery charging and discharging management.
- Configuration backup and comparison tools.
- Operation without dependence on cloud services.

## Safety and Limitations

This is an independent, community-oriented reverse-engineering project and is not official documentation from Deye.

The register map may differ between inverter models, firmware versions, and hardware revisions.

Writing incorrect values can cause unexpected inverter behavior, interrupt power delivery, or affect battery operation.

Grid protection and grid compliance parameters should not be modified using unverified register commands.

**Always read and back up existing configuration values before attempting any write operation. Validate changes carefully and confirm the resulting values by reading them back.**

## Privacy

The publicly shared workbook has been prepared to remove personal or device-identifying information, including local network identifiers and private measurement timestamps.

The technical register addresses, protocol information, configuration values, and documented observations have been retained for research and interoperability purposes.

## Contributions

Contributions, corrections, and additional verified register mappings are welcome.

If you have the same inverter model, please share reproducible findings, firmware-specific differences, and confirmed register behavior.

The long-term objective is to create a reliable, community-maintained reference for **fully local monitoring and configuration of the Deye AE-FS2.0-2H2 inverter**.
